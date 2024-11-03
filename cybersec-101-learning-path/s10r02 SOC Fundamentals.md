# SOC Fundamentals

## [Task 1] Introduction to SOC

Technology makes things more efficient, but this leads to more responsibilities. We no longer worry about losing physical assets - we're concerned with the secrets/critical data that's stored in physical files. Organizations carry this data in network and systems, and modifications/loss of this data can be significant. Attackers attempt to discover and exploit new vulnerabilities in these networks and systems daily. Nowadays, a team is needed to handle organizational security.

This is where the Security Operations Center (SOC) comes in - this is a specialized facility operated by a specialized team. This team continuously monitors an organization's network and resources and identifies suspicious activity to prevent damage. They work 24/7.

**[Task 1, Question 1] What does the term SOC stand for?** - Security Operations Center

## [Task 2] Purpose and Components

The main goals of the SOC team is to perform detection and response. They have resources available in security solutiosn to help them achieve these goals. The entire company's network and systems are integrated into the SOC so continuous monitoring can be provided from one central location.

The detection goals include:
- Detecting vulnerabilities - weaknesses that an attacker can exploit to carry out their objectives; they can appear in the OS and programss. Vulnerabilities are not strictly the SOC's responsibility, but unfixed vulnerabilities can affect the security level of the entire company.
- Detecting unauthorized activity - where an attacker was able to get into someone's account without authorization; this can be confirmed with clues like geographic location
- Detect policy violations - violations of rules/procedures created to help protect a company atgainst security threats and ensure compliance
- Detect intrusions - unauthorized access to systems and networks.

Response goals include supporting incident response efforts. Certain steps must be followed to respond to a security incident, including minimizing impact and performing root cause analysis. The SOC helps with this.

The three pillars of an SOC are _people_, _process_, and _technology_. Professionals working with state-of-the-art technology in the presence of proper processes contributes to a mature SOC. This will be the focus of the remainder of the room.

**[Task 2, Question 1] The SOC discovers an unauthorized user is trying to log in to an account. What capability of SOC is this?** - Detection

**[Task 2, Question 2] What are the three pillars of a SOC?** - people, process, technology

## [Task 3] People

The people of an SOC will always be important. Security solutions can generate a lot of alerts and red flags - this results in a lot of noise. Efforts in responding to all of it will be a waste of time and resources. The people are here to focus on what's actually problematic in the alerts, which can lead to prompt response.

The following roles in an SOC include:
- SOC Analyst (Level 1): First responders to detection. Perform basic alert triage to determine if a detection is harmful. These are reported via proper channels.
- SOC Analyst (Level 2): Some detections require deeper investigation, which is what these guys do. Data is correlated from many sources for proper analysis.
- SOC Analyst (Level 3): Experienced professionals who proactievly look for any threat indicators and support in the incident response activities. Level 3 folks handle situations that require detailed response, containment, eradication, and recovery.
- Security Engineer: Analysts work on security solutions, deployment, and configuration. Engineers deploy and configure these solutions to ensure smooth operation.
- Detection Engineer: Security rules are the logic built behind security solutions to detect harmful activities. Level 2 and 3 analysts create these rules, and the SOC team can sometimes use this role independently for this responsibility.
- SOC Manager: Manages the processes the SOC team follows and provides support, and remains in contact with the Chief Information Security Officer (CISO) to provide him with updates on the SOC team's current security posture and efforts.

Roles in the SOC team can increase and decrease based on the size and criticality of the organizations.

**[Task 3, Question 1] Alert triage and reporting is the responsibility of?** - SOC Analyst (Level 1)

**[Task 3, Question 2] Which role in the SOC team allows you to work dedicatedly on establishing rules for alerting security solutions?** - Detection Engineer

## [Task 4] Process

The roles above have their own processes. Here are some important processes that exist within an SOC:
- Alert Triage: The basis of SOC. The first response to any alert is to perform triage and analyze the specific alert. This determines the alert's severity and how we should prioritze it. Answers the five Ws:
  - What? - What was alerted on?
  - When? - When was the alert generated?
  - Where? - Where was the alert generated from? (e.g. directories, computers, etc.)
  - Who? - Which user was the alert reported under?
  - Why? - Why did the alert trigger in the first place?
- Reporting: We need to escalate harmful alerts to higher-level analysts for timely response and escalation. This is typically done as tickets sent to the relevant people. The report should discuss the five Ws along with thorough analysis and screenshots for evidence.
- Incident Response and Forensics: Sometimes the malicious activities in question are critical, and so high-level teams need to perform incident response with forensics activities possible. The goal is to discover the incident's root cause by analyzing the artifacts from a system/network.

**[Task 4, Question 1] At the end of the investigation, the SOC team found that John had attempted to steal the system's data. Which 'W' from the 5 Ws does this answer?** - Who

