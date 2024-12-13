# Eviction

## [Task 1] Understand the Adversary

To conclude the section on Cyber Defense Frameworks, we'll use MITRE ATT&CK to analyze an adversary. We're in a corporation that manufactures rare earth metals, and we've been informed that APT28, an APT group, is attempting to attack organizations similar to ours. We'll need to figure out what this group's TTPs are so we can figure out if it has been doing any damage to our network. We're provided a link to the MITRE ATT&CK Navigator Layer for this group, so let's make use of it.

We can start by looking at the techniques employed by this group. Some techniques have overlap across multiple tactics; for example, Spearphishing Links are used to perform reconnaissance (phishing for information) _and_ for initial access.

![image](https://github.com/user-attachments/assets/f31eda52-7409-4fc9-94e9-3835db5efcf5)

**[Task 1, Question 1] What is a technique used by the APT to both perform recon and gain initial access?** - Spearphishing link

Now let's look at what APT28 does once they complete their reconnaissance phase. The next phase in an attack is Resource Development. We see that they set up and compromise domains, web services, email accounts, and tools for their attack:

![image](https://github.com/user-attachments/assets/1257eba7-8699-48ab-8c03-964616c2a56e)

**[Task 1, Question 2] Sunny identified that the APT might have moved forward from the recon phase. Which accounts might the APT compromise while developing resources?** - Email accounts

We just found out that the APT may have gained initial access via social engineering techniques. They attempted to make the user execute code for the threat actor. Our focus should now be on figuring out whether APT28 were actually successful in executing code. In this case, we need to find evidence of user execution techniques. APT28 employs these techniques:

![image](https://github.com/user-attachments/assets/12c9bb06-1d8d-429d-8ac9-a981d40e059d)

**[Task 1, Question 3] E-corp has found that the APT might have gained initial access using social engineering to make the user execute code for the threat actor. Sunny wants to identify if the APT was also successful in execution. What two techniques of user execution should Sunny look out for? (Answer format: <technique 1> and <technique 2>)** - Malicious file and malicious link

Assuming that they were able to pull off user execution, then we'll need to look for evidence of successful execution. This will be indicated by the usage of command/scripting interpreters. APT28 uses these:

![image](https://github.com/user-attachments/assets/26cba8ff-d4aa-49ef-9b9d-e46bc014ae55)

**[Task 1, Question 4] If the above technique was successful, which scripting interpreters should Sunny search for to identify successful execution? (Answer format: <technique 1> and <technique 2>)** - Powershell and Windows Command shell

It turns out that there were some obfuscated scripts after all, and upon further investigation it seems that they change the registry in some way. If the attacker is attempting to establish persistence, then based on the ATT&CK matrix for this group, they're likely attempting to set registry run keys:

![image](https://github.com/user-attachments/assets/caa8b72b-a2b2-4bbf-828c-fb1234220a7c)

**[Task 1, Question 5] While looking at the scripting interpreters identified in Q4, Sunny found some obfuscated scripts that changed the registry. Assuming these changes are for maintaining persistence, which registry keys should Sunny observe to track these changes?** - Registry run keys

We've also identified that something was executed on the machine to assist with defense evasion, and the next question specifically calls out proxy execution. Under Defense Evasion, APT28 does perform proxy execution with Rundll32:

![image](https://github.com/user-attachments/assets/6a3a3e8e-3ec6-40fa-ac5d-dd4b3f0afe88)

**[Task 1, Question 6] Sunny identified that the APT executes system binaries to evade defences. Which system binary's execution should Sunny scrutinize for proxy execution?** - Rundll32

Let's look at some other techniques used by AP28. Under Discovery, we have the following:
- File and Directory iscovery
- Network Sniffing
- Peripheral Device Discovery
- Process Discovery

We find that tcpdump has been placed on the system. We'll talk about it in a later room in this path, but here's a brief description of what it is:

![image](https://github.com/user-attachments/assets/e750e129-9520-446c-bbbe-7f29744a2272)

tcpdump facilitates _network traffic capture_. So it seems like this is the tool that the adversary uses to perform network sniffing.

**[Task 1, Question 7] Sunny identified tcpdump on one of the compromised hosts. Assuming this was placed there by the threat actor, which technique might the APT be using here for discovery?** - Network sniffing

Now let's examine the lateral movement techniques used by APT28. We're told that it uses remote services, and there just so happens to be a technique in the ATT&CK Framework. This is what is marked in the matrix for that technique:

![image](https://github.com/user-attachments/assets/b0c38534-000c-48ac-9472-34071879bb4b)

This group uses SMB and Windows Admin shares to move through the enivronment. It's worth keeping an eye out for this.

**[Task 1, Question 8] It looks like the APT achieved lateral movement by exploiting remote services. Which remote services should Sunny observe to identify APT activity traces?** - SMB/Windows Admin shares

One of the adversary's goals is to steal intellectual property off of information repositories. This will clearly involve the Collection tactic/category - the attackers are trying to collect data, after all - so we'll look there in the matrix. There's a technique for collecting data from information repositories, and it turns out that they like targeting SharePoint:

![image](https://github.com/user-attachments/assets/e971aa1d-ef5a-45da-aed2-20f9781a2cd5)

**[Task 1, Question 9] It looked like the primary goal of the APT was to steal intellectual property from E-corp's information repositories. Which information repository can be the likely target of the APT?** - Sharepoint

Once the APT collects the data, they then attempt to use a C2 for data exfiltration. However, they weren't able to reach out to that C2. The APT would probably try to get around this using some techniques like proxying. In the Command and Control tactic/category, we see that there is a technique for using proxies.

![image](https://github.com/user-attachments/assets/5de633be-7b55-44df-8981-2c7ef5566366)

They are likely to use an external proxy (using other victims as proxies for C2 traffic) or a multi-hop proxy (routing traffic over Tor and VPN servers to obfuscate C2 traffic). Knowing this, we're able to help thwart the APT and stop it from stealing the company's intellectual property!

**[Task 1, Question 10] Although the APT had collected the data, it could not connect to the C2 for data exfiltration. To thwart any attempts to do that, what types of proxy might the APT use? (Answer format: <technique 1> and <technique 2>)** - external proxy and multi-hop proxy
