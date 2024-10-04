# Retracted

## [Task 1] Introduction

Our next practice room in this section of the learning path has us investigate a potential ransomware attack that was seemingly reverted later. We need to figure out what happened...simple as that.

## [Task 2] The Message

A characteristic aspect of any ransomware attack is a ransom note being left on the victim's desktop (that's where they're most likely to look, in any case). So, checking around on the desktop in the VM provided by the room, we see a file named `SOPHIE`. I'd say this looks pretty suspicious!

![image](https://github.com/user-attachments/assets/e19fd534-f7c2-42c7-96b4-18bd3a6dd1ef)

We can check the properties of the file to get the full file path.

![image](https://github.com/user-attachments/assets/d0cae68a-ad25-4f3d-b4db-97a1943978e4)

**[Task 2, Question 1] What is the full path of the text file containing the "message"?** - `C:\Users\Sophie\Desktop\SOPHIE.txt`

We _can_ hypothesize that the file was created in Notepad, but it would be a good idea to check and make sure. We'll do this with Event Viewer. First up, we'll notice that, in the properties of the text file, we see that it has been created and modified at around 2:25 PM (14:25) on January 8, 2024.

So, we want to find either evidence of file creation at around 2:25 PM, or we want to find evidence of a Notepad process starting at around the same time. The Sysmon logs would help out with finding this information; they can be found in Event Viewer under Applications and Services Logs -> Microsoft -> Sysmon -> Operational. We know that Event ID 11 indicates file creation, and Event ID 1 indicates process creation. Filtering by Event ID 11 doesn't yield much results in our desired timeframe, so we'll look at Event ID 1 entries.

We can click on Filter Current Log to begin investigating. We can set the date range to only encompass events that happen on January 8th, 2024. We could go further and filter for the specific timeframe, but I'd figure we could look at events that happen throughout the entire day - it might help give us some more context into what's going on here.

![image](https://github.com/user-attachments/assets/e21c3c8b-c7bd-419a-a92d-93ee108b971b)

...and then we can make sure that we're only filtering for Event ID 1.

![image](https://github.com/user-attachments/assets/847c6a30-c086-46ed-bd6d-5d0ef52c47d1)

There are quite a few files remaining, but not as many as there were without the filter. Investigating the files shows us something fascinating: Notepad was opened to create the document...

![image](https://github.com/user-attachments/assets/76aeceec-4777-492c-96b1-4856f431c602)

**[Task 2, Question 2] What program was used to create the text file?** - `notepad.exe`

...and it was opened at around 2:25 as shown above (and below).

![image](https://github.com/user-attachments/assets/b91afa2e-69f2-4d35-aef2-555fea06db52)

**[Task 2, Question 3] What is the time of execution of the process that created the text file?** - `2024-01-08 14:25:30`

## [Task 3] Something Wrong

So far, we know that a potential ransom note was created at roughly 2:25 PM on January 8th, 2024. We're finding out now that our client downloaded a file from the Internet that turned out to be a virus, so let's focus on investigating that. It stands to reason that we only care about events created _before_ the ransom note was created, so this allows us to narrow down our focus.

We could filter for Event ID 1 to start with and look for events taking place before this timeframe. There is something interesting in the logs that come up that isn't _strictly_ related to our current question, and I'll leave that as an exercise for the viewer to find out. We'll touch back on it later.

In the meantime, we see that at 2:15 PM, a certain, suspiciously-innocuous-sounding file ran from Windows Explorer.

![image](https://github.com/user-attachments/assets/a3adcfc7-243f-479c-bc8f-019d88f4858d)

This seems like the kind of file someone may be tricked into downloading thinking it was safe. It's likely this was the "installer."

**[Task 3, Question 1] What is the filename of this "installer"?** - `antivirus.exe`

We see the path of the downloaded file in the Image field above.

**[Task 3, Question 2] What is the download location of this installed?** - `C:\Users\Sophie\download`

So we know that an "installer" was downloaded at `2:15:00 PM`, and we're looking for file extensions being added and being totally encrypted. At a glance, there's no event IDs in Sysmon that may help us look for changed extensions...unless you count Event ID 11! According to the Sysmon documentation, available at Microsoft's website, file creation events occur when a file is created...or _overwritten_. This is exactly what's happened here, so we'll take a look at events with event ID 11. We can filter for events that start on 2:15 PM.

Doing so reveals...quite a few files were created once the `antivirus.exe` file was ran. Here's one:

![image](https://github.com/user-attachments/assets/fbe91069-98a0-41fd-ba3a-2a5204e89d6e)

You'll note that there's a new extension in the filenames.

**[Task 3, Question 3] The installer encrypts files and then adds a file extension to the end of the file name. What is this file extension?** - `.dmp`

In light of what we've found out, we want to find connection events taking place at around 2:15 PM on January 8th. Turns out there's a Sysmon ID made especially for this: Event ID 3. If we filter for event ID 3 and look for events that take place at around this time, we see one see particularly interesting event.

![image](https://github.com/user-attachments/assets/226a4ac0-1b0b-46bc-89ae-9d6c7066d16d)

In this case, the `DestinationIp` is what we are looking for.

**[Task 3, Question 4] The installer reached out to an IP. What is this IP?** - `10.10.8.111`

## [Task 4] Back to Normal

Now we want to figure out what happened after the "installer" was downloaded. Our filter from the previous question still works, as long as we include events that take place after 2:15. The event that takes place immediately after the event in the previous question is an RDP connection.

![image](https://github.com/user-attachments/assets/ed5163c3-9472-488f-b8de-9202766537f4)

**[Task 4, Question 1] The threat actor logged in via RDP right after the "installer" was downloaded. What is the source IP?** - `10.11.27.46`

When we were hunting for the installer earlier, I had mentioned that there was something interesting in the logs, when we apply the filters for Event 1 and for the timeframe (before 2:25). Did you find it?

Now that we're here, we can make our job a little easier by simply noting that the RDP connection was established at around 2:19 PM, so this allows us to narrow down our filter further. Let's do so, and since we're looking for a downloaded file being run again, we want to examine Event ID 1. You'll notice that a certain file was downloaded and ran at 2:24 PM.

![image](https://github.com/user-attachments/assets/f44143bf-5fe8-4046-9aec-919f2404a30b)

Potentially unusual, no? I mean, it might not actually be a decryptor (all we have is just a name, after all), but it may well explain why the ransomware disappeared!

Either way, the time is given in the `UtcTime` field.

**[Task 4, Question 2] This other person downloaded a file and ran it. When was this file run?** - `2024-01-08 14:24:18`

## [Task 5] Doesn't Make Sense

The goal of this last task is to recap everything we know about the incident. Note that this room "begins" once everything has already happened - we arrive at our investigation last. So, to wit...

1. Sophie downloaded some kind of malware onto her computer and ran it at around 2:15 PM.
2. This malware encrypted the files on the computer. Presumably a ransomware note was shown!
3. Sophie is likely alarmed by this point and runs out to get help with the situation. I think that's a reasonable assumption to make here.
4. Meanwhile, someone logs into Sophie's computer over RDP, at around 2:19 PM. This person begins snooping around.
5. At around 2:24 PM, this person downloads what is probably a decryptor.
6. Then, at around 2:25 PM, this person tells Sophie to check her Bitcoin. This file may actually not be a ransomware note after all, and more of a friendly heads-up?
7. As discussed above, we finally arrive to begin our work. According to when the room was created, this happened in July of 2024. Pretty unfortunate timing on our part! (In all seriousness, this definitely happened last)

**[Task 5, Question 1] Sophie ran out and reached out to you for help.** - 3

**[Task 5, Question 2] Sophie downloaded the malware and ran it.** - 1

**[Task 5, Question 3] A note was created on the desktop telling Sophie to check her Bitcoin.** - 6

**[Task 5, Question 4] The intruder downloaded a decryptor and decrypted all the files.** - 5

**[Task 5, Question 5] The malware encrypted the files on the computer and showed a ransomware note.** - 2

**[Task 5, Question 6] Someone else logged into Sophie's machine via RDP and started looking around.** - 4

**[Task 5, Question 7] We arrive on the scene to investigate.** - 7
