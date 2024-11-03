# Defensive Security Intro

*Also in: Cyber Security 101 - section 10, room 1*

## [Task 1] Introduction to Defensive Security

Previously, we learned about offensive security, which is focused no identifying and exploiting system vulnerabilities to enhance security. Offensive security folks - namely red teamers and penetration testers - focus on exploiting software bugs, insecure setups/configuration, and poor access control (among other things) and report their results so folks can come in to fix them.

This is where the defensive security team comes in; their jobs are to prevent intrusions from occurring to begin with, and (because this is unfortunately an inevitability) when an intrusion does happen, they detect and respond to them. Folks on a defensive security team may be referred to as the *blue team.*

Some defensive security tasks include
- Enhancing user cybersecurity awareness: Training users so they can protect against attacks targeting their systems
- Documenting, managing assets: Understanding what systems and devices are in play so security can be handled adequately
- Updating, patching systems: Ensuring systems are correctly updated and patched against known vulnerabilities and weaknesses
- Setting up preventative security: Configuring intrusion prevention systems (IPS) and firewalls to control what traffic can go through the network and what traffic needs to be blocked
- Setting up logging and monitoring: Detecting malicious activities and intrusions by using logging and monitoring solutions on the network

Other related topics include:
- The Security Operations Center, SOC
- Threat intelligence
- Digital Forensics and Incident Response, DFIR
- Malware analysis

**[Task 1, Question 1] Which team focuses on defensive security?** - blue team

## [Task 2] Areas of Defensive Security

Here, we'll focus on the SOC (and threat intelligence), as well as DFIR (and malware analysis).

The SOC is a team of cybersecurity professionals, with their job being to monitor the network and its systems to detect malicious events. SOC folks are interested in the following:
- Vulnerabiltiies: System vulnerabilities can be leveraged to gain unauthorized access into a system, so it's essential to update or patch systems to close these vulnerabilities. If a system cannot be patched for whatever reason, then folks should be focusing on doing whatever they can to stop an attacker from exploiting it. SOC folks aren't necessarily assigned to remediate vulnerabilities, though this is a key aspect of their work.
- Policy violations: Companies put policies in place to protect networks and systems, and people may knowingly or unknowingly violate those policies. We are interested in identifying policy violations and mitigating them.
- Unauthorized activity: The SOC can find events where unauthorized activity is occurring on a user's account, and then block further events from occurring. This may include compromised accounts being controlled by adversaries.
- Network intrusions: Someone can always just gain access into a network - either they find a way in, or a user unwittingly creates a way in (say, by clicking a malicious link or downloading a malicious attachment in an email). The SOC can detect this and help stop it from escalating.

One key task performed by the SOC is gathering threat intelligence. _Intelligence_ simply refers to information about actual and potential enemies. _Threats_ are actions that could disrupt or adversely affect a system. Thus...threat intelligence is focused on collecing information to prepare against actual adversaries and actions they make take to compromise system security.

Threat intelligence is required for a threat-informed defense. Different companies may be targeted by different adversaries. You're in possession of customer data? Folks will probably want to steal that. You're in the business of producing something? You will probably have someone trying to stop that production. Different threat actors may be motivated by different things, too - a nation-state actor might be doing something for a political reason, whereas a ransomware group may just want mre money.

The key to intelligence is data. We need to be able to collect, process, and analyze data from various sources (local sources like network logs, public sources like forums, etc.). When we process data, we put it into formats for analysis, and when we analyze it, we try to extract information about attackers, motives, and possible steps we can take to help with defense. This information can be described as an adversary's tactics, techniques, and procedures.

Part of DFIR is digital forensics. Forensics is the use of science to investigate crimes and establish facts. Now that digital systems are so widespread, digital forensics (formerly computer forensics) developed to investigate crimes involving systems.

As opposed to SOC work, digital forensics is focused on analyzing the evidence of an attack as well as its perpetrators. This also includes other crimes such as intellectual property (IP) theft, cyber espionage, and possession of unauthorized content. In digital forensics, information can be gathered from various sources:
- The file system: A digital forensics _image_ (a low-level copy) of a system's storage can tell us a lot of information about what was installed on the computer, what files were created and overwritten, and what files were deleted.
- System memory: If an attacker runs malicious programs in memory without saving it to the disk, then a forensic image of the system's memory can be analyzed to get information about the attack.
- System logs: Client and server computers will maintain different log files about what's going on. Log files generally give us a lot of information about what happened on a system, and even if an attacker tries to clear their traces, you'll often find that some traces will still remain.
- Network logs: Logs of network packets that have traversed a network are useful for figuring out whether an attack was/is in progress and, if so, what it entails.

