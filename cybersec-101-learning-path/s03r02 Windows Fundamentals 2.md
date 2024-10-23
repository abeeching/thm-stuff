# Windows Fundamentals 2

## [Task 1] Introduction

We will continue our dive into basic Windows concepts by looking at some other utilities available within the operating system. These tools can be helpful if you want to make changes to system settings, get some basic information about the system, see how well the system is performing, and perform advanced tasks. Again, the VM in this room will automatically open in split screen.

## [Task 2] System Configuration

System Configuration, or `msconfig`, is for advanced troubleshooting. Its main job is to help with startup issues. There are a few ways to launch this utility. One way is to go to the Start Menu and type in `msconfig`. Note that you will need Administrator privileges to run the utility.

There are five tabs in this utility:
- General: Here you can select hwat devics and services Windows can load upon boot; the main options are normal (load everything), diagnostic (only load basic devices and services), and selective (choose what to load).
- Boot: Here you can define boot options for the OS. Some basic options include using Safe Boot, booting without a GUI, logging boot information, and more.
- Services: This lists all services configured for the system regardless of their state. Services are special apps that run and handle things in the background.
- Startup: This lists all apps that are set to run at startup. The System Configuration tool is not primarily meant for app startup management; this functionality is delegated to Task Manager nowadays.
- Tools: This links to various utilities that we can run to configure the OS further. Each tool has a brief description explaining what it does.

Let's open up System Configuration and see what's going on. For the first question, we can check the Services tab and see who's manufactured what. If we scroll through (or use the column names at the top to sort), we can spot one service that has been manufactured by Systems Internals:

