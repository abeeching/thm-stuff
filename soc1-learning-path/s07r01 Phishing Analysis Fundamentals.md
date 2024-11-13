# Phishing Analysis Fundamentals

## [Task 1] Introduction

Spam and phishing are common _social engineering attacks_. Social engineering attack vectors include phone calls, text messages, and emails. Our focus for this sequence of rooms will be the email attack vector. Spam is an incredibly prevalent...thing...on the Internet. In the context of email, it simply refers to unsolicited emails getting sent out, and it has been around for many decades.

What's more concerning is the prevalence of phishing email attacks. An organization can invest so much time into developing a layered defense strategy, but it won't mean much if an unsuspecting user follows a link in a phishing email or runs a malicious attachment, allowing an attacker into the network.

There exist products that can help combat spam and phishing, but inevitably such emails will come through. As analysts we'll have to figure out how to determine if a given email is benign or malicious. We can also use this information to improve our security products and detect spam/phishing emails better.

The attached VM in this room will open in Split Screen View, and will be used in later tasks.

## [Task 2] The Email Address

Ray Tomlinson invented the concept of the e-mail and the use of the `@` symbol for addresses back in the 1970s - during the ARPANET days, before the modern-day conception of the Internet.

An email address is comprised of the user mailbox/username, the `@` symbol, and the domain. For instance, in `alice@example.com`, `alice` is the username and `example.com` is the domain. Email addresses help people figure out where to send emails to.

**[Task 2, Question 1] Email dates back to what tiem frame?** - 1970s

## [Task 3] Email Delivery

There are three key protocols used for sending and delivering email:
- SMTP: Simple Mail Transfer Protocol. Responsible for sending emails.
- POP3: Pop Office Protocol version 3. Responsible for transferring emails between a client and a server.
- IMAP: Internet Message Access Protocol. Responsible for transferring emails between a client and a server.

POP3 and IMAP serve the same purpose, but there are important functional differences between the two:
- Emails and downloaded/stored on a single device in POP3. Emails can be downloaded to many devices in IMAP.
- Sent messages are stored on the one device from which the email was sent in POP3. They're stored on the server in IMAP.
- Emails can only be accessed from the one device emails were downloaded to in POP3. They can be synced and accessed across many devices in IMAP.
- In POP3, if you want to keep messages on the server, you need to make sure the "Keep Email on Server" setting is enabled, otherwise all messages will get deleted from the server once moved to the single device's client.

Let's suppose that Alice wants to send Bob an email at `bob@example.com`. Let's see how these protocols are used to send the email:
1. Alice composes the email in her email client and sends it off.
2. SMTP: The SMTP server determines where to send Alice's email by consulting DNS for information regarding `example.com`.
3. DNS: Information on `example.com` is obtained and sent back to the SMTP server.
4. SMTP: Alice's email address begins to be sent across the Internet to Bob's mailbox at `exaple.com` (i.e., it exits the network).
5. SMTP: Alice's email gets sent through various SMTP servers before finally arriving at the destination SMTP server.
6. SMTP: Alice's email gets into the network with the destination SMTP server.
7. POP3/IMAP: Alice's email is forwarded into the local POP3 or IMAP server, waiting for Bob.
8. POP3/IMAP: Bob logs in to his email client, which queries the local POP3 or IMAP server for new emails.
9. POP3/IMAP: Alice's email is copied (in IMAP) or downloaded (in POP3) to Bob's mail client.

Each protocol has its associated ports, including recommended ports for security. By default, for instance, you'll see SMTP on port 25.

The questions that follow may require some research (or perhaps a look at the secure protocols room in Cyber Security 101):

**[Task 3, Question 1] What port is classified as Secure Transport for SMTP?** - 465

**[Task 3, Question 2] What port is classified as Secure Transport for IMAP?** - 993

**[Task 3, Question 3] What port is classified as Secure Transport for POP3?** - 995

## [Task 4] Email Headers

As a heads up - throughout these rooms you should NOT directly interact with any IP addresses, URLs, email addresses, and (especially) attachments that you see. This section will provide real examples of phishing emails, and you could risk compromising the security of your device and data if you're not careful. The work should be kept to the VMs and other relevant sites (e.g., VirusTotal).

