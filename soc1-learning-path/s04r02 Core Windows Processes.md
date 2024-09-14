# Core Windows Processes

## Purpose of the Room
Attacks on any system will result in deviations from the norm. A lot of the time, these deviations will be picked up on by antivirus and EDR (endpoint detection and response) tools. However, this isn't always the case!
When you have to defend against an attack on a system, you may very well have to rely on your knowledge of what the norm is so you can identify malicious processes.

## [Task 2] Exploring Task Manager
The Windows Task Manager is the GUI utility that allows you to see what's happening on a system. From the outset, it's a fairly barren interface:

![image](https://user-images.githubusercontent.com/56741029/233916252-9e9d79c0-8a90-49fe-a483-15fdcc96096d.png)

This is Task Manager's simplified view. It allows you to see what programs are currently running on a system. It helps if that's all you need to see, but there are plenty of times where you'll want to know more information.
Hence, you'll want to click on the More details button in the bottom right.

![image](https://user-images.githubusercontent.com/56741029/233916486-c4124e65-399b-40d1-bae8-7580d1f64dae.png)

(NOTE: Task Manager looks quite different on Windows 11, which is the newest iteration of Windows at the time of writing. It's still possible to follow along with that version; the "tabs" will be to the left.)

That's more like it! By default, Task Manager has five tabs:
- Processes
- Performance
- Users
- Details
- Services

On Windows 10, you can add and remove tabs as you wish. Thus, if you're following along in this room using a different machine, you may see different tabs.

In the *Processes* tab, you will see your apps split as follows:
- Apps
- Background processes
- Windows processes

By default, you'll only have a few columns of data to work with. You may right-click any of the column headers to see more options, and you can add and remove columns as you wish. The columns are, as follows:
- Name: It tells you the name of the process. Straightforward.
- Publisher: Whoever put together the program or the file.
- PID: The Process Identifier Number. Windows gives each program that starts a unique PID. A program may have multiple running processes; each of those get their own PID.
- Process Name: The filename of the process. For instance, Task Manager is Taskmgr.exe.
- Command Line: This is what you would type into Command Prompt to begin the process.
- CPU: The percentage of the CPU (processing power) being used by the process.
- Memory: The amount of memory being used by the process.

Other options include being able to show the disk/network/GPU/power usage. Chances are good that you've seen this stuff before! I certainly have - I've played enough video games that barely run on my computer.

Now let's move onto the Details tab. The room suggests you sort the PID column so the PIDs are in ascending order. We can add more information about these processes, such as Image path name and Command line. If you see a process running with an expected PID but an unexpected path name/command line, then it's worth checking out!

The other tabs give you a good overview of what's happening on the computer - how it's performing in general, the users that are on right now, services that have started and stopped, etc.

Task manager's super helpful when you're getting started, but there's an important detail that we miss if we just use Task Manager - parent processes. Processes are capable of starting other processes as needed, and sometimes we can spot abnormalities in that.

We can use other tools such as Process Hacker and Process Explorer to find these parent processes! Process Explorer, in particular, is covered in more detail in the Sysinternals room, which I will have notes written up on at some point(tm).

### Analyzing Processes on the Windows Command Line

Now, sometimes, you'll have to do some troubleshooting and analysis without a GUI. Maybe you're working on the computer in question remotely, or you can't get into the GUI for some reason. Thus, it's also good to learn about some tools in Command Prompt that help us with process analysis.
- tasklist: You can think of this as a command-line Task Manager. You get a list of processes (Image Names), PIDs, session names and numbers, and memory usage. A good place to start!
- Get-Process: The PowerShell equivalent to tasklist. On its own, you can use it to list all of the processes running on a system. This tells you some more information than tasklist does, like how many handles the process has opened or the amount of nonpaged and paged memory in use. You can also use this to get all of the data about a set of processes, filter processes by certain criteria, and so on.
- ps: An alias for Get-Process in PowerShell.
- wmic: The Windows Management Instrumentation Command line. There's a LOT you can do with this, but for our purposes you can use wmic process list to get a list of the processes being run. Other commands exist that allow you to start and terminate applications, get process identifiers, and find specific processes.

## [Task 3] System
This is a very special process that will spawn some other processes - we'll cover those in just a moment.

In Windows, PIDs are typically assigned randomly. This is not the case with System - it is always the process with PID 4. It runs in kernel mode (read ahead for more details on what that means).

Properties of System, gleamed from Process Hacker:
- Image Path - C:\Windows\system32\ntoskrnl.exe (NT OS Kernel)
- Parent Process - System Idle Process (0)
- Number of Instances - 1
- User Account - Local System (NT AUTHORITY\SYSTEM)
- Start Time - should be at system boot time

Thus, you can use these clues to determine if System has been tampered with:
- System having a parent process other than System Idle Process (0). If you check the properties of the System process without Process Hacker, you shouldn't see anything listed as its parent.
- System running with multiple instances. There should only be one!
- System having a PID other than 4.
- System not running in Session 0.

**[Task 3, Question 1] What PID should System always be? - 4**

### User vs. Kernel Mode

Consider this a simplified overview of the concept.

Generally speaking, operating systems have two modes for programs to operate in. User programs will generally operate under User Mode (affectionately called Userland by some, if I remember correctly). User mode programs will do what they are programmed to do until either (a) they're done, or (b) they need to start messing with memory and other sensitive operating system internals.

Giving user programs the ability to mess with memory is a Really Bad Idea. Programs could easily mess with the memory of other programs, or they could just waste time on the operating system. So, when a user-mode program wants to mess with that stuff, it reaches out to the operating system and asks it to do the work for them. The OS obliges and enters kernel mode.

You can think of kernel mode as a privileged mode for operating system stuff. When programs are doing stuff in kernel mode, they are able to mess with those sensitive operating system internals discussed earlier. So, the OS does what it needs to do in kernel mode, and then it lets the user-mode program get back to its work. In the ideal world, nothing breaks! Yay!

In the context of our current discussion, System is a kernel-mode process (you could probably tell given that its image path is, well, the NT OS Kernel!).

## [Task 4] System --> smss.exe

Once System gets its faculties in order, it begins its first user-mode process - the Windows Session Manager. As the name would suggest, it is responsible for making new sessions.

This starts the kernel and user modes of the Windows subsystem, which includes
- win32k.sys (kernel mode)
- winsrv.dll (user mode)
- csrss.exe (user mode)

This process starts csrss.exe - the Windows subsystem - and wininit.exe in Session 0, which is an isolated session just for the operating system. Then it starts csrss.exe and winlogon.exe for Session 1, which is the user session. The first child instance creates child instances in new sessions; this is done by smss.exe copying itself into the new session and self-terminating.

Note also that smss.exe also launches any other subsystem in the Required value of HKLM\System\CurrentControlSet\Control\Sesssion Manager\Subsystems. It also creates environment variables, virtual memory paging files, and so on. It's got a big job, huh?

Normal characteristics of this process:
- Image Path - %SystemRoot%\System32\smss.exe
- Parent Process - System
- Number of Instances - One master instance and one child instance per session. The child instance exits after creating the session.
- User Account: Local System
- Start Time: Within seconds of boot time for the master instance

Thus, you can spot unusual behavior as follows:
- A different parent process other than System, PID 4
- An image path different from C:\Windows\System32
- More than one running procecss - remember, children self-terminate and exit after each new session.
- The running User is not the SYSTEM user
- Unexpected registry entries for Subsystem - remember, check the Required value!

**[Task 4, Question 1] What other two processes do smss.exe start in Session 1? - csrss.exe, winlogon.exe**

### The NT Architecture

As discussed earlier, operating systems do work in user mode and in kernel mode; Windows is no exception. In Windows NT, user mode is made up of system processes and DLLs, and user mode programs work with the kernel through _environment subsystems_. These implement different APIs, which may call into kernel mode routines in different ways to accomplish different purposes. The main subsystems are as follows:
- Win32: This runs 32-bit Windows applications. It enables support for Command Prompt, text window support, shutdowns, and hard-error handling for other environment subsystems. It also supports Virtual DOS Machines (VDMs), allowing MS-DOS and 16-bit applications to run. This is also where window management (that is, displaying menus, allowing interactions, etc.) resides.
- OS/2: This supports 16-bit character-based OS/2 applications. You don't really see this around anymore, since it was dropped after Windows XP.
- POSIX: This supports applications that were written to the POSIX 1 or related ISO/IEC standards. This has been replaced multiple times; today, we use the Windows Subsystem for Linux as an equivalent.
- Security: This supports... well... security features! It deals with tokens, account authorization, permissions, login authentication, and so on. It also deals with Active Directory and client-side file/print sharing.

Specific parts of code are run in kernel mode. One of these is the Windows Executive services, which deals with I/O, object management, security, and process management. Most system calls reside here, too, though some calls actually go directly into the kernel layer for performance reasons. For the purposes of our discussion, we won't cover the kernel mode side of things in much detail, though this is where you start dealing with resource management, memory, registry system calls, and so on.

### More on the Session Manager Subsystem

Not much to add here, but this program will always run until winlogon.exe or csrss.exe ends, in which case Windows will begin shutting down. If the processes do not end in an expected manner, smss.exe can freeze the system, or other Really Bad Things can happen. It can also initiate new user sessions as needed, as discussed earlier. The Local Sesssion Manager Service (lsm.exe) will send requests to SMSS to initiate these new sessions.

## [Task 5] csrss.exe

This is the Client-Server Runtime Process. It's the user-mode part of the Windows subsystem, and always runs. If the process is terminated, as briefly discussed above, Bad Things happen. This is responsible for the Win32 console window and process thread creation/deletion. Each instance of this process loads these DLLs:
- csrsrv.dll
- basesrv.dll
- winsrv.dll
- (and other DLLs)

This is also resposible for making the Windows API available to other processes, mapping drive letters, and handling Windows shutdown. It's also busy! Remember that this is called from smss.exe at startup for Session 1.

Normal characteristics of this process include:
- Image Path - %SystemRoot%\System32\csrss.exe
- Parent Process - This is created by an instance of smss.exe, which self-terminates. Therefore, if you try to find Parent Process information in Process Hacker, you'll probably see "Non-existent process (PID)". This is OK.
- Number of Instances - 2+
- User Account - Local System
- Start Time - Within seconds of boot time for the first two instances, Sessions 0 and 1. Start times for additional instances occur as new sessions are created; however, we only often create Sessions 0 and 1.

Therefore, unusual behavior includes:
- The existence of a parent process.
- An image file path other than C:\Windows\System32.
- Misspellings. Maybe something like cssrs.exe? Rogue processes can do this to hide themselves in plain sight.
- SYSTEM not being the user of this process.

**[Task 5, Question 1] What was the process which had PID 384 and PID 488? - smss.exe**

### More on the Client-Server Runtime Subsystem

This is a user-mode system service. When a user-mode process calls a function involving console windows, process and thread creation, among other things, the Win32 libraries (kernel32.dll, user32.dll, gdi32.dll) will send an inter-process call to the CSRSS processs, and it'll take care of the rest. That is, no system calls are made by the user-mode processes directly; they just let CSRSS do its thing.

If csrss.exe or winlogon.exe are corrupted or otherwise inaccessible, then smss.exe will tell the kernel to stop starting up with a good ol' Blue Screen of Death.

In Windows 7 and later, CSRSS spawns conhost.exe to handle drawing console windows for command line programs with the permissions of that user.

Again...uh...this is a vital process for Windows operation. Don't listen to people telling you to mess with this!

## [Task 6] wininit.exe

This is the Windows Initialization Process, which launches the following in Session 0:
- services.exe - Service Control Manager
- lsass.exe - Local Security Authority
- lsaiso.exe - Credential Guard and KeyGuard-related process. Only shows up if you even have Credential Guard.

This is another critical process that runs in the background, along with its child processes.

Normal behavior includes:
- Image Path - %SystemRoot%\System32\wininit.exe
- Parent Process - Created by an instance of smss.exe, so you'll see "Non-existent process (PID)".
- Number of Instances - 1
- User Account - Local System
- Start Time - Within seconds of boot time.

Therefore, abnormal behavior includes:
- The existence of an actual parent process
- An image file path other than C:\Windows\System32
- Subtle misspellings, again to hide rogue processes in plain sight
- Multiple instances of the process
- The process not running as SYSTEM

**[Task 6, Question 1] Which process might you not see running if Credential Guard is not enabled? - lsaiso.exe**

## [Task 7] wininit.exe --> services.exe

This is the Service Control Manager, as discussed earlier. It handles system services - the loading and interaction of services on the system. You can check out the database that this process handles by running sc.exe in a Command Prompt. Information regarding services is in the Windows Registry, under HKLM\System\CurrentControlSet\Services.

This process loads device drivers that are marked as auto-start into memory. When a user logs into a machine successfully, this process sets the value of the Last Known Good control set, KHLM\System\Select\LastKnownGood, to that of the CurrentControlSet. In other words, if something were to break during startup, this process notes down what the last good configuration was that allowed the user to boot into the system.

This process is used by a bunch of other processes: svchost.exe, spoolsv.exe, msmpeng.exe, dllhost.exe, and so on.

Normal characteristics:
- Image Path - %SystemRoot%\System32\servicess.exe
- Parent Process - wininit.exe
- Number of Instances - 1
- User Account - Local System
- Start Time - Within seconds of boot time

Unusual behavior:
- Parent process other than wininit.exe
- Image file path other than C:\Windows\System32
- Subtle misspellings
- Multiple instances
- Not running as SYSTEM

**[Task 7, Question 1] How many instances of services.exe should be running on a Windows system? - 1**

### More on the Service Control Manager (SCM)

This process initializes its database of installed services using the following registry keys:
- HKLM\SYSTEM\CurrentControlSet\Control\ServiceGroupOrder\List - names, order of service groups. Each service has an optional Group value in the registry, which governs the order of initialization of a service or device driver.
- HKLM\SYSTEM\CurrentControlSet\Services - the actual database of services and device drivers and is read into SCM's internal database. SCM reads each service's Group value, as well as its DependOnGroup and DependOnService keys (load order dependencies).

Once it handles this, it checks to make sure that the services that were supposed to start on startup actually started, and it makes a note of those that didn't. Then it starts services for System and other accounts (it handles the latter situation by using lsass.exe to "log in" to these other accounts, more on that process later).

Starting in Vista, some services have delayed auto-start, which was designed to alleviate the issue with longer startups, as well as the startup time of critical services. SCM prioritizes auto-start services that aren't delayed. It also helps with the loading of device drivers and it deals with... network drive letters? Huh.

## [Task 8] wininit.exe --> services.exe --> svchost.exe

This is the Service Host (Host Process for Windows Services)! It hosts and manages Windows services. The services running in this process are implemented as DLLs. The DLL to implement can be found in HKLM\SYSTEM\CurrentControlSet\Services\<SERVICE NAME>\Parameters. You can find the location of this DLL in Process Hacker by right-clicking svchost.exe and then clicking Services --> the service name --> Go to service and then checking its properties.

![image](https://user-images.githubusercontent.com/56741029/234002931-1a065cd7-5565-4b84-940a-623cdbf8b146.png)

Note that legitiamte svchost.exe processes are called with -k as shown in the binary path above. This groups similar services to shaw the same process, and was implemented to help reduce resource consumption. Services that run with the same binary path will have different values for ServiceDLL.

Because svchost.exe will always have multiple running processes on any system, adversaries often create malware that masquerade as this process. They might name it svchost.exe, or they may misspell it a little bit (e.g. scvhost.exe). Alternatively, they may just install and call a malicious service!

Normal characteristics:
- Image Path - %SystemRoot%\System32\svchost.exe
- Parent Process - services.exe
- Number of Instances - many
- User Account - Varies depending on the instance. Could be SYSTEM, Network Service, Local Service, or (in Windows 10) the logged-in user.

Unusual behavior:
- Parent process different from services.exe
- Image file path other than C:\Windows\System32
- Subtle misspellings
- Absence of -k parameter

**[Task 8, Question 1] What single letter parameter should always be visible in the Command line or Binary path? - k**

### More on svchost.exe

When svchost is launched with a specific parameter, it looks for a value of the same name under HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Svchost. This is interpreted as a list of service names. Then it tells the SCM about the services it hosts, and SCM sends it a start command to let all of them start.

Starting in Windows 10, version 1703, services are no longer grouped into host processes if your computer has more than 3.5 GB of memory. Each service is its own process, which makes things easier to debug and improves resilience. However, this adds memory overhead.

Due to how services are handled in most Windows versions, troubleshooting issues with svchost services involves reconfiguring the service(s) so they run in their own svchost instances. This can be accomplished with certain commands such as `sc config foo type= own` and then changing it back to `shared`. Note that this isn't foolproof; sometimes issues may arise only when services are lumped together in a single svchost process.

### Abusing svchost.exe

There's a lot of ways you can see svchost.exe show up as a malicious process, assuming the process even goes by a different name! I was going to list some of these, but I think it's better just to share the link the room provides.
https://www.hexacorn.com/blog/2015/12/18/the-typographical-and-homomorphic-abuse-of-svchost-exe-and-other-popular-file-names/

## [Task 9] lsass.exe

This is the Local Security Authority Subsystem Service, which is resposible for enforcing security policies, verification of users, changing of passwords, creation of access tokens, and logging. It crates security tokens specifically for SAM (Security Account Manager), Active Directory, and NETLOGON. Authentication packages are specified in HKLM\System\CurrentControlSet\Control\Lsa.

Adversaries like to target this process too! Tools like mimikatz can be used to dump credentials from this process, and (as you probably suspect by now!) adversaries can just mimic this process to hide in plain sight.

Normal characteristics:
- Image Path - %SystemRoot%\System32\lsass.exe
- Parent Process - wininit.exe
- Number of Instances - 1
- User Account - Local System
- Start Time - Within seconds of boot time

Unusual behavior:
- Parent process other than wininit.exe
- Image file path other than C:\Windows\System32
- Subtle misspellings
- Multiple instances
- Not running as SYSTEM

**[Task 9, Question 1] What is the parent process for LSASS? - wininit.exe**

### Abusing LSASS and Preventing LSASS Abuse

The first thing I think of when I hear about LSASS abuse is that old Sasser worm from the Windows XP days. It killed the lsass.exe process, if I recall correctly, which would cause the computer to restart all the time. That was then, though, and this is now. And, oh boy, we have a lot more to worry about now.

This process specifically protects two things - hashes and, on Active Directory, Ticket Granting Tickets (TGTs). The process stores hashes of passwords that you use to log into Windows (it never stores the passwords in plaintext, that would be Really Really Bad). When logging in, it checks to see if the password hash you supply is the same as the password hash that it has for you. If so, then you're let in! Nice!

Windows password hashes can be stored in different ways - two deprecated hash types include LAN Manager (LM) and NT. For backwards compatibility reasons, though, LSA may still hang onto LM and NT hashes. Hashes are all stored in one of two places: a local SAM database or a networked Active Directory database (NTDS.DIT). To help distinguish between different hash types, LSA loads corresponding authentication packages.

These are the LSA secrets. This is how one could abuse them.

Hashes are typically associated with time-to-live elements - that is, hashes can expire. Before they expire, though, LSA is able to reuse a hash upon request, which allows for a single-sign-on experience for the end user. Instead of having to re-enter a password all the time, LSA can just re-verify you. How kind! However, this is the premise for what's known as the _pass-the-hash attack_.

Depending on the situation, LSA secrets can be stored in the process's memory on a user's behalf, and as alluded to earlier, there are ways to extract these secrets from the command line. This lets an attacker assume the role of the legitimate user... and it gets worse! In an Active Directory setting, hashes are used to authenticate access to network resources, so with a pass-the-hash attack, an adversary can move throughout the network to find more accounts to compromise. Access to LSA secrets can also be used for other attacks, such as pass-the-ticket attacks for TGTs.

To stop this from happening, we must assume that it _has_ happened in the first place. We assume the worst-case scenario - that a breach happened - and work from there. Windows provides some mechanisms to accomplish this, including Device Guard (to ensure boot integrity and OS code integrity) and Credential Guard (to isolate secrets so that only privileged software can access them). In Windows 10, LSA is moved into a separate container that allows this se3curity to exist, which makes it really REALLY hard for hackets to extract secrets. (This separate container is known as LSAlso.)

## [Task 10] winlogon.exe

This is the Windows Logon process, which is responsible for handling the Secure Attention Sequence (SAS). If you've ever had to press CTRL+ALT+DELETE to log into your computer, this guy's to blame. It also loads the user's profile, as well as NTUSER.DAT. Following this, userinit.exe loads the user's shell. It _also_ locks the screen and runs the user's screensavver, among other things. Remember (from all those words ago...) that smss.exe launches this process, along with a copy of csrss.exe, in Session 1.

Normal characteristics:
- Image Path - %SystemRoot%\System32\winlogon.exe
- Parent Process - an instance of smss.exe, so you shouldn't see a parent process.
- Number of Instances - 1+
- User Account - Local System
- Start Time - Within seconds of boot time for the first instance. Additional instances occur as new sessions are created via Remote Desktop, Fast User Switching, etc.

Unusual behavior:
- Existence of a parent process
- Image file path other than C:\Windows\System32
- Subtle misspellings
- Not running as SYSTEM
- In HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon, the Shell value being set to something other than explorer.exe.

**[Task 10, Question 1] What is the non-existent parent process for winlogon.exe? - smss.exe**

### Userinit

The registry key HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\Userinit specifies what programs should run when a user logs on. By default, this is userinit.exe, which sets up login scripts, network connections, and ultimately, the shell. It's possible to add or change the programs that run when a user logs in by changing this key.

### More on winlogon.exe

Other tasks accomplished by Winlogon include assignment of security to the user shell and allowing for multiple network provider support. Note that malware can attach itself to this process (an older example being Vundo). Increased memory usage on this process is a way to tell that it's been hijacked.

## [Task 11] explorer.exe

Last, but certainly not least, the shell. This is Windows Explorer, which allows the user to access their folders and files, the Start Menu, the Taskbar, and so on. This should be listed in HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell. This process will spawn many child processes as you use the system.

Normal characteristics:
- Image Path - %SystemRoot%\explorer.exe
- Parent Process - created by userinit.exe, which then exits. You should not ese a parent process.
- Number of Instances - 1+, per interactively logged-in user
- User Account - Logged-in user(s)
- Start Time - First intsance when the first interactive user logon session begins

Unusual behavior:
- Existence of an actual parent process
- Image file path other than C:\Windows
- Running as an unknown user
- Subtle misspellings
- Making outbound TCP/IP connections

**[Task 11, Question 1] What is the non-existent process for explorer.exe? - userinit.exe**

## Extra Material: RuntimeBroker.exe and taskhostw.exe

Windows 10 introduced a whole bunch of new processes. Here are the ones that the room suggests you do research into:

RuntimeBroker.exe is a process introduced in Windows 10 that handles permissions that Microsoft Store apps may use, such as those for your location or microphone. Generally, this process should not consume a lot of memory unless you're actually using a Windows Store program (unless you have left notifications on, which engages RuntimeBroker). Misbehaving apps can spike the process's memory usage; the solution to this is simply to reinstall the app in question, or to kill the process itself and restart.

(This is apparently a successor to taskhost.exe, which was a process that acted as a host for other processes that ran from DLLs instead of EXEs.)

Taskhostw.exe, formerly taskhostex.exe, handles processes that are running as DLLs as well. (To me, this is strange - based on the wording of the room, it appears that this is a successor to taskhostex.exe, which allows DLLs to be treated as EXEs in Task Manager. Oh well.)
