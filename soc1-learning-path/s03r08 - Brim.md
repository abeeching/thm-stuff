# Brim

## [Task 1] Introduction

In brief: Brim is an open-source application that can process pcap and log files, and it primarily provides searching and analytics capabilities. A VM is provided with the exercise files.

## [Task 2] What is Brim?

Brim is an open-source desktop application that processes pcap and log files via the Zeek log processing format. It also makes use of Zeek signatures and Suricata rules -- thus, a knowledge of Zeek would be nice to have before jumping into the room. It can handle packet capture files (pcap files created via tcpdump, tshark, Wireshark...) as well as structured log files (e.g., those from Zeek).

Brim itself is built on open-source platforms:
- Zeek, for log generation
- Zed language, a log querying languge that provides keyword searches, filters, pipelines...
- ZNG data format, a data storage format that supports saving data streams
- Electron and React, for cross-platform UI

Brim offers a middle-of-the-road solution for investigating large pcap files -- Wireshark does not handle large pcap files (> 1 GB) that well, and while tcpdump and Zeek is more efficient for this purpose, time and effort is needed to harness their capabilities. Brim provides a simple, yet powerful GUI application that can help investigate these large pcap files.

Generally, the room suggests that you should handle medium-sized pcaps with Wireshark, log creation and event correlation with Zeek, and multiple logs with Brim. A more detailed breakdown of the differences between the three follows:
- Zeek does not provide a GUI, but Brim and Wireshark both do.
- Wireshark and Zeek allow you to sniff traffic, but Brim does not.
- Brim and Zeek are capable of processing logs, but Wireshark does not.
- Wirshark and Zeek can handle packet decoding, but Brim cannot.
- Only Zeek has support for scripting.
- Brim and Zeek have support for signatures, but Wireshark does not.
- Wireshark and Zeek can be used to extract files, but Brim cannot.
- Brim does OK with large pcaps, Wireshark doesn't do so well, and Zeek does pretty well.
- Zeek is a little harder to manage, by virtue of it being a CLI application.

## [Task 3] The Basics

Upon starting Brim, you are greeted with the landing page, which has three secitons and a window for importing files. There's also some information on supported file formats. The sections are
- Pools: data resources, investigated pcaps/log files
- Queries: list of all available queries
- History: list of all launched queries

Pools represent imported files. When you load a pcap, Brim will process the file and create Zeek logs, and it will then correlate them and display all findings on a timeline. Timelines provide capture start and end dates and can be used to help correlate events. There are also information fields (as in Zeek), and you can hover over them to get more details that may be helpful in creating custom queries. The rest of the log details can be found in the rightmost pane. The export function, located near the timeline, allows you to export results.

To correlate log entries, you can look at the Correlation section of the log details pane - this gives information about source and destination addresses, durations, and associated log files. This can help guide investigations. You can also right-click fields to filter, count fields, sort data, look into more detiails, and even perform Whois lookup or view the packets in Wireshark.

Queries are key in correlating findings and finding events of interest. Brim keeps track of the queries you have run in the History area. You can load specific queries from the Queries section by double-clicking a query you would like to execute. This will send the query to the search bar. Queries can have names, tags, and descriptions. Brim has twelve pre-made queries, located in the "Brim" folder, and these will help us understand query structure and get some basic information about a pcap. We can create new queries, of course; we simply need to click the `+` button.

For the first question, we load up `sample.pcap`. We can investigate the first DNS query here to get more detail, including the `qclass_name` - located at the bottom of this window:

![image](https://github.com/user-attachments/assets/e6e7c694-f222-4e2b-9565-e9efd3705d9d)

**[Task 3, Question 1] Process the `sample.pcap` file and look at the details of the first DNS log that appears on the dashboard. What is the `qclass_name`?** - `C_INTERNET`

We can also investigate the first NTP query in the dashboard pretty easily -- the `duration` value will be on the right-hand side of the details window:

![image](https://github.com/user-attachments/assets/83dcb4cc-2483-4fd2-ae9e-97a1ac152b38)

**[Task 3, Question 2] Look at the details of the first NTP log that appears on the dashboard. What is the `duration` value?** - `0.005`

And here, we can investigate the STATS query which is easily viewable on the dashboard. The `reassem_tcp_size` value is close to the bottom of the details window:

![image](https://github.com/user-attachments/assets/1d29a6f1-88b3-4f23-9674-44811315ba57)

**[Task 3, Question 3] Look at the details of the STATS packet log that is visible on the dashboard. What is the `reassem_tcp_size`?** - 540

## [Task 4] Default Queries

Now we'll go over some of the default queries that Brim comes with:
- Activity Overview: Provides general information about the pcap file, which can be helpful for further investigation and custom queries.
- Windows Networking Activity: Windows networking activity, source/destination addresses, named pipes, endpoint/operation detection. Helps with understanding Windows-specific events, such as SMB enumeration, logins, etc.
- Unique Network Connections: Information on unique connections and connection-data correlation, helping with identifying weird/malicious connections, suspicious activities, and beaconing.
- Unique DNS Queries, and HTTP Requests: Queries that provide DNS queries and HTTP methods, respectively. Helps with identifying anomalous DNS and HTTP traffic. Can filter for specific HTTP methods.
- File Activity: Lists the available files, helping to detect possible data leakage attempts and suspicious file activity. This also includes detected MIME types, filenames, and hash values (MD5 and SHA1).
- Show IP Subnets: Lists the available IP subnets, helping analysts determine possible communications outside scope and IP addresses that are abnormal.
- Suricata queries: Information based on Suricata rule results, category-based, source/destination based, and subnet based. Suricata is a threat-detection engine that can operate like an IDS and IPS. It can find anomalies like Snort does, and you can even use the same signatures with it.

We can use the File Activity query to figure out what files were there, including GIFs. There is only one GIF in the list, and we can look for more details by double-clicking the relevant packet. This will give us the name:

![image](https://github.com/user-attachments/assets/a1b934d1-3a48-4886-a29c-2839235ccb73)

**[Task 4, Question 1] Investigate the files. What is the name of the detected GIF file?** - `cat01_with_hidden_text.gif`

For this one we can make use of a custom query. The queries are Zeek-like (as we'll see in later tasks), though if we want to investigate the `conn` log file, we'll want to preface our query with `_path=="conn"`. We can cut out `geo.resp.country_code`, `geo.resp.region`, and `geo.resp.city`. Then we can sort by the cities -- in the GUI, you can just click on the column header "geo > resp > city":

![image](https://github.com/user-attachments/assets/9f2caee0-a89c-4729-b840-5e8f0efc7944)

**[Task 4, Question 2] Investigate the conn log file. What is the number of the identified city names?** - 2

We can investigate the Suricata alert queries provided by Brim -- we see a potential corporate privacy violation occur in communications between `10[.]10[.]57[.]178` and `44[.]228[.]249[.]3`. Conveniently enough there's an alert on the main dashboard that provides us the information we need, though we could search by `src_ip == 10.10.57.178 and dest_ip == 44.228.249.3`.

![image](https://github.com/user-attachments/assets/613dba7c-05d9-400d-8c7c-88a5bc08ab60)

**[Task 4, Question 3] Investigate the Suricata alerts. What is the signature ID of the alert category "Potential Corporate Policy Violation"?** - 2,012,887

## [Task 5] Use Cases

Before we develop any custom queries, we need to go over some basics of how to construct a Brim query first.
- Basic searching: You can search for any string or numeric value -- for instance, if you need to find logs containing, say, `127.0.0.1`, you can just type that in.
- Logical operators: Brim uses `and`, `or`, and `not` and they operate as they typically do (e.g., in Snort or Zeek). If I wanted to find IP addresses starting with 192 that communicate with NTP, `192 and NTP` will do.
- Filter values: We can filter for specific things using the syntax `(FIELD NAME) == (VALUE)`. If I wanted to filter by source IP, I could do `id.orig_h == (IP)`.
- List specific log file contents: You can get the contents of a log file by using the syntax `_path=="(LOG NAME)"`. For instance, if I needed to see the `conn` log, I'd run `_path=="conn"`.
- Count field values: This lets you count the number of available log files; just use the syntax `count () by (FIELD)`. If I wanted to count the available log files, I'd run `count () by _path`.
- Sort findings: The `sort` syntax allows you to, well, sort things. I can use the command `count () by _path | sort -r` to recurively sort the results from counting.
- Cut specific fields from a log file: We can use `cut (FIELD)` to cut out a specific field name. If I wanted the source IP from `conn`, I can run `_path=="conn" | cut id.orig_h`.
- List unique values: The `uniq` syntax lets you list unique values. If I wanted to list off unique source IPs, I could run `_path=="conn" | cut id.orig_h | sort | uniq`.

A lot of the syntax and functionality is similar to that in Zeek. You should use field names and filtering options (instead of relying on blindly searching for things), since Brim can index log sources really well.

Here are some examples for queries you'd set up in different use cases:
- Communicated Hosts: A common first step of an investigation is to identify what hosts were communicating on the network; that way, analysts can see if there is any suspicious and abnormal activity. The query would be `_path=="conn" | cut id.orig_h, id.resp_h | sort | uniq`.
- Frequently Communicated Hosts: We can expand upon the previous use case by figuring out which hosts were communicating most frequently. The query would be `_path=="conn" | cut id.orig_h, id.resp_h | sort | uniq -c | sort -r`.
- Most Active Ports: It's impossible (or, at the very least, nigh impossible) to hide packet traces. So even if an attacker does their very best to keep suspicious activities hiden, we can still track something down. One way to do this is to investigate the ports and services that are in use the most often. One query to accomplish this would be `_path=="conn" | cut id.resp_p, service | sort | uniq -c | sort -r count`. Another might be `_path=="conn" | cut id.orig_h, id.resp_h, id.resp_p, service | sort id.resp_p | uniq -c | sort -r`.
- Long Connections: Long-running connections may be indicative of an anomaly, especially if the involved client was not meant to serve a continuous service. We can investigate durations of connections with the query `_path=="conn" | cut id.orig_h, id.resp_p, id.resp_h, duration | sort -r duration`.
- Transferred Data: We can also investigate the transferred data size, which may be anomalous if (a) the client involved isn't meant to send and receive files and/or act as a file server and (b) the amount of transferred data is larger than expected with this in mind. One query for this might be `_path=="conn" | put total_bytes := orig_bytes + resp_bytes | sort -r total_bytes | cut uid, id, orig_bytes, resp_bytes, total_bytes`.
- DNS and HTTP Queries: We might want to investigate suspicious and abnormal domain connections and requests, which may be indicative of C2 communications and potentially compromised hosts. For DNS, we can use `_path=="dns" | count () by query | sort -r`, and for HTTP we can use `_path=="http" | count () by uri | sort -r`.
- Suspicious Hostnames: If we can spot suspicious and abnormal hostnames, we may be able to find rogue hosts. Thus, it's worth checking out the DHCP logs: `_path=="dhcp" | cut host_name, domain`.
- Suspicious IP Addresses: Similarly, identifying suspicious and abnormal IPs can be essential. Connection logs are stored in a single log file, so filtering through IPs may actually be more reliable. A query to handle this would be `_path=="conn" | put classnet := network_of(id.resp_h) | cut classnet | count() by classnet | sort -r`.
- Detecting Files: Investigating transferred files is important for finding malware or exfiltration of sensitive information. Our query is simply `filename!=null` in this case.
- SMB Activity: Useful to investigate to find malicious activities such as exploitation, lateral movement, malicious file sharing. Our query is `_path=="dce_rpc" OR _path=="smb_mapping" OR _path=="smb_files"`.
- Known Patterns: If you have signatures, attacks, and anomaly patterns available to you, you can look for known patterns in pcaps with Brim. Brim supports Zeek and Suricata logs, so if a given anomaly can be detected with either of those, Brim should be able to pick it up, too. You can use queries like `event_type=="alert" or _path=="notice" or _path=="signatures"` to look for known patterns.

## [Task 6] Exercise: Threat Hunting with Brim | Malware C2 Detection

The room provides a walkthrough through a large portion of this threat hunting exercise. We'll hone in on the most relevant parts of the walkthrough for these questions, though it's good to know what their thought process was first:
1. They first run `count() by _path | sort -r` to get an idea of what types of log entries there are.
2. The query `_path=="conn" | cut id.orig_h, id.resp_p, id.resp_h | sort | uniq -c | sort -r count` is used to get an idea of which hosts frequently communicated. The `10.22` and `104.168` IPs are abnormal.
3. They also look at relevant ports and services with the query `_path=="conn" | cut id.resp_p, service | sort | uniq -c | sort -r count`. Nothing abnormal was found, but there's a lot of DNS stuff going on.
4. They investigate DNS with the query `_path=="dns" | count() by query | sort -r`, and use VirusTotal to identify potentially malicious domains. The domains `hashingold[.]top` and `ouldmakeithappp[.]top` were found to be suspicious, and the IPs starting with `68.138` and `45.147` are suspicious.
5. Then we run the command `_path=="http" | cut id.orig_h, id.resp_h, id.resp_p, method, host, uri | uniq -c | sort value.uri` to investigate what files were interacted with. This is where we get the answer to the first question in this task--this is a pretty suspicious file download:

![image](https://github.com/user-attachments/assets/f2006198-4b68-4e8d-bc42-32b3d52ffdb1)

**[Task 6, Question 1] What is the name of the file downloaded from the CobaltStrike C2 connection?** - `4564.exe`

This detection is validated in VirusTotal, and we see that it is associated with CobaltStrike. Specifically, the address `104[.]168...` where the file came from is associated with the C2. We can look for the number of CobaltStrike connections over port 443 with this information. One way to do this would be to run the query `dest_ip==104[.]168[.]44[.]45 and dest_port==443 | count() by dest_port`. This gives us the following info:

![image](https://github.com/user-attachments/assets/800897c0-7504-429b-8fef-458d0836005a)

**[Task 6, Question 2] What is the number of CobaltStrike connections using port 443?** - 328

Now, they do one more thing, and that's investigate the Suricata logs with the query `event_type=="alert" | count() by alert.severity, alert.category | sort count`, and it turns out there are _329_ instances of unknown traffic. If we were to look into the unknown traffic, we'd see that a lot of it correlates with the CobaltStrike connectivity, so there might be something else we're missing.

We can run the query `event_type=="alert" | cut alert.signature | sort -r | uniq -c | sort -r count` and try to see if Suricata came up with any additional alerts. Turns out it did, and one of these - IcedID - is an additional C2 channel!

![image](https://github.com/user-attachments/assets/7a4cc20c-cc59-41fc-9a5b-93f1b5e8f09f)

**[Task 6, Question 3] There is an additional C2 channel used in the given case. What is the name of the secondary C2 channel?** - IcedID

## [Task 7] Exercise: Threat Hunting with Brim | Crypto Mining

Now we'll open the pcap file for Task #7. Here we need to look for cryptocurrency mining. A symptom of cryptomining is the overuse of essential corporate resources like computing power and internet. This may also involve the installation of third-party applications and tools, which can lead to more vulnerabilities.

Here's the process they use, up to the first question:
1. Use `count() by _path | sort -r` to figure out how many logs we're working with. There's not much.
2. Use `_path=="conn" | cut id.orig_h, id.resp_p, id.resp_h | sort | uniq -c | sort -r count` to look for frequently communicated hosts. There's an IP address starting with `192.168`... that may require our attention.
3. Then we look at the port numbers and services used with the query `_path=="conn" | cut id.resp_p, service | sort | uniq -c | sort -r count`.

By examining the results of this query, we can figure out how many connectons used port 19999:

![image](https://github.com/user-attachments/assets/2a7d1012-362a-4ecf-a03d-96a06664b36f)

**[Task 7, Question 1] How many connections used port 19999?** - 22

And we can also scan through this query to figure out what service was being used on port 6666:

![image](https://github.com/user-attachments/assets/7df9dd1e-d342-4e37-8cad-50debfc71f19)

**[Task 7, Question 2] What is the name of the service used by port 6666?** - `irc`

Either way, this is very evidently abnormal port usage. From here, we can use `_path=="conn" | put total_bytes := orig_bytes + resp_bytes | sort -r total_bytes | cut uid, id, orig_bytes, resp_bytes, total_bytes` to confirm our findings and find more issues. Turns out that the `192.168` address from earlier is incredibly problematic.

If we add `| id.resp_h==101.201.172.235`, we can see how many bytes were transferred in total over port 8888:

![image](https://github.com/user-attachments/assets/ca52083f-cb37-4e58-a692-d20107c828e1)

**[Task 7, Question 3] What is the amount of transferred total bytes to `101.201.172.235:8888`?** - 3,729

From here, we can do the following:
1. We can use Suricata to hunt for low-hanging fruits and correlate our findings: `event_type=="alert" | count() by alert.serverity, alert.category | sort count`
2. Suricata confirms that there is cryptomining activity, so we can now focus on which data pools are involved. We can list associated connection logs with the suspicious IP and run a VirusTotal search against the destination IP: `_path=="conn" | 192.168.1.100`
3. VirusTotal confirms that the destination server is a mining server. We may need to look at multiple addresses to confirm in a real-life situation, but this will do for now. We wrap up by looking for MITRE ATT&CK techniques: `event_type=="alert" | cut alert.category, alert.metadata.mitre_technique_name, alert.metadata.mitre_technique_id, alert.metadata.mitre_tactic_name | sort | uniq -c`.

To answer this last question, we'd want to add `alert.metadata.mitre_tactic_id` to the list of fields we `cut`. This yields:

![image](https://github.com/user-attachments/assets/9e961fc6-422a-4424-add1-a0415d0eb000)

**[Task 7, Question 4] What is the detected MITRE attack ID?** - TA0040
