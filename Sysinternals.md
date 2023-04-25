# Sysinternals

## [Task 1] Introduction

The Sysinternals suite of tools is a huge collection of tools that can be used to monitor what's happening on Windows, ranging from the file and disk to networking to security and more. The tools were created in the late 90s by Mark Russinovich and Bryce Cogswell. In 2006, these tools were acquired by Microsoft and Mark Russinovinch joined Microsoft; he would later become the CTO of Azure. I like to think that Russinovinch is a pretty big name around these parts, given his high-profile discoveries in the past.

Sysinternals is a very popular suite of tools used by IT professionals and even red teamers and adversaries. Clearly this is a set of tools worth adding to your toolkit.

**[Task 1, Question 1] When did Microsoft acquire the Sysinternals tools? - 2006**

### Why You Should Trust The Guy Who Made It - Sony Music CD Rootkits

In October of 2005, Russinovinch was testing a tool he developed with Bryce Cogswell called _RootkitRevealer_. Based on how the tool worked, it looked like it was just scanning for rootkit-like behavior on the system... which he ultimately found.

The tool revealed many hidden registry keys and even a hidden directory, none of which was obviously connected to any of the software he had set up at the time. He ultimately discovered that they were the result of software installed by Sony after he ran an autoron program on one of their CDs. The news, that Sony's Digital Rights Management (DRM) software had a rootkit, spread rapidly throughout the internet back in the day. Users didn't consent to this stuff happening on their systems, and the EULA didn't even indicate that this would happen.

(This is because, back then, Windows would only perform signature checks on drivers that were set up via plug-and-play. Thus, Windows would only warn users about installing unsigned drivers in these cases. XCP's related drivers were unsigned, but they weren't set up through plug-and-play. Fun, right?)

Ultimately, Sony discontinued using XCP technology on CDs and recalled them, offering exchanges for CDs not riddled with XCP and a way to uninstall the program on the computer.

How were Russinovinch's programs used at the time to figure all of this out? Well...
- The Aries driver, which was the crux of the XCP technology, made use of system call hooking. That is, it replaced pointers for certain system call functions with its own. This was a technique that Russinovinch and Cogswell pioneered in Regmon, a registry monitoring application.
- LiveKd was used to debug the kernel and investigate how Aries hooked into the system calls. Upon investigation, they found that Aries's code was (likely) structured after their work done in developing Regmon.
- NtCrash2 was a program Russinovinch wrote in 1997 to stress-test input parameter validation of the Windows kernel. One of the hooks XCP uses checks filename input parameters that are relevant to the kernel - these are pointers passed by applications, so they could be (and were) invalid. This program triggered the bug, crashing the system.

The actual details of XCP's implementation are interesting, but become irrelevant in the context of this room. It's worth a read if you're not famliar with it, especially the part where XCP was capable of _crashing your system by unloading a driver while the system is running._

### Why You Should Trust The Guy Who Made It - Symantec Rootkit-ish Technology

Following this, Russinovinch discovered that Symantec, the developers of the Norton antivirus program, utilized rootkit-like behavior. Norton SystemWorks hides a directory from the user so customers wouldn't accidentally delete important files. On paper, this is a beneficial use of rootkit technology; however, this made a good place for malware to hide.

Russinovinch points out that, while this kind of technology can be beneficial, it's dangerous in that it removes some control of the operating system from the user, which can invite a whole slew of problems. The point of these detours is this: I think he knows what he's talking about.

## [Task 2] Install the Sysinternals Suite

Sysinternals may be downloaded and run from the local system. Given that there are 70+ tools to work with, you can opt to download the entire suite or just a handful of tool(s).

To get a tool or two, you can go to the Sysinternals Utilities Index page, https://docs.microsoft.com/en-us/sysinternals/downloads/. The tools are listed in alphabetical order, but you can look around by category (e.g., process utilities for inspecting Windows processes). From this website, you should be able to download the entire suite as well.

Alternatively, one can use the Sysinternals Live URL, https://live.sysinternals.com/. More on this little thing in a moment. The tools are listed in alphabetical order too.

Optionally, once you download the Sysinternals suite and extract the files somewhere on your computer, you can add them to your environment variables. This allows you to launch the tools from Command Prompt from anywhere on your system.

