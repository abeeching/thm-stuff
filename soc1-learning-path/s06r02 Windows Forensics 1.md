# Windows Forensics 1

## [Task 1] Introduction to Windows Forensics

Computer forensics is essential to cybersecurity - sometimes you need to collect evidence of activities performed on computers. This is part of the wider digital forensics field, which focuses on analysis of evidence from digital devices in general. Digital forensics can be used for many situations - legal cases and private investigations/analysis.

Digital forensics can be used to solve criminal cases. The example given in the room is that of the BTK serial killer case - the case had gone cold for more than a decade when the killer started taunting police by sending out letters. He then sent a floppy disk to a local news station, which was taken into evidence by police. The floppy disk contained a Word document, and with metadata and other evidence, the police were able to pinpoint his location and arrest him.

Windows is the most-used desktop operating system - private users and enterprises tend to prefer it. It holds most of the desktop market share, so knowing how to perform forensic analysis on a Windows machine is important. Our focus in this particular room is on gathering information from the Registry and make conclusions based on that information.

Artifacts in digital forensics are essential pieces of information that provide evidence of human activity. In a real crime scene, artifacts may include fingerprints, buttons of shirts and coats, tools used to do the crime...in computer forensics, artifacts are used to trace a person's actions, and they are typically in places where typical users don't go to.

While this may raise concerns about the operating system spying on us (which are understandable), it turns out that Microsoft's efforts to improve the user experience give us a lot of artifacts we can use in forensic analysis. Typically, each system with the same Windows version has the same out-of-the-box experience, but users personalize their systems as they keep using them. This may include changing the Desktop layout, icons, bookmarks in the internet browser, usernames, installed applications and more.

Windows saves these preferences to make the computer more personalized, but they can also be used by forensics to identify activities performed on a system. These preferences are stored in the Registry, user profile directories, app-specific files, and more. It turns out that these features do have a good use!

**[Task 1, Question 1] What is the most used Desktop Operating System right now?** - Microsoft Windows

## [Task 2] Windows Registry and Forensics

The Registry is just a collection of databases that holds the system's configuration data. It can be about the hardware, software, or the user's information, and can include data about recently-used files, programs, and devices. This makes the Registry beneficial for forensics. The Registry can be accessed by running `regedit.exe`, a built-in utility to view and edit the Registry. You can access this utility by pressing Windows+R simultaneously, and then entering `regedit.exe` into the Run prompt that appears. There are other tools we can use to explore the Registry; we'll touch on them later.

The Registry contains keys and values. In `regedit.exe`, the folders are the keys, and the values are the data stored in the registry keys. A registry hive is a group fo keys, subkeys, and values stored in a single file on the disk.

There are five root keys in the Registry:
- `HKEY_CURRENT_USER` (HKCU): Contains root of the configuration information for the user who _is currently logged in_. This includes folders, screen colors, and Control Panel settings - anything that is associated with the user's profile.
- `HKEY_USERS` (HKU): Contains all actively loaded user profiles on the computer. HKCU is a subkey of this key.
- `HKEY_LOCAL_MACHINE` (HKLM): Contains configuration information specific to the computer, for any user.
- `HKEY_CLASSES_ROOT` (HKCR): A subkey of `HKLM\Software`. Makes sure that the correct program opens when you open a file by using Windows Explorer. Since Windows 2000, the information for this key has been stored under both HKLM and HKCU. The `HKLM\Software\Classes` key contains default settings for the local computer, and values in `HKCU\Software\Classes` override the default settings and can be applied to the logged-in user. HKCR merges these sources of the Registry, even for programs that were designed for earlier versions of Windows. If you write values to a key under HKCR and it already exists under HKCU, the system will store the information in HKCU.
- `HKEY_CURRENT_CONFIG`: Contains information about the hardware profile that is used by the local computer at system startup

To view properties of the values in each registry key, you can right-click them.

**[Task 2, Question 1] What is the short form for `HKEY_LOCAL_MACHINE`** - HKLM

## [Task 3] Accessing Registry Hives Offline

