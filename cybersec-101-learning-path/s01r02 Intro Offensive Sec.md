# Offensive Security Intro

## [Task 1] What is Offensive Security?

One of the ways we can protect systems is to think like a hacker - breaking into systems, exploiting bugs, gaining unauthorized access - and then applying that knowledge to close any gaps you find and enhancing defenses.

This room covers an offensive security example in a safe, legal environment, and is designed to show you how an ethical hacker operates.

**[Task 1, Question 1] What involves simulating a hacker's actions to find vulnerabilities and gain unauthorized access?** - Offensive Security

## [Task 2] Hacking Your First Machine

We'll be using virtual machines (VMs) to create environments where we can apply practical knowledge. In this room, the attached VM contains a fake bank application, Fakebank, and our job is to hack it. Opening the VM will open it in TryHackMe's Split Room View, though you may find it helpful to click the fullscreen button so you have more room to work with in the VM.

The button will be in the bottom-right of the VM split screen and it looks like the image below:

![image](https://github.com/user-attachments/assets/2ee2bb50-b25b-4cb3-bc00-eaf8ce3a926f)

In this room we'll use a tool called Gobuster to brute-force the FakeBank website and find hidden directories and pages. Gobuster goes through a list of potential page/directory names and tries to access a website with each of them. If it exists, Gobuster will note it for us. To use Gobuster, we'll need to do the following:
1. Open the Terminal (the icon at the right of the VM below the Firefox icon)
2. Run Gobuster with the command `gobuster -u http://fakebank.thm -w wordlist.txt dir`
3. Use the results of the Gobuster tool to transfer money between bank accounts.

Gobuster is used to find sensitive pages that should've really been made private (you'll often find these to be, say, administrator pages). A website developer may accidentally forget to make a page private, or may otherwise make a private page public for some reason.

The Gobuster command above has the folloiwng formatting:
- The `-u` "flag" tells Gobuster what website we want to scan.
- The `-w` "flag" tells Gobuster what words to iterate through - this is what Gobuster uses to look for hidden files and directories.

Once Gobuster scans the website, you'll see a list of websites that it found, e.g. `/images`. You're particularly interested in entries where it says `(Status: 200)`. This indicates that there's an actual website there. Once we find the website in question, we can add it to the URL in the address bar.

Accessing sensitive pages can be used to do all sorts of nefarious things - in this case, we can use it to transfer money to and from any bank account. As an ethical hacker (as long as you had legal permission to actually do this against a website), you would report your findings to the bank so they can fix it before a hacker exploits it. In this case, we'll just go ahead and transfer the money to our account.

The TryHackMe room has a series of screenshots demonstrating the process. But for completion's sake, here's what the Gobuster output should look like in the terminal:

![image](https://github.com/user-attachments/assets/c9847508-1fba-4368-9da8-d1fd36665f79)

...We spot the directory `/bank-transfer`, so we can add it to the URL in the web browser. Going there leads us to the bank transfer tool:

![image](https://github.com/user-attachments/assets/de255726-66b5-4636-8c5d-49f432662dc4)

We're sending 2000 USD from account number 2276 to number 8881. When we do so, we can go back to our bank account page (`fakebank.thm` will do, or you can go to Our Products & Services -> Accounts on the webpage). At the top, we'll see a prompt telling us we hacked the bank, and we'll get the answer to the next question.

**[Task 2, Question 1] Above your account balance, you should now see a message indicating the answer to this question. Can you find the answer you need?** - BANK-HACKED

Ethical hackers do stuff like this to find vulnerabilities in web applications, including pages that really shouldn't be open to the public. Now that we're done here, we can scroll up in the TryHackMe room and click the red "Terminate" button to close the virtual machine instance.

## [Task 3] Careers in Cyber Security

Of course, using Gobuster to spot hidden pages in a target website isn't all that a offensive security specialist does. This is just one of the things you might do with such a team. Your endgoal is to use whatever methods you can (and are allowed to use) to find loopholes, and then report those loopholes so the defensive security can come in and fix them. That'll be the topic of the next room.
