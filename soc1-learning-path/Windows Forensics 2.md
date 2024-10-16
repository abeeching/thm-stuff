# Windows Forensics 2

## [Task 1] Introduction

In the previous room, we began learning about Windows forensics by investigating the Registry, and we practiced extracting forensic artifacts from there, such as system and user information, accessed files and folders, programs ran, and external devices connected to the system.

The Registry, however, is not the only place where we can find forensic artifacts. We will learn how to find forensic artifacts in other places, different filesystems, and locations within these filesystems. We'll also discuss how to recover deleted files. We'll use EZTools, as before, to parse information, as well as Autospy.

## [Task 2] The FAT Filesystems

In computer systems, storage devices are simply collections of bits. These bits need to be organized so that they can be converted to useful information. This is why we have different filesystems that can organize the bits in a hard drive according to standard.

The File Allocation Table (FAT) is one such filesystem, and it has been the default filesystem for Microsoft's operating systems since at least the '70s. While it no longer is the default in Windows (see the next task), it is still in use today. This filesystem is based on a table that indexes the location of bits allocated to different files.

The following data structures are supported under FAT:
- Clusters: The basic storage unit of the FAT filesystem, each file stored on a storage device is just a group of clusters
- Directories: Contain information about file identification- filename, starting cluster, filename length
- File Allocation Table: Linked list of all clusters, containing status of the cluster and pointing to the next cluster in the chain

The bits making up a file are stored in clusters, and all of the identifying information are stored in direcotries. The location of each cluster is stored in the File Allocation Table itself.

Now that we know that FAT divides available disk space into clusters for easier addressing, we can dive deeper into the different versions of FAT. The number of clusters used depends on the number of bits used to address the cluster. Originally, FAT was developed with 8-bit cluster addressing, but as storage needs increased, FAT12, FAT16, and FAT32 were introduced (the latest being in 1996).