What if you don't have access to a live system? If you only have access to a disk image, you won't be able to use `regedit` on it, so you will need to know where the hives are located on the disk. Many of these hives can be found in `C:\Windows\System32\Config`. Some of the hives found here include
- DEFAULT: `HKEY_USERS\DEFAULT`
- SAM: `HKEY_LOCAL_MACHINE\SAM`
- SECURITY: `HKEY_LOCAL_MACHINE\Security`
- SOFTWARE: `HKEY_LOCAL_MACHINE\Software`
- SYSTEM: `HKEY_LOCAL_MACHINE\System`

There are also two other hives that contain user information can be found in the User Profile directory, which is `C:\Users\<USERNAME>` in Windows 7 and above. The hives are
- NTUSER.DAT: `HKEY_CURRENT_USER` (when the user logs in)
- USRCLASS.DAT: `HKEY_CURRENT_USER\Software\CLASSES`

The USRCLASS.DAT hive is located in `C:\Users\<USERNAME>\AppData\Local\Microsoft\Windows`. The NTUSER.DAT hive is simply found in `C:\Users\<USERNAME>`. Both are hidden files.

You can also make use of the AmCache hive, which is pretty important. The hive is located in `C:\Windows\AppCompat\Programs\Amcache.hve`, and it contains information on progams that were recently run on the system.

Other sources of forensic data are the registry transaction logs and backups. Transaction logs are effectively the journal of the registry hive, keeping track of changes. These are often used when writing data to registry hives - the latest changes to the registry may be stored here before being moved into the registry hives themselves.

Transaction logs for each hive are stored in `.LOG` files in the same directory as the hive itself. It'll have the same name as the hive itself, but the extension will be `.LOG` (for instance, `SAM.LOG` contains the transaction log for the SAM hive). If there are multiple transaction logs, their extensions will be numbered, e.g. `.LOG1`, `.LOG2`, and so on. It is necessary to look at these logs when performing registry forensics.

Registry backups are the opposite of transaction logs. These are backups of registry hives located in `C:\Windows\System32\Config`, and they are copied to `C:\Windows\System32\Config\RegBack` directory every ten days. This would be a good place to look if you suspect that some registry keys might've been deleted or modified recently.

**[Task 3, Question 1] What is the path for the five main registry hives - DEFAULT, SAM, SECURITY, SOFTWARE, and SYSTEM?** - `C:\Windows\System32\Config`

**[Task 3, Question 2] What is the path for the AmCache hive?** - `C:\Windows\AppCompat\Programs\Amcache.hve`

## [Task 4] Data Acquisition

In doing forensics, you'll either encounter a live system or an image taken of a system. You should practice imaging the system or copying the required data and performing forensics on it. The process is called *data acquisition*. In this room, we will focus on how to acquire registry data from a live system or disk image.

The forensically correct method of analyzing the Registry is to acquire a copy of the data and perform analysis on that. We can't directly copy registry hives from `%WINDIR%\System32\Config`, since it is considered restricted by Windows. In order to acquire these files, we need to use some tools.

KAPE is used for live data acquisition, including registry data. It's primarily a command-line utility, but it has a GUI component. We'll cover it more in a dedicated room, but for now we just need to know that CAPE can be used to extract Registry files.

Autopsy is another tool that can be used to acquire data from live systems or from disk images. You can add a data source, navigate to the files you want to extract, then right-click and select Extract File(s).

FTK Imager is similar to Autopsy - you can extract files from a disk image or a live system by mounting the disk image or drive in FTK. There is an option to Export Files in the toolbar at the top. You can also use the Obtain Protected Files option, but this requires being on a live system. This allows you to extract all registry hives to a location of your choosing, but you cannot copy the `amcache.hve` file, which is needed to investigate evidence of recently-ran programs.

In this room, we will be using a VM that already has the data extracted.

## [Task 5] Exploring Windows Registry

Once the hives are extracted, you can use a tool to view them (as in the Registry Editor). To read registry hives, you can use the following tools
- Registry Viewer: AccessData utility, similar to the Windows Registry Editor. Only loads one hive at a time, and does not take transaction logs into account.
- Registry Explorer: An Eric Zimmerman tool (EZTool) - the EZTools are useful for performing digital forensics and incident response. It can load many hives simultaneously and add data from transaction logs into the hive to make a "cleaner" hive with more up-to-date data. You can also check Bookmarks to get to forensically important registry keys.
- RegRipper: Takes a registry hive as input and outputs a report extracting data from forensically important keys and values. This is a text file that will show the results in sequential order. Has a CLI and GUI component. Does not take transaction logs into account, so you need to use Registry Explorer to merge transaction logs with the registry hives for a more accurate result in RegRipper.

