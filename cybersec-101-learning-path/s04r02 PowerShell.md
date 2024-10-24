# Windows PowerShell

## [Task 1] Introduction

In this second part of the Command Line section, we'll take a look at PowerShell, what it can do, how its language works, how to use some basic commands, and its implications in cybersecurity. It's helpful to understand the Windows/AD Fundamentals, and it's good to know the basics of using Command Prompt - PowerShell builds on it.

## [Task 2] What is PowerShell?

PowerShell is a task automation solution, made up of a command line shell, a scripting language, and a configuration management framework. PowerShell is an object-oriented command line, meaning it works with complex data types. This makes it effective for working with system components. It's primarily designed for Windows, though it can be used in macOS and Linux as well.

You may well wonder why we even have a second command line solution to begin with. As with most things, it has to do with technological demand. Windows saw increased use in more complex enterprise environments, and it became clear that the Command Prompt simply was not going to cut it. Batch files couldn't do all the heavy lifting. Microsoft decided that it was time to develop some more sophisticated tools.

Unix was able to handle things perfectly fine, but Microsoft engineer Jeffrey SNover found that Unix had treated everything as text files. Windows saw everything as structured data and APIs, so simply porting over the Unix toolset wouldn't quite do the job. Snover decided that it was time to try an object-oriented approach, combining scripting with Microsoft's .NET framework.

And so here we are. 2006 saw the introduction of PowerShell to the Windows operating system, allowing administrators to automate things. 2016 saw the introduction of PowerShell Core, which is designed to run on macOS and Linux as well.

We've been talking about objects and object-oriented things for a little while now, and folks who are familiar with programming may have an idea of what's going on here. In programming, objects are items with properties (characteristics) and methods (actions that can be taken on those objects). PowerShell works in a similar way. Objects in PowerShell are fundamental units that contain data and functionality. Objects in PowerShell may contain properties such as usernames and file sizes, and they may have methods such as functions for copying files and stopping processes.

As we saw in the previous room, the traditional Command Prompt handles things in text - data is input, processed, and output as plain text. PowerShell uses cmdlets ("command-lets") to do stuff; cmdlets process and output objects that retain properties and methods. This leads to some more powerful and flexible data manipulation - there's no need to parse text.

**[Task 2, Question 1] What do we call the advanced approach used to develop PowerShell?** - object-oriented

## [Task 3] PowerShell Basics

We'll start by connecting to the virtual machine. We've done a lot of work with SSH in the Linux command line, though now's a good time to introduce Remmina. This is a GUI client that can be used to access the machine over SSH. It's available in the AttackBox.
1. Start the machine provided in the room as well as the AttackBox.
2. At the top-left, click Applications. Then click Internet -> Remmina.
3. You'll be prompted to enter a password. Simply click Cancel to ignore the prompt.
4. In the top-left, click the RDP drop-down menu and click SSH. Then paste the target machine IP into the bar at the top. Press Enter.
5. When prompted, the credentials are as follows. The username is `captain` and the password is `JollyR0ger#`

PowerShell can be launched in a few ways depending on the situation.
- You can go to the Start Menu, search for `powershell`, and then run Windows PowerShell or PowerShell, whichever comes up.
- Press Win + R to open the Run dialog, type `powershell`, then press Enter.
- In File Explorer, you can navigate to any folder, type `powershell` into the address bar, and then press Enter. This has the advantage of opening PowerShell directly in the folder that you're in.
- In Task Manager, go to File -> Run New Task and type `powershell`. Press Enter to start it.
- Launch Command Prompt (`cmd.exe`), and then type `powershell` and press Enter.

Since we're only connected via SSH and not RDP (that is, we're only in a command prompt), we'll use the latter method. Once we SSH into the machine, we can type in `powershell` and press Enter to get started. You'll know you're in PowerShell when you see `PS` at the very left of where you enter commands.

Again, PowerShell uses cmdlets to do things; these are a bit more powerful than your usual Windows commands. The naming convention for these is pretty straightforward, too: `Verb-Noun`. The `Verb` is the action, and the `Noun` is the object that the action will be performed on. `Get-Content` will _get_ (retrieve) the _content_ of a file and display it. `Set-Location` will _set_ the _location_ of the current working directory (analogous to `cd`).