FAT12 would use 12-bit cluster addressing for 2^12 = 4096 clusters. FAT16 used 16-bit cluster addressing for 2^16 = 65,536 clusters maximum. FAT32, in fact, used 28 bits to address clusters, meaning that it has 2^28 = 268,435,456 clusters maximum (less than what you'd expect). Not all of the clusters in these FAT variations are used for file storage - some are used for administrative purposes (e.g. storing the end of a chain of clusters, unusable parts of disks, etc.).

To summarize:
- FAT12 used 12 addressable bits, FAT16 used 16, and FAT32 used 28.
- FAT12 has 4,096 clusters maximum, FAT16 had 65,536, and FAT32 had 268,435,456.
- FAT12 supported clusters of 512B-8KB, FAT16 supported 2KB-32KB, and FAT32 supported 4KB-32KB.
- FAT12 had a maximum volume size of 32MB, FAT16 had a maximum of 2GB, and FAT32 had a maximum of 2TB (though because Windows limits formatting to 32GB, that's the effective maximum on Windows machines -- note that volume sizes formatted on other operating systems with larger volume sizes are supported by Windows, though).

FAT12 is pretty rare to find nowadays, but FAT16 and FAT32 are still used in some places such as USB drives, SD cards, and digital cameras. Their maximum volume and file size constraints make them limited in use, however.

FAT32 became way too small for modern-day storage demands (consider high-resolution images and video). Microsoft has since moevd to the NTFS filesystem, but digital media devices didn't need the security features and overhead introduced with NTFS. Thus, these manufacturers lobbied Microsoft to develop a new filesystem.

This filesystem was exFAT - the Extensible FAT filesystem. This is the default for SD cards larger than 32GB, and has been adopted widely by most manufacturers of digital devices. exFAT supports a cluster size of 4KB to 32MB, and has a maximum file size/volume size of 128 PB. Some of the overhead from the FAT filesystem is reduced to make it lighter and more efficient. Per directory, it can hold 2,796,202 files at maximum.

**[Task 2, Question 1] How many addressable bits are there in the FAT32 filesystem?** - 28 bits

**[Task 2, Question 2] What is the maximum file size supported by the FAT32 filesystem?** - 4GB

**[Task 2, Question 3] Which filesystem is used by digital cameras and SD cards?** - exFAT

## [Task 3] The NTFS Filesystem

FAT is a relatively basic filesystem that can do the job when it c omes to organizing data. However, it doesn't offer much in terms of security, reliability, and recovery, and there are limitations when it comes to file and volume sizes. This led Microsoft to develop the New Technology File System (NTFS) to add these features. It was introduced with Windows NT 3.1 in 1993, an operating system that was primarily used in an enterprise/business setting. It wasn't until Windows XP that this became the norm for all versions of the Windows operating system.

NTFS has a system in place to keep track of changes to the metadata in the volume, which helps with recovering from a crash or data movement due to defragmentation. This is in the form of a log, found in `$LOGFILE` in the volume's root directory. NTFS is considered to be a journaling filesystem, since it will keep track of changes that are made that haven't been written to the filesystem yet.

NTFS also has access control capabilities that allow us to define the owner of a file or directory, as well as the permissions for each user.

NTFS can also keep track of changes made to a given file with Volume Shadow Copies - this allows a user to restore previous file versions for recovery, or to perform a system restore. Ransomware has been noted to delete shadow copies from victims to prevent this kind of recovery.

NTFS also provides support for Alternate Data Streams (ADS). Files are simply streams of data, organized in a filesystem. ADS allows files to have multiple streams of data stored in a single file. Browsers use ADS to identify files downloaded from the internet, and malware has been noted to hide their code in an ADS.

Similarly to FAT, NTFS makes use of a table to hold information about files - in this case, we have the Master File Table (MFT). This is a structured database that tracks objects stored in a volume. Some files within the MFT are particularly critical:
- `$MFT` is the first record of the volume. The Volume Boot Record (VBR) points to the cluster where it is located. This stores information about the clusters where all other objects present on the volume are located. This contains a idrectory of all files present on the volume.
- `$LOGFILE` stores the transactional logs of the filesystem, and can help maintain filesystem integrity when a crash happens.
- `$UsnJrnl` is the Update Sequence Number (USN) Journal. This can be found in `$Extend`, and it contains information about all files changed in the filesystem and why the change was made. This is also known as the "change journal."

MFT Explorer is yet another of Eric Zimmerman's tools, and it is used to explore MFT files. This can be done via the command line and via the GUI. In this room we will be making use of the CLI tool. We can access this with an elevated command prompt (i.e., one run as an administrator). The command for running it is `MFTECmd.exe`; running this on its own generates a help prompt, and depending on the command you use you can parse data from different files in the NTFS filesystem.

To parse the $MFT file on its own, we might run `MFTECmd.exe -f (PATH TO MFT) --csv (PATH TO SAVE OUTPUT)`. From there, you could use EZViewer to view the output of MFTECmd, or to view the other CSV files that will be generated in later tasks. It lists information about all the files present in the volume. Note that this program does not support `$LOGFILE` as of now.

For the first question, we can run the command `MFTECmd.exe -f C:\Users\THM-4n6\Desktop\triage\C\$MFT --csv C:\Users\THM-4n6\Desktop\csv` from an elevated command prompt in the EZTools folder. The EZTools will create directories if they don't exist for the output. Upon doing so, we can load our MFTECmd CSV output file in EZViewer.

![image](https://github.com/user-attachments/assets/64580b10-d3f8-4529-abf6-0fea7cd3bee0)

We're looking for the size of the file located at `.\Windows\Security\logs\SceSetupLog.etl`. We can find cells in EZViewer by using CTRL+F:

![image](https://github.com/user-attachments/assets/4af65b56-37aa-493c-9e81-732f6b21e348)

Clicking around a few times gives us the desired file. The number to the right is the file size.

![image](https://github.com/user-attachments/assets/2db9ceca-9b24-468d-b4b8-d3529b4de3c9)

**[Task 3, Question 1] Parse the $MFT file placed in `C:\users\THM-4n6\Desktop\triage\C\` and analyze it. What is the size of the file located at `.\Windows\Security\logs\SceSetupLog.etl`?** - 49152

For the next question, we'll want to rerun the same command as last time, however we'll replace the file name with `C:\Users\THM-4n6\Desktop\triage\C\$Boot`. This MFT file will contain information about the boot sector of the volume, giving us the answer to the next question:

![image](https://github.com/user-attachments/assets/9989cc73-28d3-40c1-8e05-8749926e5641)

**[Task 3, Question 2] What is the size of the cluster for the volume from which this triage was taken?** - 4096

## [Task 4] Recovering Deleted Files

Understanding filesystems is helpful for understanding how files are deleted, recovered, and ultimately wiped. We know now that a filesystem will store the location of a file on the disk in a table or database. So what happens when we delete a file?

When a file is deleted, the system deletes the entries that store the file's location on the disk. To the filesystem, it is now free "real estate" in which it may write new files or data. However, the contents of the deleted file will still be there as long as it is not overwritten by the filesystem or the disk firmware.

Similarly, we may find that there is data on the disk in unallocated clusters - these may potentially be recovered. If we wanted to recover this kind of data, we would have to understand the file structure for different file types, and then identify the specific file via data in a hex editor. This is not something we will cover - instead, we'll look at a tool that will help do the job for us.

It's worth noting what a "disk image" is first, though. A disk image is simply a bit-by-bit copy of a given disk drive. This saves all the data in a disk, including metadata, in a single file. Making images of the physical evidence in a forensic investigation is important - this helps ensure the data is not contaminated during the forensics process, and it allows us to copy the image files to other disks for easier analysis.

The tool we use in this room is Autopsy. There's a whole room dedicated to it, but we'll discuss how to use it for recovering deleted files now:
1. Open Autopsy - in the VM attached, it will be on the Desktop.
2. Click "New Case."
3. Enter a case name (in this case it may be whatever you want), and click Next.
4. Add any desired details in the Optional Information section - this could include case numbers and examiner information. Then click Finish.
5. Autopsy will then do some processing, and then allow you to select a host. For our case we'll leave this the default option and click Next.
6. From there, we click "Disk Image or VM File" in the "Select Data Source Type" screen, then click Next.
7. We point Autopsy to the location of the data source (our disk image/VM file). The disk image we use in this room is `usb.001` and it is located on the Desktop. Then we click Next.
8. We may then choose different ingest modules to set up. There are many to choose from, and they may be helpful to use in certain cases, but they also add overhead. We won't need to use any of the modules in this task, so we hit Deselect All and then hit Next.
9. Autopsy then loads the disk image, and then we can navigate through it.

The Data Sources pane on the left shows the data sources we have added to Autopsy - we may add more sources as needed. The File Views and Tags menus show what Autopsy has found after processing the data. We'll want to expand the Data Sources menu and click on the `usb.001` disk image.

On the right-hand pane we'll see the contents of the disk image. All files and folders present on the disk should be there, and in the lower-right-hand pane we'll see details about the selected files. We can view the data in hext format, view file metadata, and more.

Some files in the right-hand pane will have an "X" marked on them. This means that they were deleted. To recover a deleted file, we can right-click on it, and then click Extract File(s). We can specify where to put the extracted file, and then the file will be recovered.

Once we run through the steps outlined above in Autopsy, we can see the data on the `usb.001` disk image:

![image](https://github.com/user-attachments/assets/2be0f997-529c-4340-b4bf-6b7694b1c0d9)

At the bottom are the three deleted files. We can also access these files directly by going to File Views -> Deleted Files. We see the name of the other `xlsx` file that was deleted in the screenshot above.

**[Task 4, Question 1] There is another `xlsx` file that was deleted. What is the full name of that file?** - `Tryhackme.xlsx`

We also see a text file that was deleted in the above screenshot.

**[Task 4, Question 2] What is the name of the TXT file that was deleted from the disk?** - `TryHackMe2.txt`

Let's recover that text file. Right-clicking the text file in Autopsy gives us several options; we may extract the file by clicking Extract File(s). We can save this file anywhere we'd like - I simply chose the Desktop for simplicity's sake - and then view it as usual.

![image](https://github.com/user-attachments/assets/f3324389-8319-486d-a44a-e22b9d794d98)

**[Task 4, Question 3] Recover the TXT file from Question #2. What was written in this `txt` file?** - `thm-4n6-2-4`

## [Task 5] Evidence of Execution

Now let's see where to find artifacts present in the filesystem to do forensic analysis. We may chiefly be interested in any evidence of execution.

One place to find evidence of execution is prefetch files. When you run a program in Windows, it stores its information for future use, which can be used to load the program quickly in case it is frequently needed. We can find this information in Windows prefetch files, located in `C:\Windows\Prefetch`. These files have an extension of `.pf`, and they contain the last runtimes of the application, how many times the application was run, and any file or device handles were used by the file. This makes it a good source of information to see what was recently executed.

To parse these files, we can use the Prefetch Parser EZTool. In an elevated command prompt, you can run `PECmd.exe` to get a list of options. If you wanted to run the program on the file and save the results in a CSV, you can run `PECmd.exe -f (PATH TO PREFETCH FILES) --csv (PATH TO SAVE CSV)`. If you wanted to run it against an entire directory, run `PECmd.exe -d (PATH TO PREFETCH DIRECTORY) --csv (PATH TO SAVE CSV)`.

Another location worth checking is the Windows 10 Timeline. In Windows 10, a list of recently used applications and files is stored in an SQLite database called the Timeline, and this can be a good source of information about recently-used programs. It contains the application that was executed and its "focus time" (i.e., the time that it was actually used). It can be found at `C:\Users\(USERNAME)\AppData\Local\ConnectedDevicesPlatform\{RANDOM FOLDER}\ActivitiesCache.db`.

The WxTCmd.exe EZTool can be used to read the Timeline. Running it on its own provides help on how to use it, and if you just want to parse the Timeline, you'd run `WxTCmd.exe -f (PATH TO TIMELINE) --csv (PATH TO SAVE CSV)`.

Lastly, we can use Windows Jump Lists. Jump lists are used to help users go straight to their recently used files from the taskbar; you would make use of this if you right-clicked the application's icon in the taskbar to see a list of recently opened files in that application.

The data is stored in `C:\Users\(USERNAME)\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations`. It can include information about what apps were executed, the first time of execution, and the last time of execution against an AppID.

The JLECmd.exe EZTool can be used to parse jump lists. On its own, you get a list of options that can be used with the program, but you can run the command `JLECmd.exe -f (PATH TO JUMP LIST) --csv (PATH TO SAVE CSV)` to parse a jump list file.

For the first question, we'll want to check the Prefetch files. Prefetch tells us how many times the application was run, so this is a good place to start. The command we'll want to run from an (elevated) command prompt in the EZTools folder is `PECmd.exe -f C:\Users\THM-4n6\Desktop\triage\C\Windows\Prefetch\GKAPE.EXE-E935EF56.pf --csv C:\Users\THM-4n6\Desktop\csv`. 

(Here's your friendly reminder that Windows has a tab autocomplete function in Command Prompt, meaning you can type the first few characters of what you want to find and then press TAB to get the rest of the filename.)

Upon running this command, we can either view the output in Command Prompt itself, or we can view the CSV file that gets generated with EZViewer. In Command Prompt, we are told how many times this file was run:

![image](https://github.com/user-attachments/assets/46e88794-a43f-4bb8-93e1-c7daf4f30530)

**[Task 5, Question 1] How many times was `gkape.exe` executed?** - 2

We also see the last time this file was ran in the output above.

**[Task 5, Question 2] What is the last execution time of `gkape.exe`?** - 12/01/2021 13:04

The focus time of an application is given in the Windows 10 Timeline data, so we'll want to use `WxTCmd.exe`. We can run the command `WxTCmd.exe -f C:\Users\THM-4n6\Desktop\triage\C\Users\THM-4n6\Desktop\triage\C\Users\THM-4n6\AppData\Local\ConnectedDevicesPlatform\L.THM-4n6\ActivitiesCache.db --csv C:\Users\THM-4n6\Desktop\csv`. We'll then want to open the `THM-4n6_Activity.csv` file in EZViewer.

![image](https://github.com/user-attachments/assets/c6874e26-2268-4201-a7c2-693e530200eb)

There are multiple instances of notepad.exe in this CSV file, though we'll want the row with a start time of 11/30/2021 10:56. Looking at this row, and looking at the Duration column, gives us the focus time:

![image](https://github.com/user-attachments/assets/ccab0ae3-e3d1-4b2b-b553-747c6edd6b5f)

**[Task 5, Question 3] When `notepad.exe` was opened on 11/30/2021 at 10:56, how long did it remain in focus?** - `00:00:41`

To see programs that were used to open certain files, we would do well to check jump lists. In the given triage folder, there are multiple jump lists in a single directory. If we run the command `JLECmd.exe -d C:\Users\THM-4n6\Desktop\triage\C\Users\THM-4n6\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations\ --csv C:\Users\THM-4n6\Desktop\csv`, we get a lot of information. We can either read the Command Prompt output or check the generated CSV file in EZViewer.

It'll be easier to load the generated CSV into EZViewer. We can check the AppIdDescription and Path columns to see what opened `ChangeLog.txt`:

![image](https://github.com/user-attachments/assets/ba86bb15-2153-409b-adeb-9c49c43b2c5c)

**[Task 5, Question 4] What program was used to open `C:\Users\THM-4n6\Desktop\KAPE\KAPE\ChangeLog.txt`?** - Notepad.exe

## [Task 6] File/Folder Knowledge

Now what if we need to know what files or folders were opened? We can first take a look at shortcut files, which are automatically generated by Windows whenever a file is opened locally or remotely. These files give us information about the first- and last-opened times of the file, the path of the opened file, and some other information. They can be found in `C:\Users\(USERNAME)\AppData\Roaming\Microsoft\Windows\Recent\` or `Microsoft\Office\Recent\`. The LECmd.exe EZTool can be used to read shortcut files. You can run the tool on its own to see a list of commands, but if you just want to parse shortcut files, run the command `LECmd.exe -f (PATH TO SHORTCUT FILE) --csv (PATH TO SAVE CSV)`. The creation date of the shortcut file tells us when the file was first opened, and the modification time tells us the last time it was accessed.

We can also examine the Internet Explorer/Microsoft Edge browsing history, which may seem a little strange, but it can include files that were opened on the system (regardless of whether or not they were opened in the browser). The history can be accessed in `C:\Users\(USERNAME)\Appdata\Local\Microsoft\Windows\WebCache\WebCacheV*.dat`. The files that were opened appear with a `file:///*` prefix in the browsing history. We can use many tools to analyze Web cache data, but Autopsy does the job just fine:
1. Add a new data source. Select Logical Files.
2. Point Autopsy to the folder you want to analyze files from. For this task, we can use `C:\Users\THM-4n6\Desktop\triage\C`.
3. This time, for the ingest modules, you should Deselect All and then select Recent Activity. Then click Next.
4. To view Web history, expand the Data Artifacts menu in the left pane, and click Web History.
5. The Web History will appear in the right panel, and you can see what files were accessed from there.
6. If you need to view more information about the file accessed, you could click the Data Artifacts tab in the lower-right pane.

As we discussed in the previous task, we can use jump lists to see what files were opened recently and what programs were recently executed.

Remember, jump lists can be found in `C:\Users\(USERNAME)\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations`. For more information, see the end of the previous task.

For both of these questions, the JLECmd.exe CSV output from the previous task works quite nicely. We can find the `regripper` path in there, and the LastModified column will answer the first question for us:

![image](https://github.com/user-attachments/assets/32a7d842-d620-41bf-87ee-4905e6d6d492)

**[Task 6, Question 1] When was the folder `C:\Users\THM-4n6\Desktop\regripper` last opened?** - 12/1/2021 13:01

The CreationTime column above will answer the next question.

**[Task 6, Question 2] When was the above-mentioned folder first opened?** - 12/1/2021 12:31

## [Task 7] External Devices and USB Device Forensics

Sometimes we'll also want to look into what devices were connected to the computer. Whenever a new device is attached to the system, we can see that device's setup information in `setupapi.dev.log`, which can be found in `C:\Windows\inf\`. This log contains the device's serial number, and the first and last times the device was connected.

We can also look into shortcut files again, if needed. These are created automatically by Windows for files opened locally or remotely, and this can include information about connected USB devices. It can provide us with information about the volume name, type, and serial number.

We can find this information in `C:\Users\(USERNAME)\AppData\Roaming\Microsoft\Windows\Recent` or in `Microsoft\Office\Recent`. We parse it with `LECmd.exe`.

**[Task 7, Question 1] Which artifact will tell us the first and last connection times of a removable drive?** - `setupapi.dev.log`