## [Task 6] System Information and System Accounts

Now let's go over where to look when performing registry forensic analysis. The first step in forensic analysis is to gather system information, so we'll focus on getting system and account information from the registry.
- OS Version (`SOFTWARE\Microsoft\Windows NT\CurrentVersion`): Use to determine OS version from which the data was pulled through the registry.
- Current Control Set: (`SYSTEM\ControlSet00X` and `HKLM\SYSTEM\CurrentControlSet`): Hives containing machine configuration data, controlling system startup. Typically `ControlSet001` points to the control set the machine booted with, and `ControlSet002` points to the last-known good configuration. The `CurrentControlSet` is a volatile control set created when the machine is live, so you'll often want to refer to that hive.
  - If we need to figure out which set is the current control set, then we check `SYSTEM\Select\Current`. For the last known good control set, check `SYSTEM\Select\LastKnownGood`.
- Computer Name (`SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName`): Useful to check so that you can verify you're working on the correct machine.
- Time Zone Information (`SYSTEM\CurrentControlSet\Control\TimeZoneInformation`): Need to establish the timezone the computer is located in to help understand the chronology of events as they happened. Some data in the computer have their timestamps in UTC or GMT and others in the local timezone. Knowing the local timezone helps with establishing timeline.
- Network Interfaces (`SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces`): Gives a list of network interfaces on the machine we're investigating. Each interface is represented by a unique indentifier (GUID) subkey, containing values relating to the interface's TCP/IP configuraiton. Provides us with information like IP addresses, DHCP IP address, subnet mask, DNS servers, and so on. This can help you make sure that you're performing forensics on the correct device.
- Past Networks (`SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Unmanaged` and `Managed`): Past networks a given machine was connected to. Registry keys contain past networks as well as when they were last connected - for the latter, check the last write time of the key.
- Autoruns (`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Run` and `RunOnce`; `SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce`, `Run`, and `policies\Explorer\Run`): Programs or commands that were run when a user logs on.
- Services (`SYSTEM\CurrentControlSet\Services`): Gives information about services. If the `start` key is set to `0x02`, then the service is set to start at boot.
- SAM and User Information (`SAM\Domains\Account\Users`): SAM hive contains user account, login, group information. This key specifically contains relative identifiers (RIDs), number of times the user logged in, last login time, last failed login, last password change, password expiry, password policy, password hint, and any groups that the user is a part of.

For these questions, we'll be checking the screenshots posted in this room. If we check the first screenshot, we see that there is a key for the `CurrentBuildNumber` -- this will be our answer for the first question:

