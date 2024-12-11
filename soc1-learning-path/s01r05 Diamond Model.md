# Diamond Model

## [Task 1] Introduction

In 2013, cybersecurity professionals developed the Diamond Model for intrusion analysis, which breaks apart any intrusion into four main components: the adversary, the infrastructure, capabilities, and the victim. These are the _core features_. Also present in the Diamond Model are the social, political, and technological dimensions for an attack. The core features are all interconnected with each other, hence the name _Diamond Model_.

It's a straightforward model that illustrates the basic concepts of an intrusion and the adversary, but it can be expanded and used to cover further ideas and concepts. Intelligence and analysis of other events may be incorporated to analyze adversary campaigns and forecast adversary operations. It also helps explain an event to a non-technical audience.

## [Task 2] Adversary

_Adversary_ is the catch-all term for attackers, enemies, cyber threat actors, hackers... it's effectively the person or group who was behind the cyberattack. Adversaries use use capabilities against a victim to achieve whatever their intent is. Typically, we won't know much about the adversary, especially if we're looking at an event that just happened. This information will become clearer as an investigation proceeds.

It's worth noting that there are two broad classes of adversaries, with slight differences in their characteristics.
- An adversary _operator_ is the actual hacker(s) conducting an intrusion.
- An adversary _customer_ is an entity that benefits from the intrusion. This is NOT necessarily the same as the operator. A customer might control different operators simultaneously, each with their own capabilities and infrastructure.

**[Task 2, Question 1] What is the term for a person/group that has the intention to perform malicious actions against cyber resources?** - Adversary Operator

**[Task 2, Question 2] What is the term of the person or a group that will receive the benefits from the cyberattacks?** - Adversary Customer

## [Task 3] Victim

A _victim_ is the target of an adversary. This can be an organization or a person, but it can also be as specific as an email address, IP address, domain, etc.

Victims may allow attackers a way into the organization they want to attack. In every cyberattack there will be a victim. If a spearphishing attachment is opened by the selected target, then that person is a victim.

As with adversaries, there are two broad classes of cyberattack victims:
- Victim personae: People, organizations being targeted whose assets are being attacked and exploited. Can include organization/people's names, industries, job roles, interests, and so on.
- Victim assets: The attack surface, the set of systems/networks/emails/hosts/IPs/social networking accounts/etc.

**[Task 3, Question 1] What is the term that applies to the Diamond Model for organizations or people that are being targeted?** - Victim Personae

## [Task 4] Capability

_Capabilities_ are the skills, tools, and techniques used by the adversary in an event. This encompasses the adversary's tactics, techniques, and procedures (TTPs) and can include all techniques used to attack the victims regardless of sophistication.

The _capability capacity_ describes the vulnerabilities and exposures that the individual can make use of. An _adversary arsenal_ is a set of capabilities that belong to an adversary. An adversary uses all the tools they have in their aresenal to carry out an attack.

Adversaries need certain capabilities to carry out attacks, possibly including malware and phishing capabilities. An attacker might acquire these as a service.

**[Task 4, Question 1] Provide the term for the set of tools or capabilities that belong to an adversary.** - Adversary Arsenal

## [Task 5] Infrastructure

The _infrastructure_ of an adversary is the software/hardware that they use. Generally it is the physical and logical interconnections that the adversary uses to deliver a capability and/or maintain control of capabilities. This includes things like C2 infrastructure, and can be developed with IP addresses, domain names, email addresses, and even malicious USB devices.

There are a few types of infrastructure:
- Type 1 Infrastructure includes all the components controlled by the adversary.
- Type 2 Infrastructure includes all the components controlled by an intermediary, regardless of whether they are aware of it. The victim will see this infrastructure and their first reaction will be to assume that it belongs to the adversary. Attackers use this infrastructure to obfuscate the source of the activity, and can include malware staging servers, malicious domain names, compromised emails, and so on.

