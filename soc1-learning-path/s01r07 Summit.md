# Summit

## [Task 1] Challenge

For our first practical room in this path, we're going to be looking at the Pyramid of Pain. Our organization wants to conduct a threat simulation and detection engineering engagement to hepl wiht malware detection, and we've been hired on as an external pentester in a purple-team scenario. The tester will attempt to run malware samples on a simulated internal user's workstation, and we'll need to configure the organization's security tools so we can stop the malware from executing.

To do this, we'll climb the Pyramid of Pain, detecting things that make it increasingly harder for adversaries to operate. The machine attached to this task will allow you to access a webpage where the work will be done.

When you launch the webpage, you will be greeted with an email from the pentester. The general process for this challenge will be (1) scan the sample provided and (2) find a way to block the file.

Let's investigate `sample1.exe`. Here's what we find:

![image](https://github.com/user-attachments/assets/b61ef9a1-7ddf-4547-b9aa-b6d0d6205d92)

A good starting point would be to try to block the file based on hashes. Luckily, you can add hashes to a blocklist in PicoSecure by going to the menu and clicking Manage Hashes.

![image](https://github.com/user-attachments/assets/c408aa62-e2e8-4b4e-aa85-a53e462ace7d)

You can choose any one of the three hash types, and paste it in. When you do so, the pentester will send you a new email that includes the flag for this question.

**[Task 1, Question 1] What is the first flag you receive after successfully detecting `sample1.exe`?** - `THM{f3cbf08151a11a6a331db9c6cf5f4fe4}`

Unfortunately, our pentester friend reminds us that while hashes can be used to uniquely identify a file, it doesn't take much to change the hash. Changing one bit will significantly alter the hash, so we'll need to do a little more legwork. The pentester sent us `sample2.exe`, so let's scan it and see what we can find. Doing so gives us similar information as last time, but now we also have network activity info:

![image](https://github.com/user-attachments/assets/e4d6d8cb-1d1b-4045-95fd-03d18df82420)

Let's try to create a detection rule based on that. We can use the Firewall Manager in PicoSecure for this purpose. A lot of the malicious communication appears to take place with the IP `154.35.10.113` - communication over this address involves the standard port for Metasploit communication, after all. Let's block it. We can go ahead and block egress communication to this IP from any IP on our network:

![image](https://github.com/user-attachments/assets/ed938e1b-560e-4796-8ee1-129471af8fdf)

This is enough to block the second sample and obtain our second flag.

**[Task 1, Question 2] What is the second flag you receive after successfully detecting `sample2.exe`?** - `THM{2ff48a3421a938b388418be273f4806d}`

Sphinx tells us that it's pretty easy to set up a new IP address and get around the block - they've gone ahead and set up access with a cloud provider, so now they have many public IPs. We'll need to try a different approach to block sample 3.

Analyzing sample 3 gives us basic information and network activity, as before. But now we have some more detailed information about the connections that this sample makes:

![image](https://github.com/user-attachments/assets/1aadd44c-1df2-4f4d-8834-f3a7e9ced6f8)

Turns out that the malicious communication is occurring with that `bresonicz` domain. A backdoor is presumably being downloaded from this domain, and there's clearly some other communication happening with it. Let's block the domain this time by going to the DNS Filter. Just so that we're comprehensive, we'll go ahead and block the entire domain, not just the `emudyn` subdomain:

![image](https://github.com/user-attachments/assets/608f906b-7de6-4d0a-ba7a-54a78526e62c)

This yields the next flag.

**[Task 1, Question 3] What is the third flag you receive after successfully detecting `sample3.exe`?** - `THM{4eca9e2f61a19ecd5df34c788e7dce16}`

Now Sphinx is getting a little annoyed. It's quite easy to get new domains, sure, but it also takes a little bit of time to register them and set up new records and all that. However, many attackers would continue their operations in spite of this, and Sphinx is no different. With sample 4, we'll need to investigate what artifacts/changes are made on the system. Here's what our scanner picked up on:

![image](https://github.com/user-attachments/assets/e9a41e4a-52d3-4c81-a63d-a31648229fdc)

We see that the sample makes a change to `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection` - namely, it sets the `DisableRealtimeMonitoring` value to 1, meaning real-time protection is turned off. From here on, we'll want to look at the Sigma Rule Builder.

We'll talk more about Sigma rules later, but these are detection rules that can be matched against certain behaviors and activities. In this case, we have a registry key that's being edited, so let's look at Sysmon Event Logs:

![image](https://github.com/user-attachments/assets/92aa00ca-1028-46b9-ae5b-d60da6ef17e6)

Then we are prompted to choose a Sysmon event to target. In this case, we'll want to look at Registry Modifications.

![image](https://github.com/user-attachments/assets/80213b39-8efe-420b-9f8d-cca8a2648968)

Now we'll want to set up the rule. We're told to enter information about the registry key being changed. The key is `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection`. The Registry Name is `DisableRealtimeMonitoring` and the value is 1. For the ATT&CK ID, we'll need to investigate the ATT&CK matrix. Looking around, the Defense Evasion tactic includes the _Impair Defenses_ technique, which is what's going on here.

![image](https://github.com/user-attachments/assets/c2123c6f-8730-4f9e-8eb4-755eb44a59b3)

Hence, our Sigma Rule will look like this:

![image](https://github.com/user-attachments/assets/6a01bb6d-f3cf-4ece-beb7-d00d5c599ced)

Doing this yields our next flag.

**[Task 1, Question 4] What is the fourth flag you receive after successfully detecting `sample4.exe`?** - `THM{c956f455fc076aea829799c0876ee399}`

Now Sphinx is mad. We've forced the attacker to reconfigure their tools and methods. Now they had to spend time and effort coming up with new ways to continue their operations. Fortunately (for them) they have a good budget, so they can keep going, but at around this point some attackers would give up.

A new sample is available for us to investigate, so let's do so. Apparently, the attacker has some server-side stuff that allows them to change artifacts on the host machine, so we'll need to block based on behavior in a different way.

We see, in the HTTP requests, that the sample reaches out to an IP address to get a beacon file. That beacon file then communicates with what seems to be a malicious domain:

![image](https://github.com/user-attachments/assets/53068e90-816a-4309-9c9c-6dff8c6823ae)

Sphinx was also kind enough to attach a log of outgoing connections, and there's a lot of connections that communicate with the malicious IP address - `51.102.10.19`. Every time the communication happens, 97 bytes are transferred. This might be something we can block:

![image](https://github.com/user-attachments/assets/9e1bcd49-1c2d-4b52-8865-e3d7dd256459)

We'll create a new Sigma rule for this one. We'll create a rule based on Sysmon Event Logs, but this time, we'll target events relating to network connections. We are then prompted to enter information about the kind of connection we want to block.
- The remote IP that seems malicious is `51.102.10.19`. However, remember that the attacker can change IPs pretty easily. So we'll actually want to detect _any_ IP address instead.
- All communications with this IP seem to take place over port 443, but the attacker can change this as needed. To be comprehensive, we'll also block _any_ remote port. The rest of our rule will be specific enough that it picks up on just the malicious traffic.
- We saw that 97 bytes were transferred each time a request was made, so we'll note this in the Size field.
- The frequency of the communications is every 30 minutes, which is 1800 seconds.
- Everything we see here is characteristic of a command-and-control server, so we'll note that as the ATT&CK ID.

![image](https://github.com/user-attachments/assets/4145f2e7-e712-4f65-9efd-d5c8e3d5449a)

This yields our next flag.

**[Task 1, Question 5] What is the fifth flag you receive after successfully detecting `sample5.exe`?** - `THM{46b21c4410e47dc5729ceadef0fc722e}`

It appears that Sphinx is at their wits' end. We've now forced them to change the tool that they use to carry out the attack. In this case, they had to create a new tool and learn how to use it, and this uses up their resources significantly. Our goal now is to change the attacker's techniques and procedures.

Let's look at the analysis for sample 6. Interestingly, the analysis contains a new section about files:

![image](https://github.com/user-attachments/assets/ad43b353-69e2-4bbc-ab7c-cc14c8fffcd8)

What's up with that? Sphinx shared a log of commands that they used with previous samples, so let's see what's inside.

![image](https://github.com/user-attachments/assets/e7b33e0a-8348-4ddf-a395-ca2f48a52ffe)

Turns out that whatever tool he's using creates a log of data to exfiltrate - `exfiltr8.log`. Let's try to set up a rule based on this. Going into the Sigma Rule Builder, we can create a new rule based on Sysmon Event Logs. This time, we'll target events pertaining to File Creation or Modification.

We know that the exfiltration file is created in the `%temp%` directory, so that will be the file path. The file itself is called `exfiltr8.log`, and based on the commands and file name, we can say that the attacker is employing the Exfiltration tactic.

![image](https://github.com/user-attachments/assets/4c20aa7d-abab-4598-9619-348a680cb5ef)

This is enough for Sphinx to give up attacking the system. We got to the top of the Pyramid of Pain! Doing all of this yields the final flag:

**[Task 1, Question 6] What is the final flag you receive from Sphinx?** - `THM{c8951b2ad24bbcbac60c16cf2c83d92c}`