**[Task 4, Question 2] The SOC team detected a large amount of data exfiltration. Which 'W' from the 5 Ws does this answer?** - What

## [Task 5] Technology

We need security solutions in order to conduct the processes described above. Technology, as one of the pillars of SOC, refers to the security solutions. Good security solutions efficiently minimize the manual effort needed to detect and respond to threats.

An organization's network consists of many devices and apps, and as a security team, individually detecting and responding to threats in each device and app would require significant effort and resources. Hence, we just use security solutions that centralize all the information of the devices and apps present in the network and automate the detection and response capabilities.

Some of the security solutions used in an SOC include:
- Security Information and Event Management (SIEM): Popular tool used in SOC environments that collects logs from network devices (log sources). Detection rules are set up in the solution and help identify suspicious activity. Detections are provided once they've been properly correlated and alerted upon. Modern solutions also provide user behavior analytics and threat intelligence, along with machine learning to enhance detection capabilities.
- Endpoint Detection and Response (EDR): Provides SOC with real-time and historical visibility into device activities, operating at the endpoint level. Thees can carry out automated responses and the EDR has extensive deteciton capabilities for endpoints, letting you investigate them in detail and respond with a few clicks.
- A firewall provides network security and acts as a barrier between the external and internal networks, such as the Internet. It monitors incoming and outgoing network traffic and filters any unauthorized traffic. There are also detection rules set up to help identify and block suspicious traffic before it reaches the internal network.

Other security solutions include antiviruses, EPP, IDS/IPS, XDR, SOAR, and so on. Deciding what technology is deployed in the SOC comes after carefully considering the threat surface and the available resources in an organization.

**[Task 5, Question 1] Which security solution monitors the incoming and outgoing traffic of the network?** - firewall

**[Task 5, Question 2] Do SIEM solutions primarily focus on detecting and alerting about security incidents? (yea/nay)** - yea

## [Task 6] Practical Exercise of SOC

Now let's step into the shoes of a Level 1 Analyst in the SOC team. We have an alert about a port scanning activity, and we can examine the SIEM logs for more information. We must answer the five Ws. Note that port scanning was conducted by the vulnerability assessment team in the network from the host `10.0.0.8`.

In the site, we start by acknowledging the alert and investigating it in the SIEM. We've been told that a port scan was detected, so that's our _what_.

**[Task 6, Question 1] What: Activity that triggered the alert?** - port scan

Now let's try to identify the time. We're seeing a lot of alerts from June 12, 2024 at 17:24.

![image](https://github.com/user-attachments/assets/1db55491-1719-4e04-81a2-c409cff7865e)

The SIEM in this instance only displays those events that are most relevant to the alert under investigation, so we can be pretty confident that this is the _when_.

**[Task 6, Question 2] When: Time of the activity?** - June 12, 2024 17:24

The destination host IP seems to be the same for most communication. We know that the vulnerability assessments were conducted from the host at `10.0.0.8`, so we can assume that we only care about the events with that as the source IP. The destination IP in many of these cases is `10.0.0.3`:

![image](https://github.com/user-attachments/assets/250d240c-b4c2-445e-ac4d-37894a9eb0fa)

**[Task 6, Question 3] Where: Destination host IP?** - `10.0.0.3`

For the source host, we look at the rows where the source IP is `10.0.0.8` and the destination is `10.0.0.3`. This yields

![image](https://github.com/user-attachments/assets/bb9264eb-4227-49c9-80f1-7c299de1c4e6)

**[Task 6, Question 4] Who: Source host name?** - NESSUS

Now we must consider the _why_. We're told that the vulnerability assessment folks were port scanning from the `10.0.0.8` host, so it appears that this is all intentional and perfectly okay.

**[Task 6, Question 5] Why: Reason for the activity? Intended/Malicious** - Intended

We do see that there are some cases where the source and destination IPs are swapped - this suggests that the other computers and sending responses back to the port scanner:

![image](https://github.com/user-attachments/assets/540cb24b-96f5-4a32-83f3-69634b2b543b)

**[Task 6, Question 6] Additional Investigation Notes: Has any response been sent back to the port scanner IP? (yea/nay)** - yea

With all of this, we have enough information to close the alert. This is a false positive, since there wasn't actually a security incident taking place - this was just normal, intended operation. Selecting those options, we can close out the alert in the alert dashboard and receive our flag:

![image](https://github.com/user-attachments/assets/bf560367-f271-4bc7-a26f-1d7ddd83fc63)

**[Task 6, Question 7] What is the flag found after closing the alert?** - `THM{000_INTRO_TO_SOC}`

## [Task 7] Conclusion

And that is what's going on with an SOC! We learned about the responsibilities of an SOC and the pillars of any mature, effective SOC: the people, processes, and technology. We even took a look at a sample incident investigation.
