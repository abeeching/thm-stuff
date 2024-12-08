# Pyramid of Pain

## [Task 1] Introduction

This first section of the learning path is focused on cyber defense frameworks, i.e., the models we use to characterize cyber incidents, attacks, adversaries, and all that fun stuff. Our first framework will be the Pyramid of Pain.

This framework lists off several types of indicators, categorized by how easy it is for an adversary to change each type of indicator. This is being applied in solutions like Cisco Security, SentinelOne, and SOCRadar, and can help with cyber threat intelligence, threat hunting, and incident response. It's useful to know as a threat hunter, incident responder, or SOC analyst--if you're able to force an adversary to change one of the trickier indicators, then that may well discourage them from continuing a cyber attack against your organization.

## [Task 2] Hash Values (Trivial)

At the lowest level of the pyramid are hash values. A hash value is simply a value, of fixed length, that uniquely identifies data. Hashes are produced by hashing algorithms, most commonly:
- MD5 (Message Digest, see RFC 1321), which is an algorithm designed by Ron Rivest in 1992 and uses a 128-bit hash value. The problem is that these are actually not secure. In 2011, the Internet Engineering Task Force published RFC 6151, which outlines various attacks against MD5 hashes, including hash collision.
- SHA-1 (Secure Hash Algorithm 1, see RFC 3174), which was invented by the US National Security Agency (NSA) in 1995. SHA-1 produces a 160-bit hash value as a 40-digit hexadecimal number. It, too, has been deprecated in 2011. In fact, it was outright banned for digital signatures in 2013, since it was susceptible to brute-force. This now leads us to...
- SHA-2 (Secure Hash Algorithm 2), which was developed by the US National Institute of Standards and Technology (NIST) and the NSA in 2001 to replace SHA-1. SHA-2 has many variants, most commonly SHA-256. This returns a hash of 256 bits as a 64-digita hexadecimal number. SHA-3 has since been invented and is even stronger than SHA-2. NIST recommends the usage of these hashing algorithms.

As a reminder, hashes are not cryptographically secure if two files can have the same hash value or digest. This is what we mean by a hash collision, and we talk about hashing and issues with it in more detail in the Cyber Security 101 path.

Hash values can be handy in a pinch, since they can be used to gain easy insight into a specific malware sample, malicious/suspicious files, as well as malicious artifact references. Folks who do write-ups on ransomware and malware reports are likely to include hashes related to the malicious/suspicious files somewhere in the reports. The DFIR Report and FireEye Threat Research Blogs, in particular, include these.

Various tools are used to look up files based on hash values and investigate how malicious they are. Two of these tools are VirusTotal and MetaDefender Cloud - OPSWAT.

Again, these can be helpful in a pinch. If your system has a file that hashes to a known-malicious hash value, then congrats, you've found a malicious file. However, hashes have this property that, if you change even one bit of the data, the entire hash changes, with there being no evident pattern. This is good because it allows us to identify files uniquely, and this is bad because this allows attackers to easily change a hash value.

This task provides the following hash: `b8ef959a9176aef07fdca8705254a163b50b49a17217a4ff0107487f59d4a35d`. There's also a link to a document showing the VirusTotal results for this hash. This illustrates how helpful hashes can be for getting quick insight into how malicious a certain file is. VirusTotal gives us this information:

![image](https://github.com/user-attachments/assets/d0eb0a42-01f9-420f-84e3-5b89aa7b0114)

Here, we see the filename and its hash, as well as a notice that a good chunk of vendors and sandboxes found this file to be malicious.

**[Task 2, Question 1] Analyze the report associated with the hash `b8ef959a9176aef07fdca8705254a163b50b49a17217a4ff0107487f59d4a35d`... What is the filename of the sample?** - `Sales_Receipt 5606.xls`

## [Task 3] IP Address (Easy)

Next up are IP addresses. Chances are good that you understand the importance of these - these identify devices connected to a network. IP addresses are used to send and receive information over a network. If you need some more background context, there are introductory rooms on networking in TryHackMe that you should review.

IP addresses can be quite valuable for defense. As we saw in the previous room, a common procedure for dealing with malicious IP addresses is to block them (or otherwise drop/deny them) on a firewall. This isn't foolproof though--there's nothing stopping an experienced adversary to get a new public IP address.

(Just as a heads-up: If you're seeing IP addresses in, well, any TryHackMe room...chances are good that you don't want to interact with them unless you're working with the TryHackMe virtual machines. They're probably malicious in some way. You should not attempt to interact with IPs unless you are told to do so in a safe environment.)

Fast Flux is a technique that adversaries use to thwart IP blocking attempts. In short, Fast Flux can be used to hide malicious activities behind compromised hosts as proxies, with the goal being to make the command and control server (C2, C&C) tough to find by security professionals. If an attacker can just have a bunch of IP addresses associated with a domain name that changes, then it's pretty clear that being forced to change IP addresses is no big deal for them.

For this task, we'll be looking at a report generated from `any.run`, a sandbox application that allows a user to interact with and analyze malware in a safe way. The file in question is `some_malicious_file.bin`, which is noted to be an instance of a particularly dangerous piece of ransomware. The report is quite long, but we'll eventually see the Network Activity section, which tells us the IP addresses and domains that this file tried to communicate with.

![image](https://github.com/user-attachments/assets/6d8776ae-3b8a-4d30-91d1-0a04be3698a0)

**[Task 3, Question 1] Read the following report to answer this question. What is the first IP address the malicious process (PID 1632) attempts to communicate with?** - `50.87.136.52`

**[Task 3, Question 2] Read the following report to answer this question. What is the first domain name the malicious process (PID 1632) attempts to communicate with?** - `craftingalegacy.com`

## [Task 4] Domain Names (Simple)

We're approaching the end of the "easy-to-change" indicators; the last one that isn't too bad to change is domain names. Having some understanding of DNS helps for this task, and you can catch up on it via TryHackMe's rooms, though here's a quick summary of the most important information. Domain names effectively map IP addresses to strings of text. Domain names may contain a domain and a top-level domain, e.g. `example.com` has the top-level domain `.com`. You may also have domains with a subdomain in front, e.g. `subdomain.example.com`.

In theory, domain names should be tricky to change. To have a domain name to begin with, one must purchase the domain, register it, and modify DNS records. However, many DNS providers have loose standards, and some provide APIs that make it easier for an attacker to simply change domains.

For a while there was a particularly nasty attack in play that would redirect users to a malicious domain, even though it seems legitimate. We call it the Punycode attack. Instead of having a link go to the `adidas` website, there might be a link that goes to `adıdas` - notice the "i"!

Punycode allows one to convert words that cannot be written in ASCII into Unicode ASCII encoding. This effectively means that the fake Adidas website might actually have a domain of `xn--addas-o4a`. Nowadays, browsers are pretty good at translating obfuscated characters into the full Punycode domain name. It's worth checking proxy or web server logs for malicious domains like this.

Attackers will also hide malicious domains with URL shorteners. These simply create short, unique URLs that redirect to a specified website. URL shorteners include TinyURL, `bit.ly`, `goo.gl`, among many others. Fortunately, there's an easy way to see where a website will send you--simply add a `+` to the end of the URL. This allows you to get a preview of where the link will send you.

Again, we'll be using `any.run` to answer certain questions about a specific malware sample. This sandbox, among other things, includes information on HTTP requests made, DNS requests made, and processes communicating with a given IP address. This information is located in the "networking" tab below the machine snapshot.
- The HTTP Requests tab shows any HTTP requests made after the sample was detonated, and can be used to see if there are any resources getting retrieved from a server (such as droppers and callbacks).
- The Connections tab shows any communications made since the detonation of the sample, helping you see if a process is communicating with another host (e.g., C2 traffic, FTP transfer)
- The DNS Requests tab shows any DNS requests made since the sample was detonated. DNS requests are often made by malware to check for internet connectivity. If malware can't reach the Internet, then it may not be able to function. Additionally, some malware can use this to recognize that they are in a sandbox.

The task gives you a link to Any.Run's analysis of the `some_malicious_file.bin` file we saw before. We'll want to check it out and look at the DNS Requests. We see a familiar domain from the previous task:

![image](https://github.com/user-attachments/assets/30c7545e-4393-47b3-b662-c89564fb29c2)

**[Task 4, Question 1] Go to this report on `app.any.run` and provide the first suspicious domain request you are seeing, you will be using this report to answer the remaining questions of the task.** - `craftingalegacy.com`

As it happens, we're not really going to be using the Any.run sandbox for the rest of this task.

**[Task 4, Question 2] What term refers to an address used to access websites?** - Domain Name

**[Task 4, Question 3] What type of attack uses Unicode characters in the domain name to imitate a known domain?** - Punycode attack

For this last question, we'll need to preview a TinyURL link. We could also...just go to it. But that's not necessarily good practice. You generally shouldn't just click on links like these randomly. Adding a `+` sign to the end of the link and going to it yields

![image](https://github.com/user-attachments/assets/39f993c8-df8e-4d92-bb8a-03f187e23a78)

**[Task 4, Question 4] Provide the redirected website for the shortened URL using a preview: `https://tinyurl.com/bw7t8p4u` - `https://tryhackme.com/`

## [Task 5] Host Artifacts (Annoying)

Now things start to become a nuisance for an attacker. If we're able to detect these things, then the attacker would have to make some significant changes to their tools and methods, which can be time-consuming and require more resources. Host artifacts are simply traces or observables that attackers leave on the system. This can include registry values, suspicious process execution, attack patterns/indicators of compromise (IOCs), dropped files, or anything related to the threat.

Some examples:
- Microsoft Word launches a process that it shouldn't need to.
- After opening a malicious application, you might see a lot of suspicious files being written and connections being made.
- Files that are suddenly dropped or modified by a malicious actor.

As before, we'll look at a report for a malicious Word document. This document leads to an extremely dangerous Trojan horse being planted on the computer. We can investigate the network activity and see what it tries to communicate with.

![image](https://github.com/user-attachments/assets/15e279e6-8820-4ef1-9ebc-88650ec777fe)

There's a lot of connections being made by `regidle.exe`, and we can see a few POST requests.

**[Task 5, Question 1] A process named `regidle.exe` makes a POST request to an IP address based in the United States (US) on port 8080. What is the IP address?** - `96.126.101.6`

Further down in the report, we can see the malware's behavior. Any.run specifically calls these out as being malicious:

![image](https://github.com/user-attachments/assets/9b442066-2d21-4023-9d0f-f35a0d8f63a8)

We're looking at the file being dropped immediately after the sample is detonated.

**[Task 5, Question 2] The actor drops a malicious executable (EXE). What is the name of this executable?** - `G_jugk.exe`

We'll look at one more report - this time, it comes from VirusTotal.

![image](https://github.com/user-attachments/assets/db83c925-ebb9-4b06-b215-1e7c02533b69)

This provides detection information about a given IP address.

**[Task 5, Question 3] Look at this report by VirusTotal. How many vendors determine this host to be malicious?** - 9

## [Task 6] Network Artifacts (Annoying)

Network artifacts, which can be user-agent strings, C2 information, or URI patterns followed by HTTP POST requests, are considered to be annoying as well. If you're able to detect and respond to a threat at this level, then the attacker would need to go back and change tactics/modify tools, which gives defenders more time to respond and detect upcoming threats or remediate existing ones.

A suspicious user-agent string may be one that isn't normally seen in your environment, or seems out of the ordinary. See RFC 2616 for more information on user-agent strings. If you're able to find a custom User-Agent string used by an attacker, you may be able to block them to make compromising the network more difficult.

Network artifacts can be detected by analyzing PCAPs with Wireshark, TShark, or with IDS logs (e.g., Snort). We'll cover these tools in the Network Traffic Analysis section of this path.

HTTP POST requests may contain suspicious strings, i.e., gibberish.

This screenshot contains user-agent strings commonly associated with the Emotet Downloader Trojan.

![image](https://github.com/user-attachments/assets/d3e0623f-a867-4aea-b322-669f36aae2d4)

Researching this User-agent string on Google tells us that we're dealing with some Internet Explorer version used on Windows 7.

![image](https://github.com/user-attachments/assets/d6caa37f-1f15-44e2-afd1-26a4eb3bcc52)

**[Task 6, Question 1] What browser uses the User-Agent string shown in the screenshot above?** - Internet Explorer

And here is a screenshot from Wireshark containing suspicious strings in HTTP POST requests.

![image](https://github.com/user-attachments/assets/4bcc0018-5d8b-4ccf-b089-4fa8a2518ef6)

**[Task 6, Question 2] How many POST requests are in the screenshot from the pcap file?** - 6

## [Task 7] Tools (Challenging)

Now we move into indicators that are challenging for adversaries to change if detected. If we're able to block an attack based on the _tools_ in use, then this forces an attacker to do one of two things: they either have to use a new tool that serves the same purpose, or just give up attacking the network. Using a new tool isn't as easy as you might think; an attacker might have to create a whole new tool (requiring money and time), find a tool with the same potential, or get training on how to be proficient with a tool.

Attackers may use these toools to create malicious documents for phishing, backdoors for C2 infrastructure, custom EXE and DLL files that do the work they need to do, payloads, or password crackers.

If you're attempting to block an attacker at this level, using antivirus signatures in conjunction with detection and YARA rules can help out. If you need access to samples, malicious feeds, and YARA results, MalwareBazaar and Malshare are both good places to start looking. Detection rules can be found in the SOC Prime Threat Detection Marketplace - here, security professionals share detection rules for different kind of threats, including recent CVEs being exploited.

Another good weapon is fuzzy hashing. This lets you perform similarity analysis, where you can match two files even if they have minor differences between them. You can check SSDeep for fuzzy hashing information, and VirusTotal provides information from them for given malware samples.

**[Task 7, Question 1] Provide the method used to determine similarity between the files.** - Fuzzy Hashing

The information for the next question can be found by checking out the SSDeep Project's website:

![image](https://github.com/user-attachments/assets/1dd02d5e-6d92-448e-b3d8-53c033f2023b)

**[Task 7, Question 2] Provide the alternative name for fuzzy hashes without the abbreviation** - context triggered piecewise hashes

## [Task 8] TTPs (Tough)

The top of the pyramid is perhaps the most brutal thing an attacker has to change if detected - their tactics, techniques, and procedures (TTPs). MITRE ATT&CK - something we'll discuss later - has all the steps taken by an adversary to achieve their goals, starting from phishing attempts to persistence and exfiltration.

Being able to detect TTPs in use and responding to them gives adversaries little change to fight back. An attacker, say, might use a pass-the-hash attack to move laterally throughout a network. If you can detect this attack in progress by checking event logs, you can remediate it.

This, again, forces the attacker to choose between one of two options. They either need to do more research and training and perhaps reconfigure their own custom tools, or they will need to give up and choose another target. At this point, the time and resources expended on continuing an attack will likely be too much for what they want to do, and so an attacker will be more likely to change targets.

It's a good exercise to check out the ATT&CK matrix now, so you can see what techniques an attacker might be using to carry out an attack. The matrix splits up the techniques into different, broad categories. Here's the section of the matrix for exfiltration, for instance:

![image](https://github.com/user-attachments/assets/ad935fb3-977f-40d1-bc5a-b2942c80b5b2)

**[Task 8, Question 1] Navigate to ATT&CK Matrix webpage. How many techniques fall under the Exfiltration category?** - 9

MITRE also keeps track of advanced persistent threat (APT) groups, which is what the next question will be referencing. You can search for an APT by using the search bar on the page. Here's what it looks like if we look up the Chimera APT:

![image](https://github.com/user-attachments/assets/62f881fd-ce49-4d32-9134-3626b0072499)

It's a good exercise to investigate an APT via MITRE (or perhaps the FireEye APT Groups website, and others). Then you can start thinking about how you can detect the attacker with their TTPs - what rules you can create and what approaches you can take in detecting them.

On their page, among other things, they list out the software that this group typically uses. You can click on the software name to get more information about it from MITRE. One of the programs that this group uses is Cobalt Strike, which is described as follows:

![image](https://github.com/user-attachments/assets/621fd1f7-773f-4138-b266-190b4d347ba9)

**[Task 8, Question 2] Chimera is a China-based hacking group that has been active since 2018. What is the name of the commercial, remote access tool they use for C2 beacons and data exfiltration?** - Cobalt Strike

## [Task 9] Practical: The Pyramid of Pain

And for our final task, we need to assemble the Pyramid of Pain. Here, we have a series of descriptions, and we need to match them up with the tiers on the Pyramid of Pain. In brief:
- Hash Values: Remember that, while they are the easiest for an attacker to change, they allow us to find malicious files. We're able to attribute these to certain threat actors.
- IP Addresses: These can help us figure out what an adversary might be trying to communicate on, including all parts of their infrastructure.
- Domain Names: These typically need to be purchased by attackers, but this is easier to do than you might think. Domain names can be leveraged in attacks on their own, such as with Punycode and typosquatting (e.g., making a small change in a legitimate domain name, hoping that people will type the legitimate domain incorrectly).
- Network (and host) Artifacts: These are things that you can find either in the network traffic or on the host machine that indicate an attack is in progress. This may include dropped files, evidence of C2 communication, and suspicious processes.
- Tools: These are used by attackers to carry out whatever their goals are. As an example: If an attacker wants to perform data exfiltration, they may use Cobalt Strike as discussed in the previous task.
- TTPs: These represent the attacker's plans, objectives, and procedures they take to carry out those plans and accomplish those objectives. These can include the tools they use, but at this level of the pyramid we're looking at what an attacker is trying to do holistically.

To complete the task, you click one of the descriptors, and then click the corresponding level on the Pyramid of Pain below. Doing this correctly yields the flag.

**[Task 9, Question 1] Complete the static site. What is the flag?** - THM{PYRAMIDS_COMPLETE}
