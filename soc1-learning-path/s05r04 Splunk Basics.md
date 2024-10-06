# Splunk: Basics

## [Task 1] Introduction and [Task 2] Connect with the Lab

Splunk is another one of the leading SIEM solutions, and it provides the ability to collect, analyze, and correlate network and machine logs in real time. We'll be exploring the basics and functionalities of Splunk, and learn how it provides better visibility of network activities and help with speeding up detection.

The Splunk instance can be accessed by starting the machine in the room, and navigating to the IP address (`http://IP`) in either the AttackBox or via the VPN connection.

## [Task 3] Splunk Components

There are three main components to Splunk.
1. The Forwarder - This is a lightweight agent installed on the endpoint to be monitored, and it simply collects the data and sends it to the Splunk instance without using up too many resources. It'll grab logs from web servers generating trafic, Windows machine logs including Windows Event Logs, PowerShell data, and Sysmon data, Linux host logs, and database-related logs.
2. The Indexer: It processes the data it receives from forwarders. It normalizes the data into field-value pairs, determines the type of the data being worked with, and stores them as events accordingly. This helps make the data easy to sift through later.
3. The Search Head is a component of Splunk's Search and Reporting app which can be used to search indexed logs. If a user searches for a term or utilizes Splunk's Search Processing Language, the request is sent to the indexer, and the indexer spits out relevant events in field-value pairs. You can also use this component to transform results into presentable tables and other charts and visualizations.

**[Task 3, Question 1] Which component is used to collect and send data over the Splunk instance?** - Forwarder

## [Task 4] Navigating Splunk

When you access Splunk for the first time, you will see the default home screen. The very top panel of the home screen is the Splunk Bar, and it allows you to see system-level messages, configure the Splunk instance, review the progress of jobs, receive miscellaneous information like tutorials, and search for things. You can also switch between installed Splunk apps from here; this ability will be at the left side of the Splunk Bar.

The Apps Panel is located at the left of the home screen, and it shows you the apps installed in the Splunk instance. Each Splunk instance comes preinstalled with the Search and Reporting app.

The Explore Splunk section allows you to quickly add data to the Splunk instance, add new Splunk apps, and access the Splunk documentation.

Beneath the Explore Splunk sectioon is the Home Dasboard. By default, there are no dashboards, however you can choose from many dashboards readily available in the Splunk instance. You can click the space to "Choose a home dashboard" and then you can use the dropdown menu or the "dashboards listing page" to access the entire set of dashboards. You can also create dashboards and add them to the Home Dashboard - these can be isolated from other dashboards by clicking on the "Yours" tab.

From the Home screen, if we navigate to the Add Data tab from the Explore Splunk section, we see this at the bottom:

