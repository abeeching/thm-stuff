# Wazuh

## [Task 1] Introduction and [Task 2] Required: Deploy Wazuh Server

We'll be exploring Wazuh, an EDR software solution, in this room. We'll be discussing what an EDR is and how it can be used, how to navigate Wazuh and work with its rules and alerts, and how to deal with logs, among other things.

An endpoint deteciton and response (EDR) solution is a set of tools and applications that can be used to monitor a device for activities that could indicate threats and security breaches. EDR solutions may allow you to
- Audit devices for vulnerabilities
- Monitor devices for suspicious activities
- Visualize complex datasets and events with graphs
- Record normal operating behavior for anomaly detection

Wazuh itself is an open-source, freely available, extensive EDR solution that can be used in all environment scales, originally released in 2015. It operates with a management and agent module. A device specifically set up for running Wazuh is a _manager_. A manager manages _agents_, which are installed on devices that you'd like to monitor.

The room provides a machine you can use that has Wazuh set up on it. You'll have to go into the AttackBox or connect via the VPN to access the resources for this room. When logging in, make sure you select Global Tenant.

The answers to the questions below can be found in the information for Task 1.

**[Task 1, Question 1] When was Wazuh created?** - 2015

**[Task 1, Question 2] What is the term that Wazuh calls a device that is being monitored for suspicious activity and potential security threats?** - Agent

**[Task 1, Question 3] Lastly, what is the term for a device that is responsible for managing these devices?** - Manager

## [Task 3] Wazuh Agents

Agents are devices that record events and processes happening on the device. This can include authentication events and user management. Agents offload logs of these events to designated collectors, such as Wazuh. For Wazuh to be populated, you must install agents onto devices. Wazuh can help guide you through this process, including:
- Identifying operating system prerequisites
- Setting the address of the Wazuh server that the agent should send logs to (either a DNS entry or IP address)
- Assigning agents to groups

In Wazuh, the wizard for setting up agents can be accessed via the Wazuh menu -> Agents -> Deploy New Agent. Once you access the wizard, you can set up all the settings, and at the very end you will be given a command to run on the device that you want to set up as an agent.

Let's see what's going on with the agents. When we log in to Wazuh, we actually already see the answer to our first question at the very top - there are 2 agents that are being managed by this instance.

