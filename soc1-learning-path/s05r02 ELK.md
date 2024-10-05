# Investigating with ELK 101

## [Task 1] Introduction and [Task 2] Incident Handling Scenario

We're going to use the Kibana interface as a SIEM to search, filter, and create visualizations and dashboards. Our goal in this room is to investigate VPN logs for anomalies, though we'll get a brief overview of the components of Elasticstack.

The incident scenario is this: We are part of an SOC team working for the US-based `CyberT` company. We're looking at the VPN logs of employees and we're seeing some anomalies in how the VPNs are being used. Our gol is to investigate the VPN logs for January 2022. We've already got a few things to work off of:
- The VPN logs are being ingested into the index `vpn_connections`.
- The index contains the VPN logs for January 2022.
- The user `Johnny Brown` was terminated on January 1st, 2022.
- There are some failed connection attempts against some users, and that's worth investigating.

## [Task 3] ElasticStack Overview

ElasticStack is a series of open-source components that, when linked together, let users take data from any source and in any format and perform searches, analysis, and real-time visualizations. ELK (or, more precisely, ELBK) is an acronym.
- **E**lasticSearch: This is a full-text search and analytics engine used to store JSON-formatted documents, and this helps store, analyze, and perform correlation on the data. RESTful APIs are used to interact with the data.
- **L**ogstash: This is a data processing engine that takes data from different sources, applies filters, normalizes them, and then sends it to the destination. The destination might be Kibana, or it might just be a port that's listening for stuff. Logstash is split into the input portion (which defines the source of the logs), the filter portion (which lets you specify what kind of logs you'd like to ingest), and the output portion (where the logs need to go).
- **B**eats is a host-based agent, known as a data-shipper. Its job is to transfer data from the endpoints to ElasticSearch. Each beat is a single-purpose agent, sending specific data out. This includes, say, Winlogbeat, which transfers Windows events logs to ElasticSearch.
- **K**ibana is a web-based data visualization tool that works with ElasticSearch to analyze and investigate the data in real-time. You can create multiple visualizations and dashboards for more visibility as needed. More on this later.

In short:
- ElasticSearch is the database that is used to search and analyze data.
- Logstash helps collect, parse, and send off log data.
- Beats is used to collect data from multiple agents.
- Kibana is used for displaying and visualizing ElasticSearch data.

**[Task 3, Question 1] Logstash is used to visualize the data.** - nay

**[Task 3, Question 2] Elasticstash supports all data formats apart from JSON.** - nay

## [Task 4] Kibana Overview and [Task 5] Discover Tab

We are now ready to explore the different features of Kibana. We are able to deploy our own instance of Kibana and connect to it in this room.

We'll start at the Discover tab, where an analyst is likely to spend most of their time. This shows the ingested logs, or documents, the search bar, normalized fields, and more. Analysts can search for logs, investigate anomalies, and apply filters based on search terms and time periods here.

Some of the key information available in the dashboard interface are
1. Logs/documents: Each log is considered a single document, and it contains information about a single event, along with associated values and fields.
2. The fields pane: On the left, there is a list of fields parsed from the logs. You can add fields to the filter or remove them from the search as needed. If you click on the field itself, you can see the top five values for each field and how often they occur as a percentage. To add and remove fields to a filter, use the plus and minus buttons.
3. Index patterns: Located above the fields pane, this allows you to select the index pattern from a list. An index pattern is used to tell Kibana which data we want to explore, and it corresponds to certain properties of the fields. One index pattern may well point to many indices. When logs get ingested into ElasticSearch, they are normalized into corresponding fields and values, which is done by creating a dedicated index pattern for the data source.
4. Search bar: This is where you can add search queries and apply filters to narrow down results. Under the search bar, the Add Filter button can be used to apply a filter to the fields.
5. The time filter: You can narrow down results based on the time duration. There are many options available for filtering and limiting logs.
6. Time interval chart: This chart, located above the logs and documents, shows the event counts over time. This is also known as the timeline. You can select any of the bars on the bar chart to show the logs in that specified period, which is helpful if you're seeing a spike in logs at a given time. There's also a count of logs here, and this number changes depending on the timeframe selected.
7. The top bar: Located at the top right, this contains various options to save the search, open saved searches, share searches, and more.

The Quick Select tab, located next to the time filter (specifically the calendar icon), lets you select certain time frames quickly, e.g. last 15 minutes, last 30 minutes, last hour, and so on. Given a short enough timeframe, this will effectively cause the logs to refresh after the time has passed.

Documents are only shown in raw form by default. To make them easier to peruse, we can click into a document and select important fields to create a table showing only those fields. Simply highlight a field in a document, and click the "Toggle Column in Table" button on the left. This helps reduce noise and makes the data more presentable, and you can even save the table structure once you create it so that users can see it when they log into the dashboard.

