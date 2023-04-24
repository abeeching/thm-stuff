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

**[Task 3, Question 1] 4**

### User vs. Kernel Mode

Consider this a simplified overview of the concept.

Generally speaking, operating systems have two modes for programs to operate in. User programs will generally operate under User Mode (affectionately called Userland by some, if I remember correctly). User mode programs will do what they are programmed to do until either (a) they're done, or (b) they need to start messing with memory and other sensitive operating system internals.

Giving user programs the ability to mess with memory is a Really Bad Idea. Programs could easily mess with the memory of other programs, or they could just waste time on the operating system. So, when a user-mode program wants to mess with that stuff, it reaches out to the operating system and asks it to do the work for them. The OS obliges and enters kernel mode.

You can think of kernel mode as a privileged mode for operating system stuff. When programs are doing stuff in kernel mode, they are able to mess with those sensitive operating system internals discussed earlier. So, the OS does what it needs to do in kernel mode, and then it lets the user-mode program get back to its work. In the ideal world, nothing breaks! Yay!

In the context of our current discussion, System is a kernel-mode process (you could probably tell given that its image path is, well, the NT OS Kernel!).

## [Task 4] System --> smss.exe

### The NT Architecture

### More on the Session Manager Subsystem

## [Task 5] csrss.exe

### More on the Client-Server Runtime Subsystem

## [Task 6] wininit.exe

## [Task 7] wininit.exe --> services.exe

## [Task 8] wininit.exe --> services.exe --> svchost.exe

### More on svchost.exe

### Abusing svchost.exe

## [Task 9] lsass.exe

### Abusing LSASS and Preventing LSASS Abuse

## [Task 10] winlogon.exe

### More on winlogon.exe

## [Task 11] explorer.exe

## Extra Material: RuntimeBroker.exe and taskhostw.exe