To get to the Environment Variables, open Command Prompt and run sysdm.cpl. Then click Advanced --> Environment Varibales. From here, select Path and Edit it. Select New and enter the folder path where Sysinternals was extracted to, then confirm the changes. Once you do all of that, you can open a new Command Prompt window and try to run any of the Sysinternals tools (e.g., just type in procmon). If it works, then you're set!

(Alternatively alternatively, you can use a PowerShell command to download and install all of the Sysinternals tools. Download-SysInternalsTools [FILE PATH]).

The answer to this task's question can be found by going to this page: https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite.

**[Task 2, Question 1] What is the last tool listed within the Sysinternals suite? - ZoomIt**

## [Task 3] Using Sysinternals Live

Sysinternals Live allows you to execute Sysinternals tools directly from the Web without manually downloading them. You can do so by entering a tool's Sysinternals Live path into Windows Explorer or a Command Prompt as "live.sysinternals.com/(toolname)" or "\\live.sysinternals.com\tools\(toolname)".

For instance, if we wanted to launch Process Monitor, we would navigate to \\live.sysinternals.com\tools\procmon.exe. We would also see that this doesn't work quite yet.

In order for this to work, you must ensure that the WebDAV client is installed and running - this allows a local amchine to access a remote machine running a WebDAV share. On Windows 10, this client is installed but the client is likely not running. Hence, you'll see "The network path was not found" if you try to run a Sysinternals Live program.

Thus, you'll have to open PowerShell and run `start-service webclient`. To make sure it's running, run `get-service webclient` and make sure the status is Running. Furthermore, you'll have to ensure Network Discovery is enabled in the Network and Sharing Center. This can be launched from the command line as follows:
`control.exe /name Microsoft.NetworkAndSharingCenter`

Once you're in the Network and Sharing Center, click on Change advanced sharing settings and Turn on network discovery for your current profile.

On Windows Server, you want to set up the WebDAV Redirector from the Serer Manager or via PowerShell. The PowerShell command to run this is `Install-WindowsFeature WebDAV-Redirector -Restart`. This restarts the server once installation is complete, which is required. Then you can verify that it has been installed by running `Get-WindowsFeature WebDAV-Redirector | Format-Table -Autosize`.

