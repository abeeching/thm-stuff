# Unattended

## [Task 1] Introduction

In this scenario, we'll be investigating a suspicious-looking janitor's activities. An employee spotted the janitor leaving his office during a lunch break; the goal is to see if there were any suspicious activities from 12:05 to 12:45 on November 19th, 2022. If suspicious activities are detected, then we need to see if any files were accessed or exfiltrated. We're accessing a live system in this scenario, but we have access to the disk image located in `C:\Users\THM-RFedora\Desktop\kape-results\C`. We also have access to various tools located in `C:\Users\THM-RFedora\Desktop\tools`.

The attached VM will start in Split Screen View. We are advised to run Registry Explorer immediately since this can take a few minutes to start. 

## [Task 2] Windows Forensics Review

This room relies on knowledge from the Windows Forensics sequence, and the KAPE room is recommended to help make the process more efficient. The cheatsheet provided in this task can help.

## [Task 3] Snooping Around

We'll start by seeing what this user attempted to search for - it seems like they already knew what they wanted to find. To do so, we'll want to investigate any registry entries pertaining to Windows searches. We can check the `C\Users\THM-RFedora\NTUSER.DAT` file for this. Specifically we're looking for `Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths` and `Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery` in this case. If we check `WordWheelQuery`, we can right-click the various values and click Data Interpreter to see what values there are.

Doing so for the second value in the `WordWheelQuery` registry key gives us the following:

![image](https://github.com/user-attachments/assets/326c67f7-a449-4334-afc3-a9047819e5e3)

We see in the interpreter that the user searched for a `.pdf` file. We can also spot it in the lower-right-hand corner.

**[Task 3, Question 1] What file type was searched for using the search bar in Windows Explorer?** - `.pdf`

Doing the same for the third value in the `WordWheelQuery` key yields:

![image](https://github.com/user-attachments/assets/107aa852-33fb-42fe-9462-e20abf205698)

We see that "continental" was searched for. The user must have been looking for a `continental` document...

**[Task 3, Question 2] What top-secret keyword was searched for using the search bar in Windows Explorer?** - continental

## [Task 4] Can't Simply Open It

They seemed to find what they were looking for, but they ran into some issues trying to open the document. They needed to do something first before they could read it. We'll investigate this angle of the case with Autopsy. We'll set up the case to investigate the contents of the image acquired in KAPE. When setting up Autopsy, we need to add some logical files as a Data Source and point it to `C:\Users\THM-RFedora\Desktop\kape-results\C`. The only module we need is the Recent Activity module, so we can uncheck all other modules. Once all that's done we can just let Autopsy do its thing.

Since we're looking for things that were put in the Downloads folder, we'll want to check the Web Downloads:

![image](https://github.com/user-attachments/assets/18b83d06-99c3-4721-9403-e92b1d8a5f10)

We see that the Continental file is actually a 7zip file, since it has a `.7z` extension. So, it would be natural to assume that they would have to install 7zip first before they can access it. The `7z2201-x64.exe` download is probably what we're looking for.

**[Task 4, Question 1] What is the name of the downloaded file to the Downloads folder?** - `7z2201-x64.exe`

If we look in the Autopsy results further, we see the date and time in which this file was downloaded:

![image](https://github.com/user-attachments/assets/a8fc07d3-d818-400d-8452-2c9af597d2e6)

**[Task 4, Question 2] When was the file from the previous question downloaded? (`YYYY-MM-DD HH:MM:SS UTC`)** - `2022-11-19 12:09:19 UTC`

We are then told that an image was accessed. We can actually check the RecentDocs registry key back in `NTUSER.DAT` for this - we see that there is a `.png` subkey. Checking it, we see that `continental.png` was accessed. The last write time of the key would suggest that the file was accessed at `2022-11-19 12:10:21`.

![image](https://github.com/user-attachments/assets/7c5255b8-22ef-49c3-905f-aac838e1f667)

**[Task 4, Question 3] Thanks to the previously downloaded file, a PNG file was opened. When was this file opened? (`YYYY-MM-DD HH:MM:SS`)** - `2022-11-19 12:10:21`

## [Task 5] Sending It Outside

Now that they have the tools, they'll try to exfiltrate the data outside of the network. We're told that the attacker created a text document and we need to figure out how many times it was accessed. If it was accessed a few times, there's a good chance that it will show up in the jump lists - these contain commonly-used documents that might show up in, say, the taskbar. The command we'll want to run, from the `C:\tools\JLECmd` directory, is `JLECmd.exe -d C:\Users\THM-RFedora\Desktop\kape-results\C\Users\THM-RFedora\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations --csv C:\Users\THM-RFedora\Desktop\csv`. Then we can investigate the output. Turns out there is, indeed, a text file that was opened up multiple times:

![image](https://github.com/user-attachments/assets/78399fc7-b2ab-496a-916f-424a4c161c61)

**[Task 5, Question 1] A text file was created in the Desktop folder. How many times was this file opened?** - 2

This incidentally also tells us the last time the file was modified:

**[Task 5, Question 2] When was the text file from the previous question last modified? (MM/DD/YYYY HH:MM)** - `11/19/2022 12:12`

We're told that the contents of this file were exfiltrated to `pastebin.com`. If we check the Web History results back in Autopsy, we see this pop up:

![image](https://github.com/user-attachments/assets/efb571ac-51f9-4a5b-8c9e-8fc947806d3c)

**[Task 5, Question 3] The contents of the file were exfiltrated to `pastebin.com`. What is the generated URL of the exfiltrated data?** - `https://pastebin.com/1FQASAav`

If we go to this pastebin link, we see the following...

![image](https://github.com/user-attachments/assets/7a681c01-15cb-4500-9f47-8e70c3f0d872)

**[Task 5, Question 4] What is the string that was copied to the pastebin URL?** - ne7AIRhi3PdESy9RnOrN
