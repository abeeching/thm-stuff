# MITRE

## [Task 1] Introduction to MITRE

We've spent a majority of this room looking at various models for cyberattacks and adversaries. However, maybe we're interested in learning more about vulnerabilities that a cyberattacker can use, or perhaps we'd like to know specifically about an adversary's techniques. MITRE Corporation created a whole bunch of resources that help with this, and more. We'll be looking at their various projects.

## [Task 2] Basic Terminology

Before moving on, we should be aware of some terminology used in the MITRE projects.
- Advanced Persistent Threat (APT): This is a team/group (a threat group) or a country (a nation-state group) that engages in long-term attacks against victims. They are more sophisticated than the average hacker.
- Tactics, Techniques, and Procedures (TTPs): Characteristics of an attacker.
  - Tactics: The adversary's goals/objectives.
  - Techniques: How the adversary to accomplish a goal
  - Procedures: How the techniques are executed

## [Task 3] ATT&CK Framework

The Adversarial Tactics, Techniques, and Common Knowledge - or just ATT&CK - Framework is a compendium of tactics and techniques used by adversaries based on real-world observations. It began development around 2013, when it became clear that we needed to keep track of the TTPs used by APTs. It began life as the Fort Meade Experiment (FMX), where selected security professionals would emulate adversarial TTPs against a network. The results obtained from the emulation would then be used to construct a knowledge base of adversary TTPs.

Since then, the ATT&CK Framework has expanded to include the other major operating systems on the market - macOS and Linux. Security researchers and threat intelligence folks help build up the ATT&CK Framework, and it has been used both in blue teams (defensive security) and red teams (offensive security). It's accessible straight from your web browser.

We'll be taking a look at the ATT&CK Matrix for Enterprise, which contains fourteen tactics. Under each tactic is the set of techniques an attacker would use to perform the tactic. The tactics line up with the Cyber Kill Chain.

As an example, we might take a look at Initial Access, which encompasses techniques like Phishing, Supply Chain Compromise, and so on. Some techniques, like Phishing, have additional sub-techniques. In this case, phishing may be conducted with a Spearphishing Attachment, Link, or service.

Clicking any of the techniques will bring you to a page dedicated to the technique. This gives you more information, including a description of the technique, examples of the technique in use, and how to mitigate it. You can also search for techniques and groups directly on the MITRE ATT&CK website.

The same data can be viewed in the MITRE ATT&CK Navigator, which allows you to navigate through and annotate ATT&CK matrices. These can be used to visualize defensive coverage, red/blue team planning, detected techniques, and so on. When visiting a group or tool page on the MITRE website, you can click the ATT&CK Navigator Layers button > View to see the ATT&CK Navigator matrix for that particular group or tool.

The Navigator contains three sets of controls in the top-left: selection controls, layer controls, and technique controls. The question mark at the far right will provide more information about how to use the Navigator.

In short, ATT&CK allows you to understand an adversary's tactics and techniques, and the Navigator allows you to map threat groups to tactics and techniques as needed.

For the questions that follow, we'll be referring to version 8 of the Phishing page in MITRE ATT&CK.

**[Task 3, Question 1] Besides Blue teamers, who else will use the ATT&CK Matrix? (Red Teamers, Purpe Teamers, SOC Managers?)** - Red Teamers

Upon clicking the link to the Phishing page in MITRE, we get some information including how MITRE identifies the technique:

