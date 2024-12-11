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
