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

## [Task 5] Malware Sandbox

## [Task 6] PhishTool

## [Task 7] Phishing Case 1

## [Task 8] Phishing Case 2

## [Task 9] Phishing Case 3
