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

## [Task 4] Reconnaissance Phase

Reconnaissance is the process of discovering and collecting information about a target, perhaps about the system in use, the web application, employees, locations, and more. We'll be looking for any reconnaissance attempts against `imreallynotbatman.com`. One good place to start would be to investigate the inbound communication towards the webserver. Thus, we may run a simple query like `index=botsv1 imreallynotbatman.com` to look for any inbound traces of our domain. The following log sources contain the domain:
- Suricata
- stream:http
- fortigate_utm
- iis

If we want to identify, say, the IP address performing reconnaissance activity, we may want to start by looking at the web traffic. One way to do this would be to look at `stream:http`, and specifically examine the `src_ip` field. Our search query would be `index=botsv1 imreallynotbatman.com sourcetype=stream:http`. This will only pull the information from the `stream:http` logs. We can even go further and investigate the top values of the `src_ip` field directly. We have two IPs here, `40.80.148.42` and `23.22.63.114`. The former IP is seen in many logs, so maybe that's worth checking out first.

If we want to confirm any suspicious about any of the IPs, we would want to click on the IP (or otherwise filter for it) and examine the logs. Some fields of interest might be the `user-agent`, POST requests, URIs, and more.

Now let's make sure that this IP is the one actually performing reconnaissance. If we check the Suricata logs for this IP, using the query `index=botsv1 imreallynotbatman.com src=40.80.148.42 sourcetype=suricata`, then we'll eventually find some interesting logs containing signature alerts. This is something we'll want to note for later.

**[Task 4, Question 1] One Suricata alert highlighted the CVE value associated with the attack attempt. What is the CVE value?** - CVE-2014-6271

**[Task 4, Question 2] What is the CMS our web server is using?** - joomla

**[Task 4, Question 3] What is the web scanner the attacker used to perform the scanning attempts?** - acunetix

**[Task 4, Question 4] What is the IP address of the server `imreallynotbatman.com`? - `192.168.250.70`
