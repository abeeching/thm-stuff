# The Greenholt Phish

## [Task 1] Just Another Day as a SOC Analyst

A sales executive at Greenholt, PLC receives an email from a customer. The customer never used generic greetings before, and the exec was not expecting any money to be transferred to his account. The email has an attachment in it that he never requested. Let's see what the deal is.

The virtual machine attached to this task contains the email we need to investigate. Right-click `challenge.eml` on the Desktop, select Open With Other Application, and select Thunderbird Mail.

First, let's take a look at the visible headers in Thunderbird:

![image](https://github.com/user-attachments/assets/2169e58d-3a7a-4552-ade4-b7464c3b4a93)

We can gleam quite a bit of information from this.

**[Task 1, Question 1] What is the Transfer Reference Number listed in the email's Subject?** - 09674321

**[Task 1, Question 2] Who is the email from?** - Mr. James Jackson

**[Task 1, Question 3] What is his email address?** - `info@mutawamarine[.]com` (defanged; need to remove brackets)

**[Task 1, Question 4] What email address will receive a reply to this email?** - `info[.]mutawamarine@mail[.]com` (defanged; need to remove brackets)

Now we'll need to investigate the originating IP, and we can only do this by checking the email's source code. While the wording might suggest we need to check the header literally called "X-Originating-Ip", that's not what we're looking for. We actually want to check one of the "Received:" fields.

![image](https://github.com/user-attachments/assets/9ded38bd-4805-4b90-a3e6-508fb3f32007)

We're specifically concerned with this one because the "helo" field contains the domain in the "From" field.

**[Task 1, Question 5] What is the Originating IP?** - `192.119.71.157`

To learn more about the owners of this IP, we can perform a WHOIS lookup. ICANN has this information on the registrant.

![image](https://github.com/user-attachments/assets/98e65573-d04e-43b4-9b6c-2d3482ad354c)

**[Task 1, Question 6] Who is the owner of the Originating IP? (Do not include the "." in your answer.)** - Hostwinds LLC

Now let's check some SPF and DMARC records. The Return-Path domain can be found in the source code:

![image](https://github.com/user-attachments/assets/04882445-d980-411d-aa49-f4e320c71322)

We can use dmarcian's tools for this purpose. For instance, here's what SPF Surveyor has to say about the domain.

![image](https://github.com/user-attachments/assets/7177e428-8b87-4237-a0e9-7081d0ad123c)

**[Task 1, Question 7] What is the SPF record for the Return-Path domain?** - `v=spf1 include:spf.protection.outlook.com -all`

And here's what DMARC Inspector has to say about it:

![image](https://github.com/user-attachments/assets/9e2d5696-566f-4752-9ae3-e131a22e4090)

**[Task 1, Question 8] What is the DMARC record for the Return-Path domain?** - `v=DMARC1; p=quarantine; fo=1`

Now let's investigate the attachment. Here's what we see in Thunderbird:

![image](https://github.com/user-attachments/assets/02d32198-0ed6-4f22-9838-b1cde4fcd159)

**[Task 1, Question 9] What is the name of the attachment?** - `SWT_#09674321___PDF__.CAB`

If we save - not open - the attachment to our computer, we can grab the SHA256 hash by using `sha256sum`:

![image](https://github.com/user-attachments/assets/e1ef1e95-be38-43b4-a9c9-d06f7a079f29)

**[Task 1, Question 10] What is the SHA256 hash of the file attachment?** - `2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f`

We'll need to grab the file size and actual file extension for this file. We are advised not to use the tools in the virtual machine in the hint. Fortunately, we have a file hash, so we can load that into VirusTotal and see what comes up:

![image](https://github.com/user-attachments/assets/1a152339-5334-438d-a7d2-6b003a83f0f2)

**[Task 1, Question 11] What is the attachment's file size? (Don't forget to add "KB" to your answer, NUM KB)** - 400.26 KB

**[Task 1, Question 12] What is the actual file extension of the attachment?** - RAR