Now let's see what makes up an email message when it arrives in the inbox. This is important for analyzing potentially malicious emails manually. Every email has two main components: the header (which contains information about the email, such as the servers that relayed teh email) and the body (text/HTML-formatted text). The syntax for email messages is the Internet Message Format (IMF).

Some email headers are immediately visible when viewing an email, and are worth noting down:
- The "From" field, which denotes the sender's email address,
- The "Subject" field, which denotes the subject line,
- The "Date" field, which denotes when the email was sent, and
- The "To" field, which denotes the recipient's email address.

The procedure for viewing additional email header information will vary from email client to email client. Generally, it will involve going to the email in question, clicking the Additional Options button (the icon will typically look like "..."), and then clicking "View raw message" or "Show original" or something like that. You may need to hit another option to revert back to the regular/rendered HTML format.

When viewing the raw HTML of a message, you'll see more noteworthy email headers:
- X-Originating-IP: The IP address the email was sent from. This is an example of an X-header.
- Smtp.mailfrom and header.from: These indicate the domain the email was sent from. They are located within Authentication-Results.
- Reply-To: This is the email address a reply will be sent to, instead of the Fro email address.

The questions that follow require reading [this article](https://web.archive.org/web/20221219232959/https://mediatemple.net/community/products/all/204643950/understanding-an-email-header) from Media Template.

**[Task 4, Question 1] What email header is the same as "Reply-to"?** - Return-Path

**[Task 4, Question 2] Once you find the email sender's IP address, where can you retrieve more information about the IP?** - `http://www.arin.net`

## [Task 5] Email Body

The email body is the part of the email that actually contains the text the sender wants you to see. This can be in the form of plain text, or it can be HTML-formatted (links, markup, etc.). The option to view the raw message/source code/original message is used to see the raw HTML of the email.

Emails may contain attachments; you can view attachments from an email's HTML format or by viewing the source code. If you look at an attachment in the source code, you may see the following associated headers:
- Content-Type: Describes _what_ the email attachment is. If it's a PDF document, you'll see application/pdf.
- Content-Disposition: Specifies this as an attachment.
- Content-Transfer-Encoding: If the attachment is encoded, it'll specify the encoding here (e.g., base64).

Of course, you should take caution when interacting with email attachments in this section (and in general). At least in these rooms, you should avoid double-clicking an attachment by accident.

Note also that headers specific to "content" can be found in various locations within an email message source code - they're not only associated with attachments. You may see a text/html Content-Type, and an 8bit Content-Transfer-Encoding value.

The first question relies on the screenshot of an email's source code. We're paying close attention to this section of the code:

![image](https://github.com/user-attachments/assets/0137ad93-68d7-4627-8746-68863e49fb87)

**[Task 5, Question 1] In the above screenshots, what is the URI of the blocked image?** - `https://i.imgur.com/LSWOtDI.png`

And this question relies on the last screenshot containing the source code of the attachment. We're concerned mainly with the headers:

![image](https://github.com/user-attachments/assets/f54b6af7-85b6-4577-988f-2fdd104f636a)

**[Task 5, Question 2] In the above screenshots, what is the name of the PDF attachment?** - `Payment-updateid.pdf`

Now let's reconstruct an email attachment! If we go to the `Samples` directory on the Desktop in the attached VM and open the `email2.txt` document, we see the following:

![image](https://github.com/user-attachments/assets/8b10ae7e-b9aa-4317-858f-4d4e6f94407a)

The `Tools` directory contains a local copy of CyberChef, so we can use that to reconstruct the attachment. If we take just the base64 data (everything between the headers and the last `-----------------`) and paste it into CyberChef, then apply the "From Base64" operation, we get this:

![image](https://github.com/user-attachments/assets/b77edf0f-71f0-49d7-9f9b-9751feb6f096)

There's nothing of particular interest in the output as it is, but we can actually save the output as a PDF document - it'll actually be interpreted as a PDF file by the system, now that we've converted it. Doing so gives us the answer to the last question for this task.

![image](https://github.com/user-attachments/assets/702c0888-2085-4b9a-b9a7-1341e539b573)

**[Task 5, Question 3] In the attached virtual machine, view the information in email2.txt and reconstruct the PDF using the base64 data. What is the text within the PDF?** - `THM{BENIGN_PDF_DOCUMENT}

## [Task 6] Types of Phishing

Now let's see how emails are used for malicious purposes. There are different types of phishing and social engineering attacks.
- Spam: Unsolicited junk email sent out, in bulk, to many recipients. Malicious spam can be referred to as "MalSpam."
- Phishing: Emails sent to target(s) purporting to be from a trusted entity to lure them into providing sensitive info.
- Spear phishing: Targets a specific individual/organization, seeking sensitive information
- Whaling: Similar to spear phishing, but targets specifically a C-level high-position individual, such as a CEO/CFO.
- Smishing: Phishing attempts done via mobile device text messages.
- Vishing: Phishing attempts done via voice calls.

Phishing attacks may have one of several objectives, including credential harvesting or gaining initial access into a computer.

Phishing emails have some common characteristics:
- The sender email name or address will masquerade as a trusted entity, i.e., email spoofing
- The email subject line/body is written with a sense of urgency. You'll see keywords like "invoice" and "suspended" and the like.
- The email body, if in HTML, will look like that of a trusted entity (e.g., it may _look_ like an Amazon email)
- The email body, if in HTML, may be poorly formatted or written.
- The email body may contain generic content, e.g., "Dear Sir" or "Dear Madam."
- Hyperlinks will oftentimes use URL shortening services to hide their true origin.
- Malicious attachments may be included, posing as legitimate documents.

We'll be looking at these techniques in subsequent rooms.

Briefly: We'll be defanging hyperlinks and IP addresses in this room. Defanging a URL/domain/email will make it unclickable, which can help you avoid accidentally going to a malicious site or opening a malicious attachment. Special characters will be replaced with different characters. It's good to defang URLs and IPs before sending them off for analysis. You can defang with CyberChef's Defang operations.

In this quick scenario, Alexa is the victim, and Billy is the analyst assigned to the case. Alexa forwarded the email to Billy for analysis. It might be difficult to read the raw HTML contents of the email in Mousepad; you can right-click it, click "Open With Other Application" and then click "Mozilla Thunderbird" to see how it would render in an actual email client.

This gives us the sender's email address and the subject line, and this tells us that the email is masquerading as a communication from Home Depot:

![image](https://github.com/user-attachments/assets/66816174-96fc-4fa9-8ef5-779cab70fa49)

**[Task 6, Question 1] What trusted entity is this email masquerading as?** - Home Depot

**[Task 6, Question 2] What is the sender's email?** - `support@teckbe.com`

**[Task 6, Question 3] What is the subject line?** - Order Placed : Your Order ID OD2321657089291 Placed Successfully

To see the URL, you can either hover over the links (be careful not to click on it), or you can check the source code and look for any `<a>` tags with the `href` property. Looking through the source code, we see one URL crop up time and time again:

![image](https://github.com/user-attachments/assets/bdc7216f-7e97-44f2-9547-6713c1ecd4dc)

This is what we're looking for. If you wanted to be safe you could try to copy the URL from the source code, though you'll pick up some extra symbols that you'll have to remove later, and you'll have to make sure that you get the entire URL. You can copy the URL from Thunderbird if you don't want to worry about that.

Regardless of how you get the URL, we'll want to defang it first. We can paste it into CyberChef, and then apply the "Defang URL" Operation to get a safe link.

![image](https://github.com/user-attachments/assets/fcdba89d-94e4-4ac0-930a-e4f1dcfa57f2)

**[Task 6, Question 4] What is the URL link for "- CLICK HERE?" (Enter the defanged URL)** - `hxxp[://]t[.]teckbe[.]com/p/?j3=EOowFcEwFHl6EOAyFcoUFV=TVEchwFHlUFOo6lVTTDcATE7oUE7AUET==`

## [Task 7] Conclusion

It is worth mentioning what a _BEC_ means before we wrap up this room. A BEC - Business Email Compromise - occurs when an adversary gets control of an internal employee's account, and then uses it to convince other internal employees to perform unauthorized and fraudulent actions.

In the next few rooms, we'll be looking at techniques used in phishing email campaigns, as well as tools that will help us in analyzing an email header and body.

**[Task 7, Question 1] What is BEC?** - Business Email Compromise
