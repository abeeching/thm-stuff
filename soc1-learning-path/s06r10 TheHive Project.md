# TheHive Project

## [Task 1] Room Outline

Our focus is on TheHive Project, which is a security incident response platform. We'll be looking at what TheHive is, what it can do, how to set it up and use it, and how to create a case assessment.

## [Task 2] Introduction

TheHive is a scalable, open-source, freely-available security incident response platform designed to assist analysts and practitioners in SOCs, CSIRTs, and CERTs to track, investigate, and act upon security incidents. Security analysts can collaborate on investigations simultaneously to get real-time information on new/existing cases, tasks, observables, and IOCs available to all team members.

There are three core functions in TheHive:
- Collaborate: Multiple analysts from an organization can work together on the same case simultaneously, and via live-stream capabilities, everyone can track what's going on in real time.
- Elaborate: Investigations correspond to cases, and the details can be broken down into associated tasks. These tasks may be created from scratch or via a template engine, and analysts can record their progress, attach artifacts of evidence, and assign tasks as needed.
- Act: Quick triaging can be supported by allowing analysts to add observables to their cases, leveraging tags, flagging IOCs, and identifying previously seen observables to feed their threat intelligence.

## [Task 3] TheHive Features and Integrations

TheHive has a rich feature set that supports analyst workflows, including:
- Case and task maangement: Each investigation corresponds to a case that has been created, and each case can be broken down into one or more tasks for added granularity and turned into templates for easier management. Analysts can record their process, attach pieces of evidence and noteworthy files, and add tags and other archives to cases.
- Alert triage: Cases can be imported from SIEM alerts, email reports, and other security event sources. This lets an analyst go through imported alerts and decide whether to escalate them into investigations and incident response.
- Observable enrichment with Cortex: Cortex is an observable analysis and active response engine, and it lets analysts collect more information from threat indicators by performing correlation analaysis and developing patterns from cases.
- Active response: Analysts can use Responders and run active actions to communicate, share information about incidents, and prevent/contain a threat.
- Custom dashboards: Statistics on cases, tasks, observables, metrics, and more can be compiled and distributed on dashboards that can be used to generate useful KPIs in an organization.
- Built-in MISP integration: MISP is a threat intelligence platform for sharing, storing, and correlating IOCs of targeted attacks and other threats. This lets analysts create cases from MISP events, import IOCs, and export their own identified indicators to their MISP communities.

Other integrations include DigitalShadows2TH and ZeroFox2TH, free and open-source extensions of alert feeders from DigitalShadows and ZeroFox respectively. These ensure that alerts can be put into TheHive and transformed into new cases with predefined templates and adding to existing cases.

**[Task 3, Question 1] Which open-source platform supports the analysis of observables within TheHive?** - Cortex

## [Task 4] User Profiles and Permissions

In TheHive, you can create an organization group to identify the analysts and assign different roles based on a list of preconfigured user profiles:
- `admin` - full administrative permissions, can't manage any cases or other data related to investigations
- `org-admin` - manage users, all org-level configuration; create and edit cases, tasks, observables; run analyzers, responders
- `analyst` - create and edit cases, tasks, observables; run analyzers and responders
- `read-only` - only read cases, tasks, and observables

Each user profile gets a predefined list of permissions that would allow the user to do different tasks based on their role. When a profile is selected, its permissions will be listed.

The permissions include:
- `manageOrganization (1)`: create and update organization
- `manageConfig (1)`: update configuration
- `manageProfile (1)`: create, update, delete profiles
- `manageTag (1)`: create, update, delete tags
- `manageCustomField (1)`: create, update, delete custom fields
- `manageCase`: create, update, delete cases
- `manageObservable`: create, update, delete observables
- `manageAalert`: create, update, import alerts
- `manageUser`: create, update, delete users
- `manageCaseTemplate`: create, update, delete case templates
- `manageTask`: create, update, delete tasks
- `manageShare`: share case, task, and observables with other organizations
- `manageAnalyze (2)`: execute analyze
- `manageAction (2)`: execute actions
- `manageAnalyzerTemplate (2)`: create, update, and delete analyzer templates

Some notes:
1. Organizations, configuration, profiles, tags are global objects; the related permissions are effective only on the admin organization.
2. Actions, analysis, template are available only if the Cortex connector is enabled

You can also perform actions like creating case custom fields, custom observable types, custom analyzer templates, and importing TTPs from the MITRE ATT&CK framework.