Let's explore the Discover tab in the provided Kibana instance. Simply click the three bars at the top-left and click Discover:

![image](https://github.com/user-attachments/assets/2064b05d-a192-44c5-b502-d36a818d69d1)

We're already in the `vpn_connections` index, so we just need to get the data to appear. Once we get here, we'll need to set the dates appropraiately. We need to get the data from 31st December 2021 at 0:00 to 2nd Feb 2022 at 23:30. We can do this by setting the start and end times to absolute values.

![image](https://github.com/user-attachments/assets/7d9bee43-e2ee-480b-b604-c675767b348e)

If we wanted to account for the last thirty minutes of February 2nd, we'd have to set our endpoint to February 3rd, 2022 at 0:00. However, this is sufficient for our task. The number of logs is given in the top-left of the timeline/bar chart.

![image](https://github.com/user-attachments/assets/4e7a1055-0a48-4dc3-a033-4f32b54c1081)

**[Task 5, Question 1] Select the index `vpn_connections` and filter from 31st December 2021 to 2nd Feb 2022. How many hits are returned?** - 2861

Now let's analyze some of this data. Say we wanted to figure out the IP address that had the maximum number of connections; in other words, we'd like to know which source IP address shows up the most. If we click `Source_ip` in the fields pane, we can get a breakdown of the most commonly-occurring IP addresses:

![image](https://github.com/user-attachments/assets/08828e19-d4c0-4fea-9696-5849eda5e08a)

**[Task 5, Question 2] Which IP address has the max number of connections?** - `238.163.231.224`

And if we wanted to know the user who was responsible for the most amount of traffic, we simply check the field `UserName`:

![image](https://github.com/user-attachments/assets/a8959105-f948-44f5-b59e-0fefa2666d5e)

**[Task 5, Question 3] Which user is responsible for max traffic?** - James

We're going to make our lives a little bit easier before moving on by creating a table. In this case, we're being told to create a table with the fields IP, UserName, and Source_Country. Let's do that real quick. Open any of the documents, and scroll down to the relevant fields. Then click the "Toggle Column in Table" button:

![image](https://github.com/user-attachments/assets/846a69fa-ca8a-4c4a-b961-afa2bdd9370b)

Once we do this for each field, we get something that looks like this:

![image](https://github.com/user-attachments/assets/02e05cb7-5fd2-4896-90b6-d36864ffc18e)

From here, we can click Save in the top-right to save our table structure. Once you create the table, the Fields Pane changes a little bit to include "Selected fields" -- these are the fields that we've specifically toggled as columns in our table. From here, we can easily access whatever information we need. Say we want to figure out the Source IP with max hits for the user Emanda. We might've seen earlier that Emanda was one of the top five values for UserName, so let's click UserName and click the blue plus button next to Emanda's name:

![image](https://github.com/user-attachments/assets/d4cea66b-de86-4735-a794-23947a5ba8bd)

This apples the username Emanda as a filter, changing the logs that are displayed. This will also change the frequency of values in the fields, so if we were to check the `Source_ip` field now, we should see that a different IP address has the most hits:

![image](https://github.com/user-attachments/assets/6f3818c4-945e-42fc-afde-d5faac9cbadb)

**[Task 5, Question 4] Apply Filter on UserName Emanda. Which source IP has max hits?** - `107.14.1.247`

For this next question, I believe we're looking at the entire set of logs now, so we can remove the Emanda filter from the previous question by clicking the "X" button next to the filter. It'll be located under the search bar:

![image](https://github.com/user-attachments/assets/7a14ad18-a4e6-4ef3-9891-ef8e13661301)

Now we'll be back to the 2,861 logs from earlier. You'll notice a spike in the bar graph that takes place on January 11th. Click the bar corresponding to January 11th so we can filter only for logs in that timeframe. This changes the time range, so we'll need to switch it back later. If we wanted to figure out what IP is causing this spike, we can check the `Source_ip` field as per usual:

![image](https://github.com/user-attachments/assets/25b5dbb1-7626-4a6f-97b4-f8a9e069362b)

**[Task 5, Question 5] On 11th Jan, which IP caused the spike observed in the time chart?** - `172.201.60.191`

For this last question, we'll need to reset the date ranges to what we had before. We'll have to set them manually, unfortunately. Now let's try a more complicated filter. Say we want to get cnonections from the IP `238.163.231.224`. We can check the Source IP field and click the plus button next to it, since it's the most common IP address:

![image](https://github.com/user-attachments/assets/ce27a324-0b8d-4732-8d08-829f209aafd5)

...but now, let's also say we want to exclude New York from the entries. Since that's a state in the US, we'll want to check the `source_state` and exclude it from the filter by clicking the minus button next to it:

![image](https://github.com/user-attachments/assets/f9be0d32-5b87-47f5-95e4-1040b56ec45d)

This yields the following data:

![image](https://github.com/user-attachments/assets/23e069fa-6c7c-4a3c-851b-19b245dd4d2e)

**[Task 5, Question 6] How many connections were observed from IP `238.163.231.224`, excluding the New York state?** - 48

## [Task 6] KQL Overview

KQL, also known as the Kibana Query Language, is used to help search the ingested logs and documents in ElasticSearch. Note that Kibana also suppots what's known as the Lucene Query Language. You can switch to it by clicking "KQL" in the search bar, and then disabling KQL from there; however, our focus will not be on Lucene.

KQL allows us to search for logs based on free text searches and on field-based searches. Free-text search simply means that you can search the logs for given text. For instance, if I searched "security" in the logs, I would get all logs with the text "security" in them, regardless of the field. If we search for the text "United States" in `vpn_connections`, we'd get all documents containing "United States" throughout the logs. Note that this isn't exactly a free-text search; it's looking for specific values that show up in the logs. So searching for "United" wouldn't return anything.

Wildcards can be used, however, to help search for parts of text. For instance, "United" won't give us anything, but "United*" would -- it simply looks for all logs containing "United" and any other term.

KQL comes with a series of logical operators that we can use, as well:
- The OR operator: Returns documents where one or both of the terms are found
- The AND operator: Returns documents where both of the terms are found
- The NOT operator: Returns documents where the term is not found

Field-based search is used to look for specific values in specific fields. The syntax for a field-based search is `FIELD:VALUE`. For instance, if we wanted to find the user Smith in our table, we would type `UserName:Smith`. We can combine field-based searches with the logical operators listed above.

Let's try using some KQL! Say we want to look for logs where the source country is the United States, and where the users involved are James or Albert. We could construct the query `"James" or "Albert" and "United States"`. If we wanted to use a field-based search, `Source_Country:"United States" AND UserName:("James" OR "Albert")` would work too. Note that this question is specifically looking for logs that have the US as the source country -- I found the wording a little confusing here and tried to exclude the US from the search results.

![image](https://github.com/user-attachments/assets/619979b1-dafa-4970-9a08-478b6cea3141)

**[Task 6, Question 1] Create a search query to filter out the logs from Source_Country as the United States and show logs from User James or Albert. How many records were returned?** - 161

Now we just need to find instances of the user Johny Brown connecting to the VPN. If we use a free-text search, we'd just want to look up `"Johny Brown"`; otherwise, we'd want to look up `UserName:"Johny Brown"`. Either way, we get one result:

![image](https://github.com/user-attachments/assets/1a602f54-bd1e-4c35-b525-1a43fe8ccae1)

**[Task 6, Question 2] As User Johny Brown was terminated on 1st January 2022, create a search query to determine how many times a VPN connection was observed after his termination.** - 1

### More on KQL Searches

A few extra things about KQL searching that is not explicitly touched upon in the room:
- The quotation marks search for terms in the order provided. For instance, we could very well have searched for `United States`, but this would also return instances of `States United`...if any were in the logs.
- You need to escape certain characters with a backslash, UNLESS you surround them with quotation marks. Specifically, you must escape the `\`, `(`, `)`, `:`, `<`, `>`, `"`, and `*` symbols.
- You can create range queries, which can help with finding logs with certain values in fields. For instance, `http.response.bytes < 10000` returns all documents where the number of bytes in the HTTP response is less than 10000. We cna combine these with the logical operators to look for more complex ranges.
- Range syntax can be used to search for strings, IP addresses, and timestamps too. If we wanted to find timestamps earlier than two weeks ago, we may use `@timestamp < now-2w`. Note that you need to use acceptable date math formats.
- Date math: Simply put, date math expressions are those involving time units  and dates (either `now` or a date string ending with `||`). Time units include `y` for years, `M` for months, `w` for weeks, `d` for days, `h` or `H` for hours, `m` for minutes, and `s` for seconds. You can use `+` to add a certain amount of time to the search, `-` to subtract a certain amount of time, or `/` to round down to the nearest time unit. For instance, `2001.02.01\|\|+1M/d` will look for all logs starting from 2001-02-01 plus one month, rounded down to the nearest daay.
- Parentheses are used to specify precedence when using multiple queries. They can also be used for shorthand syntax if you're querying for many values in the same field, as I did in Task 6, Question 1.
- Wildcards can also be used to query multiple fields. If we had a field, say `datastream`, and it had multiple subfields, we could use `datastream.*:VALUE` to search them all. This may return errors of the subfields are of different types (i.e. some are numeric, others are strings).
- If you ever have nested fields, which are just arrays of fields that can be queried independent of each other, you can use the syntax `FIELD:{NESTED_FIELD:VALUE}` to find a specific value in a nested field. If you have multiple layers of nested fields, then you would have to use `FIELD.NESTED_LAYER_1{NESTED_LAYER_2:VALUE}`. You can combine multiple nested fields in the curly braces with the logical operators from earlier.

### More on Lucene Query Language

Lucene is used if you want to opt out of KQL, which you might do if you want to take advantage of Lucene's advanced features such as support for regular expressions for for fuzzy term matching (i.e. identifying similar, but not equal, values). Lucene does not support nested objects and scripted fields. I'd check the documentation for more details, but generally if you're using Lucene:
- Free-text and field-based searching works the same.
- To search for ranges, use bracketed range syntax, e.g. `status:[400 TO 499]` will return all logs with a status of 400 to 499.
- For open ranges, use wildcards, e.g. `status:[400 TO *]`.
- You can use the logical operators and parentheses described above for more complex queries.
- To use regular expressions, wrap them with forward slashes, e.g. `/(REGEX)/`.
- To use fuzzy searching, add `~` to the end of a word. Fuzzy searching uses edit distances of 2, which means it'll look for terms that can be found by making two changes at most (insertion, deletion, substitution, transposition of characters), though you can use an edit distance of 1 to catch most human misspellings. To use an edit distance of 1, add `~1` to the end of a word.
- You cannot combine fuzzy searching and wildcards.

## [Task 7] Creating Visualizations

We will now move on to the visualization tab, or the Visualize Library. You can access this library directly from the three bars at the top-left, or you can click on any field in the Discover tab and click the Visualize button. The Visualize Library allows you to create tables, pie charts, bar charts, and more based on data from the documents, which can help with creating simple, presentable representations of data.

ELK allows you to create correlations between multiple fields, and it's pretty intuitive: when you're creating your visualization, you can simply drag other fields into the visualization, and they are added in with the data. This helps with presenting the correlations between different data. You can also create a table with the values of selected fields as columns.

When you're done creating visualizations, you can save them by clicking the Save option on the top-right and filling out the form that appears. You can add visualizations to a dashboard if needed - more on this in the final main task of this room. When you're done, you can hit Save and Add to Library.

Say we want to create a table of usernames and IP addresses involved in failed attempts. We'll start by navigating to the Visualize Library, and we'll create a new Visualization. We'll specifically want the Lens visualization:

![image](https://github.com/user-attachments/assets/82c64106-b6a9-44c9-9f0e-b176b2382bd7)

First, we know we want to find _failed_ login attempts, so we'll want to filter for those. We can see information about login attempts in the `action` field. Specifically, if the `action` is `failed`, then we know that there's a failed login attempt. Now we know what to filter. Click the Add Filter button, set the field to `action`, the operator to `is`, and the value to `failed`. Once you save this filter, we should be able to start adding fields as needed:

![image](https://github.com/user-attachments/assets/08fbf2e1-e5cd-49d0-bdca-d17768a2385e)

We know we need usernames and IP addresses, so we can drag the fields `UserName` and `Source_ip` to the area where it says "Drop some fields here to start." Once we do, we'll want to change the visualization type from "Bar vertical stacked" to "Table." This gives us this, which will already constitute the answers to our questions in this task:

![image](https://github.com/user-attachments/assets/7aeffe5e-c721-44fd-92bf-86f49a3641d9)

But... let's make this table look a little nicer anyway. To the right (or at the bottom if you're looking at the AttackBox in split view), you can click each of the column names in the table. From there, you can change how the values are ranked/sorted, and you can even change the display names. We'll update the display names to be a bit more descriptive.

![image](https://github.com/user-attachments/assets/92ac3b68-82fc-4cd5-ba16-bc86599b3e75)

That's a little better. From here, we can (and probably should!) save this table. The image above, again, gives us the answers to the following questions.

**[Task 7, Question 1] Which user was observed with the greatest number of failed attempts?** - James

**[Task 7, Question 2] How many wrong VPN connection attempts were observed in January?** - 274

## [Task 8] Creating Dashboards

Dashboards can give us some good visibility into log collection. Multiple dashboards may be created to fulfill specific needs.

If we've saved some searches and visualizations, we can use this to create a custom dashboard. To do so...
1. Go to the Dashboard tab (three bars on the top-left!) and click on Create New Dashboard.
2. You can create visualizations from here, or you can click on Add From Library.
3. You can click on the visualizations and saved searches to add them to the dashboard.
4. Once you add things to the dashboard, you can adjust them. You can resize them by dragging the edges around, you can move them around by dragging the name of the dashboard element around, and you cand adjust their settings by clicking the cogwheel in the corner of each element.
5. Once done, save the dashboard by clicking the Save button at the top-right.

This is not required to complete the room, but it doesn't hurt to get familiar with creating dashboards.