![image](https://github.com/user-attachments/assets/c7686d1f-07b8-4e71-b3d8-f9d4bc2733fd)

You'll note that each option tells us what we can upload. There's some slight vagueness to the question, but we're looking for the option that explicitly states we can upload files and ports on the Splunk platform instance.

**[Task 4, Question 1] In the Add Data tab, which option is used to collect data from files and ports?** - Monitor

## [Task 5] Adding Data

Splunk can ingest any data, and when data is added to Splunk, it is processed and transformed into a series of events. These sources can be event logs, website logs, firewall logs, and more. They are split into categories:
- Files and directories: Most data you might be interested in comes directly from files and directories.
- Network events: Remote data from network ports, SNMP events from remote devices
- IT operations: Data from IT Ops apps, including Nagios, NetApp, Cisco...
- Cloud services: Data from cloud services such as AWS and Kinesis
- Database services: Data from daatabases such as Oracle, MySQL, Microsoft SQL Server
- Security services: Data from security services like McAfee, Active Directory, Symantec...
- Virtualization services: Data from virtualization services like VMWare
- Application servers: Data from app servers like JMX/JMS, WebLogic, WebSphere...
- Windows sources: Windows-specific inputs such as Windows Event Log, the registry, Active Directory, WMI, etc.
- Other sources: Other input sources that don't fit these categories, e.g. FIFO queues, scripted inputs, etc.

We'll focus on VPN logs in this room. If we click Add Data from the Splunk home screen, we see options toa dd various data sources. We'll use the Upload option in this case to send the data in from the local machine. The log file we're using is attached to the task, and can be found in the AttackBox in `/root/Rooms/SplunkBasic`.

To upload the data:
1. Select Source - select the log source.
2. Select Source Type - select what type of logs are being ingested.
3. Input Setings - select the index to dump these logs in, and the hostname to be associated with them.
4. Review - verify that all is good.
5. Done - you've uploaded the data! You can now check the Search and Reporting app to begin analyzing them.

There are plenty more logs that we can ingest, of course, and Splunk can support many source types. For now, though, we'll just look at the VPN log file. Let's upload it! We'll start by clicking Upload in the "Or get data in with the following methods" section:

![image](https://github.com/user-attachments/assets/a189d4b7-2a00-4413-a3e5-a88110f00da3)

And then we'll upload the file in question:

![image](https://github.com/user-attachments/assets/42d7f4b3-61f8-4637-bb19-c8a4226fb4c5)

Next, we'll see the Select Source Type page. Splunk does a pretty good job of setting the appropriate source type - since we have a JSON, it's able to parse it pretty well. We can thumb through the events on the right-hand side to make sure all looks well, but we can move on with this:

![image](https://github.com/user-attachments/assets/3ce55e96-1c92-4cc3-8a12-31d592f6b642)

Afterwards, we see the Input Settings. We don't have much reason to change the host settings right now, but the task explicitly asks us to create a new log index, `vpn_logs`. So we can do that by clicking Create a New Index, giving the name of the index, and leaving all other settings default.

![image](https://github.com/user-attachments/assets/fd85f927-ffec-446e-8c26-18825db061a5)

Here's what we have when all is said and done:

![image](https://github.com/user-attachments/assets/796834b5-02f5-4e53-a64d-0816a01ad9f8)

And then we can start searching. Let's see how many events we have to work with! The number of events is given underneath the search bar in Splunk's Search and Reporting app.

![image](https://github.com/user-attachments/assets/788b2105-f9ed-4902-89f0-77efc92131da)

**[Task 5, Question 1] Upload the data attached to this task and create an index `VPN_Logs`. How many events are present in the log file?** - 2862

Now we'll need to start searching through it. Splunk is pretty similar to ELK in how you do searching for values in fields. Instead of using a colon `:`, you would use an equals sign `=`, i.e. `FIELD=VALUE`. Thus, we can append `UserName="Maleena"` to our search to look only for events involving the user `Maleena`. Note that this entire query is case-sensitive.

![image](https://github.com/user-attachments/assets/bc561f0a-97bf-47a3-8064-a6c387376da5)

**[Task 5, Question 2] How many logs by the user `Maleena` are captured?** - 60

Great! Now let's look in other fields. We want to associate a user with a (presumably source) IP. So let's try searching the logs `Source_ip="107.14.182.38"` and see what happens. Our final query will be like the one displayed above, just with the `UserName` query replaced with the `Source_ip` query. Investigating the logs in this search tell us that there is only one `UserName` associated with this IP - `Smith`.

![image](https://github.com/user-attachments/assets/13865fff-10cc-406d-8456-f223a690a26c)

**[Task 5, Question 3] What is the name associated with the IP `107.14.182.38`?** - `Smith`

Now we'll have to start making some guesses at how Splunk handles logical operators, and by "guess" we really mean "check the documentation." Fortunately, Splunk is pretty consistent with the other SIEM we covered in this section, ELK, in that we can negate a query by using the word `NOT`. So, we can try using `Source_Country NOT "France"` and see what happens. Turns out this is exactly what we needed.

![image](https://github.com/user-attachments/assets/e0173a26-0914-4563-9116-2fb2a1a45487)

**[Task 5, Question 4] What is the number of events that originated from all countries except France?** - 2814

And last, but not least, we'll look at how many events were observed by the given IP. This is a simple `FIELD=VALUE` search -- `Source_ip="107.3.206.58"` should do the trick.

![image](https://github.com/user-attachments/assets/d18cf358-107c-4938-8550-3f0704d15f93)

**[Task 5, Question 5] How many VPN events were observed by the IP `107.3.206.58`?** - 14
