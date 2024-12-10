# Unified Kill Chain

## [Task 1] Introduction

Previously, we've seen the Cyber Kill Chain, which is a good way of explaining the process an attacker has to follow to conduct a cyberattack. That being said, it was not without its problems, chiefly that it hasn't been updated in over a decade. Understanding how a cyber threat behaves, what its goals are, and how it operates is key in building a defense, and cyberattacks have evolved since the Cyber Kill Chain was produced.

With this in mind, we turn to another kill chain - the Unified Kill Chain.

## [Task 2] What is a "Kill Chain"?

Quick recap: The term _kill chain_ comes from the military and is used to describe the stages of an attack. This extends quite nicely to cyberattacks, as we can outline the steps an adversary takes to approach and intrude a target. One attacker might scan, then exploit a web vulnerability, and then escalate privileges, as an example. While broad, that would be an example of a kill chain.

**[Task 2, Question 1] Where does the term "Kill Chain" originate from?** - military

## [Task 3] What is "Threat Modeling"?

Key to the development of cybersecurity is _threat modeling_, a process of identifying risk.
1. You would start by determining what systems need to be secured and what function they serve. It's vital to figure out what systems are criticala to business operations, and which ones contain sensitivei nformation.
2. From there, you figure out what vulnerabilities and weaknesses the systems/apps have and how they may be exploited.
3. Once vulnerabilities are identified, you would create a plan of action to protect systems and apps from the vulnerabilities.
4. And then you would put in place policies to prevent vulnerabilities from occurring again where possible. This may invovle making changes to the software development lifecycle, and it may also involve employee training.

The goal of threat modeling is to give a high-level overview of an organization's IT _assets_ (the technical term for hardware and software in IT) and how to resolve any vulnerabilities. The Unified Kill Chain promotes threat modeling by illustrating attack surfaces (i.e., how a system may be attacked through exploits).

Other frameworks are involved in the threat modeling process, such as STRIDE, DREAD, and CVSS. TryHackMe has a room on the Principles of Security in the Cyber Security 101 path, and this goes into more detail about those frameworks.

**[Task 3, Question 1] What is the technical term for a piece of software or hardware in IT (information technology)?** - asset

## [Task 4] Introducing the Unified Kill Chain

The Unified Kill Chain was published in 2017 with the goal of complementing - not replacing or competing with - other kill chain frameworks, such as the Cyber Kill Chain mentioned previously and MITRE ATT&CK.

In the Unified Kill Chain, there are eighteen phases of an attack, which this room groups into a few phases for brevity's sake. Thus, it is a more recent kill chain framework and has much detail.
1. Reconnaissance: Research, identify, and select a target
2. Weaponization: Set up the infrastructure for an attack
3. Delivery: Deliver a weaponized object (e.g., the payload) to the target
4. Social Engineering: Manipulate people into doing unsafe actions
5. Exploitation: Exploiting vulnerabilities to achieve some kind of code execution
6. Persistence: Establishing a persistent presence on the system once inside
7. Defense Evasion: Techniques that help an attacker avoid detection from defense mechanisms
8. Command and Control: How an attacker communicates with infected hosts over the network
9. Pivoting: Tunnelling traffic through a controlled system to get to other systems that aren't directly accessible
10. Discovery: Gaining knowledge about a system and its network
11. Privilege Escalation: Techniques that allow an attacker to get higher privileges on a system or network
12. Execution: Execution of attacker-controlled code on the target locally or remotely
13. Credential Access: Gaining access to or otherwise controlling system, service, or domain credentials
14. Lateral Moevment: Moving "horizontally" to other systems to gain access to and control them
15. Collection: Identifying and gathering data from a network
16. Exfiltration: Taking data from a target network
17. Impact: Techniques that manipulate, interrupt, or destroy a target system or its data
18. Objectives: The socio-technical techniques of an attack intended to accomplish some goal.