You can run the Sysinternals Live tools in one of two ways:
1. Run from the command line, e.g. `\\live.sysinternals.com\tools\procmon.exe`
2. Map a drive for the Sysinternals Live tools by running `net use * \\live.sysinternals.com\tools\` and browsing the suite from your local machine. It shows up as a network location! Nice!

**[Task 3, Question 1] What service needs to be enabled on the local host to interact with live.sysinternals.com? - WebClient**

## [Task 4] File and Disk Utilities

Tool 1 - Sigcheck - This is a utility that shows a file version number, timestamp information, and digital signature details such as certificate chains. You can also check a file's status on VirusTotal. You can use this program, for instance, to check for unsigned files in C:\Windows\System32.
`sigcheck -u -e C:\windows\system32` (Note that you may need to add `-accepteula` to use the tools)
- The -u switch shows files that are unknown by VirusTotal or have non-zero detection rates there.
- The -e switch scans executable images only, regardless of extension.

Tool 2 - Streams - This program allows you to read and write to alternate file streams. What's the deal with that?

In NTFS - the current filesystem of Windows - the Alternate Data Stream (ADS) file attribute may be applied to a file. Every file has at least one data stream, $DATA, and ADS allows files to contain multiple streams of data. Windows Explorer doesn't display this to the user by default; you'll need to use 3rd party executables to do so. You can use PowerShell to view ADS for files. Due to this, there are both legitimate and malicious uses for ADS.

As an example, files downloaded from the Internet will have identifiers written to ADS that indicate the file came from the Internet (e.g., :Zone.Identifier:$DATA). This is why, if you ever check the properties of a file downloaded from the Internet, Windows displays a little security message at the bottom indicating the file may be blocked.

Tool 3 - SDelete - This allows you to delete one or more files/directories and to cleanse free space on a logical disk. It implements the DOD 5220.22-M method for sanitizing data. It has been used by adversaries to delete files.

For this question, you're told that there is some text in the ADS. I can only think of one tool that would be useful here, out of the ones discussed. Streams looks pretty good, right?

**[Task 4, Question 1] There is a txt file on the desktop named file.txt. Using one of the three discussed tools in this task, what is the text within the ADS? - I am hiding in the stream.**

### A Brief Overview of the Other File and Disk Utilties

But wait... There's more!
- AccessEnum: Tells you who has what access to directories, files, and registry keys.
- Contig: Optimize individual files or create new files that are contiguous. Basically a defragmenter for commonly-used files.
- DiskMon: Monitors all hard disk activity.
- DiskView: A graphical overview of disk sectors.
- NTFSInfo: Gives you detailed information abuot NTFS volumes, including information about the Master File Table (MFT).
- PsFile: Shows what files have been opened remotely.
- PsTools: A suite of tools for dealing with remote processes and other tasks.
- ShareEnum: Scans file shares on the network and their security settings.

## [Task 5] Networking Utilities

TCPView is a tool that shows you detailed information about TCP and UDP endpoints on a system, including local and remote addresses and the state of TCP connections. This provides more information (in a convenient presentation) compared to Netstat, which ships with Windows. Tcpvcon is a command-line utility that offers the same functionality.

Windows has a built-in tool that provides similar functionality called the Resource Monitor. From a command-line, this can be opened with `resmon`. Here, you can check TCP Connections to view the remote addresses for processes with an outbound connection. You can also open Resource Monitor from the Performance Tab in Task Manager.

But that's Resource Monitor, and this is TCPView. In TCPView, you can turn on and off TCP v4, v6 and UDP v4, v6 filters according to your needs. You can also use the green flag icon to use the States Filter, which gives you a list of states to filter by (e.g. Closed, Listen, Established, Ack, etc.).

The hint in this question suggests you use an online whois tool, such as https://www.whois.com/whois to get the name of the organization from the screenshot below:

![image](https://user-images.githubusercontent.com/56741029/234121486-6e809336-6618-46a6-b514-bc0b34e9f971.png)

Inputting 52.154.170.73 into (most) whois tools tells us the organization!

**[Task 5, Question 1] Using WHOIS tools, what is the ISP/Organization for the remote address in the screenshots above? - Microsoft Corporation**

### A Brief Overview of the Other Networking Utilities

There's more to discuss here, too!
- AD Explorer: an advanced Active Directory viewer and editor.
- AD Insight: a real-time monitoring tool used to help troubleshoot Active Directory client apps.
- PsFile: Tells you what files have been opened remotely.
- Whois: Tells you who owns an Internet address.

## [Task 6] Process Utilities

Tool 1 - Autoruns - This tells you what programs are configured to run during system bootup or login, as well as those that run when you start built-in applications such as Internet Explorer and media players. This combines information from the startup folder and the Run/RunOnce registry keys, among others. This gives you information about shell extensions, toolbars, browser helper objects, Winlogon notifications, and more. It's particularly useful for spotting malicious items that can be used to establish persistence on a machine!

There are a lot of tabs to sift through in Autoruns, ranging from Everything to scheduled tasks to image hijacks.

Tool 2 - ProcDump - This allows you to monitor an application for CPU spikes and to generate a crash dump during a spike. This can be used for troubleshooting purposes. We can use Process Explorer to do the same (more on that in a moment). You can right-click a process to create a minidump or a full dump of a process.

Tool 3 - Process Explorer - Better known as Task Manager, but on performance-enhancing drugs. This shows you all active processes, including names of owning accounts, along with other information such as handles that have been opened (e.g., connections to remote folders) and DLLs/memory-mapped files that have been loaded. We discussed this tool at length in the Core Windows Processes room.

If you right-click a process, you can examine its properties. For instance, you can check the services registered in svchost.exe and the connections being used by a process. This is particularly useful for determining how a process is communicating outside the network - online tools such as Talos Reputation Center may be used to supplement.

Process Explorer allows you to use an option called Verify Signatures. This gives you another column indicating whether or not a process has a verified signature associated with it. You can also run the program at logon or replace it with Task Manager entirely.

Characteristic of Process Explorer is the use of different colors. Here's what they mean:
- Purple: Files might be packed.
- Red: The process is exiting.
- Green: The process just loaded/spawned.
- Light blue: The process was run by the same account that started Process Explorer.
- Dark blue: The process is selected.
- Pink: The process is a service.
- Dark Grey: This process has been suspended. You can resume it if you wish.

Tool 4 - Process Monitor - This gives you a real-time overview of filesystem, registry, and process/thread activity. This provides the same functionality that Filemon and Regmon did, and it has a set of new enhancements including non-destructive filtering, comprehensive event properties, thread stacks, and more. Filters can be set to capture events related to certian PIDs. Event capture can be toggled on and off as needed.

Due to the volume of events collected by Procmon, one has to understand how to use the filter feature. To fully understand Procmon's output, one must also understand processes/threads in Windows and Windows API calls.

Tool 5 - PsExec - This is a light-weight telnet replacement that allows you to execute processes on other systems without having to manually install client software. This lets you launch interactive command prompts on remote systems and other tools like IpConfig. As you may surmise, this is a tool that is utilized by adversaries.

The questions can be answered after examining the new entries in the Image Hijacks tab in Autoruns. ...In the virtual machine provided by the room, of course.

**[Task 6, Question 1] What entry was updated? - taskmgr.exe**

**[Task 6, Question 2] What is the updated value? - c:\tools\sysint\procexp.exe**

### More on Procmon

A couple of things to note when using Procmon:
- You can enable and disable event classes from being captured. The menu bar has a couple of icons that you can enable and disable. The farthest icon to the right is disabled by default.
- You can add filters for various conditions, including architecture, company, PID, parent PID, sequence, sessions, users, and so on. You can change the nature of the conditions being filtered, too; for instance, you may wish to do a comparison test. It's just as easy to remove filters from the Filter dialog box as well.
- You can choose to include and exclude certain events as needed.
- Filter rules may be added by right-clicking a procecss in Procmon.
- You can save filters in Filter --> Save Filter, and then you can pull up your custom filters by checking Organize Filters in that same dropdown. You can Load Filters there too.
- It's possible to manually export filters from the Organize Filter dialog box, so you can import them later.
- Highlights can be used to make certain properties easier to see. Right click an event, clicki on Highlight and choose the attribute. It'll change to a light blue color. You can see the highlight rules in Filter --> Highlight, in the Proecss Monitor Highlighting dialog. You can add and remove highlights from here, and you can convert highlighted things into filters. Neat!
- There's also some advanced stuff you can do that allows you to filter for events happening at levels beneath Windows (e.g. low-level hardware). This is some pretty advanced stuff, so I won't be diving into too much detail here, but it's worth noting!

### Understanding Procmon - Processes and Threads

Processes get resoures needed to execute a program, including virtual address spaces, executable code, handles to system objects, security context, PIDs, yadda yadda yadda. All processes start with a single thread - the primary thread. It can create additional threads as needed.

Threads are entities within processes that may be scheduled for execution. All threads of a process share the process's virtual address space and resources. Each thread gets exception handlers, scheduling priority, thread local storage, thread identifiers, and so on. System can save a thread's context - its set of machine registers, kernel stack, environment block, and user stack. Threads also get their own security context.

In Windows, multiple threads may be executed simultaneously in what's known as pre-emptive multitasking. A system can execute as many threads as there are processors on the computer in a multiprocessor computer.

A job object allows groups of processes to be managed as a unit - these are namable, securable, sharable objects that control attributes of the processes associated with them. Operations on a job object affect all processes associated with that job object.

Applications may use thread pools to reduce the number of application threads and manage the worker threads. Applications may queue work items, associate work with waitable handles, bind with I/O, and automatically queue based on a timer.

User-mode scheduling (UMS) is a mechanism that apps can use to schedule their own threads. An app may switch between UMS threads in user mode without needing to get the system scheduler involved, and it can regain control of the processor if a UMS thread blocks in the kernel. Each UMS thread has its own thread context instead of sharing the thread context of a single thread. UMS is more efficient than thread pools for short-duration work and few system calls.

Fibers are units of execution that must be manually scheduled by the application. They run in the context of the threads that schedule them. Each thread may schedule multiple fibers. Fibers don't provide advantages over well-designed multithreaded apps, though they can help with porting apps that were designed to schedule their own threads.

### Understanding Procmon - Windows API Calls

There is a BOATLOAD of API stuff on Microsoft's website. https://learn.microsoft.com/en-us/windows/win32/apiindex/windows-api-list

### More on PsExec

At the most basic level, psexec requires a computer name and a command to run. For instance, `psexec \\REMOTECOMPUTER hostname` will run hostname on the REMOTECOMPUTER. In simple cases, psexec will exit the remote session and return the exit code the remote process returned.

On remote computers, psexec will do the following:
1. Create PSEXESVC.exe in C:\Windows
2. Create and start a Windows service on the remote computer, PsExec
3. Execute the program under a parent process of psexecsvc.exe
4. Stop and remove the PsExec service upon completion

psexec can also execute commands on the local machine; you may want to do this so you can perform commands under the SYSTEM account, using the -s switch.

psexec can also run commands on multiple computers:
- You can provide a comma-delimited list, e.g. `\\COMP1,COMP2,COMP3`
- You can use `\\*` to execute a command on all computers in Active Directory
- psexec can read from a text file, e.g. a list of computers, and execute commands on multiple machines that way. For instance, `psexec @comps.txt hostname` runs hostname on all computers listed in comps.txt. It must be a line-delimited file.

psexec can copy any local program to the remote computer prior to execution using the `-c` switch. Remote processes can be run with different credentials using `-u (AD-domain-name\)username` and `-p password` switches. Without the -u switch, psexec will impersonate your account on the remote computer.

### A Brief Overview of the Other Process Utilities

I sure hope you enjoy lists.
- ListDLLs: Lists all DLLs currently loaded
- PsKill: Terminates local or remote processes
- PsService: View and control services
- ShellRunas: Launch programs as a different user via context-menu entries.

## [Task 7] Security Utilities

The main security utility that can be found in Sysinternals is Sysmon, which is a Windows system service and device driver that monitors and logs system activity to the Windows event log. It collects information about process creation, network connections, changes to file creation time, and so on. There's a whole room about it.

### A Brief Overview of the Other Security Utilities

There's other stuff for security, too:
- AccessChk: Shows you the level of access the user or group you specify has to files, Registry keys, or services
- Autologon: Bypass password screen during logon
- PsLogList: Dump event log records
- Rootkit Revealer: This is that rootkit detection utility that was used to expose XCP, as described earlier.

## [Task 8] System Information

WinObj seems to be the main tool here. It is a program that uses the native Windows NT API, provided by NTDLL.DLL, to access and display information on the NT Object Manager's namespace. If you recall from the Core Windows Processes room, Windows starts two sessions - Session 0 for the OS, and Session 1 for the first user.

WinObj will show information for Session 0 (DosDevices) and Session 1 (WindowStations). This information should correlate to the information for the csrss.exe process in Process Explorer.

### A Brief Overview of the Other System Information Tools

We're almost done here!
- ClockRes: This gives you the resolution of the system clock, which is the maximum timer resolution.
- LiveKd: Microsoft kernel debugging; was used to help expose XCP, as discussed earlier.
- LoadOrder: Shows the order in which devices are loaded onto the system.
- LogonSessions: Lists the active logon sessions on a system.
- PsInfo: Obtains information about a system.

## [Task 9] Miscellaneous

Tool 1 - BgInfo - This automatically displays information about a Windows computer on the desktop's background, such as the computer name, IP address, service pack version, and more. This is helpful for managing multiple machines. You can leave this up on a server desktop, so when someone RDPs into it, they get some basic information pretty quickly.

Tool 2 - RegJump - This takes a registry path and makes Regedit open to that path. It will accept root keys in both standard (HKEY_LOCAL_MACHINE) and abbreviated form (HKLM). This saves you the trouble of having to manually drill down into the registry all the time. You can query the Registry WITHOUT this tool by using command line stuff.
- reg query (Command Prompt)
- Get-Item or Get-ItemProperty (PowerShell)

(It's still pretty useful to have, though!)

Tool 3 - Strings - This scans the file you pass it for UNICODE/ASCII strings of a de fault length of 3 or more characters. I understand this is a starting point tool for basic malware analysis.

To answer the question, you can use a similar command to that provided in the room. You would just replace `zoom*` with `*pdb`.

![image](https://user-images.githubusercontent.com/56741029/234149116-17d5583d-f098-4be6-933a-8c8162196bc1.png)


**[Task 9, Question 1] Run the Strings tool on ZoomIt.exe. What is the full path to the .pdb file? - C:\agent\_work\112\s\Win32\Release\ZoomIt.pdb**

### A Brief Overview of the Other Miscellaneous Tools

Last set of tools!
- BlueScreen: Ever want to simulate a blue screen on your computer? This is a sreensaver for you!
- Desktops: This lets you create up to 4 virtual desktops and switch between them. In Windows 11, this is a built-in feature!
- NotMyFault: Ever wanted to crash/hang/cause kernel memory leaks on your computer? Well, you can with this!
- ZoomIt: Allows you to zoom and draw on the screen. Neat!