![image](https://github.com/user-attachments/assets/1c3aad32-b052-48ce-ac6a-ed6e81dc538b)

**[Task 3, Question 2] What is the ID for this technique?** - T1566

As noted above, MITRE contains information on how to mitigate techniques. In this case, here are the mitigations for general phishing attempts:

![image](https://github.com/user-attachments/assets/eac06b57-e244-42c9-867a-bff7b27c49af)

A common way of mitigating phishing is to train users. If a phishing email makes it to a user's inbox and they click on it, then there's a pretty good chance that they'll get compromised. So, one way to stop phishing would be to teach users how to spot phishing emails and general social engineering techniques.

**[Task 3, Question 3] Based on this technique, what mitigation covers identifying social engineering techniques?** - User Training

MITRE ATT&CK pages also contain information on how to detect the usage of techniques, and where you might be able to detect these things in. For instance, here's the Detection information for the phishing technique:

![image](https://github.com/user-attachments/assets/c5653a8e-4d9e-424b-b4b5-a1e6a89d9985)

We're particularly interested in the data sources - that is, we want to know where we would look to spot a technique that's in use. In this case, we've got three data sources to check: application logs, files (these can be obtained from phishing emails), and network traffic.

**[Task 3, Question 4] What are the data sources for Detection? (format: source1,source2,source3 with no spaces after commas)** - Application Log,File,Network Traffic

MITRE also discusses how the technique has been used by groups and how it is involved with certain pieces of software, among other things. These constitute the Procedure Examples listed on the technique page:

![image](https://github.com/user-attachments/assets/56bd7a01-a156-4450-a105-0f28130de473)

In the phishing technique, the rows that have a "G" in their ID signify groups that have used the technique. In this case, we see that Axiom, GOLD SOUTHFIELD, and INC Ransom have all used phishing in their attacks before. The rows with "S" in their ID indicate software that has been spread by the technique (or software that employs the technique). Other information may be found depending on the technique, including how the technique was used in campaigns.

**[Task 3, Question 5] Which are the first two groups to have used spear-phishing in their campaigns? (format: group1,group2)** - Axiom,Gold SOUTHFIELD

We can follow the links in the group/software names above to learn more about the groups and software in question. Let's look at the page for Axiom. MITRE will list off associated groups, techniques used, and software used by groups on these pages. Here's the Associated Group information provided by MITRE for Axiom:

![image](https://github.com/user-attachments/assets/7a8ab8df-9bcb-410c-80c6-5cb01303a732)

**[Task 3, Question 6] Based on the information for the first group, what are their associated groups?** - Group 72

Going back to the phishing technique page, we may also investigate the software in use. For instance, we'll take a look at Hikit. Software pages will include information on the techniques used, as well as the groups that use the software. Here are the techniques employed by Hikit:

![image](https://github.com/user-attachments/assets/4c47aa71-cf30-44bb-abff-9466985892d1)

As we'd expect, Hikit is associated with the phishing technique.

**[Task 3, Question 7] What software is associated with this group that lists phishing as a technique?** - Hikit

Also, MITRE will provide a brief description of the software in question:

![image](https://github.com/user-attachments/assets/b2da6f5c-db84-4d23-8554-993d1fbb7088)

**[Task 3, Question 8] What is the description for this software?** - Hikit is malware that has been used by Axiom for late-stage persistence and exfiltration after the initial compromise.

Referring back to the description of Axiom, we are given more information about other groups that it may share an overlap with:

![image](https://github.com/user-attachments/assets/26179d7b-2898-4ac0-a6f3-2a3d799266bf)

**[Task 3, Question 9] This group overlaps (slightly) with which other group?** - Winnti Group

And if we check out the Axiom's groups techniques...

![image](https://github.com/user-attachments/assets/2248ee8b-f1b6-451a-9b95-f8859d5c7eee)

We see there are fifteen techniques in use (not all of them are included in the above screenshot). For the purposes of the question, we're only concerned with the techniques and not any of the sub-techniques. MITRE designates techniques with `TXXXX`, where `XXXX` is just a number. Subtechniques are then denoted with `TXXX.YYY`, where `.YYY` denotes the subtechnique ID. In this case, we can just count the number of `TXXXX` entries in the table to get our answer.

**[Task 3, Question 10] How many techniques are attributed to this group?** - 15

## [Task 4] CAR Knowledge Base

The Cyber Analytics Repository, or CAR, is a knowledge base of analytics based on the ATT&CK model. CAR focuses on providing validated, explained analytics on certain techniques and concepts, as well as the theory behind their use and why they're used. CAR is defined in terms of pseudocode, but can be easily extended to specific tools such as Splunk.

As an example, we might want to look at the CAR page on Scheduled Task - FileAccess. This gives us a description of how an adversary might used the Windows Task Scheduler feature to schedule command execution and where the tasks are stored on the system. Furthermore, we'll see pseudocode and information on how to find the analytic in Splunk. You may also find information on how to look for it with the Event Query Language (EQL), which queries, parses, and organizes Sysmon data. Sysmon will be the subject of a later room.

You can view a full list of the analytics put up in CAR as well as what implementations are available for a given analytic and what OS platform it applies to. The ATT&CK Navigator also has some techniques that overlap with those present in CAR.

CAR allows us to find analytics that take us further than what's provided in the ATT&CK Framework; it is an additional resource for us to use, rather than a replacement for the framework.

If we explore the Scheduled Task - FileAccess CAR entry, we get links to information in the ATT&CK Framework:

![image](https://github.com/user-attachments/assets/0d3c4b3e-0f0d-4745-a1d8-3f4db3965c1e)

Since we're looking for a _tactic_, we can click the links in the Tactic(s) section of the table and see more information about each of them. For the purposes of answering this question, we're looking for _Persistence_, which has the required tactic ID:

![image](https://github.com/user-attachments/assets/c276989a-52e5-487a-8918-1d5d82f4571d)

**[Task 4, Question 1] What tactic has an ID of TA0003?** - Persistence

The CAR main page has more information on how it works, including how some the source code for some analytics are meant for specific products. For example, there are Zeek scripts (which will be covered in the third section of this path) that allow us to examine SMB and RPC traffic primarily. It uses teh library BZAR.

![image](https://github.com/user-attachments/assets/04618f06-a901-4085-8ff2-9f4c94c0070a)

**[Task 4, Question 2] What is the name of the library that is a collection of Zeek (BRO) scripts?** - BZAR

In the CAR website, you can click on the Analytics button at the top to see the full list of analytics. Scrolling through, we see various analytics, including one for running an executable with the same hash, but different names:

![image](https://github.com/user-attachments/assets/93427ef9-21fd-4e1e-9b97-7cdd646e2037)

It turns out that this is a technique for masquerading.

**[Task 4, Question 3] What is the name of the technique for running executables with the same hash and different names?** - Masquerading

Now we're asked to look at CAR-2013-05-004, which focuses on Execution with AT, a Windows built-in command that allows for persistence, privilege escalation, and remote execution. We covered the fact that detections and techniques can be found in the CAR page, as well as Implementations. We also see references to data models (e.g., what objects are involved, what happens to those objects, etc.). Beneath Implementations, we see this:

![image](https://github.com/user-attachments/assets/3e0f2654-800c-45e6-8f4f-dc08b82c6885)

CAR just so happens to provide unit tests, which allow you to see how this analytic would be generated by an attacker.

**[Task 4, Question 4] Examine CAR-2013-05-004, besides Implementations, what additional information is provided to analysts to ensure coverage for this technique?** - Unit Tests

## [Task 5] MITRE Engage

Proceeding, we have MITRE Engage, which allows you to plan and discuss adversary engagement operations. It focuses on engaging the adversary directly via cyber _denial_ (preventing the ability to conduct operations) and cyber _deception_ (intentionally misleading the adversary). The website for Engage provides a starter kit for this approach, including whitepapers and documents explaining methodologies and processes, along with checklists. Engage has its own matrix with actions placed into various categories:
- Prepare: Actions that can be taken to plan for adversary engagement
- Expose: Actions that are taken when an adversary triggers deception activities
- Affect: Actions that impair the ability of adversaries to carry out operations
- Elicit: Actions that allow one to observe an adversary and learn about their TTPs
- Understand: Actions that allow for analysis of the adversary and the actions taken

More information can be found with the Engage Handbook. The Engage Matrix Explorer allows you to directly interact with the matrix, and you can filter for information from ATT&CK directly. By default, the matrix focuses on the _Operate_ phase of the engagement process, which includes the Expose, Affect, and Elicit categories above.

Let's look at the Engage matrix. We'll start by looking at some of the steps that can be taken to prepare for adversary engagement. You'll have to click on the Prepare tab at the top of the page in order to see this portion of the matrix.

![image](https://github.com/user-attachments/assets/1be55075-155a-40f0-88bb-af148815db04)

We can click through each of the actions in the matrix to get a brief description about them, as well as a link for further information. Here's the information on _Persona Creation_, which involves planning and creating some fictitious user (ostensibly for deception actions later).

![image](https://github.com/user-attachments/assets/d09fea10-121d-4ab0-866a-d2f988f339ec)

**[Task 5, Question 1] Under Prepare, what is ID SAC0002?** - Persona Creation

Of course, we may be interested in learning how to implement the actions in the Engage matrix. Engage has various tools, aside from the matrix, that can help us here. You can see this by going to the very top of the Engage page and going to Tools > All Tools.

It's worth checking the resources available here. If you scroll through, you'll probably spot this worksheet:

![image](https://github.com/user-attachments/assets/6aeb63c1-db23-4c71-9e44-d5d7815da641)

This is related to the previous question, for sure, but it's also worth checking out the contents of that worksheet. It goes into quite a bit of detail about how to create a fake user. The worksheet suggests that you should provide information about the persona, where they live, what work they do, when they work, what websites they visit, and so on.

![image](https://github.com/user-attachments/assets/a78218e7-26a0-4c65-af1b-1d38273b7dcf)

**[Task 5, Question 2] What is the name of the resource to aid you with the engagement activity from the previous question?** - Persona Profile Worksheet

Let's head back to the matrix. This time we'll want to take a look at the Operate tab of the matrix. For the next question, we'll want to find an activity that involves _baiting_ a specific response from an adversary. You can click through the various activities to figure this one out, and you might start out by looking for activities that suggest we're trying to bait the adversary into doing something. _Lures_ might be a good place to start:

![image](https://github.com/user-attachments/assets/918d4243-2fef-49f7-bcb0-37ada9c5bf5f)

And indeed it's what we're looking for - we attempt to elicit a response from an adversary by using deceptive systems and artifacts.

**[Task 5, Question 3] Which engagement activity baits a specific response from the adversary?** - Lures

We conclude our look into MITRE Engage by looking at some of the activities present in the Understand tab of the matrix. This category specifically lists _Threat Model_ as one of the actions that can be taken, and the definition is as follows:

![image](https://github.com/user-attachments/assets/6bfcf480-5bf4-4f12-8dda-8f4395babb63)

**[Task 5, Question 4] What is the definition of Threat Model?** - A risk assessment that models organizational strengths and weaknesses

## [Task 6] MITRE D3FEND

As the naming scheme for this resource suggests, MITRE's Detection, Denial, and Disruption Framework Empowering Network Defense Framework (the D3FEND Framework) takes the opposite approach to the ATT&CK Framework. It is focused on outlining countermeasures that can be taken against a cyberattack. This resource is still in beta (as of the room's writing), and is funded by the NSA. Checking an artifact in the D3FEND matrix gives information about what the defense tactic is, how it works, examples of it in use, and how it is related to other artifacts.

Like with other MITRE resources, it is possible to filter this matrix based on techniques from the ATT&CK matrix. At the top left of the matrix, there's actually an option to perform an ATT&CK Lookup, which will filter out artifacts on the D3FEND matrix based on the ATT&CK technique.

![image](https://github.com/user-attachments/assets/86415e16-25f1-454e-87f3-a2e05b48388f)

**[Task 6, Question 1] What is the first MITRE ATT&CK technique listed in the ATT&CK Lookup dropdown?** - Data Obfuscation

If you click on any of the ATT&CK techniques in this list, you'll be given information on how you might be able to detect and mitigate the technique. The D3FEND Inferred Relationships graph provides defense techniques that might help in dealing with the technique. Here's the graph for the Data Obfuscation technique:

![image](https://github.com/user-attachments/assets/46c3d226-6a3c-4e2b-9399-1a1fa4a8ad38)

The boxes to the left are all D3FEND techniques. We see how they can be used to detect and isolate data obfuscation attempts, as well as how they can be used to analyze and filter through outbound internet traffic. Note that this feature is still in development; it is likely to change in the future.

**[Task 6, Question 2] In D3FEND Inferred Relationships, what does the ATT&CK technique from the previous question produce?** - Outbound Internet Network Traffic

## [Task 7] ATT&CK Emulation Plans

Part of MITRE Corporation is the MITRE Engenuity foundation, which promotes the development of projects and services to help advance cybersecurity efforts, among other things. It is responsible for the Center of Threat-Informed Defense (CITD) organization, the Adversary Emulation Library, and the ATT&CK Emulation Plans.

The CITD consists of companies and vendors around the globe, who are all tasked with researching cyber threats and associated TTPs. The companies and vendors include Microsoft, Verizon, Splunk, AttackIQ, and Red Canary. The end goal of the organization is to advance open-source software, methodologies, and frameworks, as well as to expand the knowledge base provided by the ATT&CK Framework.

The Adversary Emulation Library provides freely-accessible adversary emulation plans, which are useful in blue/red teaming exercises. These are contributions from CITD.

The ATT&CK Emulation Plans provide a step-by-step guide on how to mimic a certain threat group. This might be helpful for an organization if they're worried about specific threat groups targeting them. There are a wide variety of plans available on CITD's Adversary Emulation Library GitHub page, though the room calls out the plans for APT3, APT29, and FIN6. Let's look at these.

We'll start by looking at the APT 3 Emulation Plan. Navigating to the link in the task for APT3, we are led to a MITRE ATT&CK page that provides a broad description of the emulation process. There's a flowchart here illustrating three phases of an attack that could be carried out by APT3:

![image](https://github.com/user-attachments/assets/7c24fa3f-3c95-4e62-81e4-cd6f89b8fa32)

**[Task 7, Question 1] In Phase 1 for the APT3 Emulation Plan, what is listed first?** - C2 Setup

Beneath these are the documents for the emulation plan, including the overall outline as well as a Field Manual that supplements the emulation plan. The latter provides command-by-command actions that a group is known to use and how to exhibit the same behavior using other tools. We'll be taking a look at the main Emulation Plan document. You can use the Table of Contents to find information about how the group carries out a specific part of the cyberattack - in this case, here's how they might accomplish persistence:

![image](https://github.com/user-attachments/assets/f1c682cc-ebe8-43dd-825f-8ad14f132890)

We see that a Windows binary gets replaced in this step.

**[Task 7, Question 2] Under Persistence, what binary was replaced with cmd.exe?** - `sethc.exe`

Now we'll take a look at APT29. This leads us to CITD's GitHub page, which provides a lot of information about how the group operates. We'll be looking at two scenarios:
1. How APT29 conducts a "smash-and-grab," then performs espionage to gather and exfiltrate data; this includes other activities that are present in other cyberattacks.
2. How APT29 more slowly and stealthily compromises the initial target and carries out a cyberattack from there.

There are links on the GitHub page to the emulation plans for these two scenarios. Here's a brief outline of the infrastructure used in Scenario 1:

![image](https://github.com/user-attachments/assets/a1475170-7fc3-428c-a2a2-cd6b672aa48f)

**[Task 7, Question 3] Examining APT29, what C2 frameworks are listed in Scenario 1 Infrastructure? (format: tool1,tool2)** - Pupy,Metasploit Framework

And here's the infrastructure used in Scenario 2. Note that the outline calls out a server that's running a general _offensive framework_. The authors specifically went with PoshC2 during their testing:

![image](https://github.com/user-attachments/assets/4f2b2c5f-9206-4af2-be03-e42ba973cdf6)

**[Task 7, Question 4] What C2 framework is listed in Scenario 2 Infrastructure?** - PoshC2

We're actually not going to look at FIN6 for the last question (it's particularly instructive to look at its emulation plan, though). We'll be taking a look at the Sandworm Team, which was attributed to Russia and was responsible for cyberattacks including the spread of NotPetya. This is a particularly interesting piece of malware that disguises itself as ransomware while destroying the target system as much as possible. We'll be looking at the Emulation Plan, Scenario 1.

![image](https://github.com/user-attachments/assets/52e4428d-f475-4377-8cfc-7f6b973ecf40)

We can look up these tools in MITRE ATT&CK, so let's do so. We'll be checking out the P.A.S. Webshell. You can either look up the Sandworm Team in CTI > Groups, or you can look up the webshell in CTI > Software. For some reason, the search didn't work for me. Doing so yields the following information:

![image](https://github.com/user-attachments/assets/93a1c578-b56d-4ea6-88b6-0a38967efd0c)

**[Task 7, Question 5] Examine the emulation plan for Sandworm. What webshell is used for Scenario 1? Check MITRE ATT&CK for the Software ID for the webshell. What is the id? (format: webshell,id)** - P.A.S.,S0598

## [Task 8] ATT&CK and Threat Intelligence

CTI, by the way, refers to _cyber threat intelligence_. It is the subject of the next section of rooms in this path, but in brief, it is focused on gathering information about TTPs associated with an adversary. This allows defense teams to make better strategic decisions. Typically, you'll have in-house teams in larger corporations that are dedicated to collecting threat intelligence, and you may also have vendors that provide threat intelligence such as CrowdStrike.

Let's take a look at how MITRE ATT&CK can be used for threat intelligence. Say we're working in aviation, and the organization we're working in wants to move to the cloud. We're concerned about any APT groups that might target this sector and our move to the cloud, and we'd like to check if there are any gaps in coverage.

Checking the CTI > Groups page on the ATT&CK website, we can look for adversaries that target the aviation sector. A simple CTRL+F, followed by a search for "aviation," will do. Here's one that we might be interested in:

![image](https://github.com/user-attachments/assets/7b771d38-67be-4999-9381-1010683fdf17)

**[Task 8, Question 1] What is a group that targets your sector who has been in operation since at least 2013?** - APT33

Let's drill deeper into this group. Going to its page on ATT&CK, we can see that they employ a wide variety of techniques. Considering that we're attempting to move to the cloud, this is probably going to be one of the most concerning techniques for us:

![image](https://github.com/user-attachments/assets/d6ea57cf-eacb-4aaf-a16b-99a0fae94d7a)

**[Task 8, Question 2] As your organization is migrating to the cloud, is there anything attributed to this APT group that you should focus on? If so, what is it?** - Cloud Accounts

The brief description of the technique above tells us how APT33 employs the Cloud Accounts technique: they use a piece of software known as _Ruler_, which is a command-line tool that abuses Microsoft Exchange services.

**[Task 8, Question 3] What tool is associated with the technique from the previous question?** - Ruler

So now we know that this APT group has a tool that can be used to compromise cloud accounts. We're probably interested in figuring out how to mitigate cloud account compromise, so let's investigate the technique itself in ATT&CK. Here are the list of mitigations provided:

![image](https://github.com/user-attachments/assets/0df11f4c-dbea-4826-8756-d7a326d07834)

We see that Multi-Factor Authentication is recommended for cloud accounts, particularly privileged ones. There are a variety of ways of implementing MFA, one of which is SMS.

**[Task 8, Question 4] Referring to the technique from question 2, what mitigation method suggests using SMS messages as an alternative for its implementation?** - Multi-factor Authentication

Also on the Cloud Accounts technique page, we see what platforms are affected by the technique. This is something that we should probably note down:

![image](https://github.com/user-attachments/assets/11673eae-2b9e-4b13-b4b0-2cefac3e7a32)

**[Task 8, Question 5] What platforms does the technique from question #2 affect?** - IaaS, Identity Provider, Office Suite, SaaS

Reminder: MITRE's resources are not exclusively for blue teamers/defenders - they can be used by red teamers as well! As we saw in previous tasks, red team folks can use the information provided here to mimic adversaries and bypass controls, which can be beneficial in identifying security gaps. MITRE's resources are more for _purple teaming_, where blue teams and red teams come together to analyze an organization's security.
