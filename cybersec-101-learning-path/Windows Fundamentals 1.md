# Windows Fundamentals 1

## [Task 1] Introduction to Windows

There's an extremely good chance that you are using Windows right now! It's a complex product with many files and utilties, and our goal in the Windows Fundamentals sequence of rooms is to give a general overview of these things. You may well be familiar with some of this stuff already, but it's still worth diving into these rooms... you might pick up on something you didn't know!

The VM in this room opens in Split-Screen View, though it may be worthwhile trying to connect to it via Remote Desktop for practice. If you're in the AttackBox or on a virtual machine of your own connected to the THM VPN, then you can pull up some Remote Desktop software and connect with the credentials provided in the task.

## [Task 2] Windows Editions

Windows has been around since around 1985, and since then it has established itself as the dominant operating system both at home and in the corporate setting. It is because of this that adversaries are most interested in targeting folks running Windows - there's a lot of potential targets in doing so!

Here's a brief history of the OS starting from Windows XP (there were earlier versions, of course, but Windows XP is when Microsoft sort of "unified" the designs for their home and enterprise offerings):
- Windows XP: A beloved, popular version of the operating system that was around for quite a while.
- Windows Vista: An overhaul of the operating system which had many complaints levied against it (including issues in transitioning to the new system, certain features including those meant for security, and so on...)
- Windows 7: In my own assessment, this is believed to be "Windows Vista but better" - that said, there were many issues in trying to transition people to the new system, mainly due to issues with migrating to Vista.
- Windows 8: This was that weird time Microsoft decided it would try to create a mobile interface for desktop computers. Was met with mixed reception.
- Windows 10: This (kind of) brought Windows back to its standard UI design, while retaining some functionalities from Windows 8. Support set to end roughly next year as of writing.
- Windows 11: The current version of the Windows OS.
- Windows Server: This is the server OS offering from Microsoft - releases typically follow the design of the home versions. Currently we're on Windows Server 2022, though Windows Server 2025 is on its way and is designed similarly to Windows 11. The VM in this room uses Windows Server 2019.

Windows comes in two flavors nowadays - Home edition and Pro edition. There are differences between the two versions, but you can think of the Pro offering as just having more functionality than the Home offering. Pro, among other things, lets you set up connections with domains and allows you to use encryption features like BitLocker.

**[Task 2, Question 1] What encryption can you enable on Pro that you can't enable in Home?** - BitLocker

## [Task 3] The Desktop (GUI)

When you log into a Windows machine, you are greeted with the Desktop. You'll usually have to get past the login screen first, where you enter valid credentials (e.g., a username and password of either an existing Windows account or an account stored in an Active Directory/domain environment). Once you do, you're in.

The Desktop proper is where shortcuts to programs, folders, and files go. Folks will either organize their desktops nicely so they can easily get to where they want to go, or they will simply throw files and folders around with no rhyme or reason. Regardless, the point is that stuff on the Desktop is put there for quick access.

You can make changes to the Desktop to fit whatever needs you may have at any point. Simply right-click on the Desktop to have a contxt menu appear. From here, you can change the sizes of icons, specify how you want to arrange them, copy and paste items, create new items, and adjust settings. There are two options for settinsg:
- Display Settings: You can make changes to screen resolution and orientation, as well as configure multi-screen setups.
- Personalize: You can change the wallpaper (Desktop background image), fonts, themes, colors, and more.

Next comes the Start Menu. In what is probably their most elegant design decision, Microsoft made a menu that contained commonly-accessed and most-useful programs and options, and they labeled it "Start." Over the years, this label has disappeared (and in a weird moment in history, the menu itself was dropped in Windows 8 in favor of a mobile screen), but regardless the overall purpose has not changed. In Windows 10, the Start Menu is broken up into various sections.
- The leftmost side of the Start Menu - which contains the Power and Settings buttons, among other things - gives you shortcuts to actions you can perform in your account or session. This may include making changes to your account, or locking your screen, or signing out. There are also other options to access Documents, Pictures, and Settings. The Power button allows you to shut the computer down, disconnect from Remote Desktop, or restart as needed.
- The Recently Added section at the top of the Start Menu shows all recently added apps and programs. Just below are the programs and apps that have been installed, ordered alphabetically. If you have a lot of programs, you can click on the letter headings to pull up an alphabet grid and jump to any letter in the Start Menu.
- The right-hand side of the Start Menu contains icons for specific apps, programs, and utiltiies - we call these tiles. Some tiles are set by default, and if you right-click the tiles, you can pull up a context menu that allows you to adjust the tile's size, whether or not it is pinned, its properties, and more. If you want to add any programs to this section of the start menu, right-click it in the list and click Pin to Start.

The Taskbar is located at the bottom of the screen. It's got all the icons of any currently-running programs as well as any programs that you can launch that have been put there. You can right-click the Toolbar to enable and disable components as you see fit - this pulls up a context menu with various options. If you hover over programs that are curently running, you get a preview thumbnail along with a tooltip that tells you what's happening (for instance, if you have multiple browser instances open, the tooltip will tell you what page each browser instance is on at that moment). If you close the programs, they will disappear from the taskbar unless they have been pinned there.

