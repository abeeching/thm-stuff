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

Now we will analyze the analysis file we created back in Task 2. We want to see what the intruder put on the system for us.

**[Task 4, Question 1] Provide the Operating System detected for the workstation.** - Windows Server 2019 Standard 17763

**[Task 4, Question 2] What is the suspicious scheduled task that got created on the victim's computer?** - MSOfficeUpdateFa.ke

**[Task 4, Question 3] Find the message that the intruder left for you in the task.** - `THM-p3R5IStENCe-m3Chani$m`

**[Task 4, Question 4] There is a new System Event ID created by an intruder wit the source name "THM-Redline-User" and the Type "ERROR". Find the Event ID #.** - 546

**[Task 4, Question 5] Provide the message for the Event ID.** - Someone cracked my password. Now I need to rename my puppy-++-

**[Task 4, Question 6] It loosk like the intruder downloaded a file containing the flag for Question 8. Provide the full URL of the website.** - `https[://]wormhole[.]app/download-stream/gI9vQtChjyYAmZ8Ody0AuA` (remove brackets)

**[Task 4, Question 7] Provide the full path to where the file was downloaded to including the filename.** - `C:\Program Files (x86)\Windows Mail\SomeMailFolder\flag.txt`

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

**[Task 5, Question 1] What is the actual filename of the Keylogger?** - `psylog.exe`

**[Task 5, Question 2] What filename is the file masquerading as?** - `THM1768.exe`

**[Task 5, Question 3] Who is the owner of the file?** - `WIN-2DET5DP0NPT\charles`

**[Task 5, Question 4] What is the file size in bytes?** - 35400

**[Task 5, Question 5] Provide the full path of where the `.ioc` file was placed after the Redline analysis, include the `.ioc` filename as well** - `C:\Users\charles\Desktop\Keylogger-IOCSearch\IOCs\keylogger.ioc`

## [Task 6] IOC Search Collector Analysis

Here we will put this to practice in another mini-scenario. Here we have a potential intrusion that has taken place, where a malicious actor was using a tool to perform lateral movement, perhaps a pass-the-hash attack. We'll want to find the file planted on the victim's computer using the IOC Search Collector and the following information:
- File String: `20210513173819Z0w0=`
- File String: `<?<L<T<g=`
- File Size (Bytes): 834936

Note that we can make use of the Previous Analysis option in Redline - there is an analysis file we can look at in `C:\Users\Administrator\Documents\Analysis\Sessions\AnalysisSession1`.

**[Task 6, Question 1] Provide the path of the file that matched all the artifacts along with the filename.** - `C:\Users\Administrator\AppData\Local\Temp\8eJv8w2id6IqN85dfC.exe`

**[Task 6, Question 2] Provide the path where the file is located without including the filename.** - `C:\Users\Administrator\AppData\Local\Temp\`

**[Task 6, Question 3] Who is the owner of the file?** - `BUILTIN\Administrators`

**[Task 6, Question 4] Provide the subsystem for the file.** - Windows_CUI

**[Task 6, Question 5] Provide the Device Path where the file is located.** - `\Device\HarddiskVolume2`

**[Task 6, Question 6] Provide the hash (SHA-256) for the file.** - 57492d33b7c0755bb411b22d2dfdfdf088cbbfcd010e30dd8d425d5fe66adff4

**[Task 6, Question 7] The attacker managed to masquerade the real filename. Can you find it having the hash in your arsenal?** - `PsExec.exe`

## [Task 7] Endpoint Investigation

And for our last task in this room, we will run through a culminating scenario. Here a senior accountant is complaining that he cannot access some spreadsheets and other files he was working on. There's also something on his wallpaper suggesting his files got encrypted. Sounds like ransomware to me! We'll need to use Redline to investigate this. All the data is available on the VM - it can be found in the Endpoint Investigation folder on the Desktop. The analysis session there can be opened in Redline.

**[Task 7, Question 1] Can you identify the product name of the machine?** - Windows 7 Home Basic

**[Task 7, Question 2] Can you find the name of the note left on the Desktop for "Charles"?** - `_R_E_A_D___T_H_I_S___AJYG1O_.txt`

**[Task 7, Question 3] Find the Windows Defender service; what is the name of its service DLL?** - `MpSvc.dll`

**[Task 7, Question 4] The user manually downloaded a zip file from the web. Can you find the filename?** - `eb5489216d4361f9e3650e6a6332f7ee21b0bc9f3f3a4018c69733949be1d481.zip`

**[Task 7, Question 5] Provide the filename of the malicious executable that got dropped on the user's Desktop.** - `Endermanch@Cerber5.exe`

**[Task 7, Question 6] Provide the MD5 hash for the dropped malicious executable.** - fe1bc60a95b2c2d77cd5d232296a7fa4

**[Task 7, Question 7] What is the name of the ransomware?** - Cerber