The UKC framework comes with several benefits.
- It is quite recent, having been released in 2017 and later updated in 2022. Other frameworks haven't been maintained since the 2010s, when the cybersecurity landscape was different.
- The UKC has a lot of detail, splitting an attack into eighteen phases. Other frameworks generally have fewer phases.
- An entire attack, from reconnaissance to post-exploitation, is covered. Furthermore, an attacker's motivations are discussed. This is a consequence of the amount of detail the UKC has.
- The UKC covers a more realistic attack scenario. Various stages often re-occur and attackers go back and forth between steps. For instance, once an attacker exploits a machine and gets in, they might perform reconnaissance before moving on. Other frameworks don't account for this.

**[Task 4, Question 1] In what year was the Unified Kill Chain framework released?** - 2017

**[Task 4, Question 2] According to the Unified Kill Chain, how many phases are there to an attack?** - 18

**[Task 4, Question 3] What is the name of the attack phase where an attacker employs techniques to evade detection?** - Defense Evasion

**[Task 4, Question 4] What is the name of the attack phase where an attacker employs techniques to remove data from a network?** - Exfiltration

**[Task 4, Question 5] What is the name of the attack phase where an attacker achieves their objectives?** - Objectives

## [Task 5] Phase: In (Initiail Foothold)

This first portion of the Unified Kill Chain focuses on how an attacker gains access to a system or networked environment. Many techniques are used to gain a foothold and establish persistence in the system, including the following.
1. Reconnaissance: Gathering of information related to a target, either done actively or passively. The information obtained here is used throughout the rest of the UKC, and can include systems and services running, contact/employee lists, potential credentials, and network topology.
2. Weaponization: Setting up the necessary infrastructure to perform an attack, including C2 servers, and systems that handle reverse shells and payloads.
3. Social Engineering: Manipulating employees to do things in furtherance of a cyberattack, including getting a user to open a malicious email attachment, impersonating a web page and collecting user credentials from it, and calling/visiting the target and impersonating a user or otherwise gaining access to secure areas.
4. Exploitation: Taking advantage of weaknesses and vulnerabilities present in a system, specifically for code execution as per the UKC. Includes reverse shells, using automated scripts to run code, and abusing web app vulnerabilities.
5. Persistence: Maintaining presence and access on a system that an attacker has gained initial access to. Includes creating services, adding systems to a C2 server, and leaving other forms of backdoors such as reverse shells that start when the user logs in.
6. Defense Evasion: Evading defense measures put in place in the system or network, such as web app and network firewalls, antivirus systems, and intrusion detection systems. In defensive security, finding how attackers go through this stage is useful in improving defense systems.
7. Commmand and Control: Using tools developed in the weaponization stage to establish communications between the adversary and the target, usually to execute commands, steal data/credentials, and pivot through the network.
8. Pivoting: Reaching other systems in a network that are not otherwise accessible (e.g., not exposed to the Internet). Many systems on a network aren't directly reachable and can contain valuable data or have weaker security.

**[Task 5, Question 1] What is an example of a tactic to gain a foothold using emails?** - Phishing

**[Task 5, Question 2] Impersonating an employee to request a password reset is a form of what?** - Social Engineering

**[Task 5, Question 3] An adversary setting up the Command and Control server infrastructure is what phase of the Unified Kill Chain?** - Weaponization

**[Task 5, Question 4] Exploiting a vulnerability present on a system is what phase of the Unified Kill Chain?** - Exploitation

**[Task 5, Question 5] Moving from one system to another is an example of?** - Pivoting

**[Task 5, Question 6] Leaving behind a malicious service that allows the adversary to log back into the target is what?** - Persistence

## [Task 6] Phase: Through (Network Propagation)

