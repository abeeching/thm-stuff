# Search Skills

## [Task 1] Introduction

There are approximately 600 million hits for "learn cyber security" on Google.

That seems like a lot until you realize there are approximately this many hits for "learn hacking" on Google:

![image](https://github.com/user-attachments/assets/07772332-2a70-4d7c-a162-8426eb0fa313)

There is so much information out there on the Internet, and we're going to have to be able to sift through it to find what we need. Let's learn how to do so. As a bonus, we'll pick up on some tools of the cybersecurity trade.

## [Task 2] Evaluation of Search Results

Anyone can publish what they write to the Internet, and they can be writing anything. They can even contribute to public pages such as Wikis. This does open the door for people to put forth unfounded claims, including those pertaining to cybersecurity and computer science/technology in general. As readers we must be able to evaluate this information.
- Source: Who's publishing the information? Are they reputable or authoritative?
- Evidence, reasoning: Are the claims backed by evidence or sound reaosning? Are there facts and strong arguments?
- Objectivity, bias*: Is the information presented impartially and rationally? Is there mention of multiple perspectives? Is the author attempting to push some shady agenda?
- Corroboration, consistency: Can we verify the information with multiple, independent sources? Do those sources meet the considerations above?

*(I might go so far to say that it's easy to interpret most things as having a bias. You can interpret any piece of writing as having a bias based on what information is being presented, what's not being presented, the order it's being presented, and so on... This is the Internet, after all, so it's exceedingly likely people may be trying to argue about something. It may be helpful to consider what the author's end goal is - are they just trying to share information about a particular subject? Or are they trying _really hard_ to push some dubious angle?)

These questions require you to search for information. For this first question you could search, say, "bogus cryptographic method" in a search engine and see what comes up. You might see a reference to "snake oil." We can double-check by looking up "snake oil" and see what we find. This is what the Merriam-Webster's dictionary has to say on the subject:

![image](https://github.com/user-attachments/assets/6a88ba9b-b211-4b11-a8b5-2b46c4c91344)

The linked terms in the second definition refer to foolish or empty talk... so it seems like this is _the_ term for a product or method someone tries to put forth that's bogus or fraudulent.

**[Task 2, Question 1] What do you call a cryptographic method or product considered bogus or fraudulent?** - snake oil

We could start by looking up "command succeeding `netstat`" or "command replacing `netstat`" or something similar. If you look through the search results, you may see a `ss` command mentioned:

![image](https://github.com/user-attachments/assets/192fc467-03df-448a-bb0a-6f870d591368)

Let's make sure this is the one that we want. Looking up the `ss` command in your search engine might lead us to various sources. One such source comes from the SANS Institute's blog, which is a well-known organization dedicated to information security and cybersecurity training. This already looks promising. We can go farther and learn about the author by clicking his name - we get an overview of his experience in cybersecurity starting from his time in the military performing intelligence. He's even written courses in conjunction with SANS.

![image](https://github.com/user-attachments/assets/43b729b4-32d4-4f13-8167-cf026bb611e1)

The body text states that "[u]nlike the older netstat command, ss is faster and displays more detailed information..." Doing some more searching would suggest that most folks consider `ss` to be `netstat`'s successor.

**[Task 2, Question 2] What is the name of the command replacing `netstat` in Linux systems?** - `ss`

### On AI Features in Search Engines

It's worth making clear that if you have access to AI summaries of results, you should avoid solely relying on them. They may well point you in the right direction, but AI is known to be prone to "hallucinations" (read: generating factually incorrect information) and so they may get things wrong. When in doubt you should always double-check what you're searching for to make sure you're on the right track.

## [Task 3] Search Engines

What we used to answer those above questions (and most likely many _many_ other questions on the Internet) is a search engine. I'd wager there's an extremely high chance you've used one before - examples include Google, Bing, and DuckDuckGo. Now, in most cases, simply providing a few search terms will give us what we're looking for. But many search engines have advanced features to help make searching for things easier.

Here's what Google offers in the way of these advanced features:
- `"exact phrase"` - Enclosing your search term in doule quotes will look for pages that have that exact phrase. Without this, Google tends to include hits for any of the main keywords in the search query.
- `site:URL` - This is an operator you can use to limit your search to a given website and its subdomains. This can be combined with actual search queries to find things on a website.
- `-` - The minus sign can be used to omit search results that contain a particular word or phrase. Simply stick a minus sign in front of a word in your search, and Google will ignore it in searching.
- `filetype:` - This operator can be used to find files instead of webpages. You may want to find `pdf` documents, or Microsoft Word documents (`doc`), Excel spreadsheets (`xls`), and PowerPoint presentations (`ppt`), among other things. You simply provide the filetype you're looking for and you're set.

There are lists of advanced searching operators that you should seek out for your preferred search engine. The above operators are good starting points, though!

**[Task 3, Question 1] How would you limit your Google search to PDF files containing the terms `cyber warfare report`?** - `filetype:pdf cyber warfare report`

Now, our answer to the second question of the previous task will help here. Though you may want to make use of the above operators, e.g. `"ss command"` to filter for exact instances of the Linux command. Regardless of how we get there, we'll find out the following:

**[Task 3, Question 2] What phrase does the Linux command `ss` stand for?** - socket statistics

## [Task 4] Specialized Search Engines

Of course, there are other search engines that can be used to find specific information:
- Shodan: This is a search engine for devices connected to the Internet. You can search for specific server types and versions, networking equipment, and even industrial control systems (ICS) and Internet of Things (IoT) devices. There are examples provided by Shodan to help you get started, and if you subscribe to them, you can get access to trends/historical insights.
- Censys: As opposed to Shodan, Censys focuses on Internet-connected hosts, websites, certificates, and other Internet assets. This may be useful if you want to see what domains and ports are in use, as well as any rogue assets within a network. There is a list of use cases provided by Censys themselves for more examples.
- VirusTotal: This is a website that lets you scan a file for viruses against many antivirus engines. You can upload files or URLs to scan against, or you could even supply a *hash* (a unique identifier for a file (ideally)) to look for previously-uploaded files. You can even confer with the wider community for further insights on a given file, particularly if VirusTotal inaccurately reports that a file is malicious.
- Have I Been Pwned (HIBP): This website tells you if a given email has appeared in a leaked data breach. Typically, breaches will include more than just an email - it'll include a password and possibly identifying information. While passwords are stored securely, there are ways to break them (particularly if they're common or too simple). People tend to reuse passwords on multiple websites, so this can serve as a nice wake-up call for them to change their passwords.

For this first question, we ought to check Shodan. If we go to `shodan.io` and search for `lighttp`, we'll get a listing of results... but on the left-hand side, we have a tally of all the results and a tally of results by region.

![image](https://github.com/user-attachments/assets/d286ca1e-d01a-426a-b74d-18888d43a9e7)

It seems that the top country with this particular device is the US!

**[Task 4, Question 1] What is the top country with `lighttpd` servers?** - United States

The next question will require us to search VirusTotal. We can copy and paste the hash into its search bar at the top (or click the Search tab in the middle and paste it there), and we see this result from BitDefenderFalx's security analysis:

![image](https://github.com/user-attachments/assets/934f7758-7059-4f77-a666-359b11754a5a)

**[Task 4, Question 2] What does BitDefenderFalx detect the file with the hash `2de70ca737c1f4602517c555ddd54165432cf231ffc0e21fb2e23b9dd14e7fb4` as?** - Android.Riskware.Agent.LHH

## [Task 5] Vulnerabilities and Exploits

We'll often want to look up information regarding a known vulnerability or exploit. There are a few places where this information is complied, not the least of which being the Common Vulnerabilities and Exposures (CVE) program. This is effectively a database or dictionary of vulnerabilities. Each vulnerability and security issue gets its own identifier in the form of `CVE-YEAR-00000`. This identifier helps make sure people in the field are referring to the same vulnerability in discussions.

MITRE maintains the CVE system. You can search for existing CVEs by going to the CVE program website. You can also check the National Vulnerability Database (NVD) for the same information.

Now, that being said, the information provided is only for getting some basic information about vulnerabilites. What if you needed to exploit the vulnerability yourself? There's perfectly valid (and more importantly, *legal*) reasons to do this - if you recall from an earlier room, this is the job of a person on the red team. You should, of course, have legal permission to do so before actually exploiting a vulnerability.

Anyway, if you just need to find working exploit code, you could check the Exploit Database, which lists exploit codes from various authors. Some codes are even marked as tested and verified.

Alternatively, you can check GitHub for tools relating to CVEs, which can include proof-of-concepts and working exploit code.

For this question, all we need is some basic information, so we can check the CVE database. We can ismply go to `cve.org` and look up the ID CVE-2024-3094. This is what we find:

![image](https://github.com/user-attachments/assets/89a2aa52-cc93-46c2-a161-610ec03c7145)

Reading through the description, we are looking at the `xz` vulnerability from March of 2024. The full context is actually quite interesting to read through from a security perspective, but the gist of it was that someone was able to implement a backdoor in the XZ Utils repository. It had the potential to cause some extremely widespread damage, but fortunately it was detected and fixed before it could be used.

**[Task 5, Question 1] What utility does CVE-2024-3094 refer to?** - `xz`

## [Task 6] Technical Documentation

One of _the_ most helpful resources you can refer to when looking up information about a command or an OS feature is to refer to the system's official documentation. In Linux, you have access to manual pages, and in Windows, you have access to their Technical Documentation page at `learn.microsoft.com`.

Starting with Linux, manual pages (or just `man` pages) are documents that are put directly onto the system that give documentation on how to use certain commands - every command is expected to have a `man` page associated with it. It may be a curious choice to have all of this available on the system, but recall that Linux wasn't always online! So having the documentation on the system for a user to refer to was a very handy feature.

Note that man pages aren't just limited to commands, but they can be used for a variety of different things such as system calls, library functions, and configuration files. To find the man pages for a given command, type `man (COMMAND)` at the terminal, e.g. `man ls`. You can use the up and down arrows on your keyboard to scroll through them, and then press `q` to quit reading when you're done.

You can access man apges online too, by the way. You can just search for the command, or even look up `man (COMMAND)` on your search browser. The `linux.die.net` website happens to be one of the websites with the manual pages up, so if you see that in your results, that's a good place to start looking.

You may find it helpful to practice these commands in TryHackMe's AttackBox, which is a Linux system accessible from within the browser. You can click Start AttackBox at the top of the room to show the AttackBox in Split Screen view. You may hide the split-screen view (for any VM that opens in a split-screen view, including the AttackBox) by clicking the minus button at the bottom of the VM. If you need to unhide the VM, you can click Show Split View at the top.

Now, for Windows, you have access to the Technical Documentation page, as described earlier. This is simply Microsoft's documentation for their products, and you can search for different commands and topics to get information.

Of course, you may be using other products that aren't just utilities found on Windows and Linux. The common expectation in modern day is that a good product will have good, organized documentation. You need (and realistically _should_) seek out this documentation to learn more about the features of the products and functions. This will be the most up-to-date and most complete source of product information.

Let's look up some documentation! I'm going to look these up in the online resources discussed above, rather than in the AttackBox, for convenience. You should still check out the AttackBox if you haven't already, though!

Looking for the `cat` command in online documentation should give us something like this:

![image](https://github.com/user-attachments/assets/3a6279ad-2443-4ac8-9308-bbd2a226aec1)

It turns out that `cat` is just shorthand for con`cat`enate, which is a term commonly used for putting strings of text together in any context.

**[Task 6, Question 1] What does the Linux command `cat` stand for?** - concatenate

Now we'll want to look up the `netstat` command, but for Windows. Note that you may find some similar (or, in this case, the exact command) in both operating systems. However, each OS may handle the command differently, so it's important that we refer to the information in the `learn.microsoft.com` Technical Documentation.

Looking up `netstat` and reading through the documentation, we see a table of parameters. The second parameter seems to be the one we're looking for!

![image](https://github.com/user-attachments/assets/90fee83d-4fb3-44b2-b06b-68bb2cb5190b)

**[Task 6, Question 2] What is the `netstat` parameter in Microsoft Windows that displays the executable associated with each active connection and listening port?** - `-b` 

## [Task 7] Social Media

Last (but certainly not least), we should be willing to check social media platforms like Facebook, Twitter, and LinkedIn. You're probably already familiar with these guys! If you're not, you should definitely start getting familiar with them.

Ideally you can explore the platforms without being logged in, though unfortunately, most platforms severely restrict what you can do and see without logging in (there may be some ways around this depending on the platform, but these are limited at best and completely nonfunctional at worst). If you have to log in, and you DON'T want contacts to start interacting with you, create a temporary email address to explore, and then terminate the accounts when done.

Social media allows you to connect with companies and people you want to follow, sure, but it has a more important use case in cybersecurity: It lets us find people and information.

"It lets us find people" may sound weird (it probably is, come to think of it), though this can be extraordinarily useful for red teaming. Say you need to log into someone's account as part of a red team engagement. Confronted with a security question asking about someone's pet or school? Look up the person on social media and snoop around - you'll probably find something that'll help you get in!

(Now, of course, this can be a bad thing if there's an actual adversary trying to gain access to an account. Searching social media can be beneficial for defensive security too - you may be able to alert someone that they're sharing too much sensitive inforamtion, be it about themselves or about the organization or what-not.)

You would also want to use social media to stay abreast of new developments in cybersecurity - folks love to chat about new trends and technologies. For this same purpose, you may want to stay up-to-date with news outlets. Depending on the specific news outlet, you might get some fairly technical information about a new issue in cybersecurity. Look around for different outlets!

For this first question, the operative phrase is "technical background." Folks will use LinkedIn to share information about their background and any prior work experience.

**[Task 7, Question 1] You are hired to evaluate the security of a particular ocmpany. What is a popular social media website you would use to learn more about the technical background of one of their employees?** - LinkedIn

For this next question, you'll often find people discussing this sort of thing on Facebook - in the case of what schools they went to, they may even have that information as a part of their public profile there! People would often joke about how much other people would share about themselves on Facebook. Nowadays that seems to apply to any social media platform!

**[Task 7, Question 2] Continuing with the previous scenario, you are trying to find the answer to the secret question, "Which school did you go to as a child?" What social media website would you consider checking to find the answer to such secret questions?** - Facebook

(Let this be your reminder that you should keep a close eye on what you're putting out to the public!)

## [Task 8] Conclusion

The main takeaway from this room is that there are many places to seek out information, depending on what you're looking for. In fact, this room doesn't cover all of the potential sources of information! Keep your eyes and ears out - you may well discover a new source of information!