To find all available cmdlets, functions, aliases, and scripts that we can use, we can run `Get-Command` on its own. There's a lot of stuff here. Each command is represented as a `CommandInfo` object - this contains essential information/properties for each command. We can narrow down our search further, of course. If we're looking for functions specifically, we can run `Get-Command -CommandType "Function"`. There are other ways of filtering commands that we'll touch on later.

We can also make use of the `Get-Help (CMDLET)` cmdlet. This guy provides detailed information about cmdlets, including usage, parameters, and examples. Think of these as the `man` pages. You can also retrieve other information about cmdlets; this information is shared in the "REMARKS" section of the output. If you wanted to access examples, for example, you could run `Get-Help (CMDLET) -examples`.

PowerShell aliases are used to help ease the transition from Command Prompt to PowerShell. These are shortcuts or alternative names for cmdlets; many of these are the traditional Command Prompt commands that you may be familiar with. We used `dir` to access a list of the files/folders in a directory before. Here, it's an alias for `Get-ChildItem`, which does the same thing. You can view alises by running `Get-Alias`.

It's also possible to download additional cmdlets from online repositories to extend the functionality of Windows. Of course, this requires an Internet connection, which is NOT present on the VM. If you have access to the Internet, the `Find-Module` cmdlet can be used to find collections of cmdlets in various repositories. You can look for modules with a similar name by running `Find-Module -Name "(PATTERN_TO_SEARCH_FOR)"`. Generally in PowerShell, you can look for similar patterns by using the syntax `Cmdlet -Property "pattern*"` - note the `*` wildcard!

Once you've found the module you're looking for, you can install it by running `Install-Module -Name (NAME)` and follow the instructiosn there.

**[Task 3, Question 1] How would you receive a list of commands that start with the verb `Remove`?** - `Get-Command -Name Remove`

Let's check out some of the other aliases here. Running `Get-Alias` and scrolling through the output. The `echo` command has this alias:

![image](https://github.com/user-attachments/assets/ce91c6fa-1c4a-4248-8422-3de1b51ae908)

**[Task 3, Question 2] What cmdlet has its traditional counterpart `echo` as its alias?** - `Write-Output`

**[Task 3, Question 3] What is the command to retrieve some example usage for the cmdlet `New-LocalUser`?** - `Get-Help New-LocalUser -examples`

## [Task 4] Navigating the File System and Working with Files

PowerShell gives you cmdlets for navigating the filesystem and managing files.
- `Get-ChildItem` lists files and directories in the current working directory.
- `Get-ChildItem -Path (PATH)` lists files and directories in the specified path.
- `Set-Location -Path (PATH)` allows us to change directories.
- `New-Item -Path (PATH_TO_CREATE_ITEM) -ItemType ("Directory" OR "File")` allows us to create files and directories.
- `Remove-Item -Path (PATH_TO_ITEM_TO_REMOVE)` allows us to remove files and directories in one command.
- `Copy-Item -Path (ITEM_TO_COPY) -Destination (DESTINATION)` allows you to copy a file or a directory.
- `Move-Item -Path (ITEM_TO_MOVE) -Destination (DESTINATION)` allows you to move an item.
- `Get-Content -Path (ITEM_TO_READ)` allows you to read an item, e.g. `type`.

**[Task 4, Question 1] What cmdlet can you use instead of the traditional Windows command `type`?** - `Get-Content`

**[Task 4, Question 2] What PowerShell command would you use to display the content of the `C:\Users` directory?** - `Get-ChildItem -Path C:\Users`

Running the command in the previous question yields the following output:

![image](https://github.com/user-attachments/assets/6fd6adb0-4217-4150-bb16-0ec3825a9cfc)

**[Task 4, Question 3] How many items are displayed by the command described in the previous question?** - 4

## [Task 5] Piping, Filtering, and Sorting Data

In command line environments, _piping_ is the process of using the output of one command as the input for another. This creates a sequence (a pipeline, if you will) of operations, where data moves from one command to the next. This is done with the `|` operator in both Windows and Unix CLIs. Piping is particularly useful in Windows since it passes objects through.

Say we want to get a list of files in a directory and sort them by size. We would run `Get-ChildItem | Sort-Object Length`. The first command spits out the contents of the current working directory as objects. Those objects are then sent into `Sort-Object` and sorted by length/size.

There are specific cmdlets that can be used with piping to manipulate data/objects in more complex ways. If you only want to filter objects based on certain conditions, pipe it to the `Where-Object` cmdlet. If we wanted to list only `.txt` files in a directory, we run `Get-ChildItem | Where-Object -Property "Extension" -eq ".txt"`. The `Where-Object` cmdlet only looks for objects whose "Extension" property is equal to "`.txt`". There are other comparison operators worth being familiar with:
- `-ne` is "not equal" - this can be used to exclude objects from results based on certain criteria
- `-gt` is "greater than" - this can be used to filter for objects that exceed a certain value. This is a strict comparison, meaning that objects that are equal to the specified value will be excluded.
- `-ge` is "greater than or equal to" and is the non-strict version of the previous operator. It combines `-gt` and `-eq`.
- `-lt` is "less than" and is a strict operator that filters for objects below a certain value.
- `-le` is "less than or equal to" and is the non-strict version of the previous operator. It combines `-lt` and `-eq`.
- `-like` will filter for objects that match a certain pattern.

The `Select-Object` cmdlet selects specific properties from objects, or it can be used to limit the number of objects returned. This can be used to refine the output to show only the details that you need.

Note that you don't need to limit yourself to one pipe - if you need to pipe things through multiple cmdlets, you can use as many pipes as you'd like in a single command.

The `Select-String` cmdlet searches or text patterns within files. This is like that `grep` command in Linux, or if you found it in Command Prompt, `findstr`. This can be used to find specific content in log files and documents. The syntax for this guy is `Select-String -Path (FILE_TO_SEARCH) -Pattern (PATTERN)`. This cmdlet makes use of regular expressions (regex), which are a more powerful pattern-matching tool.

**[Task 5, Question 1] How would you retrieve the items in the current directory with size greater than 100?** - `Get-ChildItem | Where-Object -Property Length -gt 100`

## [Task 6] System and Network Information

As you may expect, there are many cmdlets that can be used to retrieve information about the system and network. Here's a few of them:
- `Get-ComputerInfo`: retrieves comprehensive system information, including OS info, hardware specifications, BIOS details, and more. The counterpart to this, `systeminfo`, doesn't retrieve as much info.
- `Get-LocalUser`: retrieves all local user accounts on the system, displaying usernames, account statuses, and descriptions.
- `Get-NetIPConfiguration`: retrieves detailed information about network interfaces on the system, including IPs, DNS servers, and gateways.
- `Get-NetIPAddress`: retrieves details for all IPs configured on the system, including those not currently active.

For this question, recall that we're logged in as `captain`. Running `Get-LocalUser` gives us this:

![image](https://github.com/user-attachments/assets/be49b65d-5c20-4e97-85e9-35848facb59f)

The `DefaultAccount`, `Guest`, and `WDAGUtilityAccount` users are all defaults, so we're left with one.

**[Task 6, Question 1] Other than your current user and the default "Administrator" account, what other user is enabled on the target machine?** - `p1r4t3`

**[Task 6, Question 2] This lad has hidden his account among the others with no regard for our beloved captain! What is the motto he has so bluntly put as his account's description?** - A merry life and a short one.

Let's make use of the commands we've been learning so far. We know that he has a folder in `C:\Users`, so we can go there by running `Set-Location -Path C:\Users\p1r4t3`. Then we can see what's in the directory by running `Get-ChildItem`.

![image](https://github.com/user-attachments/assets/b6a9a8c9-7e17-4509-af69-00a1ecf5e99a)

We're probably going to want to go to `hidden-treasure-chest`, so let's run `Set-Location -Path hidden-treasure-chest` and run `Get-ChildItem`:

![image](https://github.com/user-attachments/assets/d5e0d7de-7a50-497c-924b-1111428a4f20)

This is probably the file we want to check out. Running `Get-Content -Path big-treasure.txt` yields

![image](https://github.com/user-attachments/assets/d0b47f58-2db3-42b2-99de-6fe21b699409)

**[Task 6, Question 3] Now a small challenge to put it all together. This shady lad that we just found hidden among the local users has his own home folder in the `C:\Users` directory. Can you navigate the filesystem and find the hidden treasure inside this pirate's home?** - `THM{p34rlInAsh3ll}`

## [Task 7] Real-Time System Analysis

Now let's use some cmdlets to get more detailed information about dynamic aspects on the system - including running processes, services, and active connections. These are useful for troubleshooting and incident investigations.
- `Get-Process` gives a detailed view of all currently-running processes and how much CPU/memory is being used by each.
- `Get-Service` gives information about the status of services on the machine, including those that are running, stopped, and paused.
- `Get-NetTCPConnection` gives information about current TCP connections, local endpoints involved, and remote endpoints involved.
- `Get-FileHash -Path (FILE_NAME)` allows us to retrieve the hash for a specific file, which is useful for incident response, threat hunting, and malware analysis.

Let's run `Get-FileHash` on the `big-treasure.txt` file from earlier. You may want to maximize the Remmina window to see it in full.

![image](https://github.com/user-attachments/assets/f256cfd1-7162-4dba-887a-8c125a75493c)

As a tip, if you allow it in your browser, you can copy and paste hashes from the AttackBox and paste them into the room.

**[Task 7, Question 1] In the previous task, you found a marvellous treasure carefully hidden in the target machine. What is the hash of the file that contains it?** - 71FC5EC11C2497A32F8F08E61399687D90ABE6E204D2964DF589543A613F3E08

For this next question, you can see the example output provided in the room proper.

**[Task 7, Question 2] What property retrieved by default by `Get-NetTCPConneciton` contains information about the process that has started the connection?** - OwningProcess

This last question requires us to look for a service that matches the description used by the `p1r4t3` user. So, let's check `Get-Service`. Note that the output is very long, so we may want to pipe it to `Where-Object`. We know that the motto was "A merry life and a short one". The motto, or some portion of it, should be found in the "DisplayName" property of the service. So let's run `Get-Service | Where-Object -Property "DisplayName" -like "*merry*"`. This yields

![image](https://github.com/user-attachments/assets/e9472086-fd9d-480d-888e-763ec64bc7df)

**[Task 7, Question 3] With this information and the PowerShell knowledge you have built so far, can you find the service name?** - `p1r4t3-s-compass`

## [Task 8] Scripting

One last thing before we wrap up. PowerShell is often used for scripting, or writing/executing commands listed out in a text file to automate tasks. A script is effectively a computer's to-do list, and it can be used to help save time, reduce errors, and perform tasks that are too complex and tedious to do manually.

Scripting is a bit beyond the scope of this room; however, it has important ramifications for both cybersecurity and IT in general:
- In blue team environments, PowerShell scripts can be used to automate tasks including log analysis, anomaly detection, and extraction of indicators of compromise. This can even be used for really advanced stuff, like reverse-engineering.
- In red team environments, PowerShell scripts are used to automate tasks like system enumeration, remote command execution, and obfuscation. All of these are vital to success in a penetration test/ethical hacking scenario and can be used to simulate attacks.
- System administrators use PowerShell to automate integrity checks, manage system configurations, and secure networks in remote and large-scale environments. They can be used to enforce security policies, monitor system health, and respond to security incidents, thus enhancing security posture.

Note that `Invoke-Command` is a very useful cmdlet when it comes to working with scripts. This allows you to remotely manage and automate tasks across machines, as well as execute payloads and commands on target systems during a red team exercise. The examples provided on its `Get-Help` page include
- `Invoke-Command -FilePath c:\scripts\test.ps1 -ComputerName Server01` to run a script on a server, and
- `Invoke-Command -ComputerName Server01 -Creedntial Domain01\User01 -ScriptBlock { Get-Culture }` to run a script on a remote server.

The first command shows that we can run scripts on remote machines, whereas the second command shows that we can run a command (or a series of commands) on a remote machine without necessarily knowing how to script.

**[Task 8, Question 1] What is the syntax to execute the command `Get-Service` on a remote computer named "RoyalFortune"? Assume you don't ned to provide credentials to establish the connection.** - `Invoke-Command -ComputerName RoyalFortune -ScriptBlock { Get-Service }`

## [Task 9] Conclusion

And that'll do it. That's all we need to know when it comes to using PowerShell... and by that, I mean there's a lot more we can learn, but this'll get us started. In the next room, we'll go over the Linux command line in greater detail than before.
