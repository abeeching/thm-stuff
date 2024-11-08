# Redline

## [Task 1] Introduction

Oftentimes you'll need to do memory analysis on a potentially compromised endpoint. One of the most popular tools is Volatility, which can help with focused analysis of memory artifacts from an endpoint. That tool will be the focus of its own room; for now, we'll discuss what we should do if we don't have the time to perform a full-fledged memory analysis. This is normally the case when triaging - sometimes you just need a quick assessment of an endpoint to determine the nature of a security event.

This is where Redline comes in. This is a tool created by FireEye that gives a top-down view of a Windows, Linux, or macOS endpoint, allowing you to analyze a potentially compromised endpoint via a memory dump to find signs of malicious activities. Redline provides the following functionalities, among others:
- Windows Registry data collection
- Running process collection
- Memory image collection, only on machines before Windows 10
- Browsing history collection
- Suspicious string identification

Redline can be installed on a local machine by using the MSI installer provided. We won't need to do so for this room, as it is already available in the attached virtual machine.

**[Task 1, Question 1] Who created Redline?** - FireEye

## [Task 2] Data Collection

We'll start by looking at Redline's data collection page. You have three ways to collect data for Redline:
1. Standard Collector: This configures the script to gather a minimum amount of data for analysis. This is the preferred method for data collection in this room, and it is also usually the fastest method for data collection.
2. Comprehensive Collector: This configures the script to gather the most data from the host for further analysis. This can take up to an hour or more, and is good if you want a full analysis of the system.
3. IOC Search Collector: This collects data that matches indicators of compromise (IOCs) that you create with teh IOC Editor. This is good if you already have known IOCs that you have gathered via threat intelligence, incident response, and/or malware analysis.