The Notification Area, typically located at the bottom right of the screen, is where the date and time are displayed among other things. You might see volume and network icons, depending on how things are set up. You can go to the Taskbar Settings (in the right-click menu from the Taskbar) to change what icons appear. You should refer to Microsoft's documentation for further information.

The answers to the questions below will be found by right-clicking around in the Taskbar. See the screenshot below.

![image](https://github.com/user-attachments/assets/44fe7db6-52fa-46a8-99f2-c9456644e5dc)

**[Task 3, Question 1] Which selection will hide/disable the Search box?** - Hidden

**[Task 3, Question 2] Which selection will hide/disable the Task View button?** - Show Task View button

Right-clicking the Notification Area icon, which is that little speech bubble in the bottom right, pulls up this menu:

![image](https://github.com/user-attachments/assets/e1218035-ace3-49b4-85ae-3fcc8abfef99)

That first item in the list will be visible in the Notification Area.

**[Task 3, Question 3] Besides Clock and Network, what other icon is visible in the Notification Area?** - Action Center

## [Task 4] The File System

Nowadays, Windows operates in the New Technology File System (NTFS). Older verisons would use the File Allocation Table filesystems (FAT16, FAT32, etc.), and they would also use the High Performance File System (HPFS). FAT is still in use in various forms today - just in USBs and MicroSD devices rather than on drives in Windows machines. Developments and demands in technology necessitated newer filesystems, so here we are.

NTFS is a journaling filesystem, which means that if the system fails, the filesystem can repair folders and files on a disk using information stored in a log file. FAT couldn't do this. It also couldn't do these things:
- Support files larger than 4 GB
- Allow people to set permissions on folders and files
- Compress folders and files
- Perform encryption

If you need to know what filesystem is in use, you can open the File Explorer, navigate to This PC, and then right-click the drive the OS is installed on. Clicking Properties will give you a dialog box, showing you what filesystem you're using.

In NTFS, the permissions that can be set on a file or folder are:
- Read: Viewing and listing files/subfolders; viewing and accessing file contents
- Write: Adding files and subfolders; writing to a file
- Read and Execute: Read permissions for folders plus executing files within them - this is inherited by files and folders inside a folder; Read permissions for files plus executing the file
- List Folder Contents: Permits a user to view and list files/subfolders as well as execute files. This is inherited by folders only.
- Modify: Permits reading, writing of files and subfolders plus deletion of the folder; permits reading, writing, and deletion of a file
- Full Control: Permits all permissions on folders or files

To view the given permission for a file or folder, you can
1. Right-click the file or folder you want to check permissions for.
2. Select Proeprties from the menu.
3. Click the Security tab.
4. Select the user/computer/group from the Group or User Names section that you want to view permissions for.

NTFS also allows for Alternate Data Streams (ADS), which is a file attribute specific to it. Every file has at least one data stream - the `$DATA` data stream - and ADS lets files contain more than one stream of data. You don't get to see this in Windows/File Explorer typically, but there are third party executables and PowerShell commands that let you see this. This is significant from a security perspective, since malware authors have been known to hide stuff in ADS. That said, there are some perfectly valid reasons to use ADS. ADS can be used, for instance, to identify files that were downloaded from the Internet.

**[Task 4, Question 1] What is the meaning of NTFS?** - New Technology File System

## [Task 5] The Windows\System32 Folders

The `C:\Windows` folder is typically the folder that contains the Windows operating system. This doesn't necessarily have to be in the C drive - it can reside in any other drive and in any other folder (technically), though this is the default setup.

Windows uses environment variables to store information about the OS environment, including the OS path, among other things. So, Windows will see `%windir%` and treat it as the folder that contains the Windows OS - i.e., it interprets `%windir%` as `C:\Windows`. This can be useful if you're trying to type out file paths.

The Windows folder contains many other folders, one of the most important being `System32`. This holds critical operating system files, which means that if you're ever told to go into here, you should exercise extreme caution. If you accidentally delete a file or folder here (or more egregiously, delete the entire folder), you can make Windows ununsable. Many of the tools that will be covered in this sequence will show up here.

**[Task 5, Question 1] What is the system variable for the Windows folder?** - `%windir%`

## [Task 6] User Accounts, Profiles, and Permissions

On your average Windows local system, you will have two types of users:
- Administrators, who can make changes to the system such as adding/removing users, modifying groups and settings, and so on, and
- Standard Users, who can only make changes to folders or files that belong to them.

In the VM, you will be logged in as an Administator. There are a few ways to see which accounts are on the system. One such way is to go to the Start Menu and type `Other User`. This will pull up a shortcut to access System Settings -> Other Users. From here, you will have the option to add someone else to the PC if you are an Administrator, and you will be able to click any user acounts and change account types (e.g. from Admin to Standard User and vice versa) or remove them.

When user accounts get created, profiles are created for them. They are put under `C:\Users` as subfolders, e.g. one user's folder may be in `C:\Users\John`. The user profile is created when the user logs in for the first time; they will see messages on the login screen, including the User Profile Service. Right before they log in proper, they will see a dialog box informing them that personalized settings are being configured for various programs.

User profiles all contain these folders, among others:
- Desktop
- Documents
- Downloads
- Music
- Pictures

If you have access to it, you can also check user account information by going to Local User and Group Management. There are a few ways to access this utility:
- Right click the Start Menu and click Run. Type `lusrmgr.msc` and click Run.
- Go to the Other Users setting from earlier. If you click Add Someone Else to this PC, you will go to Local Users and Management.

This utility contains two folders, Users and Groups. The Groups folder contains the names of all local groups, including a brief description of each. Each group has permissions set to it, and users can be added to groups by the Administator. If a user is added to a group, they will receive the permissions of that group. Users may be in many groups.

We will access the Local User and Group Management utility using one of the methods described above to complete the questions in this task. Here's what we see when we click on Users.

![image](https://github.com/user-attachments/assets/ff640122-066b-477a-ae18-d8bbbf7398e6)

The other user account is the one that we're not logged into - it stands out among the rest, which are default accounts in Windows.

**[Task 6, Question 1] What is the name of the other user account?** - `tryhackmebilly`

One way to see what groups a user is in is to right-click the user and go to Properties. Then you can go to the Member Of tab to see what groups they belong to:

![image](https://github.com/user-attachments/assets/91e0832d-6c12-4586-9aae-f332df194309)

**[Task 6, Question 2] What groups is this user a member of?** - Remote Desktop Users,Users

**[Task 6, Question 3] What built-in account is for guest access to the computer?** - Guest

The question below refers to the `tryhackmebilly` account. The description is given in the Local Users and Groups interface.

**[Task 6, Question 4] What is the account description?** - `window%Fun1!`

## [Task 7] User Account Control

Many users are logged into Windows systems as local administrators, meaning they can freely make changes to the system. Such privileges are not required for users who just need to browse the Internet or work on documents. This actually leads to a bit of an issue - if malware infects the system and gains control over the user's account (more formally, it runs in the context of the local administrator), then it will have administrative privileges too, giving it the ability to make damaging changes to the system.

Thus, Windows introduced User Account Control (UAC) starting in Windows Vista. It has since been built in. When an Administrator logs into a system, the current session does NOT run with elevated permissions. If something needs higher permissions to run, then the user is prompted if they want to let it run. Note that this does NOT apply for the built-in local Administrator account.

Programs that require administrator privileges to run will be marked with a little shield icon for standard users. This indicates that a UAC prompt will appear. Double-clicking the program at this point opens the UAC prompt, and asks you for an administrator's username and password. If the password is incorrect, or if the password does not get entered, UAC closes and the program does not run. This (ideally) reduces the likelihood that malware can compromise your system.

**[Task 7, Question 1] What does UAC mean?** - User Account Control

## [Task 8] Settings and the Control Panel

There are two places to go if you want to make changes to system settings - the Settings Menu and the Control Panel. The Control Panel is there for compatibility reasons, I suppose - it was _the_ go-to place to make system changes for the longest time, including adding printers and changing/removing programs. In Windows 8, the Settings Menu was introduced, and it has been carried forward into later versions of the OS. This is the primary location a user will be sent if they need to change the system.

To access these, you can use the Start Menu. Control Panel is where you will go if you need to adjust more complex settings. Oftentimes you'll start in the Settings Menu, and then a link there will send you to the Control Panel.

If you're not sure where to go to change a certain setting, a good strategy is to simply search for what setting you need to change. If you need to make changes to the wallpaper, you could search "wallpaper" and see where Windows directs you to.

For this question, we can search for the Control Panel in the Start Menu, and then click the "View by: Category" button at the top-right. Here, we can set it to "Small icons," and from there we can just see what's at the bottom of the Control Panel.

![image](https://github.com/user-attachments/assets/54c291f1-bf08-4b13-a525-da01af7ee840)

**[Task 8, Question 1] In the Control Panel, change the view to Small Icons. What is the last setting in the Control Panel view?** - Windows Defender Firewall

## [Task 9] Task Manager

The last thing we will look at is the Task manager, which gives us information about currently running applications and processes. Other information is also available as you need it under the Performance tab. You can access the Task Manager by right-clicking the Taskbar and clicking Task Manager, or by pressing CTRL + SHIFT + ESC simultaneously.

When you open Task Manager, it'll start in Simple View - it doesn't give much more information than what apps are running. To view more information, click "More details" in the bottom left. From here, you can see various information regarding resource usage by different programs and processes, computer performance, services in use, logged-in users, and more. A lot of this stuff is described in further detail in the Core Windows Processes room, which is part of the SOC 1 pathway.

**[Task 9, Question 1] What is the keyboard shortcut to open Task Manager?** - Ctrl+Shift+Esc

## [Task 10] Conclusion

This room served as a general overview of some basic Windows functionalities. Our discussions on the features were quite general, and there's more to explore if you look into the Windows documentaiton. In later rooms in this sequence, we'll explore some more advanced functionalities including the management console and security tools.
