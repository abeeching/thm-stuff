# Phishing Analysis Tools

## [Task 1] Introduction

In the first room of the phishing section, we learned how to sift through raw email source code to get information. Now we'll look at some tools that will help us with analyzing phishing emails, such as those that help us examine email headers, obtain hyperlinks (including shortened ones), gather information about potentially malicious URLs without clicking them, and obtain malicious attachments from phishing emails and work with them.

Once more, we are looking at actual spam/phishing emails - be extremely careful if you need to interact with the IPs, domains, and attachments.

## [Task 2] What Information Should We Collect?

We should grab the following information from the email header:
- Sender email address
- Sender IP
- Reverse lookup of sender IP
- Email subject line
- Recipient email address - check CC/BCC fields too
- Reply-to email address, if any
- Date/time

Then check the email body and attachments, if any exist in the email. You should grab the following artifacts from the body:
- Any URL links, preferably unshortened
- Attachment names
- Hash values of the attachment, preferably SHA256

As always, avoid clicking on links and attachments in the email accidentally.

## [Task 3] Email Header Analysis

Pertinent information about an email can be visually collected from an email or web client; however, you should be prepared to analyze the email header to obtain information like the sender's IP address and reply-to information. Here are a few tools we can use to help with header analysis:
- Google Admin Toolbox, Messageheader: This analyzes SMTP message headers and, while it's designed explicitly for troubleshooting, can help us analyze phishing email headers too. You simply copy and paste the entire email header and run the analysis tool.
- Message Header Analyzer: This tool is similar to the Messageheader tool. Just copy and paste the email header.
- `mailheader.org`: Yet another tool.

You might see message transfer agents (MTAs) mentioned. In brief, an MTA transfers emails between sender and recipient. The definitions, as provided by NIST, are provided after the questions in this task.

You'll have noticed that these tools do the same thing. You'll probably end up preferring one tool over the others, but it always helps to have multiple tools on standby. One tool may reveal information that another tool does not.

Here's some tools that can be used to analyze sender IP address information:
- `ipinfo.io`: This tool allows you to pinpoit a user's hostname, location, related organization, and more.
- `urlscan.io`: This lets you scan and analyze websites. It utilizes an automated process to record the activity that hte page navigation creates, including contacted domains and IPs, resources requested, and additional information, among other things. This tool provides a screenshot of the URL, which can help you avoid navigating directly to the URL in question.
- Other tools like `urlscan.io` include URL2PNG and Wannabrowser.
- Talos Reputation Center: Another website that provides lookup details for a URL.

The question in this task refers to lookups done on a `capitai-one[.]com` URL.

**[Task 3, Question 1] What is the official site name of the bank that `capitai-one[.]com` tried to resemble?** - `capitalone.com`

### More on MTAs and Mail User Agents (MUAs)

As per NIST, a MTA is a program running on a mail server that receives messages from mail user agents (MUAs) and other MTAs. They'll forward messages to other MTAs or, if the recipient is on the MTA, will deliver the message to the local delivery agent (LDA). A commonly used MTA would be Microsoft Exchange.

A MUA is a mail client application that a user uses to access mail servers. These let users read, compose, and send emails. Examples include Microsoft Outlook and Mozilla Thunderbird.

## [Task 4] Email Body Analysis

The body is where the real meat of a phishing email tends to be, since it usually contains a malicious link or attachment that serves as the payload. You can extract links manually from an HTML-formatted email by just grabbing them directly or by looking at the raw email header. You can manually grab a link by right-clicking it and clicking Copy Link Location, as per usual. You can, of course, use tools if you'd prefer:
- `convertcsv.com/url-extractor.htm` allows you to copy and paste the raw email header and get the relevant links that way.
- You can also use CyberChef's Extract URLs operation to get URLs from any set of data as well.

Regardless of the method you use, if you're grabbing URLs you should at least keep an eye on the root domain - you'll likely need to perform some analysis on it, too. You should always check URL and root domain reputation, and you can do so with the tools described in the previous task.

If the email has an attachment, you should obtain the attachment safely. In Thunderbird, you can simply click the Save button to save it to your machine, and then calculate its hash with `md5sum`, `sha256sum`, and the like. Some tools for analyzing the hash of a potentially malicious attachment include:
- Talos File Reputation: Cisco Talos maintains reputations for many files, which is then fed into various product lines. You can use File Reputation to look up the reputation of a file based on its hash. It's limited compared to the Advanced Malware Protection (AMP) system.
- VirusTotal: You can use this to analyze suspicious files and URLs to detect malware and automatically share them with the security community.
- Reversing Labs: This comapny has a file reputation service as well.

**[Task 4, Question 1] How can you manually get the location of a hyperlink?** - Copy Link Location

## [Task 5] Malware Sandbox