![image](https://github.com/user-attachments/assets/9002cd79-f190-4294-a789-84d25332a8ca)

**[Task 3, Question 1] How many agents does this Wazuh management server manage?** - 2

We can see the status of the agents in the same screenshot above, but we'll actually just go to Wazuh -> Agents and confirm. As it would turn out, both agents are indeed disconnected.

![image](https://github.com/user-attachments/assets/5f4313ce-88b6-4e7e-b20b-1737d6bc613c)

**[Task 3, Question 2] What are the status of the agents managed by this Wazuh management server?** - Disconnected

## [Task 4] Wazuh Vulnerability Assessment and Security Events

The Wazuh vulnerability assessment module can be used to periodically perform vulnerability scans against devices, their installed applications and versions. Once the data is gathered, it is sent back to the Wazuh server and compared against a CVE database. The scanner performs a full scan when the agent is first installed on a device, but it must be configured to run at a set interval thereafter. When enabled, five-minute intervals are set by default.

Wazuh can test agents against certain rulesets, which can be used to perform compliance assessment. This is a little sensitive though - it will often catch commonly-performed actions such as file removal. The events captured and their severities as reported by Wazuh are all determined by rulesets. We'll be adjusting these later, but for now we can take the time to explore the events individually. Simply select the event's dropdown to get information on timestamps, tactics, descriptions, and more.

Now let's examine the security event alerts for `AGENT-001`. In Wazuh -> Agents, we can select `AGENT-001` and from there, we can go to Modules -> Security events. We may need to set up the timeframe so that it includes March 11th, 2022, but once we do so we will get the total number of events from this agent:

![image](https://github.com/user-attachments/assets/b19fd368-b4f3-43ff-9501-8beb57aa74bd)

**[Task 4, Question 1] How many Security Event alerts have been generated by the agent `AGENT-001`?** - 196

## [Task 5] Wazuh Policy Auditing

Wazuh can audit and monitor an agent's configuration while proactively recording event logs. When Wazuh is set up on a device, an audit is performed and a metric is calculated based on frameworks and legislations (e.g., NIST, MITRE, GDPR). These are frameworks that are outlined in pentesting-related rooms.

Wazuh illustrates the logs broadly, but we can use visualizations to break down the data and explore it further. We can access the Policy Management information by going to Wazuh -> Modules -> Policy Monitoring.

## [Task 6] Monitoring Logons with Wazuh

Wazuh's security event monitor can be used to detect successful and unsuccessful authentication attempts. For instance, the rule with ID 5710 in this room detects attempted, unsuccessful connections over the SSH protocol. For a given alert, you can find additional information such as:
- `agent.ip` which is the IP address of the agent the alert was triggered on
- `agent.name` which is the hostname of the agent the alert was triggered on
- `rule.description` which is a description of what the event is alerting to
- `rule.mitre.technique` which is the MITRE technique the alert relates to
- `rule.mitre.id` which is the MITRE ID of the alert
- `location` which is the location of the file the alert was generated from on the host.

The `location` may be worth investigating in further detail - you can investigate log files on the agent with the usual tools, such as `grep`, to find more information.

As mentioned earlier, Wazuh will assign severities to different situations. So, generally, Wazuh will assume that a successful login isn't worth worrying about, though you can adjust this to reflect your situation. For instance, if a user doesn't generally log in that often, you may want to increase the severity level of a successful login to their account.

Additionally, you can look at the security events in Wazuh to see how many times a user has logged in - simply apply a filter (e.g., on Windows devices, try to filter for logins where the login `GUID` exists).

## [Task 7] Collecting Windows Logs with Wazuh

We'll spend some time learning how to collect logs with Wazuh. We can capture all sorts of actions and events on a Windows OS, including authenticaiton attempts, networking connections, accessed files, and application and servvice behavior. This is where Sysmon comes in - a tool we discussed in an earlier room. The Wazuh agent can aggregate events recorded by Sysmon and set it up for processing to the Wazuh manager, though this requires Wazuh to be set up along with Sysmon. Note that Sysmon makes use of rules in the XML format.

If you have a rule in XML format, you can execute Sysmon with it by running `Sysmon64.exe -accepteula -i (XML RULE FILE)`. Once running, you can verify that the rule has been accepted by going to Event Viewer -> Applications and Services Logs -> Microsoft -> Sysmon -> Operational and checking for event ID 16 (Sysmon config state changed).

Once you know that Sysmon is accepting the rule you've written, you can configure Wazuh to send these events to the Wazuh management server. To do so, go to the Wazuh agent file (`C:\Program Files (x86)\ossec-agent\ossec.conf`) and include this snippet:

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

Afterwards, restart the Wazuh agent - in our case, restarting the operating system will suffice. Once done, you can tell Wazuh to add Sysmon as a rule to visualiz0e these events. You'll have toa dd an XML file to the local rules, located in `/var/ossec/etc/rules/local_rules.xml`. For instance, if we want to set up a rule to monitor for potentially malicious PowerShell launches, we may include this XML code:

```xml
<group name="sysmon,">
  <rule id="255000" level="12">
    <if_group>sysmon_event1</if_group>
    <field name="sysmon.image">\\powershell.exe||\\.ps1||\\.ps2</field>
    <description>Sysmon - Event 1: Bad exe: $(sysmon.image)</description>
    <group>sysmon_event1,powershell_execution,</group>
  </rule>
</group>
```

Once done, just restart the Wazuh Management server and it will be applied.

**[Task 7, Question 1] WHat is the name of the tool that we can use to monitor system events?** - Sysmon

**[Task 7, Question 2] What standard application on Windows do these system events get recorded to?** - Event Viewer

## [Task 8] Collecting Linux Logs with Wazuh

To capture logs from a Linux server, we follow the same general process as with Windows. We use Wazuh's log collector service to tell the agent what logs should be sent off to the management server. Wazuh comes with many rules that can be used to help us analyze log files; they can be found in `/var/ossec/ruleset/rules`. There are approximately 900 rules for programs including Docker, FTP, WordPress, SQL Server, MongoDB, Firewalld, and more. You can also make your own rules for different programs.

We'll need to insert the XML code pertaining to the rule we create into the `/var/ossec/etc/ossec.conf` configuration file.

**[Task 8, Question 1] What is the full file path to the rules located on a Wazuh management server?** - `/var/ossec/ruleset/rules`

## [Task 9] Auditing Commands on Linux with Wazuh

Wazuh makes use of the `auditd` package, which can be installed on Wazuh agents running Debian, Ubuntu, and CentOS. This package monitors the system for certain actions and events, and once these are detected, they will be written to a log file which we can then send off to the management server with the log collector module.

To install `auditd` on your system, you can run the command `sudo apt-get install auditd audispd-plugins`, and you can enable the service to run on boot by running `sudo systemctl enable auditd.service` and `sudo systemctl start auditd.service`.

We'll need to configure `auditd` and create a rule for commands and events we'd like to monitor. We may want to look for commands executed as root, or usage of `tcpdump` or `netcat`, or even interactions with `/etc/passwd` and other sensitive files. The rules can be found in `/etc/audit/rules.d/audit.rules`.

If we want to create a rule for monitoring commands executed by root, then we can run `sudo nano /etc/audit/rules.d/audit.rules`, and then we can add the line `-a exit,always -F arch=64 -F euid=0 -S execve -k audit-wazuh-c`. Once we've done this, we can run `sudo auditctl -R /etc/audit/rules.d/audit.rules` so the new rule can be read by auditd.

Once we've added the new rule, we'll want to configure Wazuh to detect new log files generated by auditd. To do so, we'll want to edit `/var/ossec/etc/ossec.conf` and add the following lines:

```xml
<localfile>
  <location>/var/log/audit/audit.log</location>
  <log_format>audit</log_format>
</localfile>
```

**[Task 9, Question 1] What application do we use on Linux to monitor events such as command execution?** - `auditd`

**[Task 9, Question 2] What is the full path and filename ofor where the aforementioned application stores rules?** - `/etc/audit/rules.d/audit.rules`

## [Task 10] Wazuh API

The Wazuh management server features a rich and extensive API to allow the Wazuh server to be interacted with over the command line, though this requires authentication. One way to interact with the Wazuh server is to use `curl`.

We send valid credentials to the authentication endpoint, and then we receive a token (i.e., for a session) that we will need to provide for any further interactions. We can store this token as an environment variable in the Linux machine:

`TOKEN=$(curl -u : -k -X GET "https://(WAZUH MANAGEMENT SERVER IP):55000/security/user/authenticate?raw=true")`

We can confirm that we've been authenticated by running `curl -k -X GET "https://(MACHINE IP):55000/" -H "Authorization: Bearer $TOKEN"` which should return some information about the Wazuh API without throwing an error.

From here, we can use HTTP request methods such as GET, POST, PUT, and DELETE by providing the relevant options after `-X`. For instance, we can issue the command `curl -k -X GET "https://(MACHINE IP):55000/manager/status?pretty=true" -H "Authorization: Bearer $TOKEN"`. If we wanted to look for what services are being monitored and settings configured for the Wazuh management server, we may instead go to `/manager/configuration?pretty=true&ion=global`. We can even interact with an agent if we go to `/agents?pretty=true&offset=1&limit=2&select=status%2Cid%2Cmanager%2Cname%2Cnode_name%2Cversion&status=active`.

Wazuh also provides an integrated API console within the Wazuh website to query management servers and agents. This is not as extensive as using your environment -- for instance, you can't set up scripting with Python -- it is a convenient way to interact with the API as needed. To get to the console, go to Wazuh -> Tools -> API Console. You can start by running some sample queries; to do this, click the line of the query you want to run, and press the green run button to run the query.

The syntax for queries in the API Console use the same methods (GET, PUT, POST) and endpoints (e.g., /manager/info) as you would use with `curl`. Additional API endpoints and information can be found in the Wazuh API documentation.

**[Task 10, Question 1] What is the name of the standard Linux tool that we can use to make requests to the Wazuh management server?** - `curl`

**[Task 10, Question 2] WHat HTTP method would we use to retrieve information for a Wazuh management server API?** - GET

**[Task 10, Question 3] What HTTP method would we use to perform an action on a Wazuh management server API?** - PUT

For this last question, we'll want to check Wazuh -> Tools -> API Console. If we just want to figure out the server version for Wazuh, a good place to start would be checking `/manager/info`, which is already set up as an example query. Simply select the line with `GET /manager/info`, click the green Play button, and the results will show up on the right-hand side. This includes the version number for the Wazuh server.

![image](https://github.com/user-attachments/assets/a570b8a4-cbd2-46d5-aa7f-9e882784e791)

**[Task 10, Question 4] Use the API Console to find the Wazuh server's version. You will need to add the "v" prefix to the number for this answer.** - v4.2.5

## [Task 11] Generating Reports with Wazuh

Wazuh also features a reporting module that helps you see a summarized breakdown of events that have occurred on an agent. The first step in doing this is to select a view to generate reports from. To do this, you'll wna to go to Wazuh -> Modules -> Security Events. Set up date/time or other filters and click Generate Report at the top-right to create a report. This report may take a little bit of time to generate, depending on how much data needs to be processed.

After waiting some time, you can check the report by going to Wazuh -> Management -> Reporting. The report overview dashboard lists all generated reports. To download a report, press the Save icon located on the right of the report, under the Actions header. This downloads a PDF of the report to your machine.

For this question, we'll want to generate a few reports for the different machines. You can go to Wazuh -> Modules -> Security Events and click on "Generate Report" on the top-right.

![image](https://github.com/user-attachments/assets/2a5fff55-fd92-4a52-9453-3d6e8e0d6f64)

After waiting a short time, the report will be available in Wazuh -> Management -> Reporting. You can do this for the other agent if needed; you just need to change the filter, or go into Wazuh -> Agents, select the other agent, and go into Wazuh -> Modules -> Security Events. When done, you can view the reports by clicking on the download icon next to each in the Reporting tab. Here is the information for `thm-dc-01`:

![image](https://github.com/user-attachments/assets/e51740d3-136d-45de-b394-4055603ec414)

And here it is for `agent-001`:

![image](https://github.com/user-attachments/assets/1b1dbde1-0436-4115-858e-14d45581d4c4)

It's pretty clear which one has more alerts.

**[Task 11, Question 1] Analyze the report. What is the name of the agent that has generated the most alerts?** - `agent-001`

## [Task 12] Loading Sample Data

Wazuh's management server comes with some sample data when installed, ready to load whenever needed. This data is not enabled by default in the room's VM due to performance reasons, but you can import more data to examine Wazuh's functionality further. You'll start by navigating to the correct module to load the sample data: Wazuh -> Settings -> Sample Data -> Add Data (for each of the three cards).

It takes some time to add the data for each. If the card says "Remove data", then it has been properly imported. You can check the Wazuh dashboard to see the newly-imported data. You may need to adjust the date range - the minimum needed to show the sample will be the Last 7 days (or more).
