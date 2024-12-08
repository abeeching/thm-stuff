# Junior Security Analyst Intro

## [Task 1] A Career as a Junior (Associate) Security Analyst

The Junior Security Analyst is a Triage Specialist, meaning that they triage and monitor event logs and alerts often. The responsibilities of such a role may include:
- Monitoring and investigating alerts in a 24/7 SOC operations environment
- Configuring and managing security tools
- Developing and implementing basic Intrusion Detection System (IDS) signatures
- Participating in SOC working groups and meetings
- Creating tickets and escalating incidents to upper tier analysts as needed

Typically, these jobs require these qualifications:
- 0 to 2 years of experience with security operations.
- A basic understanding of networking (i.e., know the OSI model or the TCP/IP model), operating systems (e.g., Windows and Linux), and web applications.
- Scripting and programming skills are good to have.
- Typically you'll want a CompTIA Security+ certification.

As you progress in the role, you will go up to Tier 2 and Tier 3 roles. While Tier 1 SOC analysts monitor traffic logs and events, work on tickets, close alerts, and maybe do some basic investigations...
- Tier 2 analysts focus on deeper investigations, analysis, and remediation, proactively hunt for adversaries, and monitor/resolve more complex alerts.
- Tier 3 analysts work on more advanced investigations, which may even involve malware reversing, and will also be required to perform advanced threat hunting and adversary research.

**[Task 1, Question 1] What will be your role as a Junior Security Analyst?** - Triage Specialist

## [Task 2] Security Operations Center (SOC)

The core function of the SOC is to investigate, monitor, prevent, and respond to threats 24/7 or around the clock. SOC teams focus on protecting assets and can help implement cybersecurity for an organization. The SOC is the place where efforts are coordinated in protecting against cyberattacks. The size of an SOC may vary depending on the size of the organization.

An SOC's responsibilities may include:
- Ticketing
- Collecting logs
- Maintaining a knowledge base
- Research and development
- Aggregating and correlating alerts
- Threat intelligence
- SIEM operations (Security Information and Event Management)
- Reporting

Junior security analysts try to keep informed about the latest cybersecurity threats; given the existence of social media and aggregation sites like Feedly, there hasn't been a better time to stay up-to-date. Analysts then use this information to focus on detecting threats and hunting them down, along with creating a security roadmap for the organization. Prevention procedures include getting intelligence data on the latest threats, threat actors, and their tactics/techniques/procedures, as well as maintenance of firewall signatures, patching of vulnerabilities, block-listing/safe-listing of apps, and so on.

The room specifically calls out the US Cybersecurity and Infrastructure Security Agency's (CISA) alerts on APT40, a Chinese advanced persistent threat group. It's a good exercise to examine these.

When it comes to monitoring, SOC teams make use of SIEM systems and Endpoint Detection and Response (EDR) systems. These help with monitoring suspicious and malicious network activities, mainly by allowing folks to categorize alerts based on their threat level (ranging from low to critical). This allows team members to focus on the most pressing issues first. Having these monitoring tools in place, properly configured, is critical for the SOC team.

Junior security analysts may also partake in investigations. They triage on the ongoing alerts and figure out how an attack works. If they can, they prevent bad things from happening. The "five Ws" are important to establish - who, what, when, where, and why (also including how). Analysts find the answers to these questions by looking in data logs and alerts, as well as by using open-source tools, many of which will be explored in the path proper.

Once investigation is complete, the SOC team coordinates and acts on the compromised hosts. This may involve removing hosts from the network altogether, terminating malicious processes, deleting files, and more.

## [Task 3] A Day in the Life of a Junior (Associate) Security Analyst

To understand these responsibilities, it is instructive to look at a simulation of the kind of stuff an analyst has to deal with. SOC work is not always easy - you'll have to combine information from various log sources with all sorts of tools, ranging from those that help you examine network traffic to those that help you extract data for forensics. Responding to an incident can take a long time, especially if the cyberattack is large-scale, but it is exciting and rewarding to finally see an incident get resolved.

In this path, we'll look at some of the fundamental knowledge needed to be successful in the role, and to cap off this room, we'll look at one of the most common tasks in SOC: dealing with tickets and alerts.

This task has a button to start a static site. Do so and you'll be greeted with this:

![image](https://github.com/user-attachments/assets/81f383ec-6021-4fb6-bbf9-5f10114a1fed)

We're looking for anything that seems problematic. The fact that someone was able to connect to an IP over port 22 (SSH) without authorization is a little concerning. Let's click this event to drill deeper into it. Note that the given IP address is `221.181.185.159`.

**[Task 3, Question 1] What was the malicious IP address in the alerts?** - `221.181.185.159`

In this next step, we'll need to scan the IP address. We can simply input the IP address we found earlier, and click Submit to scan.

![image](https://github.com/user-attachments/assets/61e03294-04ae-4aa3-83a5-64557ae37916)

Well, that's problematic. While the alert on the unauthorized/failed authentication attempt isn't bad on its own, it's the fact that they were able to successfully get in afterwards that's concerning. Our next step is to escalate this incident to someone appropriate. As we discussed in the task, escalating to someone higher up in the SOC is the typical procedure, and Will Griffin looks like just the guy we need to talk to.

![image](https://github.com/user-attachments/assets/b0787d84-5eb2-4bda-ada4-0caeda761658)

**[Task 3, Question 2] To whom did you escalate the event associated with the malicious IP address?** - Will Griffin

Will's given us permission to block the malicious IP (thanks, Will!), so we can do so by typing in the malicious IP address on the Firewall Block List and clicking the Block IP Address button.

![image](https://github.com/user-attachments/assets/822a52d5-86b3-4d64-820f-80f7eca55e8d)

Doing this gives us the attacker's message, i.e., our flag.

![image](https://github.com/user-attachments/assets/b0b3bbf3-e15a-4b40-a9c0-9961af836ce7)

**[Task 3, Question 3] After blocking the malicious IP address on the firewall, what message did the malicious actor leave for you?** - `THM{UNTIL-WE-MEET-AGAIN}`
