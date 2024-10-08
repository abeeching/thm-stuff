# Incident Handling with Splunk

## [Task 1] Introduction: Incident Handling

In security, an _incident_ is simply an event or action that has a negative impact on the security of a user, asset, or organization. This may include things that lead to a system crash, execution of an unwanted program, unauthorized access to sensitive information, defacement of a website, usage of USB devices against company policy, and more.

Splunk is actually quite useful when it comes to dealing with incidents, and so our goal in this room is to see how it can be used throughout the incident handling lifecycle.

## [Task 2] Incident Handling - Lifecycle

An analyst or an incident handler may be interested in knowing an attacker's tactics, techniques, and procedures. Once they can figure this out, they would be able to halt or defend against an attack, and maybe even prevent it from occurring. The incident handling process can be generalized to four steps.
1. Preparation: Level of readiness of an organization against an attack, including documentation of requirements and policies, implementation of security controls such as EDRs, SIEMs, IDS/IPS systems, etc., and training of staff.
2. Detection and Analysis: Detecting an incident and analyzing what's going on, including retrieving alerts from security controls, investigating root causes, and hunting for unknown threats.
3. Containment, Eradication, and Recovery: Taking action to prevent the incident from spreading, and then securing the network. Includes steps like isolation of the infected host, clearing the network of any infection traces, regaining control from the attack, and more.
4. Post-Incident Activity and Lessons Learned: Identifying loopholes in security posture that led to the inccident, and improving/fixing things so it doesn't happen again. Includes identifying weaknesses, adding detection rules, and perhaps implementing this knowledge into future staff training.

## [Task 3] Incident Handling - Scenario

In this room, we will be responding to an incident regarding a defaced website. We'll take the attack and map the activities to the seven phases of the Cyber Kill Chain, using OSINT and other findings to help fill in the blanks as needed:
1. Reconnaissance
2. Weaponization
3. Delivery
4. Exploitation
5. Installation
6. Command and Control
7. Actions on Objectives

You need not follow this exact sequence while investigating an actual event.

The actual investigation that we are doing would be considered as part of the Detection/Analysis phase. To do the job, we'll make use of Splunk, which is ingesting logs from the webserver itself, the firewall, Sysmon, Suricata, and more. The Data Summary tab allows us to check the visibility into both network-centric and host-centric activities. The logs are all present under `index=botsv1`, but here are the main sourcces of interest:
- WinEventLog, which contains Windows Event Logs
- WinRegistry, which contains logs related to registry creation/modification/deletion and more.
- XmlWinEventLog, which contains Sysmon event logs
- Fortigate_UTM, which contains Fortinet Firewall logs
- IIS, which contains...IIS logs
- Nessus:Scan, which contains results from the Nessus vulnerability scanner
- Suricata, which contains alerts from the Suricata IDS and what caused the alerts to trigger
- Stream:HTTP, Stream:DNS, and Stream:ICMP, which all ccontain network flows related to HTTP, DNS, and ICMP traffic.

