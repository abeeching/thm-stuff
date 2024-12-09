# Cyber Kill Chain

## [Task 1] Introduction

In military, a _kill chain_ describes the structure of an attack, including identification of the target, deciding to attack the target, the process for attacking the target, and then destruction of the target. It turns out that this is a good way of describing cyber attacks as well.

Lockheed Martin, a global security/aerospace company, established the Cyber Kill Chain framework back in 2011. These outline the steps an attacker in cyberspace uses to carry out their objectives. A successful attack occurs if an attacker gets to the end of the cyber kill chain. Our task in this room is to understand the attack phases therein.

Cyber Kill Chain analysis can be used to understand and protect against attacks, security breaches, and APTs. This can also be used when assessing your network - if certain security controls aren't in place, a step in the Kill Chain may be completed. Identifying the steps that can be taken with the current security controls will allow you to find security gaps and close them.

## [Task 2] Reconnaissance

Reconnaissance, in general, is the process of discovering and collecting information. In cybersecurity, the focus is specifically on a system or victim, and this is the planning phase of a cyber attack.

OSINT - Open-Source Intelligence - falls under the recon process. OSINT is typically one of the first steps carried out by an attacker. They can study victims by checking the information available about the company and its employees. Information such as the company's size, email addresses, and phone numbers might actually prove useful in carrying out a cyber attack.

Say an attacker doesn't know what company they want to attack, but they have done research on how to carry out an attack up to the final stage of the Cyber Kill Chain. The attacker starts from the reconnaissance phase, and begins by performing OSINT.

A common OSINT strategy is harvesting emails - this can be done by using public, paid, or free services. Email address harvesting can lead into phishing, where the attacker employs social engineering to steal sensitive data. Tools that help with email harvesting include
- theHarvester, which can also gather names, subdomains, IPs, and URLs with public data sources
- Hunter.io, an email hunting tool that lets you obtain contact information associated with a given domain
- OSINT Framework, which is a collection of OSINT tools for various use cases

The prospective attacker would also use social media websites to collect information on the target. The target victim or company might leave enough information that an attacker can use to conduct, say, a phishing attack.

**[Task 2, Question 1] What is the name of the Intel Gathering Tool that is a web-based interface to the common tools and resources for open-source intelligence?** - OSINT Framework

**[Task 2, Question 2] What is the definition for the email gathering process during the stage of reconnaissance?** - email harvesting

## [Task 3] Weaponization

Once reconnaissance is complete, an attacker would focus on creating a "weapon" - that is, something that the attacker can carry out a cyberattack with. An attacker might want to create a weaponizer that combines malware and exploits into some deliverable payload. Automated tools may be used to generate the malware, or malware can be purchased from certain sources. Sophisticated actors, including APTs, may go so far as to write their own malware, which can help with evading detection to an extent.

Some important terminology:
- Malware: Program, software that is designed to damage, disrupt, or gain unauthorized access to a computer
- Exploit: Program, code that takes advantage of some vulnerability or flaw in an application or system
- Payload: Malicious code that the attacker runs on the system

Our hypothetical adversary from before may choose to buy a pre-written payload off of the dark web, which will allow him to spend more time on the other phases. An attacker may also
- Create an infected Microsoft Office document containing a malicious macro or VBA (Visual Basic) script.
- Create a malicious payload or a very sophisticated worm, implant it on a USB drive, and leave it for someone to plug in.
- Choose C2 techniques that allow the attacker to execute commands on the victim's machine or deliver more payloads
- Select a backdoor implant, allowing the attacker to get into a computer system and bypass security mechanisms

**[Task 3, Question 1] This term is referred to as a group of commands that perform a specific task. You can think of them as subroutines or functions that contain the code that most users use to automate routine tasks. But malicious actors tend to use them for malicious purposes and include them in Microsoft Office documents. Can you provide the term for it?** - Macro

## [Task 4] Delivery

Once the payload has been developed, now it is time to transport and deliver it to the victim. There are a few ways to do this.
- Some folks will opt to send a phishing email using information they gather from reconnaissance to make it look legitimate. Our hypothetical attacker may pose as someone from another company that the target company works with, possibly sending a malicious invoice document.
- Other folks might leave infected USB drives in public places or send them to the company directly, even going so far as to print the company logo on them to make them more enticing to employees at the company.
- Still others will go for a watering hole attack, in which they compromise a website that a target normally visits and redirect them to some malicious site. This may also involve sending emails that point out the malicious URL. Once on the website, malware may be downloaded to the machine either through malicious pop-ups or with drive-by downloads.

**[Task 4, Question 1] What is the name of the attack when it is performed against a specific group of people, and the attacker seeks to infect the website that the mentioned group of people is constantly visiting.** - Watering hole attack

## [Task 5] Exploitation

Once the payload is delivered, now the attacker has to have it "detonate." That is, the payload needs to exploit some vulnerability so the attacker can gain access to the system. An attacker might create phishing emails that link to fake Office 365 login pages or contain malicious attachments. From here, an attacker can exploit further vulnerabilities to move through the network. We call this _lateral movement_ - an attacker is able to navigate through devices on the company network to reach their target.

The vulnerabilities leveraged at this stage might be server- or web-based, and there are rooms on this (such as the OWASP Top Ten room).

If they're particularly crafty, they might also use a zero-day exploit. These exploits are effectively unknown to security folks and can leave no room for detection, until somebody realizes that something has gone wrong.