![image](https://github.com/user-attachments/assets/92451fd6-00cd-473e-b074-a24c59ac2893)

**[Task 2, Question 1] What is the name of the service that lists Systems Internals as the manufacturer?** - PsShutdown

For the next few questions, we'll be making heavy use of the Tools tab. If we want to figure out who has the Windows license on this machine, we might do well to start with "About Windows," which is designed to display Windows version information:

![image](https://github.com/user-attachments/assets/cf9b377f-292a-4e27-8642-a73aa5362083)

Launching this utility tells us who is licensed to have Windows on this machine:

![image](https://github.com/user-attachments/assets/0a732c64-e85f-4c38-bef8-186b78ce22b5)

**[Task 2, Question 2] Whom is the Windows license registered to?** - Windows User

Remember that the Tools tab will show the command that it will run to launch a given app. So, for instance, the Windows Troubleshooting tool has the command listed at the bottom:

![image](https://github.com/user-attachments/assets/8964a597-988c-4e15-b8c3-33d43918932a)

**[Task 2, Question 3] What is the command for Windows Troubleshooting?** - `C:\Windows\System32\control.exe /name Microsoft.Troubleshooting`

At first glance, you might assume that the executable above is Control Panel. You'd be right, though it never hurts to check. That particular command will open up something in the Settings app. Fortunately for us, there's another tool that uses `control.exe` as the command:

![image](https://github.com/user-attachments/assets/82e1340c-4d56-4fa2-96e2-ba2bf402b8c1)

And this guy opens in Control Panel!

![image](https://github.com/user-attachments/assets/110cd8ee-ded8-47f4-8cf3-f024379261f7)

**[Task 2, Question 4] What command will open the Control Panel? The answer is the name of the `.exe`, not the full path.** - `control.exe`

## [Task 3] Change UAC Settings

In the previous room, we covered UAC in decent detail - now we'll focus on the Change UAC Settings tool that can be found in the System Configuration utility. Opening this tool leads to a dialog box with a slider giving us various options on how aggressive we want UAC to be:
- If set to "Always notify me...", then UAC will be prompted whenever you make changes to Windows settings or whenever apps try to install software/make changes to the computer.
- By default, UAC is set to notify you only whenever apps try to make chanegs to the computer. It will not worry about changes made to Windows settings.
- UAC can be set to not dim the desktop when prompted. This is not recommended, which may seem a little odd. An explanation is provided after the task's question.
- You can also just disable UAC altogether. You can imagine that this wouldn't be recommended.

As always, you can check the command that is run to launch the Change UAC Settings tool by looking in System Configuration:

![image](https://github.com/user-attachments/assets/d2e6e9eb-416d-46ab-9a3e-7182d318d605)

**[Task 3, Question 1] What is the command to open User Account Control Settings? The answer is the name of the `.exe` file, not the full path.** - `UserAccountControlSettings.exe`

### More on UAC "Dimming"

The trick with UAC is that, when it is launched, it puts Windows into a "Secure Desktop" mode. This effectively disables your main desktop while the UAC prompt is on screen, hence the Desktop being "dimmed." This is designed to prevent any background applications from messing around with the UAC prompt (e.g. overlaying things on top of UAC or messing with the mouse pointer, etc.). This setting disables Secure Desktop, meaning you can still interact with it while UAC is on screen. The setting to disable the screen dimming is there if it takes too long to dim the screen on your computer.

## [Task 4] Computer Management

Let's look at the Computer Management tool now; we can open it from the System Configuration utility. There's plenty to look at here.
- System Tools
  - Task Scheduler: This tool allows you to create and manage tasks that the computer can automatically perform at set intervals. This might involve running an application or a script at set times, or when the user logs in and off the computer. You can create a task by going to the right pane and clicking the Create Task buttons under the Actions section.
  - Event Viewer: This is where you can see the Windows logs, which is especially helpful for troubleshooting and investigation. The left pane of Event Viewer gives a hierarchical tree listing of the event log providers (i.e., sources of logs). The middle pane displays an overview/summary of events, or if a log source is chosen, it will display the actual events. The right pane gives you various actions to perform on the event logs. More on this after the questions for this task.
  - Shared Folders -> Shares: This tool lists the shares and folders that other users can connect to. Windows automatically has some default system/remote administration shares. As you might expect by now, you can right-click the shares to view their properties (particularly permissions).
  - Shared Folders -> Sessions: This lists users who are currently connected to the shares.
  - Shared Folders -> Open Files: THis lists the folders/files that connected users are accessing.
  - Local Users and Groups: This was discussed in the previous room.
  - Performance: This leads to the Performance Monitor utility. Essentially you can see real-time or logged performance data here, which can be helpful for troubleshooting performance issues. There's also a link to the Resource Monitor, which is described in further detail in a later task.
  - Device Manager: This allows you to view and configure hardware, including disabling hardware attached to the computer.
- Storage
  - Windows Server Backup: We'll ignore this one, but this would allow you to backup stuff on a Windows Server if you have the right features installed. Obviously won't be available on Windows 10/11.
  - Disk Management: This utility allows you to perform advanced storage tasks, including setting up drives, altering partitions, and altering drive letter assignments.
- Services and Applications:
  - We will ignore the Routing and Remote Access utility; this is primarily relevant to Windows Server, rather than Windows 10/11.
  - Services: This allows you to enable and disable services and view their properties, among other things.
  - WMI Control: This configures and controls the Windows Management Instrumentation (WMI) srevice, which allows scripting languages to manage Windows systems locally and remotely. There's a command-line version of the utility known as WMI Command-line (WMIC). WMIC has been deprecated in Windows 10 version 21H1; PowerShell has since taken its place.

As always, you can view the command that was used to launch the tool in the System Configuration utility.

![image](https://github.com/user-attachments/assets/faaf7ff3-75f3-49f4-9075-80172c5d872a)

**[Task 4, Question 1] What is the command to open Computer Management? The answer is the name of the `.msc` file, not the full path.** - `compmgmt.msc`

We can poke around some of these features in the VM attached in this room. For instance, we can see what tasks are slated to run if we go to Task Scheduler and scroll down to Active Tasks:

![image](https://github.com/user-attachments/assets/f7e20331-a7a1-4044-a470-e309ec252392)

Highlighted is the task we're interested in for the next question.

**[Task 4, Question 2] At what time every day is the GoogleUpdateTaskMachineUA task configured to run?** - 6:15 AM

And if we go to Shared Folders -> Shares, we can see all the folders that are currently being shared out right now. There's actually more than just the default set of folders being shared:

![image](https://github.com/user-attachments/assets/d044898a-9e6d-4845-8a1d-fe3c2763bd77)

**[Task 4, Question 3] What is the name of the hidden folder that is shared?** - `sh4r3dF0Ld3r`

### More on Event Viewer

There's a whole room dedicated to the Event Viewer (and Windows logs in general) in the SOC 1 Learning Path, though here's a brief overview of what's going on.
- Windows will log events of various types, including:
  - Errors, which indicate significant issues that can lead to data loss or impaired functionality.
  - Warnings, which may indicate potential future problems.
  - Information, which describes successful operation of an application, driver, or service.
  - Success Audits, which indicate that a security access attempt was successful (e.g., a user has successfully logged in).
  - Failure Audits, which indicate that a security access attempt has failed (e.g., a user wasn't able to log in).
- The standard logs available in Windows include:
  - Application: Contains events logged by applications; developers decide what gets logged here.
  - Security: Contains events including logon attempts (both successful and unsuccessful) and resource usage.
  - System: Contains events logged by system components.
  - Custom Logs: Contains events logged by apps that create a custom log, which may be done for performance/security reasons or to control the size of the main Windows logs.

## [Task 5] System Information

Next up in the System Configuration tools is the System Information tool. This gathers information about the computer, and then displays a view of the hardware, system components, and software environments. This primarily serves as a troubleshooting tool that can be used to diagnose computer issues. The information is split into many sections.
- System Summary: This provides general technical specifications for the computer, including processor brand and model.
- Hardware Resources: This contains some incredibly technical information about how the hardware is being used on Windows. See Microsoft's documentation.
- Components: This gives information about hardware devices installed on the computer.
- Software Environment: This gives information about the Windows OS, as well as software that you may have installed. This includes Environment Variables and Network Connections.

At the bottom of the System Information screen is a search bar, which can be used to look for specific information.

It's worth diving into Environment Variables real quick - we've only touched on them briefly, but they're pretty important. These store information about the OS environment, like the OS path, the number of processors used by the OS, and the location of temporary folders. Other programs make use of these variables for various reasons, e.g. to find the location of certain folders on the OS. You can view these variables in a few ways:
- From System Information -> Software Environment -> Environment Variables...as you may have already deduced
- From Control Panel -> System and Security -> System -> Advanced System Settings -> Environment Variables
- From Settings -> System -> About -> System Info -> Advanced System Settings -> Environment Variables

Here's what System Configuration says about the System Information tool:

![image](https://github.com/user-attachments/assets/39faa570-50ae-4a5f-845b-55c74977ab03)

**[Task 5, Question 1] What is the command to open System Information? The answer is the name of the `.exe` file, not the full path.** - `msinfo32.exe`

Here's what the System Name is. We can find it in System Information's System Summary.

![image](https://github.com/user-attachments/assets/7a6352f9-2e8d-4912-83d8-20f7db5a4a2f)

**[Task 5, Question 2] What is listed under System Name?** - `THM-WINFUN2`

And if we go to Software Environment -> Environment Variables, we can see a list of the applied environment variables, including the one for ComSpec:

![image](https://github.com/user-attachments/assets/5bd0b70f-a713-4d13-91a3-d622b72dfb0d)

**[Task 5, Question 3] Under Environment Variables, what is the value for ComSpec?** - `%SystemRoot%\system32\cmd.exe`

## [Task 6] Resource Monitor

Next up is the Resource Monitor tool. This displays per-process and aggregate resource usage information, as well as providing details about what processes are using individual file handles and modules. Advanced filtering allows you to look at the data associated with specific processes, and you're able to manage services and close applications from this interface. There's also a process analysis feature that can be used to find deadlocked processes and file-locking conflicts, allowing the user to attempt to fix the problems instead of closing apps and risk losing data.

This is a more advanced utility that can be used to perform advanced troubleshooting on the computer system. The utility has five sections:
- Overview, a general overview of the system and what's being used
- CPU
- Disk
- Network
- Memory

You can find additional information about each resource by going into the respective tab. The right-hand pane has a real-time graphical view of the resource usage for each section.

Here's what System Configuration has to say about this tool:

![image](https://github.com/user-attachments/assets/75a7ef88-fee5-49bc-a809-a1ca51672901)

**[Task 6, Question 1] What is the command to open Resource Monitor? The answer is the name of the `.exe` file, not the full path.** - `resmon.exe`

## [Task 7] Command Prompt

The next tool that we'll cover is the Command Prompt, which is the Windows command-line interface (similar to how Linux has the Terminal). Windows is primarily a GUI-based system, so you can do quite a bit of work just by clicking around; however, you may need to perform some more advanced commands via the Command Prompt (or perhaps you may encounter a machine that only has the Command Prompt as the UI!). Here we'll dive into a few basic commands for obtaining information about the system.
- `hostname` will output the computer's name.
- `whoami` will output the logged-in user's name.
- `ipconfig` is a command that shows network address settings for the computer, and can be primarily used for troubleshooting. There are many options you can use with it; run `ipconfig /?` to see them.
- `cls` will clear the Command Prompt screen, in case it ever gets too cluttered.
- `netstat` displays protocol statistics and the status of current connections. You can run it on its own or with parameters to adjust the output.
- `net` can be used to manage network resources. This does require subcommands and options, though typing `net` on its own will show you what you manage. For further info, run `net help`.
- As an example of `net` in use, you can manage users by running `net user`. There are many options available for this; see `net help user`.

System Configuration has a few command-line tools available for you to use. One of these tools is the Internet Protocol Configuration tool (or `ipconfig`!). You can see what command it will execute, as always:

![image](https://github.com/user-attachments/assets/11756b6e-bfa6-47d7-b0f7-c291556c7a96)

**[Task 7, Question 1] In System Configuration, what is the full command for Internet Protocol Configuration?** - `C:\Windows\System32\cmd.exe /k %windir%\system32\ipconfig.exe`

We may as well launch Command Prompt and see what we can do. For this question, it's fine if we open up Command Prompt or Internet Protocol Configuration from System Configuration. Using either will get us to the same place! Let's see what options we have for `ipconfig` by running `ipconfig /?`. Scrolling through the output, we see a list of options - one option gives us full configuration information. That sounds pretty detailed to me!

![image](https://github.com/user-attachments/assets/f37ad487-9753-4c1b-b3d0-25dbcec9143a)

**[Task 7, Question 2] For the `ipconfig` command, how do you show detailed information?** - `ipconfig /all`

## [Task 8] Registry Editor

The last tool we'll cover is Registry Editor. The Registry, in essence, is a database of system configuration information that Windows maintains for users, applications, and hardware. This information is constantly referenced as you use the OS. It includes things like
- User profiles
- Applications installed and document types that can be created with them
- Information for folder/application icons
- Hardware on the system
- Ports being used

The Registry gets covered in a lot of detail in the Windows Forensics 1 room - it contains a lot of information that would be of interest to a cybersecurity professional. You should generally know, though, that the Registry is an advanced tool. In fact, you should exercise caution while using this tool, as making changes to the Registry without knowing what you're doing can affect your computer adversely.

One way to view and edit the Registry is to go to the Registry Editor. Here's what System Configuration has to say about it:

![image](https://github.com/user-attachments/assets/656f1f6c-eeca-4422-a512-722f26f60864)

**[Task 8, Question 1] What is the command to open the Registry Editor? The answer is the name of the `.exe` file, not the full path.** - `regedt32.exe`

## [Task 9] Conclusion

And that about does it! We spent time in this room covering various utilities that can be found in the System Configuration/`msconfig` utility. There are other ways to get to some of these utilities; you can even access some of them from the Start Menu. You should spend some time poking around the tools described in this room (as well as the tools that were skipped over - some of them showed up in the previous room, but there's still some others that we missed). In the final part of this sequence, we'll explore tools that can be used to keep the system secure.