Incident response - the second half of DFIR - is focused on responding to an incident which could be anything ranging from an actual cyberattack to less-critical issues such as misconfigurations, intrusion attempts, and policy violations. Concrete examples include making systems inaccessible, defacing/altering a public-facing website, and data breaches. Folks have come up with processes for performing incident response; the steps are usually something like this:
1. Prepare for incident response by training the team and putting measures in place to prevent incidents from happening.
2. Detect incidents that are in progress or have occurred, and analyze what happened.
3. Contain the incident so it stops affecting other systems, eradicate it from the network entirely, and then recover any affected systems.
4. Perform post-incident activities, which may include reports and discussing lessons learned to prevent similar incidents from occurring in the future.

Sometimes in the IR process it might be worth analyzing malware. Malware - **mal**icious soft**ware** - refers to programs, documents, and files that perform malicious activities. This may include
- Viruses (code attached to programs) that can spread from computer to computer and can overwrite, alter, or delete files
- Trojan Horses, which look like they serve a beneficial purpose but actually just perform some malicious function
- Ransomware, which is a class of malware that encrypts user's files and demands payment to recover them.

The malware analysis process is focused on learning about malware using a few strategies:
- Static analysis, which inspects the malicious program's code without running it; this requires a good knowledge of _assembly language_ (or the instructions fundamental to a computer's processor)
- Dynamic analysis, which involves running the malware in a controlled environment and monitoring what it does.

**[Task 2, Question 1] What would you call a team of cyber security professionals that monitors a network and its systems for malicious events?** - Security Operations Center

**[Task 2, Question 2] What does DFIR stand for?** - Digital Forensics and Incident Response

**[Task 2, Question 3] What kind of malware requires the user to pay money to regain access to their files?** - ransomware

## [Task 3] Practical Example of Defensive Security

Now we'll run through a practical defensive security scenario - one involving an SOC. Suppose we're at a security operations center, and we're trying to protect a bank. The bank has a security information and event management (SIEM) tool, which is useful for gathering security-related information and events from various sources. These are events are put together in a unified dashboard. If the SIEM finds something suspicious has happened, then it generates an alert for us to investigate.

The challenge, when working with a SIEM, is that many alerts aren't actually malicious. An analyst must use their understanding of cybersecurity to spot events that are malicious (and, if not that, those that are suspicious). One alert may exist if a user has many failed login events - how do we know that this isn't just the user forgetting their password and trying to log in? Seeing a lot of login attempts against many different accounts at once, though...that might be more alarming.

The room has a simulated SIEM system that you can access by clicking the View Site button in the task. This opens a static site in Split View. We'll need to follow some instructions to make sense of the malicious event. The first  step is to spot a suspicious event and note the associated IP. This looks pretty suspicious to me:

![image](https://github.com/user-attachments/assets/107e139c-665d-44cf-9d34-5c6342f6fa2f)

Let's pop this IP into the IP scanner provided:

![image](https://github.com/user-attachments/assets/b1bd6460-6bff-43e3-b882-d0c7ea98aff8)

Open-source databases exist (e.g. AbuseIPDB, Cisco Talos Intelligence) that can be used to figure out if an IP is reputable and where the IP's location corresponds to. These are used to aid with alert investigations, though if we ever come across a malicious IP, we can always report it ourselves so that other people know about it.

Either way, we find out that the IP is malicious, so we'll want to escalate it. There was a sucessful authentication attempt from this address, so that's a small incident at the very least. We'll want to escalate this to the right person, so why don't we reach out to the SOC Team Lead? Usually we'll want to escalate up within the SOC:

![image](https://github.com/user-attachments/assets/8ad24e0d-07a4-47c4-b7f6-de2566f7eebe)

In this scenario, the SOC Team Lead allows us to block the malicious IP address, so we can just implement the rule in the firewall and then we're all set.

![image](https://github.com/user-attachments/assets/7ebf00a0-3aa6-4a6b-9fba-1d6c495641a2)

Doing all of this gives us our flag.

**[Task 3, Question 1] What is the flag that you obtained by following along?** - `THM{THREAT-BLOCKED}`

There's a lot of ground to cover in defensive security, and the field seems to evolve by the day. Being able to learn and adapt is going to be important for cybersecurity, and this is a pretty good place to start! Our next focus will be on how to actually learn and adapt... namely, how to effectively search for things.