Some examples of exploitation include
- A victim triggering the exploit by navigating to a link or opening an attachment
- Using a zero-day exploit to get into the system
- Leveraging software, hardware, or even people (via social engineering) to exploit vulnerabilities
- Triggering exploits for server-based vulnerabilities

**[Task 5, Question 1] Can you provide the name for a cyberattack targeting a software vulnerability that is unknown to the antivirus or software vendors?** - Zero-day

## [Task 6] Installation

At this point, the attacker would want to maintain some kind of presence on the system. If the system is ever shut off, or if their means of initial access is patched or removed, then they will no longer have access to the machine. Thus, an attacker will opt to set up a persistent backdoor, which allows an attacker to access a system that has been previously compromised. Information about persistence can be found in various TryHackMe rooms.

Persistence may be achieved with the following methods:
- Using web shells, which are malicious scripts written in web development programming languages (ASP, PHP, JSP, etc.). This allows an attacker to maintain access to the compromised system, and some folks may not even realize a web shell has been planted until too late.
- Setting up a backdoor, which can be done with various tools such as Metasploit's Meterpreter.
- Creating and modifying services on Windows. Services can be modified or created so they execute malicious scripts or payloads reuglarly. This can be achieved with tools like `sc.exe` and `reg`. Attackers using this technique will name the service something related to the OS or legitimate software on the OS.
- The payload can be added to the Run keys in the Registry. This forces the payload to execute every time the user logs in. You can also put files into either the user's startup folder or a system-wide startup folder.

Attackers may also employ timestomping, modifying a file's timestamps (including the times it was modified, accessed, created, and changed). This can help evade detection if a forensic investigator tries to look into things.

**[Task 6, Question 1] Can you provide the technique used to modify file time attributes to hide new or changes to existing files?** - Timestomping

**[Task 6, Question 2] Can you name the malicious script planted by an attacker on the webserver to maintain access to the compromised system and enables the webserver to be accessed remotely?** - Web shell

## [Task 7] Command & Control (C2 or C&C)

Once the attacker achieves persistence and gets malware to run on the victim's machine, the attacker can then establish a C2 connection through the malware to remotely control and manipulate the victim. The communication that takes place between a C2 server and a compromised host is called _C2 beaconing_.

The compromised host communicates with an external server set up by an attacker. Once the connection is established, the attacker can control the machine. Traditionally, adversaries use Internet Relay Chat (IRC) as a C2 channel. However, modern security solutions can spot malicious IRC traffic easily, so now adversaries use
- HTTP, HTTPS: This kind of C2 communication blends in easily with legitimate traffic and can be used for firewall evasion
- DNS: Infected hosts make constant requests to an attacker's DNS server. This technique is _DNS tunneling_.

Note that an adversary or another compromised host can own a C2 structure.

**[Task 7, Question 1] What is the C2 communication where the victim makes regular DNS requests to a DNS server and domain which belong to an attacker.** - DNS Tunneling

## [Task 8] Acitons on Objectives (Exfiltration)

Once the attack phases are complete, the attacker can finally achieve whatever goals they wanted to achieve. This may include
- Collecting credentials
- Performing privilege escalation to get into administrator accounts
- Internal reconnaissance, e.g., interacting with internal software
- Lateral movement through network
- Data exfiltration
- Deleting backups and shadow copies (the latter being a Microsoft technology that makes backup copies of computer files and volumes)
- Overwriting or corrupting data

**[Task 8, Question 1] Can you provide a technology included in Microsoft Windows that can create backup copies or snapshots of files or volumes on the computer, even when they are in use?** - Shadow Copy

## [Task 9] Practice Analysis

Now that we know how the Cyber Kill Chain works, we can conduct a practice analysis with a real-life attack: the Target data breach of 2013. In brief, approximately 40 million credit/debit card accounts were affected by a data breach, and Target had to pay $18.5 million. Our goal now is to figure out how it happened in terms of the Cyber Kill Chain.

Let's break this down into steps in the Static Site attached to the task. We'll assume that the reconnaissance phase was already done.
1. Weaponization: The attacker started by developing the weapon with PowerShell, which would later allow them to gain access to the target machines.
2. Delivery: Then, in a spearphishing attack, the payload was delivered to the victims. In the actual Target attack, phishing was actually conducted against a third-party vendor that had access to Target machines with a vulnerable web interface.
3. Exploitation: Once the payload was active, the attackers were able to exploit a public-facing application, namely their Point-of-Sale (POS) terminals. The POS system had vulnerabilities that the attackers were able to leverage.
4. Installation: Dynamic linker hijacking was used to help maintain presence. I think. I'm personally not 100% sure on this one, but this was probably some feature of the malware itself.
5. Commmand & Control: Fallback channels were implemented for C2 communication.
6. Action on Objectives: Data from the terminals were ultimately exfiltrated.

When the static site is complete with the items in the list placed in the right spots on the Kill Chain, you get the flag for this room.

**[Task 9, Question 1] What is the flag after you complete the static site?** - THM{7HR347_1N73L_12_4w35om3}

It _is_ worth noting that the Cyber Kill Chain - at least, as we used it in this room - was last modified in 2011. That's a long time ago, and so there might be things that the Kill Chain misses. Traditionally, it was designed to protect the network perimeter and against malware, but with the evolution of attacker TTPs and security solutions, it fails to identify certain issues. For instance, it doesn't really talk about insider threats, i.e., adversaries within the company itself.

The Cyber Kill Chain is still a good place to start in performing analysis, though it should be paired with other tools like MITRE and the Unified Kill Chain, the latter of which will be the topic of the next room.