![image](https://github.com/user-attachments/assets/9636c982-42b5-4b34-83c5-b33573b9230f)

**[Task 6, Question 1] What is the current build number of the machine whose data is being investigated?** - 19044

The second screenshot contains the answer to the second question -- we just check the `LastKnownGood` key:

![image](https://github.com/user-attachments/assets/e8414c81-b6c4-4a50-9ed9-1cc049917175)

**[Task 6, Question 2] Which ControlSet contains the last known good configuration?** - 1

And the third screenshot has the `ComputerName`:

![image](https://github.com/user-attachments/assets/da9a6f9d-567b-499c-92d8-511696ad5bca)

**[Task 6, Question 3] What is the computer name of the computer?** - `THM-4n6`

The next screenshot contains the `TimeZoneKeyName`, which would give us the timezone information for the machine in the screenshots.

![image](https://github.com/user-attachments/assets/a8ea1a17-ce7d-4536-ad8f-73c2a6b23839)

**[Task 6, Question 4] What is the value of the `TimeZoneKeyName`?** - Pakistan Standard Time

The DHCP address can be found in the `DhcpIPAddress` key.

![image](https://github.com/user-attachments/assets/dc50d94e-82b1-461d-be8a-3793aab77ab8)

**[Task 6, Question 5] What is the DHCP IP address?** - `192.168.100.58`

The last screenshot of the task contains the information for all of the users. We look at the entry where the username is `Guest`. This gives us the RID, found under the `User Id` column:

![image](https://github.com/user-attachments/assets/a8eb3293-5b8c-48aa-9186-811b908900cf)

**[Task 6, Question 6] What is the RID of the Guest User account?** - 501

## [Task 7] Usage or Knowledge of Files and Folders

Now let's figure out how to examine recently-accessed files and folders. There are a few keys that can help us:
- Recent Files (`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs`): Windows Explorer typically shows a list of recently-used files. In Registry Explorer the files are sorted by Most Recently Used (MRU) - the MRU files are at the top of the list.
- Recent Files of a Specific Extension (`RecentDocs\<EXTENSION>`): There are different keys with different file extensions that can give us some insight into specific types of files.
- Office Recent Files (`NTUSER.DAT\Software\Microsoft\office\<VERSION>`): Microsoft Office maintains a list of recently-opened documents, which can be found here. There are subkeys for each program. Note that VERSION could be 14.0 for Office 2010, 15.0 for Office 2013, and 16.0 for Office 2016 and beyond.
- Microsoft 365 Recent Files (`NTUSER.DAT\Software\Microsoft\Office\VERSION\UserMRU\LiveID_XXXX\FileMRU`): From Office 365 and beyond, the location of the recent files are tied to the user's (hashed) Live ID, which nowadays is just a Microsoft account's email address/password, etc.
- ShellBags (`USRCLASS.DAT\Local Settings\Software\Microsoft\Windows\Shell\Bags` and `BagMRU`; `NTUSER.DAT\Software\Microsoft\Windows\Shell\BagMRU` and `Bags`): When users open a folder, it opens in a specific layout, and this layout may be changed according to user preferences. The layouts may differ for different folders. Regardless, information about the Windows shell is stored and can be used to identify the most recently used files and folders. This information can be found in user hives, and ShellBags information can be read easily with Shellbag Explorer from the EZTools suite - just point it to the hive file.
- Open/Save and LastVisited Dialog MRUs (`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\OpenSavePIDlMRU` and `LastVisitedPidlMRU`): When you open and save files, a dialog box asks you to choose the location to save to or open from. Windows will remember this location, suggesting we can use it to find the most recently used files.
- Windows Explorer Address/Search Bars (`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths` and `WordWheelQuery`): Another way to identify recent activity - just look at the searches performed/paths typed in Explorer.

We can check the first screenshot of the task to get the answer to the first question. At the top, we see information about EZtools being opened:

![image](https://github.com/user-attachments/assets/c218948d-566d-49cb-a8e1-9e7c564e8d97)

**[Task 7, Question 1] When was EZtools opened?** - `2021-12-01 13:00:34`

Looking at the second screenshot, we see information about the `My Computer` folder:

![image](https://github.com/user-attachments/assets/81549bdb-4431-46e3-a162-8eeb30db98ea)

**[Task 7, Question 2] At what time was My Computer last interacted with?** - `2021-12-01 13:06:47`

And if we check the third screenshot, we see information about a file being opened/saved in Notepad. The information below can be used to answer the last two questions in this task.

![image](https://github.com/user-attachments/assets/d6f98a23-2925-4723-b17e-24e7c2748bfb)

**[Task 7, Question 3] What is the absolute path of the file opened using `notepad.exe`?** - `C:\Program Files\Amazon\Ec2ConfigService\Settings`

**[Task 7, Question 4] When was this file opened?** - `2021-11-30 10:56:19`

## [Task 8] Evidence of Execution

There are a few places we can look to get evidence of program execution.
- UserAssist (`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist\{GUID}\Count`): Used to track apps launched by the user using Windows Explorer for statistical purposes. Contains info about programs launched, time of launch, number of times executed. Does not include programs run in the command line. Mapped to user's GUID.
- ShimCache (`SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache`): Mechanism used to keep track of app compatibility with the OS and tracks all apps launched on the machine, used by Windows to ensure backwards compatibility. AKA Application Compatibility Cache. Stores filename, filesize, and last modified time of executables. Readable with Eric Zimmerman's AppCompatCache Parser, with the command `AppCompatCacheParser -- csv (PATH TO OUTPUT) -f (PATH TO SYSTEM HIVE) -c CONTROL SET TO PARSE`. A CSV file is output, which can be viewed with EZViewer.
- AmCache (`C:\Windows\appcompat\Programs\Amcache.hve` -> `Amcache.hve\Root\File\{Volume GUID}\`): Artifact related to ShimCache, storing additional data related to program execution, including execution path, installation, execution, deleted times, SHA1 hashes of programs. Information about last-executed programs can be found in the hive path above.
- BAM and DAM (`SYSTEM\CurrentControlSet\Services\bam\UserSettings\{SID}` and `\dam\UserSettings\{SID}`): Background Activity Monitor (BAM) and Desktop Activity Moderator (DAM). Keeps tabs on background app activity and optimizes power consumption of the device, part of Modern Standby system. These areas contain information about last-run programs, full paths, and last execution time.

The first screenshot contains programs that were launched and a "Run Counter" to keep track of how many times the programs were run. This information can be used to answer the first question.

![image](https://github.com/user-attachments/assets/c3e6aefb-5103-4963-981c-ff65a9be881f)

**[Task 8, Question 1] How many times was the File Explorer launched?** - 26

The remainder of the questions can be answered by referring to the information in the task.

**[Task 8, Question 2] What is another name for ShimCache?** - AppCompatCache

**[Task 8, Question 3] Which of the artifacts also saves SHA1 hashes of the executed programs?** - AmCache

**[Task 8, Question 4] Which of the artifacts saves the full path of the executed programs?** - BAM/DAM

## [Task 9] External Devices and USB Device Forensics

Something we often need to do when performing forensics is identifying connected USB or removable devices that were attached to the machine. There are a few ways to get this information in the Registry:
- Device Identification (`SYSTEM\CurrentControlSet\Enum\USBSTOR` and `USB`): Keep track of USB keys plugged in and can be used to identify unique devices, as well as the time the devices were plugged in.
- First/Last Times (`SYSTEM\CurrentControlSet\Enum\USBSTOR\Ven_Prod_Version\USBSerial#\Properties\{83da6326-97a6-4088-9453-a19231573b29}\####`): Tracks the first and last time the device was connected and the last time the device was removed. You can replace the `####` with `0064` for the first connection time, `0066` for the last connection time, and `0067` for the last removal time.
- USB Device Volume Name (`SOFTWARE\Microsoft\Windows Portable Devices\Devices`): Device name of the connected drive. Compare the GUID displayed here with the Disk ID seen on keys mentioned in the device identification section. Correlate names with unique devices.

For this first question, we can take a look at the first screenshot in this task to get the serial number:

![image](https://github.com/user-attachments/assets/a83bc147-c8fd-45b9-b463-c11e15bf4f2f)

We are looking at the Serial Number column.

**[Task 9, Question 1] What is the serial number of the device from the manufacturer "Kingston"?** - `1C6f654E59A3B0C179D366AE&0`

The name is given in the Device Name column above.

**[Task 9, Question 2] What is the name of this device?** - Kingston Data Traveler 2.0 USB Device

The friendly name is given in the last screenshot. You'll need to correlate the Disk Id information in the first screenshot with the GUID in the last screenshot. In this case, we're looking at the information in the first row:

![image](https://github.com/user-attachments/assets/523836af-627b-4599-8f13-689b89c4a282)

**[Task 9, Question 3] What is the friendly name of the device from the manufacturer "Kingston"?** - USB

## [Task 10] Hands-on Challenge

Now we get to practice all of this Registry forensics stuff. We have access to a VM with the Registry Explorer, EZViewer, and AppCompatCacheParser EZtools, as well as a triage folder containing the information we need to investigate. The scenario is that one of the desktops in an organization has been accessed by someone unauthorized. This system had multiple user accounts, even though the computers are only supposed to have one user account. There's also suspicions that the system was connected to a network drive, and that a USB was connected to the system.

Note that when using RegistryExplorer, it will warn us that the hives are "dirty." This just means that we need to integrate the transaction logs into the hives - point it to the `.LOG1` and `.LOG2` files with the same filename as the registry hive. From there, we can just follow the instructions in RegistryExplorer to save the cleaned hive.

If we're looking for user account information and how often a user account was logged into, we would want to check the SAM hive, found in `Desktop\triage\C\Windows\System32\config\SAM`. Once we load it in Registry Explorer, we can see the user account information in `ROOT\SAM\Domains\Account\Users`. The default Windows accounts are the Administrator, Guest, and WDAGUtilityAccount. The remaining accounts are user-created:

![image](https://github.com/user-attachments/assets/856e2ec4-335a-4b67-9e3e-6fdd9ddca064)

**[Task 10, Question 1] How many user created accounts are present on the system?** - 3

The Total Login Count column gives us the information, and it can be found in the same key as above. We see that there are 0 login counts for the `thm-user2` account.

![image](https://github.com/user-attachments/assets/c0898d12-00c4-4f08-b0c0-778142e180a4)

**[Task 10, Question 2] What is the username of the account that has never been logged in?** - `thm-user2`

The Password Hint column, also in the same key, tells us the password hint for each user. In this case, we can get the password hint for the `THM-4n6` account:

![image](https://github.com/user-attachments/assets/3ebe36e5-a8f0-4f4f-8f2a-33a48b9445da)

**[Task 10, Question 3] What is the password hint for the user `THM-4n6`?** - count

Now we want to find files that were accessed. To do so, we'll want to load in the `NTUSER.DAT` file. In this case, there is only one, located at `Desktop\triage\C\Windows\Users\THM-4n6`.

This was the first time I was prompted to load in transaction logs, so we do so:

![image](https://github.com/user-attachments/assets/1fe58b3b-16d3-4658-b16a-f97e0d147c9f)

We want to select all logs:

![image](https://github.com/user-attachments/assets/ff7489ad-0961-41b3-834e-5beed90d2d53)

And then we can save the clean hive in any location we'd like, and then load it:

![image](https://github.com/user-attachments/assets/d48c0b79-68af-436d-8852-1cec8b0d924a)

We don't need to load the dirty hive at this point. Let's presume that the user opened the changelog file fairly recently, enough so that Windows would have it listed as a recently-accessed document. We can navigate to `ROOT\SOFTWARE\Microsoft\Windows\Explorer\RecentDocs`. If we'd like to, we can even investigate the `.txt` subkey. This gives us the information about when the file was last opened:

![image](https://github.com/user-attachments/assets/609a58fb-4ca6-4b8e-8b21-01b0962d12be)

**[Task 10, Question 4] When was the file `Changelog.txt` accessed?** - `2024-11-24 18:18:48`

We could answer the next question by checking in NTUSER.DAT, going into `ROOT\SOFTWARE\Microsoft\Windows\Explorer\UserAssist` and checking through the different GUIDs. The GUID `{CEBFF5CD-ACE2...}` is the one we're interested in, and if we check the `Count` subkey we see the Python installer being ran.

![image](https://github.com/user-attachments/assets/230be7d1-4d21-4f0f-804f-93bf0c54c4ff)

We could also attempt to use `AppCompatCacheParser`, but it didn't seem to pick up the exact file path shown above and below for me (unless I missed something!).

**[Task 10, Question 5] What is the complete path from where the Python 3.8.2 installer was run?** - `Z:\setups\python-3.8.2.exe`

This last question will be a multi-step process. We'll need to load in the SYSTEM and SOFTWARE hives first. First, we'll want to check the GUID of the device with the friendly name "USB." To do so, we go into SOFTWARE and check `ROOT\Microsoft\Windows Portable Devices`.

![image](https://github.com/user-attachments/assets/1843c891-2606-40b3-8d27-0df9e9bed032)

We see that the "USB" device has a GUID of `{E251921F-4DA2-11EC-A783-001A7DDA7110}`. Now we jump into the SYSTEM hive and look in `ROOT\ControlSet001\Enum\USBSTOR`:

![image](https://github.com/user-attachments/assets/1a40d339-4b7e-44f9-a751-4635f8127c99)

We see that the Kingston DataTraveler 2.0 USB Device has the GUID we found earlier. We'll want to drill into that particular key for the details. We check `Disk&Ven_Kingston...\1C6F654E59A...\Properties\{83da632...}\0066`, since this contains the Last Connected information.

![image](https://github.com/user-attachments/assets/d7353198-20e8-4b2f-b69b-2a0aa7549817)

**[Task 10, Question 6] When was the USB device with the friendly name "USB" last connected?** - `2021-11-24 18:40:06`