You don't need to have malware analysis skills, necessarily, to dissect and reverse engineer an attachment to understand malware. You can instead use online tools and services to upload and analyze malware automatically - malware sandboxes. If you have an attachment, you can upload it to one of the tools described in this task to see what it tries to communicate with, what payloads are sent to the endpoint, what persistence mechanisms are employed, the indicators of compromise (IOCs) used, and more. Some sandboxes include:
- Any.Run: This allows you to analyze a network, file, module, and registry activity associated with a file. You're able to interact with an operating system directly from a browser and see feedback from your actions immediately.
- Hybrid Analysis: This allows you to detect and analyze unknown threats with hybrid analysis - you simply attach a file or URL and get going.
- JOESecurity - Joe Sandbox: This allows you to perform live interaction, URL analysis, AI-based phishing deteciton, Yara/Sigma rule support, MITRE ATT&CK matrix mapping, AI-based malware detection, mail monitoring, threat hunting and intelligence gathering, and much much more.

## [Task 6] PhishTool

As one would have it, there is one tool that is catered specifically for phishing email analysis: PhishTool. This combines threat intelligence, OSINT, email metadata, and automatic analysis into one phishing response platform. It has a free community edition that you can download and use. You're able to connect VirusTotal to your PhishTool account via a community edition API key to make use of its features.

PhishTool will automatically grab the relevant header information, including:
- Email sender
- Email recipient(s)
- Timestamp
- Originating IP
- Reverse DNS lookup
- SMTP relays and hops through them
- X-headers
- Other IP information
- Reply-To and Return-Path information

You can view an email in its regular text format or in its raw HTML source code. You can also see information about attachments and URLs, as well as attachments and how malicious they may be. If you have VirusTotal connected, you'll automatically get feedback from it. You can also grab filenames and hashes without manually interacting with the malicious email, and you have access to additional options such as:
- Examining the strings of the file
- Grabbing VirusTotal information
- Download the attachment itslf
- Flag the attachment as malicious, and resolve with notes - you can classify the phishing email depending on its goal (e.g., is it meant to be a whaling attack?) here.

You can, of course, do more for analyzing phishing emails. It doesn't hurt to toss attachments into a sandbox or to further investigate the root domain.

The strings output provided in the task is as follows:

