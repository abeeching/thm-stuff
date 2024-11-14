# Phishing Emails in Action

## [Task 1] Introduction

This room continues from the Phishing Analysis Fundamentals and will focus on showcasing some actual phishing samples. As noted in that room, you should NOT interact with any IPs, domains, attachments, and so on - they all come from real phishing emails. If you do need to interact with them, exercise extreme caution.

Our goal in this room is to see what tactics attackers employ to make phishing emails look legitimate. The more convincing a phishing email is, the better the chances are that someone will click on a malicious link, download a malicious file, or do something else (e.g., sending off money).

## [Task 2] Cancel Your PayPal Order

Say you get this email:

![image](https://github.com/user-attachments/assets/38d66e13-b07f-41fc-b7c6-9fbbb7691292)

Note the weird recipient email address. That is extremely likely not to be an actual recipient email account. Note also the weird sender details. The sender details tell us that the email came from `service@paypal.com`, but the sender email is actually a bunch of gibberish at `sultanbogor.com`.

The subject line is also interesting; it tells us there's a purchase or transaction of some sort. This is a pretty good way of creating a sense of urgency; if you don't remember making such a purchase, then you'd be more willing to check the contents of the email without really thinking about it. A classical social engineering tactic.

The email body for such an email would do its best to look like a legitimate PayPal email:

![image](https://github.com/user-attachments/assets/f140f98a-b00b-4b95-9025-9b9b13e111ee)

![image](https://github.com/user-attachments/assets/aa67d55f-83cc-4cd8-a315-2ce6f8494051)

There are no attachments in this email - just a link to cancel the order. If we check the raw HTML for this email, then we'll see where this actually goes to:

![image](https://github.com/user-attachments/assets/8a6d2948-8609-4426-a8c6-55efbe53566f)

Using a link shortener (more on this in the next room), we see that this goes to Google and not PayPal.

![image](https://github.com/user-attachments/assets/c4cc90ee-4c1a-4182-aba2-354230b6d7f0)

**[Task 2, Question 1] What phrase does the gibberish sender email start with?** - `noreply`

## [Task 3] Track Your Package

Next up, say we've got this:

![image](https://github.com/user-attachments/assets/747f50ae-97e2-437e-ad99-543a455005b4)

This email looks like it's coming from a mail distribution center, which would make it s eem pretty important. The subject line only adds to this by letting you know you can track your package (also attaching a tracking number for you), and the link in the email body simply reiterates this.

Links were disabled in the email provided in Yahoo, but you can always view the raw source code to see what's up:

![image](https://github.com/user-attachments/assets/e06d26b1-6af4-48ae-a743-a0443ffcb1f6)

We know where the link goes to now, but more importantly, we see that there is an image in the email. Yahoo incidentally blocked images from loading, but this is known as a _tracking pixel_. These are pixels that let folks know whether you've opened an email and when, if so. Tracking pixels can send this stuff back to spammers. This is part of the reason why email providers automatically block images in spam emails.

The link itself, as we'd expect, points to a shady-looking domain. You can always check around to see if it is malware, but chances are very good that it is.

Remember: Defang your URLs! You can use CyberChef's Defang URL operation for this.

**[Task 3, Question 1] What is the root romain for each URL? Defang the URL.** - `devret[.]xyz`

## [Task 4] Select Your Email Provider to View Document

Now let's suppose we got this guy:

![image](https://github.com/user-attachments/assets/d8e8c86d-a195-4814-b214-30cc30fb458e)

This email was sent on July 15th, 2021, and according to the contents of the email, the link to download the document expires on the same day. Apparently this docuemnt is provided by Citrix Files, which is a trusted company. We have an action to perform: we gotta see what's in this document. If you were to click the button to download the document, we'll see this:

![image](https://github.com/user-attachments/assets/29c59db6-91df-4e8a-9ae0-e204a9c6d1ee)

This looks like a OneDrive landing page...just, y'know, without the URL having anything to do with Microsoft. From here, we can either grab the document, or we can preview it. Either way, you would be redirected to another page:

![image](https://github.com/user-attachments/assets/14838be6-5532-4ac1-9cba-222a5d9e1c87)

OK, cool. This is an Adobe-looking page, but the tab tells us its from Microsoft's SharePoint, and the URL has nothing to do with either. Seems legit. There's also some grammatical errors on this page. If you were to click any of the sign-in options, you would be asked to enter the email credentials of your choice. If you were to actually enter credentials and attempt to log in...you'd see this:

![image](https://github.com/user-attachments/assets/2679b81d-22dc-40d9-a79e-22fe82c6b9dd)

This error message pops up regardless of the credentials you use - you're not authenticating to anything, after all. You're actually just sharing your credentials with the criminal. And of course, there are more grammatical errors on this page.

The email sample was analyzed with Any Run's sandbox.

**[Task 4, Question 1] This email sample used the names of a few major companies, their products, and logos such as OneDrive and Adobe. What other company was used in this phishing email?** - Citrix

## [Task 5] Please Update Your Payment Details

Let's check out this totally real email from Netfix Billing:

![image](https://github.com/user-attachments/assets/ce89ce81-0315-4404-b054-f88036ffb001)

Clearly, a few things are amiss. Number 1: the email address is definitely not correct - that's not a Netflix-related address. There's also a sense of urgency: your `Netfllx` ID was suspended! The body of the email reinforces this by letting you know your account's on hold. There is something weird about the misspellings present here; you'd normally see this in typosquatting, but there's nothing really stopping the attacker from actually spelling the name correctly.

Regardless, we have to open an attachment to update our Netflix account.

![image](https://github.com/user-attachments/assets/67b7c887-25da-4e02-947b-b96cda2001c6)

That's a weird phone number to send to someone who (presumably) resides in the United States. Either way, here's what's inside the PDF file:

![image](https://github.com/user-attachments/assets/7272cd82-4d91-47e8-bc76-a688513720f6)

I think we saw this attachment in the previous room! At least, we saw its source code. We'll study this particular attachment in the next room in further detail.

The question for this task requires you to confer with Netflix's help page. They've got some advice on what to do if you run into a suspicious email or text.

![image](https://github.com/user-attachments/assets/6324237b-7b34-40e5-b233-3b45dcfa6508)

The verbiage required for the question comes from [this article](https://www.consumeraffairs.com/news/police-warn-of-new-netflix-email-phishing-scam-121718.html) called out in the hint.

**[Task 5, Question 1] What should users do if they receive a suspicious email or text message claiming to be from Netflix?** - forward the message to `phishing@netflix.com`

## [Task 6] Your Recent Purchase

Let's keep looking at some phishing emails. We have a similar email to the one presented in Task 2. We have an email about a recent purchase being made on the Apple App Store.

![image](https://github.com/user-attachments/assets/4472c5f1-6678-4ab8-a55a-5ab89c172205)

The email claims to come from Apple Support, but the email address has a bunch of gibberish with a domain of `sumpremed[.]com`. You'll also notice that the email starts with `donoreply` instead of, say, `donotreply`. The victim also didn't directly receive this email - it got blind carbon copied (BCCed) to them. The actual recipient email is made to look like a real Apple email address, though the email address has `payament` instead of `payment`...

Also, like before, this email claims that some purchase was made on the app store. This creates urgency, especially if you don't remember making such an purchase. With that being said, the only thing in the email body is an attachment:

![image](https://github.com/user-attachments/assets/43208e6b-4fc3-477b-a401-3b32c10ce498)

This is a `.dot` file, which is ap age layout template file associated wit hMicrosoft Word. It looks like this:

![image](https://github.com/user-attachments/assets/47d1f8ae-637f-45ce-8e05-102a619c159f)

The document actually contains an image file designed to resemble an App Store receipt;, and the link in question contains keywords relating to apps and IOS.

**[Task 6, Question 1] What does BCC mean?** - Blind Carbon Copy

**[Task 6, Question 2] What technique was used to persuade the victim to not ignore the email and act swiftly?** - Urgency

## [Task 7] DHL Express Courier Shipping Notice

Let's look at one last email sample. Here's something from DHL, supposedly:

![image](https://github.com/user-attachments/assets/1879b46a-6d56-480e-ad3b-95899580666c)

Note that the sender email doesn't, at all, match the company being impersonated. It sounds like DHL is trying to help us ship something, though, and the HTML would lead you to believe the email came from DHL. If you were to look at the source code, you'd see the following:

![image](https://github.com/user-attachments/assets/c0f1cacb-40c1-4b6f-ac4f-5717747be0c1)

This HTML code doesn't actually contain a link to a web page. All we have in the email is an Excel attachment:

![image](https://github.com/user-attachments/assets/54e8e6ce-5c16-4486-991c-d33aab1297f2)

Here's what's in the Excel attachment:

![image](https://github.com/user-attachments/assets/8469209f-ee21-4cc0-b374-2345969185ea)

The Excel attachment actually contains a payload that doesn't launch - we'll be covering it in the next room.

![image](https://github.com/user-attachments/assets/02f01331-7c87-4967-8d2d-4b4bbfd547bd)

**[Task 7, Question 1] What is the name of the executable that the Excel attachment attempts to run?** - `regasms.exe`
