# Threat Intelligence Tools

## [Task 1] Room Outline

Now that we have a good idea of what cyber threat intelligence is, we can now start looking at the various tools that enable us to collect and share it. In this room, we'll be looking at open-source tools that allow us to scan for malicious URLs, track malware and botnet indicators, investigate phishing emails, and perform general intel gathering.

## [Task 2] Threat Intelligence

Recap: Threat intelligence involves analyzing data and information. We use tools and techniques to identify meaningful patterns, and by doing so we learn how to mitigate potential risks associated with existing or emerging threats against organizations, industries, sectors, and government. At the very least, we attempt to answer the following:
- Who's attacking?
- What's their motivation?
- What are their capabilities?
- What should we be looking out for? (artifacts, indicators of compromise)

Threat intel can be categorized as one of the following:
- Strategic: analysis of organization's threat landscape & identification of risk areas based on trends, patterns, and emerging threats
- Technical: analysis of artifacts from an adversary, which can be used as a baseline for incident response and defensive security
- Tactical: analysis of adversary TTPs, allowing us to strengthen security controls and address vulnerabilities as needed
- Operational: analysis of adversary's intent and motives to perform an attack & identification of assets at greatest risk

## [Task 3] Urlscan.io

Urlscan.io was developed to assist with scanning and analyzing websites. It automates the browsing/crawling process. When a URL is submitted to this site, information such as the domains and IP addresses contacted, requested resources, snapshots, technologies used, and other metadata are recorded. The website has two views: one showing the most recent scans performed and one showing current live scans.

The scan results for a webpage include
- A summary of general information for the URL, including identified IPs, domain registration, page history, and screenshots.
- Information on HTTP connections made by the scanner to the site, with details about the data fetched & file types received.
- Information on HTTP and client-side redirects on the site
- Links outgoing from the site's homepage
- Details of variables and cookies found on the site, which can be used for identifying frameworks used
- Lists of IPs, domains, and hashes associated with the site; these are simply indicators and don't imply malicious activity

Note that this data can change pretty frequently depending on when you perform the scan.

Let's say we performed a scan on TryHackMe's website with this tool. We use the screenshot below to answer the questions.

