# Incident Response Fundamentals

## [Task 1] Introduction to Incident Response

We have all sorts of protective measures in place for digital assets, but we need to consider what happens in the event that these measures fail. What do we do when an attacker gets into the system?

You'll see information about cyberattacks posted on the Internet pretty often. These attacks might cause a company to lose thousands of dollars. Things like these are knwon as _cybersecurity incidents_, and in order to effectively deal with them, we need to plan for them and have resources ready. This is all a part of _incident response_, which handles an incident from beginning to end. This room covers the key concepts of incident response, and concludes with a sample exercise.

## [Task 2] What are Incidents?

Many processes run on your devices, with some of them being interactive (i.e., you can actually perform actions in them), and others being non-interactive (i.e., they don't require interaction and are just needed for the device to work). All processes will generate events.

Many events are generated on a regular basis due to the number of processes and number of tasks running on a given device. Sometimes, they can indicate that something has gone wrong with the device. Security solutions are used to examine these events for harmful activities.

When a security solution finds an event (or events) associated with possible harmful activity, it will trigger an alert that the security team then analyzes. Some alerts are false positives, meaning that the alerts don't actually point to something harmful taking place (if nothing else, it may be risky, e.g. a lot of data being transferred at one time). Other alerts are true positives, meaning something _is_ actually taking place (e.g. a real phishing attempt). True positives are sometimes known as _incidents_.

If we're able to find an incident, we need to assign a severity level to the incident; this makes it easier for the security folks to figure out what to prioritize. Incidents are typically categorized as low, medium, high, or critical based on their impact.

**[Task 2, Question 1] What is triggered after an event or group of events point at a harmful activity?** - alert

**[Task 2, Question 2] If a security solution correctly identifies a harmful activity from a set of events, what type of event is it?** - true positive

**[Task 2, Question 3] If a fire alarm is triggered by smoke after cooking, is it a true positive or a false positive?** - false positive

## [Task 3] Types of Incidents

Security incidents can be put into specific categories:
- Malware infections: Malicious programs that can potentially cause damage if run, including files/text/documents/etc... may be delievered in a phishing email, for instance.
- Security breaches: Unauthorized person gets confidential data - very critical for businesses
- Data leaks: Confidential information of an individual or an organization is exposed to unauthorized entities. Can lead to reputational damage, threatening victims, etc. Can also be caused unintentionally by human error and misconfiguration.
- Insider attacks: Incidents within an organization, e.g. disgruntled employees plugging in malicious USBs. Can be hazardous, since an insider has greater access to resources than an outsider.
- Denial of service attacks: Disrupting the availability of a service to legitiamte users, usually due to the exhaustion of system resources.

Each incident can uniquely impact the victim negatively; we can't directly compare them based on just impact. One incident may cause extensive damage in one organization and minor damage in another.

**[Task 3, Question 1] A user's system got compromised after downloading a file attachment from an email. What type of incident is this?** - malware infection

**[Task 3, Question 2] What type of incident aims to disrupt the availability of an application?** - denial of service

## [Task 4] Incident Response Process

Handling incidents can be difficult in the absence of a structured process. That's why folks have developed generic approaches to follow in any given incident. Two common incident response frameworks include the SANS and NIST frameworks.

SANS and NIST have both contributed to cybersecurity in various ways. SANS offers various courses and certifications in cybersecurity, while NIST develops standards and guidelines for cybersecurity. Both of their frameworks are similar.

The SANS process is as follows:
1. Preparation: Build necessary resources to handle an incident, including teams, plans, security solutions, training...
2. Identification: Look for abnormal behavior that may indicate an incident, perhaps via security solutions and techniques
3. Containment: Identify an incident and minimize the impact via isolating the machine(s), disabling the account(s)...
4. Eradication: Remove the threat from the environment and ensure that systems are clean before recovering
5. Recovery: Recover affected systems from backup, rebuild them if needed, and test them to ensure they are ready to use
6. Lessons Learned: Identify gaps in incident detection and analysis; document them to improve the process for future incidents.

The NIST framework is similar, but the steps are reduced to four: Preparation, Detection and Analysis, Containment/Eradication/Recovery, and Post-Incident Activity.

Organizations can derive their own incident response processes by following these frameworks. Each process has a formal document listing all relevant organizational procedures. The formal document outlining this process is an _incident response plan_, and it underlines the approach during any incident. It is formally approved by senior management and consists of the procedures to be followed before, during, and after an incident.

The key components of an incident response plan include:
- Roles and responsibilities
- Methodology
- Communication plan with stakeholders, including law enforcement
- Escalation path

**[Task 4, Question 1] The Security team disables a machine's internet connection after an incident. Which phase of the SANS IR lifecycle is followed here?** - containment

**[Task 4, Question 2] Which phase of NIST corresponds with the Lessons Learned phase of the SANS IR lifecycle?** - Post Incident Activity

## [Task 5] Incident Response Techniques

The Incident and Detection/Analysis steps in SANS and NIST, respectively, can be tricky to carry out - it may be hard to identify abnormal behavior and incidents manually. Multiple security solutions exist to help with this, though; some can even respond to incidents and execute the other phases of the lifecycle. Such solutions include:
- SIEMs: These collect all important logs in a centralized location and correlates them to identify incidents
- Antiviruses: These detect known malicious programs and regularly scan systems for these
- EDR: These are deployed on systems to protect them against advanced-level threats, and can even contain/eradicate threats.

Once an incident is identified, certain procedures must be followed to investigate the attack's extent, prevent further damage, and eliminate it from the network. The steps may differ depending on the incident. Step-by-step instructions for dealing with these incidents can help with this process. We call these steps _playbooks_. Playbooks provide guidelines for a comprehensive incident response.

A playbook for a phishing email incident may include the following steps:
1. Notify stakeholders of the phishing email incident
2. Conduct header and body analysis of the email to determine if it was malicious
3. Look for attachments and analyze them
4. Determine if anyone opened the attachments
5. Isolated infected systems from the network
6. Block the email sender

A _runbook_ provides detailed, step-by-step execution of specific steps during incidents, and these can vary too, depending on the resources available for investigation.

**[Task 5, Question 1] Step-by-step comprehensive guidelines for incident response are known as?** - playbooks

## [Task 6] Lab Work Incident Response

Let's go ahead and investigate a phishing email with a malware attachment. An incident begins if the attachment is downloaded. Our goal is to see how many hosts were infected with the file, hosts on which the file was executed after getting downloaded, and hosts on which the file was only downloaded. We'd like to get a detailed timeline of events regarding this email, too.

This will be done in a static site. In the site, we'll open Jeff Johnson's email - it allegedly has a payment for the month of April and steps to withdraw the payment:

![image](https://github.com/user-attachments/assets/c6d7caee-613d-4ebe-9e68-695e2e70e99e)

**[Task 6, Question 1] What was the name of the malicious email sender?** - Jeff Johnson

We know from the task and the static site (after downloading the attachment) that a malicious incident will take place by downloading the email attachment. We call the email attachment a _threat vector_, since this is how the malware was delivered to the system.

**[Task 6, Question 2] What was the threat vector?** - Email attachment

We'll be warned to take action once we download the file. Let's do so. We see that there are three hosts that have the file present on them:

![image](https://github.com/user-attachments/assets/4d003990-eeb0-4b31-8775-4286168d5f78)

**[Task 6, Question 3] How many devices downloaded the email attachment?** - 3

Note that the last file above has actually executed the file.

**[Task 6, Question 4] How many devices executed the file?** - 1

Let's go ahead and quarantine the devices that haven't executed the file - we can do so by clicking the Quarantine buttons. Then we'll need to click the Investigate button on the last host. We'll be prompted to isolate the host and then view the process timeline. We can view what has gone on, and then we can click Finish Case to get our flag:

![image](https://github.com/user-attachments/assets/7299eb4b-b02d-444a-80d8-d04b1fa0f6f4)

**[Task 6, Question 5] What is the flag found at the end of the exercise?** - `THM{My_First_Incident_Response}`

## [Task 7] Conclusion

Here we learned about various concept regarding incident response, including what an incident is, how to prioritize incidents, incident response frameworks, and tools for carrying out incident response.
