# Snapped Phish-ing Line

## [Task 1] A Challenge Scenario

Note that the phishing kit used in this scenario came from a real-world phishing campaign, so if you need to interact with the phishing artifacts, only do so within the attached VM since it is an isolated environment.

In this scenario, we're working in IT at SwiftSpend Financial, and we're responding to employees reporting an unusual email. Some had submitted their credentials to whatever the email was pointing to, and they can no longer log in. Our goals are the following:
1. Analyze the email samples, found in the `phish-emails` directory.
2. Analyze the phishing URLs.
3. Retrieve the phishing kit used by the adversary.
4. Use CTI-related tools to get more information about the adversary.
5. Analyze the phishing kit to learn more about the adversary.

The machine in this task needs to be used to answer the following questions. It opens in split-screen view.

The room recommends we use a web browser, a text editor, and the grep command to answer the questions that follow.

First we'll need to investigate the emails we've been handed. We can open up the emails in Thunderbird and investigate their attachments to figure out who got a PDF:

![image](https://github.com/user-attachments/assets/520d114e-4862-4e14-8122-386807666d6b)

![image](https://github.com/user-attachments/assets/90b8f136-1022-49d5-8bb4-26cac34c078e)

**[Task 1, Question 1] Who is the individual who received an email attachment containing a PDF?** - William McClean

The email address is given in the first screenshot above; see the From line.

**[Task 1, Question 2] What email address was used by the adversary to send the phishing emails?** - `Accounts.Payable@groupmarketingonline.icu`

Let's open up Zoe Duncan's email. Zoe got an HTML file as an attachment:

![image](https://github.com/user-attachments/assets/713ff603-c767-4519-b473-d4d39342867d)

If we open this file in the VM's web browser, we are automatically redirected to a totally legitimate Microsoft login page.

![image](https://github.com/user-attachments/assets/62a39f52-e2a7-4090-89ac-2b65830b6aa9)

The URL is given above; we'll need to defang it with a tool like CyberChef.

**[Task 1, Question 3] What is the redirection URL to the phishing page for the individual Zoe Duncan? (defanged format)** - `hxxp[://]kennaroads[.]buzz/data/Update365/office365/40e7baa2f826a57fcf04e5202526f8bd/?email=zoe[.]duncan@swiftspend[.]finance&error`

We can enumerate the URL path to figure out if there are any interesting files on the web server. For instance, we might try to go to `/data/Update365/office365` directly, then `/data/Update365`, and then `/data`. Turns out, in the `/data` directory of the web server, there is a ZIP file that we can access:

![image](https://github.com/user-attachments/assets/935c33ea-de01-4b56-8b4d-f15709c67b11)

**[Task 1, Question 4] What is the URL to the `.zip` archive of the phishing kit? (defanged format)** - `hxxp[://]kennaroads[.]buzz/data/Update365[.]zip`

Let's download it. We can open up Terminal and navigate to where we put the file. Running `sha256sum Update365.zip` yields the following:

![image](https://github.com/user-attachments/assets/dc0fd7fb-56c6-4ad5-bc68-d88d1b246a6a)

**[Task 1, Question 5] What is the SHA256 hash of the phishing kit archive?** - ba3c15267393419eb08c7b2652b8b6b39b406ef300ae8a18fee4d16b19ac9686

Now that we have a hash, we can investigate it in VirusTotal. Searching for the hash and checking the Details gives us this information:

![image](https://github.com/user-attachments/assets/7644ffe8-e668-4f5b-ba85-182774e7356f)

**[Task 1, Question 6] When was the phishing kit archive first submitted? (format: `YYYY-MM-DD HH:MM:SS UTC)`** - `2020-04-08 21:55:50 UTC`

Unfortunately, this next question can't be reliably answered, since the SSL certificate is no longer available for us to investigate. The hint attached to this question gives you the answer.

**[Task 1, Question 7] When was the SSL certificate the phishing domain used to host the phishing kit archive first logged? (foramt: YYYY-MM-DD)** - 2020-06-25

Moving swiftly along, if we head back to the web server, we can navigate to the `Update365` directory. This contains an interesting file named `log.txt`... if we investigate it, we see a bunch of credentials! Based on the logs, Michael Ascot unfortunately sent their password in twice.

![image](https://github.com/user-attachments/assets/682f99e1-b8ee-45ed-a9af-eb77b8146fb2)

**[Task 1, Question 8] What was the email address of the user who submitted their password twice?** - `michael.ascot@swiftspend.finance`

Now let's investigate the phishing kit proper. We're in an isolated VM, so it's okay to unzip the archive and see what's inside. Therein, we see a bunch of PHP files, among other things.

![image](https://github.com/user-attachments/assets/e831b24f-c7bd-4f8d-8ea7-78f9d52a1296)

If we poke around a little bit, we see a lot of files related to the actual phishing page in the Validation directory. The `submit.php` file might be a good place to check, since that's likely to tell us where any credentials will be sent to. The email that's going to collect the credentials is stored in a variable:

![image](https://github.com/user-attachments/assets/37091a43-4a69-406a-b272-6b22f55124ca)

**[Task 1, Question 9] What was the email address used by the adversary to collect compromised credentials?** - `m3npat@yandex.com`

Now we just need to look for other emails in the phishing kit. It'll be better for us, since we know the email will have `gmail.com`, to fire up a terminal and use grep. In the Validation directory, if we run `grep -i "gmail.com" *.*` to search all the files, we'll get this as our result:

![image](https://github.com/user-attachments/assets/01ca2907-a2cd-4c98-944c-ef6fdea1c6e3)

**[Task 1, Question 10] The adversary used other email addresses in the obtained phishing kit. What is the email address that ends in `@gmail.com`?** - `jamestanner2299@gmail.com`

Now we have to find a flag. We'll need to investigate the web server once again to do this. We are told in the hint that the flag has a `.txt` extension, so we're looking for any text files in the web server that may contain the flag. This will necessitate some guesswork.

In the `Update365` directory, we have an `office365` directory that leads us nowhere:

![image](https://github.com/user-attachments/assets/0761f1b0-72a4-4bdb-a639-ea6c82010a59)

But if we investigate the rest of the web server, we don't really find anything of value. So, there's a pretty good chance that the flag file will be in the `office365` directory. This is where our guesswork begins - we have to think about what the flag file might be called. Flag files tend to be pretty straightforward, so `flag.txt` might be a good name to try. If we navigate to `office365/flag.txt`, we obtain the following:

![image](https://github.com/user-attachments/assets/d523e0a4-75be-4d5a-986e-2f95deafd435)

Cool! That's not our flag. Not yet, anyway. We'll need to do some work in CyberChef to fix it up. Pasting the text into CyberChef and applying the "From Base64" operation gives us the following:

![image](https://github.com/user-attachments/assets/51dc84a9-248d-4411-bfee-80fef56350cb)

Unfortunately, the resulting text appears to be in reverse. Fortunately, CyberChef has a "Reverse" operation.

![image](https://github.com/user-attachments/assets/5bee92e9-a5bd-43b4-a2e9-62be62a56c1d)

**[Task 1, Question 11] What is the hidden flag?** - `THM{pL4y_w1Th_tH3_URL}`
