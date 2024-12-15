# Introduction to Cyber Threat Intel

## [Task 1] Introduction

We concluded the previous section of rooms by diving into MITRE's ATT&CK Framework. This framework incorporates _cyber threat intelligence_ (CTI). CTI, generally, is vital for investigating and reporting on adversary attacks, so this section will be focused on tools that folks use to gather and share threat intel.

## [Task 2] Cyber Threat Intelligence

CTI is simply evidence-based knowledge about an adverasry, their tactics, indicators, and motivations. Having this knowledge can help create actionable advice when dealing with them, which can ultimately be used to protect assets and inform cybersecurity and management decisions.

Often, you'll see the terms "data," "information," and "intelligence" thrown around. There are some key differences between these terms that's worth covering:
- Data: These are discrete indicators associated with an adversary, and can include IP addresses, URLs, and hashes.
- Information: Multiple data points can be combined to answer certain questions (e.g., how many times a user accessed a certain site). The answers to these questions constitute information.
- Intelligence: Once you have data and information, you can correlate them to identify patterns of actions based on context.

The end goal in employing CTI is to understand the adversary and how they relate to your environment so you can defend against attacks. This usually involves answering questions like
1. Who's attacking?
2. What are their motivations?
3. What are they capable of?
4. What should we be looking out for? (indicators of compromise/IOCs, artifacts)

Threat intelligence can be obtained from different sources:
- Internally: Corporate security events (vulnerability assessments, incident response), cyber awareness training, logs/events
- Communities: Open web forums, dark web communities
- Externally: Threat intel feeds, online marketplaces, public sources (government data, publications, social media, etc.)

And threat intelligence can be classified into different categories:
- Strategic: High-level intel that looks into organization threat landscape, maps out risk areas based on trends, patterns, emerging threats that may impact business decisions
- Technical: Evidence and artifacts of attack used by an adversary, used by incident response teams to create baseline attack surface to analyze & develop defense mechanisms
- Tactical: Adversary TTPs, knowledge of which can strengthen security controls and address vulnerabilities
- Operational: Specific motives, intent in attacks; used to understand what critical assets may be targeted

**[Task 2, Question 1] What does CTI stand for?** - Cyber Threat Intelligence

**[Task 2, Question 2] IP addresses, hashes, and other threat artifacts would be found under which threat intelligence classification?** - Technical Intel

## [Task 3] CTI Lifecycle

You can get threat intel by taking raw data and turning it into contextualized, action-oriented insights geared towards triaging security incidents. There are six phases to the CTI lifecycle:
1. Planning/direction: Have objectives and goals defined. Figure out what you need to protect (assets, processes), the impact of the loss or interruption of assets and processes, data sources/intel to use, and tools/resources needed.
2. Collection: Gather required data for analysis by using commercial, private, and open-source resources. Typically automated.
3. Processing: Take logs, vulnerability info, malware, network traffic, etc. and extract, sort, organize, and correlate data. Present data in such a way that analysts can understand it. SIEMs are typically used for this purpose.
4. Analysis: Derive insights from the data, like potential threats, determining action plans, strengthening security controls or justifying investment into new ones.
5. Dissemination: Share the information with organizational stakeholders according to their expertise and needs. C-suite members may want a concise report covering trends/implications/etc., analysts may want more technical detail.
6. Feedback: Teams should provide each other feedback to improve the threat intel process and implementation of controls.

**[Task 3, Question 1] At which phase of the CTI lifecycle is data converted into usable formats through sorting, organising, correlation and presentation?** - Processing

**[Task 3, Question 2] During which phase do security analysts get the chance to define the questions to investigate incidents?** - Direction

## [Task 4] CTI Standards and Frameworks

Frameworks and standards are important for threat intel, since they allow for the distribution and use of intel as well as provide a common language with which people can talk about the intel. Some examples of frameworks include
- MITRE ATT&CK: A knowledge base of adversary behavior, focusing on indicators and tactics. Analysts use this to track behavior.
- TAXII: Trusted Automated eXchange of Indicator Information, which defines protocols for secure exchange of threat intel to help deal with threats. Supports the collection model (in which intel is collected/hosted by a producer and leads to a request-response model) and the channel model (threat intel is pushed to users from a central server).
- STIX: Structured Threat Information Expression, which is a language for communication of cyber threat information, as well as providing relaitonships between sets of threat information.
- Cyber Kill Chain: Breaks down adversary actions into steps, helping analysts and defenders identify what was going on during a cyber attack. Since the release of the Cyber Kill Chain, other kill chains have been created, such as the Unified Kill Chain.
- Diamond Model: Looks at intrusion analysis and tracking attack groups over time, focusing on different elements of an attack (adversary, victim, infrastructure, capabilities).

**[Task 4, Question 1] What sharing models are supported by TAXII?** - Collection and Channel

**[Task 4, Question 2] When an adversary has obtained access to a network and is extracting data, what phase of the kill chain are they on?** - Actions on Objectives

## [Task 5] Practical Analysis

Reporting threat intel to organizations is a key component of the CTI lifecycle, and reports may come in from various security and technology companies that research these threats. So, it only makes sense to conclude by constructing a threat intel flow chart for a simplified engagement.

We have a SIEM dashboard with the following information:

![image](https://github.com/user-attachments/assets/916c4ef5-14cd-4005-b815-2814d694db02)

And we need to fill in the flow chart below:

![image](https://github.com/user-attachments/assets/9a9a2f19-46cc-4622-a2c5-9525c9498302)

Since there aren't that many events, we can start by looking at the oldest events and working our way up. Going in this way, we see that some communication is taking place over the IP `91.185.23.222`, and we see that an email is received by John Doe from `vipivillain@badbank.com`. From this we're able to fill in the Threat Actor Email Address and Victim Email Recipient sections of the flow chart.

**[Task 5, Question 1] What was the source email address?** - `vipivillain@badbank.com`

We then see that a file was downloaded by John Doe, `flbpfuh.exe`. This is likely the malware tool, since immediately after its download there is evidence of registry file modification, traffic being sent out the network, and accounts being changed.

**[Task 5, Question 2] What was the name of the file downloaded?** - `flbpfuh.exe`

We see that files are being sent out to that IP address from earlier, so this is likely going to be the address used for extraction. The logged-in account is going to be our final piece of the flow chart - the Administrator user. Filling out the flowchart in this manner gives us the flag for this room.

**[Task 5, Question 3] After building the threat profile, what message do you receive?** - `THM{NOW_I_CAN_CTI}`