Note that you will need to set the time frame to All Time (or at least set it before August 24, 2016 I believe? But it's faster to just set it to All Time).

## [Task 4] Reconnaissance Phase

Reconnaissance is the process of discovering and collecting information about a target, perhaps about the system in use, the web application, employees, locations, and more. We'll be looking for any reconnaissance attempts against `imreallynotbatman.com`. One good place to start would be to investigate the inbound communication towards the webserver. Thus, we may run a simple query like `index=botsv1 imreallynotbatman.com` to look for any inbound traces of our domain. The following log sources contain the domain:
- Suricata
- stream:http
- fortigate_utm
- iis

If we want to identify, say, the IP address performing reconnaissance activity, we may want to start by looking at the web traffic. One way to do this would be to look at `stream:http`, and specifically examine the `src_ip` field. Our search query would be `index=botsv1 imreallynotbatman.com sourcetype=stream:http`. This will only pull the information from the `stream:http` logs. We can even go further and investigate the top values of the `src_ip` field directly. We have two IPs here, `40.80.148.42` and `23.22.63.114`. The former IP is seen in many logs, so maybe that's worth checking out first.

If we want to confirm any suspicious about any of the IPs, we would want to click on the IP (or otherwise filter for it) and examine the logs. Some fields of interest might be the `user-agent`, POST requests, URIs, and more.

Now let's make sure that this IP is the one actually performing reconnaissance. If we check the Suricata logs for this IP, using the query `index=botsv1 imreallynotbatman.com src=40.80.148.42 sourcetype=suricata`, then we'll eventually find some interesting logs containing signature alerts. This is something we may want to note for later.

If we dive into the Suricata logs in more detail - perhaps by filtering for `event_type=alert` and examining the `alert.signature` field, we see a CVE explicitly referenced:

![image](https://github.com/user-attachments/assets/fde9f61a-48ac-47bd-9c2b-efcf70c0a858)

**[Task 4, Question 1] One Suricata alert highlighted the CVE value associated with the attack attempt. What is the CVE value?** - CVE-2014-6271

We can investigate the HTTP stream logs to see what's going on. We may want to consider examining the logs where `src_ip="40.80.148.42"`, and check the top values of the `uri` field. This gives us a big clue as to what CMS our web server is using.

![image](https://github.com/user-attachments/assets/e9bbd842-a17f-4dab-becd-ef49b3b3c423)

**[Task 4, Question 2] What is the CMS our web server is using?** - joomla

If a web scanner is in use, it's likely to have its own `http_user_agent` string. Checking the values of this field in our query from the previous question, we see a few references to _acunetix_, which turns out to be a scanner!

![image](https://github.com/user-attachments/assets/e704f4c9-b4f8-4d27-84d3-031765346f77)

**[Task 4, Question 3] What is the web scanner the attacker used to perform the scanning attempts?** - acunetix

If we presume that the IP `40.80.148.42` is one of the attacking IPs, then we can assume that the most common `dest_ip` is likely to be the IP address of our web server. There's many, many instances of the IP `192.168.250.70` being connected to, so that's likely to be ours. We could check the other IP address, and while it does connect to our server, the other IP makes explicit reference to the Joomla CMS, so...

![image](https://github.com/user-attachments/assets/72295772-b7e0-4557-91cd-495703ea7fac)

**[Task 4, Question 4] What is the IP address of the server `imreallynotbatman.com`?** - `192.168.250.70`

## [Task 5] Exploitation Phase

When an attacker finds a vulnerability, they need to exploit it to gain access to the system or server. Thus, our goal during this phase of the investigation is to determine how the attacker got into the system - what vulnerability did they leverage? We already know the following bits of information:
- Two IP addresses were sending requests to the server.
- One IP, `40.80.148.42`, was attempting to scan the server with IP `192.168.250.70`.
- The attacker was using Acunetix to perform a scan.

We can start by checking the number of events associated with each source IP against the web server. Our query might be `index=botsv1 imreallynotbatman.com sourcetype=stream* | stats count(src_ip) as Requests by src_ip | sort - Requests`. This query lets us use the `stats` function to display a count of the IP addresses in the field `src_ip`. If we wanted to, we could go further and create a visualization by selecting `Visualization -> Select Visualization`.

We may also want to narrow down the results toshow only those requests sent to our web server: `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70"`. This will give us some information about inbound communications. The `src_ip` field, when this query is ran, shows three IP addresses (1 local, 2 remote) that sent HTTP traffic to our web server. We may also be interested in checking out the `http_method` field -- there, we se that most requests are POST requests.

Let's check out those POST requests in more detail: `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST`. The `src_ip` field shows us the two IP addrsses sending all the POST requests to the server.

Some fields worth investigating at this point include
- `src_ip`
- `form_data`
- `http_user_agent`
- `uri`

If we check `uri`, `uri_path`, `http_referer` and other such paths, we'll see a lot of references to _Joomla_, which is a content management service (CMS) often used on web servers. To access the administrative console, we would have to navigate to `/joomla/administator/index.php`. This is important, since if we find a lot of POST requests to this URI, we can presume that a brute-force attack took place.

Let's look into the requests sent to that URI: `index=botsv1 imreallynotbatman.com sourcetype=stream:http dest_ip="192.168.250.70" uri="/joomla/administrator/index.php`. If we're suspecting a brute-force attack, we may also be interested in what's available in `form_data`. Thus, we could adjust this query to include that information: `index=botsv1 imreallynotbatman.com sourcetype=stream:http dest_ip="192.168.250.70" uri="/joomla/administrator/index.php | table _time uri src_ip dest_ip form_data`.

Investigating the `form_data` field reveals two interesting things: the use of a `username` field, with `admin` being the only thing passed to it, and a `passwd` field, which contains many passwords across all the events. This suggests that the attacker, at IP `23.22.63.114` was trying to brute-force the admin page. Also, since all the events happened within a short timeframe, we can safely assume that an automated tool was being used.

Unfortunately, this field isn't parsed all that well, so we'll need to bust out some regular expressions to figure out what passwords were attempted. If we want to display only the logs that contain the `username` and `passwd` values, we should use the query `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST uri="/joomla/administator/index.php" form_data=*username*passwd* | table _time uri src_ip dest_ip form_data`.

From here, we can apply some regular expressions using Splunk's `rex` function. To extract the passwords in this example, we'll want to use `rex field=form_data "passwd=(?<creds>\w+)"`. We'll want to append this to our query to get a list of all the passwords, so our final query will be `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST form_data=*username*passwd* | rex field=form_data "passwd=(?<creds>\w+)" | table src_ip creds`.

This yields the list of passwords used. There is something interesting about these results though. Most of the user agents come from what we assume is a Python script used to brute-force passwords... except for one. One was just someone using Mozilla. What's the deal with that? To investigate, we'll need to examine the `http_user_agent` field as well, so we'll add it to our table: `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST form_data=*username*passwd* | rex field=form_data "passwd=(?<creds>\w+)" | table _time src_ip uri http_user_agent creds`. Upon doing this, and sorting by the `http_user_agent`, we see that there was one password attempt that used `batman`, and the IP address it came from differs: `40.80.148.42`. This IP address was using the Mozilla browser.

We can approach this first question in a few different ways. Of course, we could just extract the information directly from the reading material in the task, but eh, let's do it ourselves. We could modify the first query to look specifically for URIs: `index=botsv1 imreallynotbatman.com sourcetype=stream* | stats count(uri) as URIs by uri | sort - URIs`. This gives us the following:

![image](https://github.com/user-attachments/assets/be16ed02-a671-465b-85db-fcf0e3e44e90)

There's a LOT of communication with the search URI, sure, but there's not much sense in trying to "brute-force" that. The `administator/index.php`, which we know to be a login form, does have a lot of communication, so this is likely to be where our brute-force is taking place.

We could also check out the `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST` query and investigate the top values for `uri`. Again, it's most likely that a brute-force attempt would take place at `administrator/index.php`, since this is where you would log in to the Joomla CMS administrator interface.

![image](https://github.com/user-attachments/assets/b720dcc3-6955-4674-b970-52569a9dd7cc)

**[Task 5, Question 1] What was the URI which got multiple brute force attempts?** - `/joomla/administrator/index.php`

If we check the results of `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST uri="/joomla/administrator/index.php" | table _time uri src_ip dest_ip form_data`, then we see that, really, only one username has been used.

![image](https://github.com/user-attachments/assets/406adb0e-0d58-4100-87ea-e757f3e8b449)

**[Task 5, Question 2] Against which username was the brute-force attack made?** - `admin`

As for the correct password, it's likely the attacker used it to log in from a browser instead of letting the Python script do it. So, I'd say our password is simply `batman`.

![image](https://github.com/user-attachments/assets/3bba21d6-110e-4a07-9645-23f9138bbdd8)

**[Task 5, Question 3] What was the correct password for admin access to the content management system running `imreallynotbatman.com`?** - `batman`

With this in mind, we know that there are 413 events, and we expect `batman` to have been tried twice. Also, passwords were only tried against one username - `admin`. Hence, each password tested by the Python script is probably unique.

![image](https://github.com/user-attachments/assets/0acb6c81-81e0-41b6-868f-d235cbce5240)

**[Task 5, Question 4] How many unique passwords were attempted in this brute-force attempt?** - 412

All of the logs with the Python script as the user agent have the same IP...so it's likely that this very IP was conducting the brute-force attack.

![image](https://github.com/user-attachments/assets/780671d6-8c66-445f-8c5f-e27445ca650f)

**[Task 5, Question 5] What IP address is likely attempting a brute force password attack against `imreallynotbatman.com`?** - `23.22.63.114`

And to figure out the IP the attacker used to log in, we just check the source IP for the log with the Mozilla user agent. See the screenshot for Question 3, above.

**[Task 5, Question 6] After finding the correct password, which IP did the attacker use to log in to the admin panel?** - `40.80.148.42`

## [Task 6] Installation Phase

Once an attacker exploits the security of a system, their next job will likely be to install a backdoor or an application that provides persistence. Or they'll just try to gain more control over the system.

In the Exploitation phase, we found that the web server got compromised via a brute-force attack, courtesy of a Python script. The attacker used one IP to carry out the attack, and then a different IP to get into the server. Now let's see what the attacker did once they got on the server!

There's many places to start during this phase of the investigation, though one good place would be to check out the presence of any files with certain extensions, say `.exe`. Our query might look something like this: `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" *.exe`.

Once we run this query, we're concerned with finding any fields that have values of interest. There's not a filename field to be seen, unfortunately, but there is a `part_filename{}` field with two filenames: `3791.exe` and `agent.php`. Did these files come from the attacker? We can find out by clicking the filename in the `part_filename{}` field to add it to the query, then look for the client IP by searching the `c_ip` field. Alternatively, you could run the query `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" "part_filename{}"="3791.exe"` and check `c_ip` there. Turns out, it totally did get uploaded by the attacker!

So now that we know that, we might be interested in figuring out if the file was ever executed. We'll want to show logs from the host-centric log sources to answer that question: `index=botsv1 "3791.exe"`. If you check the `sourcetype`, you'll see results for `xmlwineventlog`, `wineventlog`, and `fortigate_utm`, among others. We may want to consider checking if there are any Sysmon Event ID 1 logs - these would indicate that a process was created. So let's run the query `index=botsv1 "3791.exe" sourcetype="XmlWinEventLog" EventCode=1`. The output suggests that the file was, indeed, executed on the server. We can corroborate our findings with the other logs, if need be.

While we still have this query pulled up, we can get the hash value of the program. We simply need to look for `3791.exe` in the `CommandLine` field. Once we do so, we can check the `Hashes` field in the only event that shows up. It's a little hard to find since it's all on one line, but the MD5 hash for this program is in there.

![image](https://github.com/user-attachments/assets/bba64a48-d058-4d62-984a-6721c26c8a31)

**[Task 6, Question 1] Sysmon also collects the hash value of the processes being created. What is the MD5 hash of the program `3791.exe`?** - `AAE3F5A29935E6ABCC2C2754D12A9AF0`

And if we check the `User` field in the same event...

![image](https://github.com/user-attachments/assets/2e567eb5-7297-4b15-a847-04c0555736fc)

**[Task 6, Question 2] Looking at the logs, which user executed the program `3791.exe` on the server?** - NT AUTHORITY\IUSR

Popping the hash into VirusTotal yields the following:

![image](https://github.com/user-attachments/assets/0996ae96-cace-44b9-98e1-d23f3a6ec96d)

**[Task 6, Question 3] Search the hash on VirusTotal. What other name is associated with this file?** - `ab.exe`

## [Task 7] Action on Objectives

We know that the website was defaced in the end, so let's try to pinpoint what actually caused the defacement. We may want to start by examining the traffic flows. One approach to doing so is to investigate the Suricata logs and the IP addresses communicating with the server: `index=botsv1 dest=192.168.250.70 sourcetype=suricata`. It doesn't seem like anything's communicating with the server, so let's reverse the direction: `index=botsv1 src=192.168.250.70 sourcetype=suricata`. Doing so yields results...

...which is interesting in and of itself. Web servers typically do not originate traffic; the browser or the client would more likely be the starting point of any communication. We see three external IPs, including `40.80.148.42` and `23.22.63.114`, both of which we found to be suspicious earlier.

Let's investigate these one at a time. If we run the query `index=botsv1 src=192.168.250.70 sourcetype=suricata dest_ip=23.22.63.114`, we see that two PHP files in the URIs as well as a JPEG file. Where do you suppose this JPEG came from? If we run the query `index=botsv1 url="/poisonivy-is-coming-for-you-batman.jpeg" dest_ip="192.168.250.70" | table _time src dest_ip http.hostname url`, we see that it comes from `prankglassinebracket[.]jumpingcrab.com`, and the image was used to deface the website.

Again, we found the JPEG by looking at the URIs for logs where our server was the source IP:

![image](https://github.com/user-attachments/assets/2e1a1563-0218-4f8f-99fe-806822740541)

**[Task 7, Question 1] What is the name of the file that defaced the `imreallynotbatman.com` website?** - `poisonivy-is-coming-for-you-batman.jpeg`

If we search `index=botsv1 sourcetype="fortigate_utm" src="40.80.148.42"`, and check the `attack` field values, we only see one value that has anything to do with an SQL injection:

![image](https://github.com/user-attachments/assets/e46881a8-7572-40ef-992a-d59b2976aa02)

**[Task 7, Question 2] Fortigate Firewall `fortiage_utm` detected an SQL attempt from the attacker's IP, `40.80.148.42`. What is the name of the rule that was triggered during the SQL injection attempt?** - HTTP.URI.SQL.Injection

## [Task 8] Command and Control Phase

We know that the attacker uploaded a file to the server before defacing it, and while doing so, the attacker made use of a dynamic DNS to resolve a malicious IP. How did the attacker leverage DNS here? To investigate the communication to and from the adversary's IP address(es), we should examine network-centric log sources. Thus, we'll first Fortigate: `index=botsv1 sourcetype=fortigate_utm "poisonivy-is-coming-for-you-batman.jpeg"`.

When we run this query, we see Source and Destination IPs, as well as URLs. If we check the `url` field, we see the fully-qualified domain name (FQDN). We can go on to verify this result by checking other log sources: `index=botsv1 sourcetype=stream:http dest_ip=23.22.63.114 "poisonivy-is-coming-for-you-batman.jpeg" src_ip=192.168.250.70`. If we check this, we can confirm that the web server was, in fact, interacting with a command-and-control (C2) server. We could also confirm this domain by checking other log sources such as `stream:dns`.

![image](https://github.com/user-attachments/assets/9781f2d9-655b-4c1a-a8f7-edff54729177)

**[Task 8, Question 1] This attack used dynamic DNS to resolve to the malicious IP. What fully qualified domain name (FQDN) is associated with this attack?** - `prankglassinebracket.jumpingcrab.com`

## [Task 9] Weaponization Phase

During weaponization, an adversary might construct malware or malicious documents to get into a system and evade detection, establish domains similar to the target domain to trick users, and perhaps set up a C2 server for post-exploitation communications and activities. Throughout our investigation, we've seen domains and IP addresses associated with the attacker. Let's see what more we can find out about the adversary via OSINT.

We know that the domain `prankglassinebracket.jumpingcrab.com` is associated with the attack. So, let's try to figure out what the IP address tied to this domain is. There are various threat intel sites that can help us do this; for instance, if we search the domain in Robtext, we see several IP addresses, domains, and subdomains associated with the FQDN we found.

Next, if we search for the IP `23.22.63.114`, we see that there are some domains that are pretty close to the Wayne Enterprise site (the guys who run our website). That's pretty suspect given what we know about the weaponization process.

We can also use VirusTotal to analyze suspicious IPs and domains, and notably, we can use it to investigate suspicious files as well. Searching for the IP addreess above in VirusTotal and going to the Relations tab tells us what the associated domains are, as well as any communicated files. In VirusTotal, we even see mention of a `www.po1s0n1vy.com`. If we investigate this domain on VirusTotal, we find some additional information about the domains.

It might do us some good to put `www.po1s0n1vy.com` through a Whois lookup (we can do this on `whois.domaintools.com`... see Question 2 of this task for more details). Doing so gives us information about relevant IP addresses, locations, and name servers.

If we check the IP address on Robtext, we see that the IP address is responsible for all of these fake Wayne Enterprises domains:

![image](https://github.com/user-attachments/assets/b5f378ac-12ee-4694-a604-4360f3255012)

**[Task 9, Question 1] What IP address has P01s0n1vy tied to domains that are pre-staged to attack Wayne Enterprises?** - `23.22.63.114`

We'll have to check a different OSINT site to get this information - as of writing, unless I'm missing something, the Whois information isn't available anymore on `whois.domaintools.com`. However, they are available on `otx.alienvault.com`. Heading there and putting the domain `www.po1s0n1vy.com` in the search bar, we can get historical Whois records. Looking through the pages of Whois records gives us this email:

![image](https://github.com/user-attachments/assets/1d499640-be53-4649-b84d-a9b10164ae1d)

It's most likely that this email was used in the adversary's campaign.

**[Task 9, Question 2] Based on the data gathered from this attack and common open-source intelligence sources for domain names, what is the email address that is most likely associated with the P01s0n1vy APT group?** - `lillian.rose@po1s0nvy.com`

## [Task 10] Delivery Phase

After an attacker creates malware to gain initial access and evade defenses, they need to actually deliver their weapon to the target system. So, our goal here is to identify any routes they could have taken into the system using threat hunting and OSINT platforms. The threat intel report suggests that this adversary has a second attack vector in case initial compromise fails. So let's see what they could do.

Some OSINT sites of interest at this phase include VirusTotal, ThreatMiner, and Hybrid-Analysis. Let's start with ThreatMiner. If we plug the IP `23.22.63.114` into ThreatMiner, we see three files associated with the IP. One has a hash value of `c99131e0169171935c5ac32615ed6261` and may also be malicious, so we'll make note of this. If we click on the MD5 hash value, we get metadata and other important information about the file.

We can continue our investigation by searching VirusTotal and looking for the hash. We can get the metadata for this malware in the Details tab.

And lastly, we can check Hybrid Analysis for behavioral analysis of the malware. We can see what the malware does once it gets executed, including
- Network communications
- DNS requests
- Contacted hosts, country mapping
- Strings present
- MITRE ATT&CK mapping
- Malicious indicators
- Import/export of DLLs
- Mutex information, if created
- File metadata
- Screenshots

We saw the associated hash when we were checking with ThreatMiner earlier.

**[Task 10, Question 1] What is the hash of the malware associated with the APT group?** - `c99131e0169171935c5ac32615ed6261`

And we can get the name by investigating the hash on VirusTotal!

![image](https://github.com/user-attachments/assets/118b2444-aa06-4007-ab08-97bfaccac67e)

**[Task 10, Question 2] What is the name of the malware associated with the Poison Ivy infrastructure?** - `MirandaTateScreensaver.scr.exe`