Once an attacker has established themselves on the target system, they would now focus on gaining additional access and privileges to systems and data. This will further the cyberattack and allow them to accomplish certain objectives. One system may be a "base" from which the attacker pivots off of.
1. Pivoting: Designating a system as a staging site and creating a tunnel between the command operations and the victim's network. The C2 system also distributes malware and backdoors at later stages.
2. Discovery: The adversary uncovers information about the system/network it is connected to, including active accounts, permissions, apps and software in use, web browser activity, files, directories, network shares, and system configs.
3. Privilege Escalation: Attaining higher privileges on the pivot system using information on accounts, vulnerabilities, and misconfigurations. The end goal is to reach SYSTEM/ROOT, a local admin, a user account with admin-like access, or otherwise a user account with certain functionality.
4. Execution: Deploying malicious code via the pivot system, including remote trojans, C2 scripts, malicious links, and malware ran via scheduled tasks for persistence.
5. Credential Access: Attempting to steal account names and passwords via keylogs and credential dumping. Once obtained, they can use these credentials in further attacks, which can make them a little trickier to detect given that they're using legitimate credentials. Often happens during Privilege Escalation.
6. Lateral Movement: Moving through the network and reaching other target systems to accomplish some objective. Typically done as stealthy as possible.

**[Task 6, Question 1] As a SOC analyst, you pick up numerous alerts pointing to failed login attempts from an administrator account. What stage of the kill chain would an attacker be seeking to achieve?** - Privilege Escalation

**[Task 6, Question 2] Mimikatz, a known attack tool, was detected running on the IT Manager's computer. What is the mission of the tool?** - Credential Dumping

## [Task 7] Phase: Out (Action on Objectives)

In the last portion of an attack, an adversary has access to critical assets and is in a position to fulfill their attack goals. Usually this involves compromising some component(s) of the CIA triad.
1. Collection: Gathering all valuable data of interest in an attempt to compromise the confidentiality of data. Usually leads into the next stage on this list. Targets include drives, browsers, audio, video, and emails.
2. Exfiltration: Stealing data by moving it out from the system, typically with encryption measures and compression in place to avoid detection. This happens over the C2 channel established in earlier phases.
3. Impact: Manipulating, interrupting, or destroying assets to compromise integrity and/or availability. Effectively the attacker disrupts business and operational processes. May involve removing account access, wiping disks, encrypting data (i.e., ransomware), website defacement, and denial of service (DoS).
4. Objectives: Once an attack is complete, the attacker has presumably accomplished all they wanted to. The actual actions taken from here on depend on what that objective was. In a financially-motivated attack, an adversary might use ransomware to force the victims to pay for their data. If an attacker wants to damage a business's reputation, they could instead grab the data off the system and release it to the public.

**[Task 7, Question 1] While monitoring the network as a SOC analyst, you realise that there is a spike in the network activity, and all the traffic is outbound to an unknown IP address. What stage could describe this activity?** - Exfiltration

**[Task 7, Question 2] Personally identifiable information (PII) has been released to the public by an adversary, and your organisation is facing scrutiny for the breach. What part of the CIA triad would be affected by this action?** - Confidentiality

## [Task 8] Practical

The practical exercise for this room requires us to match an attacker's actions to the different phases of the Unified Kill Chain. This involves just reviewing the main components of the UKC. Most commonly, you'll see attackers do at least the following:
- They will first perform reconnaissance to figure out what systme to attack and how.
- Then they will get into the system some way, some how. This involves infrastructure and exploitation.
- Once in, an attacker will typically try to maintain persistence so they can get back into the system at a later date, if needed.
- Attackers may also establish connections to machines via a command-and-control server, allowing them to remotely control hacked machines.
- Then an attacker would navigate through the systems and the network to get to their target. This may involve privilege escalation to get higher permissions on a machine, as well as pivoting to go from one machine to the next.
- Once they are done whatever they need to do, they perform actions on objectives. That is, they actually carry out what they planned to do.

Completing the static site awards you with the flag for this room.

**[Task 8, Question 1] Match the scenario prompt to the correct phase of the Unified Kill Chain to reveal the flag at the end. What is the flag?** - `THM{UKC_SCENARIO}`

The goal in using kill chains is to reconstruct the steps that an attacker took. Sometimes, however, we'd like to get a big-picture overview of what happened, who the adversary was, and what they were capable of. There are models that help us with this; one such model will be the topic of the next room.
