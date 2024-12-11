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

## [Task 6] MITRE D3FEND

## [Task 7] ATT&CK Emulation Plans

## [Task 8] ATT&CK and Threat Intelligence
