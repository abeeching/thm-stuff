# DFIR: An Introduction

## [Task 1] Introduction

Security breaches and incidents do happen... so how do we handle them? We should aim to prpeare for incidents so that we're not caught off guard by them. Digital Forensics and Incident Response, DFIR, is an essential subject of defensive security.

## [Task 2] The Need for DFIR

Sometimes you need to collect forensic artifacts from digital devices - computers, media devices, smartphones, and so on - to investigate incidents. This can help with, say, identifying an attacker's footprints to determine the extent of compromise. From there, you can restore the environment to a good state.

DFIR can be helpful in plenty of other ways, too:
- Finding evidence of an attacker in the network; looking for actual incidents
- Removing the attacker and any footholds they have in the environment
- Identifying extent and timeframe of a breach; communication with stakeholders
- Finding loopholes that led to a breach so they can be closed
- Understanding attacker behavior to pre-emptively block further intrusion
- Sharing information about the attacker

DFIR requires expertise in digital forensics and incident response. The skillset breakdown looks like this:
- For digital forensics, you should be able to identify forensic artifacts and evidence of human activity in devices
- For incident response, you should be knowledgeable in cybersecurity and be able to use forensic information to identify activities of interest

Both of these branches are dependent on each other. IR requires knowledge gained from DF, and DF takes its goals and scope from the IR process, defining the extent of forensic investigation.

**[Task 2, Question 1] What does DFIR stand for?** - Digital Forensics and Incident Response

**[Task 2, Question 2] DFIR requires expertise in two fields. One of the fields is digital forensics. What is the other field?** - Incident Response

## [Task 3] Basic Concepts of DFIR

Artifacts are pieces of evidence that point to an activity performed on a system. These are collected to support hypotheses and claims about attacker activities, so they are essential to the DFIR process. You can find artifacts in an endpoint's or server's system, memory, or network activity. Typically, enterprise environments focus on Windows and Linux operating systems, and there are rooms to help bring you up to speed on forensic artifacts available from these systems. Windows is typically used for endpoints and servers, whereas enterprise may use Linux for hosting services.

In doing DFIR, you must maintain the integrity of the evidence being collected, and thus there are certain best practiecs that need to be employed. Forensic analysis will contaminate the evidence, so the evidence must be write-protected first, then copied for analysis. This ensures the original evidence is not contaminated and remains safe while analyzing. If the copy gets corrupted, we can simply make a new one from the preserved evidence.

Not only does the evidence need to be secured, but we must also make sure to keep it in secure custody. Anybody not related to the investigation cannot get ahold of the evidence, since this may raise questions about the integrity of the data and weaken the case being built.

We often find that digital evidence is volatile - we can lose it forever if we don't capture it in time. Examples of this include RAM, which is lost when the system is shut down. This is a volatile component. Some components are not that volatile, such as hard drives, which are meant to be persistent storage. Understanding the order of volatility of evidence is critical for the collection process. It helps you prioritize the evidence to be collected.

Once artifacts have been collected and their integrity ensured, we can work on presenting them in a clear, understandable fashion and make use of the information contained in them. Timelines can help out here - we can simply put the events in chronological order. This helps provide some perspective into the investigation and corroborate information from various sources.

**[Task 3, Question 1] From amongst the RAM and the hard disk, which storage is more volatile?** - RAM

For the static website, you can start by adding the Cobalt Strike alert from the SIEM dashboard to the worksheet. We can also add the alerts relating to the same IP to the spreadsheet. This yields the following:

![image](https://github.com/user-attachments/assets/dfd0e9a8-fc08-4787-9d56-b3b6520fdea1)

Now we order the events we currently have. We can presume that somebody remotely connected to the machine at `192.168.1.150` and then attempted to download a malicious file, so we should swap the order of these two.

![image](https://github.com/user-attachments/assets/f5ac69d7-745f-44e9-b835-9919c3dc0051)

From here, we can add some alerts from Syslog. We see alerts pertaining to successful login attempts to the machine, as well as alerts relating to a malicious file reaching out to a remote host. This leaves us with the spreadsheet below.

![image](https://github.com/user-attachments/assets/d4301a51-5327-46f4-8e2f-6a2871d0732d)

We can assume that "John Doe" logged into the host remotely once the SSH connection was established, so we'll place that event right after the incoming SSH connection event. We can also presume that `malicious-file` was ran after the attacker attempted to download Cobalt Strike. Ordering the events in this manner gives us the flag:
1. Incoming SSH connection
2. John Doe logs in
3. Attacker tries to download Cobalt Strike
4. Attacker runs `malicious-file` and it reaches out to the remote IP.

**[Task 3, Question 2] Complete the timeline creation exercise in the attached static website. What is the flag that you get after completion?** - `THM{DFIR_REPORT_DONE}`

## [Task 4] DFIR Tools

There are various tools that can be used to help with DFIR, and many of these have rooms that we'll dive into in the SOC path:
- Eric Zimmerman's Tools, EZTools: Zimmerman is a security researcher who has written some tools to help with Windows forensic analysis. This will be explored in the Windows Forensics rooms.
- KAPE: Kroll Artifact Parser and Extractor, another tool by Zimmerman that automates the collection and parsing of forensic artifacts and helps with timeline creation.
- Autopsy: Open-source forensics platform for analyizng data from digital media, including devices, hard drives, and removable drives. Plugins may be used to speed up the process and extract specific data from raw sources.
- Volatility: Tool for performing memory analysis on memory captures from both Windows and Linux operating systems.
- Redline: Incident response tool developed and distributed by FireEye, and can gather forensic data from a system and help with analysis of that data
- Velociraptor: Advanced endpoint monitoring, forensics, and response platform. Open-source and powerful.

While these tools can help with DFIR, we need an actual methodology for performing DFIR.

## [Task 5] The Incident Response Process

A prominent use of digital forensics is to perform incident response, so we can create a process for incident response and then see how digital forensics fits into it. There are various standardized methods for performing incident response.
1. NIST (SP-800-61): Preparation -> Detection and Analysis -> Containment, Eradication, and Recovery -> Post-Incident Activity
2. SANS (Incident Handler's Handbook): Preparation -> Identification -> Containment -> Eradication -> Recovery -> Lessons Learned

The SANS methodology can be abbreviated as PICERL, which can make it easy to remember. Note that both processes listed above are similar to each other; NIST just combines some of the steps into a single step. We'll examine the SANS methodology in greater detail:
1. Preparation: Phase before an incident happens. Includes having the right people, processes, technology to respond to an incident.
2. Identification: Incident identified through some indicators, which are then analyzed for false positives, documented, then communicated to stakeholders.
3. Containament: Incident is contained, effort is made to limit its effects. May include short-term or long-term fixes based on forensic analysis.
4. Eradication: Removing the threat from the network. Need to have done forensic analysis and proper containment before this step.
5. Recovery: Once threat is removed, bring back services that were disrupted as they were before the incident happened.
6. Lessons Learned: Review the incident, document the process, take steps based on the findings from the incident to improve incident response for future incidents.

**[Task 5, Question 1] At what stage of the IR process are disrupted services brought back online as they were before the incident?** - Recovery

**[Task 5, Question 2] At what stage of the IR process is the threat evicted from the network after performing the forensic analysis?** - Eradication

**[Task 5, Question 3] What is the NIST equivalent of the step called "Lessons Learned" in the SANS process?** - Post-Incident Activity