![image](https://github.com/user-attachments/assets/c63723b3-561a-44bc-b2a6-b2fddc42c00f)

**[Task 6, Question 1] Look at the Strings output. What is the name of the EXE file?** - `#454326_PDF.exe`

## [Task 7] Phishing Case 1

Let's look at some suspicious emails, then. We've been sent some suspicious emails from other coworkers, and we have to obtain details from each email. Our end goal is to get enough information to implement specific rules that stop our colleagues from receiving additional spam and phishing emails. We can use the tools discussed in this room, or our own resources, to analyze email headers and bodies throughout the next few tasks. We'll start the VM in this task and analyze the email placed on its desktop. You can open it with Thunderbird by right-clicking, selecting "Open with Other Application" and selecting "Mozilla Thunderbird" in the dialog box that appears.

Judging by the email From field, subject line, and contents, we can see that this email is trying to impersonate Netflix:

![image](https://github.com/user-attachments/assets/cf00e38e-115a-48ab-8b8e-fd50407c5041)

**[Task 7, Question 1] What brand was this email tailored to impersonate?** - Netflix

We'll grab the information in the "From" header, shown above.

**[Task 7, Question 2] What is the From email address?** - `N e t f I i x <JGQ47wazXe1xYVBrkeDg-JOg7ODDQwWdR@JOg7ODDQwWdR-yVkCaBkTNp.gogolecloud[.]com>` - NOTE that I defanged the email ever-so-slightly. You'll want to remove the brackets for this answer.

From henceforth we'll want to use the headers to get the required information. We'll just grab the information straight from the headers for these questions. For instance, the originating IP address is in the `X-Originating-Ip` field:

![image](https://github.com/user-attachments/assets/78a5519c-1eb9-41d6-8eef-f05ad99bf79a)

To defang, you can use CyberChef's Defang IP Addresses operation.

**[Task 7, Question 3] What is the originating IP? Defang the IP address.** - `209[.]85[.]167[.]226`

The headers seem to reference some random `etekno` domain:

![image](https://github.com/user-attachments/assets/b2018246-2aeb-466f-804b-f2ad9448269d)

This doesn't have anything to do with Netflix, so it's possible that these relate back to the phish-er in some way.

**[Task 7, Question 4] From what you can gather, what do you think will be a domain of interest? Defang the domain.** - `etekno[.]xyz`

If you want to grab a link from an email, you can either check the source code, or you can right-click the link in the email and click "Copy Link Location":

![image](https://github.com/user-attachments/assets/22d63ff2-b00d-4417-ad25-7412a553918d)

Regardless of what you do, you should use CyberChef's Defang URL operation to defang the URL. A local copy of CyberChef is available in the attached VM for your perusal.

**[Task 7, Question 5] What is the shortened URL? Defang the URL.** - `hxxps[://]t[.]co/yuxfZm8KPg?amp==1`

## [Task 8] Phishing Case 2

Continuing on, let's tkae a look at another suspicious email. We saw this email and attachment in previous rooms, and we can access the Any.Run analysis for it [here](https://app.any.run/tasks/8bfd4c58-ec0d-4371-bfeb-52a334b69f59).

In the top-right of the AnyRun analysis, you will see what it classifies the email/document as:

![image](https://github.com/user-attachments/assets/d3d9a02c-9474-447d-99e2-0d3ef5311e4c)

**[Task 8, Question 1] What does AnyRun classify this email as?** - Suspicious activity

The name of the PDF can also be found in the top-right, as shown in the above screenshot.

**[Task 8, Question 2] What is the name of the PDF file?** - `Payment-updateid.pdf`

While it only gives us the MD5 hash, we can always check with other sources. For instance, AnyRun's Text Report on this submission - which you can access by clicking the Text Report button in the top-right - gives us the SHA256 hash as well:

![image](https://github.com/user-attachments/assets/19063921-6496-4e84-86f2-d1aae072adc6)

You can also paste the MD5 hash into VirusTotal and check for other hashes that way.

**[Task 8, Question 3] What is the SHA 256 hash for the PDF file?** - `cc6f1a04b10bcb168aeec8d870b97bd7c20fc161e8310b5bce1af8ed420e2c24`

You would normally check the reputation of IP addresses in AnyRun by looking at the bottom-left pane and checking the HTTP requests, connections, and DNS requests tabs, but for some reason reputation information didn't show up. Fortunately, the text report has us covered:

![image](https://github.com/user-attachments/assets/bc7c661e-cf8b-4e40-b488-a508a088bef3)

![image](https://github.com/user-attachments/assets/53a76cb8-707f-4934-b3a7-5f893df2e883)

The two malicious IPs in the last screenshot are the same. Of course, to defang the addresses, we can use a tool like CyberChef (though, with IP addresses, all you really need to do is place brackets around the dots).

**[Task 8, Question 4] What two IP addresses are classified as malicious? Defang the IP addresses. (answer: `IP_ADDR`,`IP_ADDR`)** - `2[.]16[.]107[.]24,2[.]16[.]107[.]83`

The bottom-left pane also has a Threats tab, which shows us potentially problematic connections and processes. In this case, we have potentially bad traffic coming from `svchost.exe`.

![image](https://github.com/user-attachments/assets/4d82aebc-811e-43ed-b875-796172e07f24)

**[Task 8, Question 5] What Windows process was flagged as Potentially Bad Traffic?** - `svchost.exe`

## [Task 9] Phishing Case 3

And one more for the road. This one was also examined in AnyRun, and can be found [here](https://app.any.run/tasks/82d8adc9-38a0-4f0e-a160-48a5e09a6e83).

Once again, we check the top-right pane to see the details of the file and AnyRun's classification of it:

![image](https://github.com/user-attachments/assets/d6cae5b1-ff0d-4bd1-aad3-d130ee74cdb6)

**[Task 9, Question 1] What is this analysis classified as?** - Malicious activity

The filename is found in the top-right as well.

**[Task 9, Question 2] WHat is the name of the Excel file?** - `CBJ200620039539.xlsx`

The Text Report for this sample contains the hashes, and we can always check in with VirusTotal, if we need to, to obtain the same information.

![image](https://github.com/user-attachments/assets/4761a4b7-0b7e-4701-b592-f50139daa24d)

**[Task 9, Question 3] What is the SHA 256 hash for the file?** - `5f94a66e0ce78d17afc2dd27fc17b44b3ffc13ac5f42d3ad6a5dcfb36715f3eb`

There are three HTTP requests to websites that AnyRun deems malicious:

![image](https://github.com/user-attachments/assets/405695ff-b57c-4809-9708-58a6ca035279)

These are, in fact, the only three connections/DNS requests noted by AnyRun.

**[Task 9, Question 4] What domains are listed as malicious? Defang the URLs and submit answers in alphabetical order. (answer: `URL1,URL2,URL3`)** - `biz9holdings[.]com,findresults[.]site,ww38[.]findresults[.]site`

While the latter DNS request is only marked as "unknown", we were already told in the previous question that it leads to a malicious website, so we can include this one as well.

![image](https://github.com/user-attachments/assets/24a2dcfc-699e-4bd4-9560-6a1de1bde53c)

Of course, if we're not sure, we can corroborate our findings with, say, Cisco Talos Intelligence. Regardless, we simply provide our defanged IP addresses in order from lowest to highest (going off of the first octet):

**[Task 9, Question 5] What IP addresses are listed as malicious? Defang the IP addresses and submit answers from lowest to highest (answer: `IP1,IP2,IP3`)** - `75[.]2[.]11[.]242,103[.]224[.]182[.]251,204[.]11[.]56[.]48`

The top-right of the main AnyRun analysis page is tagged with the vulnerability this sample is trying to exploit. See the screenshot for the first question of this task. Incidentally, this is a memory corrutpion vulnerability present in Microsoft Office 2007 (Service Pack 3), Office 2010 (Service Pack 2), Office 2013 (Service Pack 1) and Office 2016. It would allow an attacker to execute arbitrary code as the current user of the system.

**[Task 9, Question 6] What vulnerability does this malicious attachment attempt to exploit?** - CVE-2017-11882