Service providers provide the services critical for both infrastructure types. This includes ISPs, domain registrars, and webmail providers.

**[Task 5, Question 1] To which type of infrastructure do malicious domains and compromised email accounts belong?** - Type 2 Infrastructure

**[Task 5, Question 2] What type of infrastructure is most likely owned by an adversary?** - Type 1 Infrastructure

## [Task 6] Event Meta-Features

The Diamond Model provides room for six _meta-features_, which can provide further information, intelligence, or context to the Diamond Model.
1. The timestamp, which simply proivdes the date and time of the event. They are typically written as `YYYY-MM-DD HH:MM:SS.MSS`, and in the Diamond Model you can include timestamps for when events started and stopped. These can help determine patterns and group malicious activity together.
2. The phase of a cyberattack that a certain event takes place in (e.g., what step of the Cyber Kill Chain are we in?). Typically in any cyberattack, an attacker must execute a series of phases to achieve a desired result, so one event might not encompass the entire attack.
3. The results, or the consequences of a cyberattack. You won't normally know this with high confidence (at the very least, you probably won't know the full scope of the incident), though you can broadly state if the event succeeded/failed if you know this, and you might be able to tie back consequences to the CIA triad. You may also want to document all the effects of the event - were passwords lifted? Were data exfiltrated?
4. The direction of the attack, whether it takes place from victim to infrastructure, infrastructure to victim, infrastructure to infrastructure, adversary to infrastructure, infrastructure to adversary, or bidirectional. This may not be known.
5. The methodology of the attack, which effectively classifies the intrusion event (e.g., phishing, DDoS, breaches...).
6. The external resources used, including software (OS, virtualization, Metasploit...), knowledge on how to use the software, information to use in the attack, hardware (servers, workstations, routers...), funds, facilities, and access to the victim.

**[Task 6, Question 1] What meta-feature does the axiom "Every malicious activity contains two or more phases which must be successfully executed in succession to achieve the desired result" belong to?** - Phase

**[Task 6, Question 2] You can label the event results as "success", "failure", and "unknown". What meta-feature is this related to?** - Result

**[Task 6, Question 3] To what meta-feature is this phrase applicable "Every intrusion event requires one or more external resources to be satisfied prior to success"?** - Resources

## [Task 7] Social-Political Component

The _socio-political component_ generally describes the adversary's needs and intent, including financial gain, gaining notoriety in the hacker community, hacktivism, espionage... it's also possible that the victim is providing a product to the adversary, as is the case with cryptomining operations.

## [Task 8] Technology Component

The _technology component_ relates the capabilities and infrastructure of an adversary. This tells us how the adversary operates and communicates.

## [Task 9] Practice Analysis

Now that we understand the Diamond Model, let's construct a sample one. I'm not 100% sure why the 2015 Ukraine cyberattacks are referenced here, since the Diamond Model here seems to reference a more recent event. Regardless:
- It was found that APT2166, an underground hacking group, was responsible for this incident. We'd say this is the _adversary_.
- We're told that the event occurred at a certain timeframe - this would constitute our timestamp.
- Attackers targeted the IT systems of the corporation, so we'd say that this is the victim.
- Attackers used OneTrick to deploy ransomware onto the corporation's servers. We have a specific malware identified here, so we could consider this to be our resources.
- The attackers then stole the data from the corporation and sold it on an underground hacking forum. This is the result of the attack.
- Attackers were able to get in with legitimate credentials obtained through phishing. This can be considered part of the adversary's capabilities, as it is a technique the adversaries used.
- When attackers got into the network, they then pivoted to internal databases and file shares. This is part of the attackers' methodologies.
- We can map these events more generally to the Cyber Kill Chain model developed by Lockheed Martin.

Filling out the Diamond in this manner gives us this room's flag.

**[Task 9, Question 1] Complete all eight areas of the diamond. What is the flag that is displayed to you?** - `THM{DIAMOND_MODEL_ATTACK_CHAIN}`