(Note that the room suggests you create the analysis file. This will take too long and potentially fill up the entire disk space on the attached VM. For Task 4, we'll be using the file that was already created.)

For this room, we will launch Redline from the taskbar and create a Standard Collector. We'll choose Windows as our target platform, and then under "Review Script Configuration" we will select "Edit your script." This allows us to choose what kind of data we want to collect from the host.
- Memory: We can choose to collect memory data, including process listing, driver enumeration for Windows hosts, and hook detection for Windows hosts prior to Windows 10. In this room we will want to leave Hook Detection and Acquire Memory Image unchecked.
- Disk: We can choose to collect data on disk partitions and volumes, along with enumerating files.
- System: We can collect information about the systme, such as the machine/OS information, system restore points on Windows machines prior to Windows 10, registry hives for Windows hosts, user accounts on Windows and macOS hosts, groups in macOS hosts, and prefetch information on Windows hosts.
- Network: We can collect information about networks and browser history, which can be helpful for identifying malicious file downloads, inbound/outbound connections, and more.
- Other: We can run other tasks in the script, such as collecting hashes and verifying digital signatures.

Once we choose our data options, we will click OK and then choose a folder to save the analysis file and the data collection script to. You need to make sure this folder is empty. Once you save the collector, you'll be given instructions to run the collector.

If you go into the folder, you should see a batch file called `RunRedlineAudit`. This is the script that you run to collect data from the host, though you need to run it as an administrator to get the all the data. Running this file will open a command prompt window, indicating the script is running. It automatically closes when the collection process finishes, which will be within 15-20 minutes.

Once this script is finished, there will be a file called `AnalysisSession1` in the Sessions folder with the `.mans` extension. This is what we want to import into Redline for investigation. We can double-click the file to import the audit data.

Note that running the script multiple times will increment the number of the analysis file by 1. For instance, if you were to run the script again, you will see a file called `AnalysisSession2`.

Most of these questions can be answered with the information in the task. The last question requires some knowledge of Windows forensics, though you can check the Redline user guide for details as well.

**[Task 2, Question 1] What data collection takes the least amount of time?** - Standard Collector

**[Task 2, Question 2] You are reading a research paper on a new strain of ransomware. You want to run the data collection on your computer based on the patterns provided, such as domains, hashes, IP addresses, filenames, etc. What method would you choose to run a granular data collection against the known indicators?** - IOC Search Collector

**[Task 2, Question 3] What script would you run to initiate the data collection process? Please include the file extension.** - `RunRedlineAudit.bat`

**[Task 2, Question 4] If you want to collect the data on Disks and Volumes, under which options can you find it?** - Disk Enumeration

**[Task 2, Question 5] What cache does Windows use to maintain a preference for recently-executed code?** - Prefetch

## [Task 3] The Redline Interface

When you import your analysis file into Redline (which takes around 10 minutes), this will create a new analysis session in Redline. When the data is imported, you'll see the Start Your Investigation view. The left panel gives you all sorts of analysis data types, and this is where you do inforamtion gathering and investigations.
- System Information: This is where you see information about the machine, including the BIOS (in Windows only), operating system, and user information.
- Processes: This is where you see attributes of processes, including names, PIDs, paths, arguments, parent proicesses, usernames, and more. Expanding this allows you to see handle information, memory sections, strings, and ports.
  - Handles: Connections from processes to objects/resources in Windows. This can be used for referencing internal objects such as files, registry keys, resources, and more.
  - Memory sections: Lets you investigate unsigned memory sections used by some processes. Many processes will use legitimate dynamic link libraries (DLLs), which will be signed. Unsigned DLLs may be worth looking into.
  - Strings: Captured strings in files.
  - Ports: A critical section to pay attention to, since this gives information about inbound and outbound connections. This can be used to find C2 communications. It's especially helpful that this is under processes - some adversaries may hide this communication under typical system processes, e.g. `notepad.exe`/`explorer.exe` having outbound connections.

Some other things of note:
- File System (which will not be included in this analysis session)
- Registry
- Windows Services
- Tasks (scheduled tasks are often used for persistence)
- Event Logs (another place to look for suspicious PowerShell events, Logon/Logoff events, user creation events, etc.)
- ARP and Route Entries (not included in this analysis session)
- Browser URL History (not included in this analysis session)
- File Download History

The Timeline helps you understand when the compromised happened and what steps a malicious actor took to escalate the attack. This records every action on the file - whether it was created, accessed, modified, changed, or had its filename altered in any way. The TimeWrinklers filter can be used to filter for events that took place around a given time; this is helpful if you know when compromise/suspicious activity started to occur. TimeCrunch will hide the same types of events that occurred within the same minute you specified.

**[Task 3, Question 1] Where in the Redline UI can you view information about the Logged In User?** - System Information

## [Task 4] Standard Collector Analysis

Now we will analyze the analysis file located in `C:\Users\Administrator\Documents\Analysis\Sessions\AnalysisSession1`. We want to see what the intruder put on the system for us. Double click the analysis session file, and after a little bit of time, Redline will open.

For the operating system, we'll want to check System Information and the Operating System Information sub-section:

![image](https://github.com/user-attachments/assets/5e02dff2-c2d4-4770-bfa0-3d5c504c093b)

**[Task 4, Question 1] Provide the Operating System detected for the workstation.** - Windows Server 2019 Standard 17763

The Tasks section gives us a list of all scheduled tasks created on the system. We'll need to scroll through it to find the suspicious one. I'd say "Microsoft Office Update Fake" is a pretty suspicious name for a scheduled task.

![image](https://github.com/user-attachments/assets/ee12514f-0e81-4f87-927f-da2435b1b625)

**[Task 4, Question 2] What is the suspicious scheduled task that got created on the victim's computer?** - MSOfficeUpdateFa.ke

The message in this case is the comment associated with the task.

**[Task 4, Question 3] Find the message that the intruder left for you in the task.** - `THM-p3R5IStENCe-m3Chani$m`

Next, we can check the Event Logs. In Redline, we have the ability to create filters - we just click the little filter icons beneath the column names to apply a filter for that column.

![image](https://github.com/user-attachments/assets/133787c0-1533-4a09-ac86-13efb4ba0c07)

In this case, filtering for "Error" types and for sources that contain "THM", we get this event ID.

![image](https://github.com/user-attachments/assets/72756f61-b9cb-4d5e-96ef-b5b5df7f1a12)

**[Task 4, Question 4] There is a new System Event ID created by an intruder with the source name "THM-Redline-User" and the Type "ERROR". Find the Event ID #.** - 546

The message for this event is as follows:

![image](https://github.com/user-attachments/assets/fe303638-486e-4313-9243-a6c9e0c45769)

**[Task 4, Question 5] Provide the message for the Event ID.** - Someone cracked my password. Now I need to rename my puppy-++-

Now let's take a look at what files were downloaded. Redline provides the File Download History, and we can see where people downloaded files from, what files were downloaded, and where they were placed. It turns out this URL has our flag here. You'll have to scroll the table to the right to confirm this:

![image](https://github.com/user-attachments/assets/2b5a0e13-d380-4686-ab96-b36797431194)

**[Task 4, Question 6] It loosk like the intruder downloaded a file containing the flag for Question 8. Provide the full URL of the website.** - `https://wormhole.app/download-stream/gI9vQtChjyYAmZ8Ody0AuA`

The flag's location is found in the Target Directory and File Name columns:

![image](https://github.com/user-attachments/assets/2552d8cf-3091-4cb9-a0d7-1619b5cf5433)

**[Task 4, Question 7] Provide the full path to where the file was downloaded to including the filename.** - `C:\Program Files (x86)\Windows Mail\SomeMailFolder\flag.txt`

Since this file is on the attached VM, we can just navigate to the directory above and open the text file. Doing so yields the following:

![image](https://github.com/user-attachments/assets/4160830f-75e7-4d06-99d7-facd690abb70)

**[Task 4, Question 8] Provide the message the intruder left for you in the file.** - `THM{600D-C@7cH-My-FR1EnD}`

## [Task 5] IOC Search Collector

Now let's look at what the IOC Search Collector can do. Recall that an IOC stands for "indicator of compromise," which means they are artifacts of potential compromise. You need to be on the lookout for these when perofrming threat hunting and incident response. This may include MD5, SHA1, SHA256, IP addresses, C2 domains, filesizes, filenames, file paths, registry keys, and more.

The IOC Editor, created by FireEye, can be used to create IOC files. FireEye says that this program runs on all versions of Windows up to Windows 7, so if you're worried about compatbility issues then OpenIOC Editor (also by FireEye) may be worth giving a shot.

In IOC Editor, we can create text files containing IOCs, modify them, and share them with other people. This room contains an example of IOCs associated with a keylogger. You don't have to create the file directly on its own if you don't want to, but hey, it's good practice. You can open IOC Editor from the taskbar, next to Redline, in the VM. Once open, you can create the directory to store the IOC file - the IOC Directory - and then you can go to File -> New -> Indicator.

From here, you will see indicators in the IOC Editor.
- You can edit the field with the Name; this is just the name of the IOC file.
- The Author field is the person who wrote the file.
- The GUID, Created, and Modified fields cannot be edited; these contain identifying information for the file as well as when it was last created and modified.
- The Description field allows you to summarize the purpose of the IOC file.

The actual IOCs will be under "Add" in the bottom-right. We'll want to include these indicators:
- File Strings: `psylog.exe`
- File Strings: `RIDEV_INPUTSINK`
- File MD5: `791ca706b285b9ae3192a33128e4ecbb`
- File Size: 35400

To add a specific IOC, you can click the drop-down button next to "Item". The Favorites entry contains commonly-used information. When you select the item, you can enter the value for the item directly, or just add it within the Properties. The fields in the properties are read-only except for Content and Comment. The value goes in the Content section. Once youe nter the value, click Save to save it. You may wish to right-click a given item for additional options.

Once you have created and saved the IOC file, you can open Redline and Create an IOC Search Collector. This option will ignore all data that doesn't match the IOC you've gathered, though you can always choose to collect additional data. The quality of the IOC analysis will be wholly dependent on the data you have available in the analysis session. To create an IOC Search Collector, click Browse... and then choose the location of the `.ioc` file. Redline automatically picks up on it and will put it in the Indicators section, along with some additional information:
- Unsupported Search Terms: These are terms that Redline cannot recognize, so they will not be matched on.
- Supported Search Terms: Redline recognizes these and will search for them.

Once you have finished reviewing the configured IOCs, you can click Next and change settings in the Edit Your Script dialog prompt. In this instance we select only the following options:
- Include Directories
- MD5
- Strings
- Include Files

We may need to select different settings in different investigations. When done editing this script, click OK, save the collector to an empty directory as before, and then run the batch file. Once the analysis is complete, you can open the `AnalysisSession1` file in Redline. Redline should attempt to generate an IOC repot automatically, though if it fails, you can click "Create a New IOC Report" and import the `.ioc` file.

Once the report is genreated, you should see a list of hits - these are things that Redline has detected based on the IOC search. You may see false positives (e.g., `chrome.dll` which only matches one of the File Strings in our IOC file). Note that the more granular and accurate your IOC file is, the less false positive results you (should) get. Look around for files with a lot of hits, and you'll find what you're looking for.

All of the questions below can be answered with the contents of the task itself, followed by the screenshots therein.

**[Task 5, Question 1] What is the actual filename of the Keylogger?** - `psylog.exe`

The next several questions can be answered by looking at the IOC Report screenshot in the task.

**[Task 5, Question 2] What filename is the file masquerading as?** - `THM1768.exe`

**[Task 5, Question 3] Who is the owner of the file?** - `WIN-2DET5DP0NPT\charles`

**[Task 5, Question 4] What is the file size in bytes?** - 35400

Note that the name of the IOC file is given in the Start Your Analysis Session screenshot in the task. The final location of this file will be found in the IOC Report. In the folder that the IOC search results are saved to - that is, `C:\Users\charles\Desktop\Keylogger-IOCSearch\` - IOC files will be stored in an `IOCs` sub-folder.

**[Task 5, Question 5] Provide the full path of where the `.ioc` file was placed after the Redline analysis, include the `.ioc` filename as well** - `C:\Users\charles\Desktop\Keylogger-IOCSearch\IOCs\keylogger.ioc`

## [Task 6] IOC Search Collector Analysis

Here we will put this to practice in another mini-scenario. Here we have a potential intrusion that has taken place, where a malicious actor was using a tool to perform lateral movement, perhaps a pass-the-hash attack. We'll want to find the file planted on the victim's computer using the IOC Search Collector and the following information:
- File String: `20210513173819Z0w0=`
- File String: `<?<L<T<g=`
- File Size (Bytes): 834936

Note that we can make use of the Previous Analysis option in Redline - there is an analysis file we can look at in `C:\Users\Administrator\Documents\Analysis\Sessions\AnalysisSession1`. This is the same as the file we investigated in Task 4. We will need to create the IOC using the information provided in this task:

![image](https://github.com/user-attachments/assets/09699057-10c6-4daa-8570-cb4f740c0e9d)

To actually have this return anything, we'll want to open up that analysis file now. Instead of creating a new IOC Collector, we can actually create a new IOC Report by going to the bottom-left, selecting IOC Reports, then selecting Create a New IOC Report:

![image](https://github.com/user-attachments/assets/c60e5137-6502-4f04-acb3-79fe0f3006c3)

We point Redline to the folder where we created the IOC file, select the IOC we want to look for in Redline, and then let it do its thing. This should give us one executable file on the machine. We can click the blue "I" button (the information button) to pull up detailed information about the file, including where it is:

![image](https://github.com/user-attachments/assets/ccf74e59-c0ca-449c-801b-de8e245a16ef)

**[Task 6, Question 1] Provide the path of the file that matched all the artifacts along with the filename.** - `C:\Users\Administrator\AppData\Local\Temp\8eJv8w2id6IqN85dfC.exe`

**[Task 6, Question 2] Provide the path where the file is located without including the filename.** - `C:\Users\Administrator\AppData\Local\Temp\`

The owner of the file can be found in the file information as well:

![image](https://github.com/user-attachments/assets/128fd210-7f87-472a-a9e6-15ed253b6637)

**[Task 6, Question 3] Who is the owner of the file?** - `BUILTIN\Administrators`

...Along with the subsystem:

![image](https://github.com/user-attachments/assets/1327036b-ee51-44ee-9436-03121979f660)

**[Task 6, Question 4] Provide the subsystem for the file.** - Windows_CUI

The device path is located near the top of the file information:

![image](https://github.com/user-attachments/assets/d49f243f-c0d1-4b10-a0f1-44dbceedca46)

**[Task 6, Question 5] Provide the Device Path where the file is located.** - `\Device\HarddiskVolume2`

And the MD5 hash can be found in the IOC Report:

![image](https://github.com/user-attachments/assets/16f26cc3-053e-4afa-b86d-b6a6eee08f32)

This _isn't_ the SHA-256 hash, to be fair. We can put this MD5 hash into VirusTotal and get the SHA-256 hash by looking at its summary:

![image](https://github.com/user-attachments/assets/fb980103-8047-4847-9090-0cae18ed9b66)

**[Task 6, Question 6] Provide the hash (SHA-256) for the file.** - 57492d33b7c0755bb411b22d2dfdfdf088cbbfcd010e30dd8d425d5fe66adff4

Judging by the output of VirusTotal - particularly the name above and the information given in the Details tab - we can conclude that this is PsExec.

![image](https://github.com/user-attachments/assets/c451e4cd-66f5-4bce-8053-1e7c8e7552d6)

**[Task 6, Question 7] The attacker managed to masquerade the real filename. Can you find it having the hash in your arsenal?** - `PsExec.exe`

## [Task 7] Endpoint Investigation

And for our last task in this room, we will run through a culminating scenario. Here a senior accountant is complaining that he cannot access some spreadsheets and other files he was working on. There's also something on his wallpaper suggesting his files got encrypted. Sounds like ransomware to me! We'll need to use Redline to investigate this. All the data is available on the VM - it can be found in the Endpoint Investigation folder on the Desktop. The analysis session there can be opened in Redline.

Checking the System Information -> Operating System Information, we see that the OS is Windows 7 Home Basic:

![image](https://github.com/user-attachments/assets/e6ba32f3-6b34-411d-9b63-80e2bef48574)

**[Task 7, Question 1] Can you identify the product name of the machine?** - Windows 7 Home Basic

If someone's leaving notes around on the Desktop, it's likely that they're using `notepad.exe` to do so. We can check the Processes tab and look for any processes that have `NOTEPAD.EXE` in them. There's only one result in the Process tab if we search for `notepad.exe`, and the arguments used to launch it tell us the name of the text file left on the Desktop:

![image](https://github.com/user-attachments/assets/58db6553-5adc-40f8-97d7-f5a8169305c4)

**[Task 7, Question 2] Can you find the name of the note left on the Desktop for "Charles"?** - `_R_E_A_D___T_H_I_S___AJYG1O_.txt`

Now let's look for the Windows Defender service. It's probably going to be in the Windows Services tab. We can, of course, just search for `defender`. Doing so yields one result, `WinDefend`, which has this as its DLL:

![image](https://github.com/user-attachments/assets/ff7a3fde-e00e-401c-b1f4-4dd5e0f1017d)

**[Task 7, Question 3] Find the Windows Defender service; what is the name of its service DLL?** - `MpSvc.dll`

We're told that the user downloaded a ZIP file from the web manually, so our next stop is the File Download History. We can search for `.zip` in this table. The file comes from `bazaar.abuse.ch`, or the MalwareBazaar, where malware samples are exchanged. The file in question is:

![image](https://github.com/user-attachments/assets/9c31e62f-4153-47ef-8195-5cddc9cde264)

**[Task 7, Question 4] The user manually downloaded a zip file from the web. Can you find the filename?** - `eb5489216d4361f9e3650e6a6332f7ee21b0bc9f3f3a4018c69733949be1d481.zip`

Let's see what else got dropped on the user's Desktop. We can look at the File System tab, and filter for `C:\Users\charles\Desktop`. This will also filter the sub-folder `Endermanch@Cerber5.bin`. We see one executable that looks pretty malicious in this list:

![image](https://github.com/user-attachments/assets/090a5d70-cfe9-476e-8a83-4bdb0b8585e3)

**[Task 7, Question 5] Provide the filename of the malicious executable that got dropped on the user's Desktop.** - `Endermanch@Cerber5.exe`

Its MD5 hash can be found by scrolling over to the right. The hash highlighted in blue is the one we're looking for:

![image](https://github.com/user-attachments/assets/82c80b12-b9dc-4072-8943-01e85266cb00)

**[Task 7, Question 6] Provide the MD5 hash for the dropped malicious executable.** - fe1bc60a95b2c2d77cd5d232296a7fa4

If we check VirusTotal's detections and details for the file with the MD5 hash above, we see a lot of references to "Cerber." Turns out that the adversary didn't really hide the name all that much!

![image](https://github.com/user-attachments/assets/05d1f2ae-26a4-4fae-aeb0-5bc49a66cd28)

**[Task 7, Question 7] What is the name of the ransomware?** - Cerber