We'll need to launch the attached VM at this point. From there we can go to `http://MACHINE_IP/index.html` via the VPN connection. Unless I'm blind I don't see a way to grab the required task file for later in the AttackBox. Unless you want to try and navigate to the THM room from inside the AttackBox and have it freak out and resize itself every two seconds.

**[Task 4, Question 1] Which pre-configured account cannot maange any cases?** - Admin

**[Task 4, Question 2] Which permission allows a user to create, update, or delete observables?** - `manageObservable`

**[Task 4, Question 3] Which permission allows a user to execute actions?** - `manageAction`

## [Task 5] Analyst Interface Navigation

In this scenario, we've captured network traffic in a suspected data exfiltration incident. This traffic contains FTP connections that were established. We'll have to analyze the traffic and create a case in TheHive to facilitate its progress. We may want to review the Wireshark room(s) before proceeding.

When an analyst logs into the dashboard, they are greeted with the Cases page. At the top, there are menu options that allow the user to create new cases and see their tasks and alerts. Active cases are populated on the center console when analysts create them.

When you click the New Case tab, a pop-up window opens allowing the analyst to enter case details and tasks. You can set the following options:
- Severity: The level of impact the incident being investigated has on the environment, ranging from low to critical
- Traffic Light Protocol (TLP): Designations to ensure that sensitive information is shared with the appropriate audience. Scales from full disclosure of info (white) to no disclosure/restricted (red).
- Permissible Actions Protocol (PAP): Indicates what an analyst can do with the information, whether an attacker can detect the current analysis state or defensive actions in place. Uses color scheme similar to TLP, part of MISP taxonomies.

In our case, we may set up a data exfiltration case as follows:
- Set Title, Date, Tags, and Description accordingly
- Severity: High
- TLP: Red
- PAP: Amber
- Tasks: Investigate Source and Destination IPs, Identify Data Source, Assign Case TTPs

Once we set this up, TheHive will create a series of pages about the case, and we'll be sent to one of them - the Details. There are four tabs we can look at - Details, Tasks, Observables, and TTPs. We may add corresponding tactic and techniques associated with the case; these come from MITRE ATT&CK. This can help map out the threat in further detail. Since we're investigating potential exfiltration, we'll select the Exfiltration tactic. Then we'll choose the technique T1048.003, Exfiltration Over Unencrypted/Obfuscated Non-C2 Protocol.

We may add the following information about a case observable in the Observables tab:
- Type: The observable data type (e.g., IP addresses, hashes, domains)
- Value: The observable value (e.g., a specific IP address such as 127.0.0.1)
- One observable per line: Create one observable per line inserted in the value field
- One single multi-line observable: Create one observable, no matter the number of lines (useful in the case of, say, long URLs)
- TLP: Traffic Light Protocol, see above
- Is IOC: Check if this observable is an indicator of compromise (e.g., Emotet-associated IP)
- Has been sighted: Check if this observable has been sighted on your information system
- Ignore for similarity: Do not correlate this observable with other similar observables
- Tags: Insightful information tags (e.g., malware IPs, MITRE tactics)
- Description: Describe the observable

In our case, we may want to add an observable for `192.168.23.42`. This is an IP address. We only need one observable per line, and we'll let the TLP be green. We may tag and describe this observable accordingly.

**[Task 5, Question 1] Where are the TTPs imported from?** - MITRE ATT&CK

For this next question, we'll want to pull up MITRE ATT&CK and check out the TTP we assigned for our case. Here are some of the detections that we can perform:

![image](https://github.com/user-attachments/assets/d03d5dde-86c7-4773-9840-0243df7b3a87)

Most pertinent to our investigation, especially considering that we're going to be putting up a PCAP file soon, would be _network traffic_.

**[Task 5, Question 2] According to the Framework, what type of Detection "data source" would our investigation be classified under?** - Network Traffic

Let's go ahead and grab that PCAP file. We can upload a new observable with the data type "file" and tag it accordingly:

![image](https://github.com/user-attachments/assets/1cf8e36d-1e1a-4f22-a81a-86c83fcfa74f)

Incidentally, I'm not sure if this has anything to do with the flag, but if we go to `http://MACHINE_IP//files/flag.html` (as opposed to `https` like the question states), we see this:

![image](https://github.com/user-attachments/assets/fbb85ac7-b11e-40cc-8177-1c54b2515535)

I suppose it's good practice to learn how to set up observables in TheHive, but I kinda wish there was a little more going on here.

**[Task 5, Question 3] Upload the pcap as an observable. What is the flag obtained from `https://MACHINE_IP//files/flag.html`** - `THM{FILES_ARE_OBSERVABLES}`