![image](https://github.com/user-attachments/assets/d045cd77-5e26-406b-952f-1ad94e925e71)

The Summary section contains information about TryHackMe's Cisco Umbrella rank. Cisco Umbrella performs domain ranking based on popularity.

**[Task 3, Question 1] What was TryHackMe's Cisco Umbrella Rank based on the screenshot?** - 345612

The domains identified can be found in the Summary as well.

**[Task 3, Question 2] How many domains did UrlScan.io identify on the screenshot?** - 13

The main domain registrar can be found by looking at the Summary, under the Live Information section.

**[Task 3, Question 3] What was the main domain registrar listed on the screenshot?** - NAMECHEAP INC

And the main IP address can be found in the Summary information, once again.

**[Task 3, Question 4] What was the main IP address identified for TryHackMe on the screenshot?** - `2606:4700:10::ac43:1b0a`

## [Task 4] Abuse.ch

This is actually a suite of tools developed by the Institute for Cybersecurity and Engineering at Switzerland's Bern University of Applied Sciences. The goal of this suite is to track malware and botnets through various platforms, including
- Malware Bazaar, which is a resource for sharing malware samples
- Feodo Tracker, which allows you to track C2 infrastructure linked with Emotet, Dridex, and TrickBot
- SSL Blacklist, which provides a blocklist for malicious SSL certificates and JA3/JA3S fingerprints
- URL Haus, which shares malware distribution sites
- Threat Fox, which allows for the sharing of indicators of compromise (IOCs)

MalwareBazaar provides an all-in-one malware collection and analysis database. You can upload malware samples for analysis and to build the intelligence database, and you can do this directly in the browser or by using an API. You can also hunt for malware samples - just set up alerts to match tags, signatures, YARA rules, ClamAV signatures, and vendor detection.

FeodoTracker shares intelligence on botnet C2 servers associated with Dridex, Emotet (Heodo), TrickBot, QakBot, and BazarLoader/BazarBackdoor. The C2 servers in use are listed here, and analysts can look through this list to see if there are any suspicious IP addresses that have been contacted on a network. You can also get IP and IOC blocklists here, as well as some information on how to mitigate/prevent botnet infections.

SSL Blacklist lets you identify and detect malicious SSL connections, typically those associated with botnet C2 servers. This is put on a denylist that you can grab for your own use. The denylist can also be used to identify JA3 fingerprints to help detect and block malware botnet C2 connections at the TCP layer.

URLhaus shares malicious URLs used for malware distribution. You can search through the database for domains, URLs, hashes, and filetypes that are suspected to be malicious, and you can validate your investigations with the information provided here. The tool also provides feeds associated with countries, AS numbers, and Top-Level Domains (TLDs) that an analyst can generate.

Lastly, ThreatFox allows analysts to search for, share, and export IOCs associated with malware. These can be exported into different formats, such as MISP events (more on this later), Suricata IDS rules, domain host files, DNS response policy zones, JSON files, and CSV files.

Let's take a look at the tools provided above. For our first question, we'll need to go to ThreatFox and investigate an IOC - in this case, an IP address. On the website, you can go to the Browse IOCs link at the top, and then you can search for the IP address in the search bar. The syntax is `ioc:212.192.246.30:5555`.

![image](https://github.com/user-attachments/assets/1d03afa4-e23e-4651-a674-909f3670c456)

Clicking on the IOC pulls up its database information. Here, we can see what malware alias it's associated with:

![image](https://github.com/user-attachments/assets/555aa956-43b0-4a07-845b-e0add6a01241)

**[Task 4, Question 1] The IOC `212.192.246.30:5555` is identified under which malware alias name on ThreatFox?** - Katana

Next, we'll need to take a look at the SSL Blacklist and check out the JA3 fingerprint provided. On the website, go to the JA3 Fingerprints link at the top, and then search for the JA3 fingerprint provided in the question.

![image](https://github.com/user-attachments/assets/9e0fb63e-32a5-459d-8937-386d6f20244c)

We can see that the fingerprint is listed because it is associated with Dridex, which is malware.

**[Task 4, Question 2] Which malware is associated with the JA3 Fingerprint `51c64c77e60f3980eea90869b68c58a8` on SSL Blacklist?** - Dridex

Now let's look at URLhaus. We're looking for a malware-hosting network with the ASN number `AS14061`. Go to the website, and then click Access Data > Statistics at the top. This will take you to a page where you can see information about URLs detected and malware sharing networks. You can press CTRL+F and look for `AS14061`:

![image](https://github.com/user-attachments/assets/ed1455cd-bed3-4dc2-8c8a-6d0cd8a3e7f4)

**[Task 4, Question 3] From the statistics page on URLHaus, what malware-hosting network has the ASN number AS14061?** - DIGITALOCEAN-ASN

Lastly, we'll look at FeodoTracker. This time we want to see what country and IP address is associated with. Go to the Browse link at the top, and then search for the IP address in the search box. Then click the search result to see the database information for the IP:

![image](https://github.com/user-attachments/assets/ce3e9615-8474-46e3-be51-958f02a106e9)

The country is GE, which is short for Georgia.

**[Task 4, Question 4] Which country is the botnet IP address `178.134.47.166` associated with according to FeodoTracker?** - Georgia

## [Task 5] PhishTool

Now let's check out PhishTool. This is a tool that we can use for email analysis. While it is not required to complete the task, knowing about the email analysis process is quite helpful. Later in this path, there will be an entire section/module dedicated to analyzing phishing emails.

Here's a brief introduction: email phishing is a very common way for a cyber attack to initiate. An unsuspecting user opens a malicious file or link, and all of a sudden, they get hit with malware, their credentials and personal data get shared out, and all sorts of nasty things happen. Email security is absolutely critical.

PhishTool allows for responsive email security via analysis of email IOCs, among other things. You can use the Community version of the tool by signing up with an account; there is also an Enterprise option. The core features of PhishTool include
- Email analysis: PhishTool gets metadata from phishing emails, and provides analysts information about the emails and metadata that they can use for triage.
- Heuristic intelligence: OSINT (open-source intelligence) is provided within the tool itself, allowing analysts to understand what TTPs were used to evade security controls, as well as how the adversary attempted to social engineer the target.
- Classification, reporting: Phishing email classifications are performed to let analysts take action quickly, and reports may be generated to provide a forensic record that can be shared.

In the Enterprise version, you can manage user-reported phishing events, report phishing email findings back to users and keep them involved in the process, and integrate with Microsoft 365 and Google Workspace.

When you log into PhishTool, you can submit an email for analysis in any of the given file formats. You can also look at a history of the submissions you've made and how they were resolved. In the Enterprise version, you can also access the In-tray tab, which lets you receive and process phishing reports posted by team members.

In the Analysis tab, you can get more details about a given email, including
- Headers: Routing information for the email, source/destination emails, originating IPs/DNS addresses, timestamps, etc.
- Received Lines: Details on the email traversal process across various SMTP servers
- X-headers: Extension headers added by the recipient mailbox to provide more information on the email
- Security: Details on security policy frameworks, like Sender Policy Framework/SPF, DomainKeys Identified Mail/DKIM, and Domain-based Message Authentication, Reporting, and Conformance/DMARC.
- Attachments: List of attachments found in the email
- Message URLs: List of external URLs associated with the email

In this tab, you can perform further lookups and mark indicators as suspicious. You can also see the plaintext and source details of the email. Once we're done investigating an email, we can click the Resolve button, which allows us to classify the email, set the flagged artifacts, and set classification codes. This information will be put into the Resolution tab in the Analysis page.

We can perform some basic email analysis without needing to use this tool, which is what we'll do for the questions in this task. It's definitely good to know that PhishTool exists, though!

When we start the VM in this task, we will see an emails folder on the desktop. Clicking on this leads us to a few email files that we'll be investigating in this room, and we're concerned with `Email1.eml` at the moment. You can double-click it, or right-click and click Open in Thunderbird Mail. This will open up the email in Thunderbird. You do not need to create an account to complete the task.

Based on the content of the email, it looks like it's trying to impersonate LinkedIn:

![image](https://github.com/user-attachments/assets/286f4125-a43a-4fb7-823d-bfd638c49f04)

**[Task 5, Question 1] What social media platform is the attacker trying to pose as in the email?** - LinkedIn

Checking the information at the top, we can see who allegedly sent the email, and who received it:

![image](https://github.com/user-attachments/assets/f0607e56-a7a5-4121-8d17-55b6a71ef7fb)

**[Task 5, Question 2] What is the sender's email address?** - `darkabutla@sc500.whpservers.com`

**[Task 5, Question 3] What is the recipient's email address?** - `cabbagecare@hotsmail.com`

To investigate the message in more detail, we can click More in the top-right, and then click View Source in the drop-down menu that appears. Inevstigating, we can get more information about where the email came from - check the Received header, for example:

![image](https://github.com/user-attachments/assets/1efd8759-7947-440c-9a5b-a21a98502aff)

This tells us what IP address the email originated from. It's typically a good practice to _defang_ any IP addresses and URLs you spot in investigations like this; all you do when you defang an address is make it impossible to accidentally click. You can use a tool like CyberChef to defang IP addresses and URLs, but the typical procedure for defanging an IP address is to enclose any periods in brackets (`[.]`).

**[Task 5, Question 4] What is the Originating IP address? Defang the IP address.** - `204[.]93[.]183[.]11`

To identify the number of hops the email had to go throuhg to get to the recipient, you need to look at how many times the email was received in the source. In this case, we have four Received headers at the top of the email:

![image](https://github.com/user-attachments/assets/f2ae61cd-c16b-4be2-a5e4-34e6de7e1e16)

Thus it must have hopped four times.

**[Task 5, Question 5] How many hops did the email go through to get to the recipient?** - 4

## [Task 6] Cisco Talos Intelligence

Cisco, recognizing the importance of collecting information for threat analysis and intelligence, created a team of security professionals known as Cisco Talos. This team provides actionable intelligence, visibility on indicators, and protection agianst emerging threats through data collected from their products. You can access the solution online.

There are six key teams within Cisco Talos:
- Threat Intelligence and Interdiction: Correlation, tracking of threats to turn IOCs into context-based intelligence
- Detection Research: Vulnerability and malware analysis for creating rules/content for threat detection solutions
- Engineering and Development: Maintenance support for the inspection engines, keeping them up to date to identify and triage emerging threats
- Vulnerability Research and Discovery: Working with software and service vendors to develop means of identifying and reporting vulnerabilities
- Communities: Maintaining the image of the team and open-source solutions
- Global Outreach: Dissemination of intelligence to customers and the security community at large

(Some information here is a little outdated with respect to the website's structure, however you can still find a lot of this information by poking around.)

When you access the Talos website, you'll see a reputation lookup dashboard with a world map, showing an overview of email traffic with indicators of whether the emails are legitimate, spam, or malware. If you click on a marker, yuo get more information associated with IPs and hostname addresses, volume of traffic, and the type of traffic.

There are more tabs that give you different intelligence resources, including
- Vulnerability Information: Disclosed and zero-day vulnerability reports marked with CVE numbers and CVSS scores. Details are provided when you select a specific report, including the timeline taken to get the report published. You'll also see advisories from Microsoft, with applicable Snort rules.
- Reputation Center: You can access searchable threat data related to IPs and files (via SHA256 hashes). Analysts rely on these options to conduct investigations. Additional data can be found under the Email & Spam Data tab.

Our task now is to take the email from the previous task and analyze it in Cisco Talos. The VM doesn't have access to the Internet, so we can just work on this in our usual browser.

First, we have an IP address - `204[.]93[.]183[.]11`. Let's see what we can find out about this address. Searching for it in Talos Intelligence yields the following information:

![image](https://github.com/user-attachments/assets/4a2f79fa-be09-4383-b562-2dc04c45c5c3)

Among other things, we can see information about the domain associated with the IP address.

**[Task 6, Question 1] What is the listed domain of the IP address from the previous task?** - `scnet.net`

Now, the IP address doesn't give us any further information about the customer on Talos Intelligence, so let's check the WHOIS records for the address. Inputting the IP address and looking through the records gives us this information:

![image](https://github.com/user-attachments/assets/b3773d52-de57-41c4-84f1-9b68d9a5be0f)

**[Task 6, Question 2] What is the customer name of the IP address?** - Complete Web Reviews

## [Task 7] Scenario 1

In this task and the next, we'll investigate the rest of the emails provided in the VM. Let's look at `Email2.eml` now in Thunderbird. The sender/receiver addresses are provided at the top:

![image](https://github.com/user-attachments/assets/0ea3d66f-e24e-494f-995c-cda72deef5a6)

**[Task 7, Question 1] According to Email2.eml, what is the recipient's email address?** - `chris.lyons@supercarcenterdetroit.com`

You'll notice that there is an attachment in the email - check the bottom if you don't see it. We'll want to save the attachment (not open it), and grab the hash from it. Open a Terminal, and navigate to the location of the attachment. Then run `sha256sum "Proforma Invoice P101092292891 TT slip pdf.rar.zip"`. This yields the hash `435bfc4c3a3c887fd39c058e8c11863d5dd1f05e0c7a86e232c93d0e979fdb28`.

We can paste this into VirusTotal's search feature to get the detection information:

![image](https://github.com/user-attachments/assets/e83371ec-9d07-46b6-b641-1fca83b5e485)

We're looking specifically for the detection alias that starts with an H - this would be Avira's detection result.

**[Task 7, Question 2] On VirusTotal, the attached file can also be identified by a Detection Alias, which starts with an H.** - HIDDENEXT/Worm.Gen

## [Task 8] Scenario 2

Our final task here is to investigate `Email3.eml`. Again, we open it in Thunderbird. At the bottom, we see that someone put an attachment in this email:

![image](https://github.com/user-attachments/assets/a13e4817-6cff-418c-9274-99d8802e54a5)

**[Task 8, Question 1] What is the name of the attachment on Email3.eml?**

We want to figure out what malware family is associated with the attachment. We can use a variety of resources to investigate this attachment, though you should first save (not open) the attachment and grab a hash of it:

![image](https://github.com/user-attachments/assets/91aba01d-fc42-4387-9af9-9be1a4d0f723)

Once we have the hash, we can investigate in VirusTotal, Cisco Talos Intelligence, or other tools. In VirusTotal, there are a lot of references to _Dridex_, as well as in Talos Intelligence:

![image](https://github.com/user-attachments/assets/4c68e4c7-5292-4483-92a9-c498afa10fce)

So it's pretty safe to assume that Dridex is the associated malware family.

**[Task 8, Question 2] What malware family is associated with the attachment on Email3.eml?** - Dridex

There are plenty more open-source tools we can use for gathering threat intelligence. In the next few rooms, we'll be looking at specific tools such as Yara and MISP.
