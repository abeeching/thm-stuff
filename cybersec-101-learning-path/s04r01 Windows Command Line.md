# Windows Command Line

## [Task 1] Introduction

This section of the learning path focuses on command processors and command-line interfaces (CLIs). Graphical user interfaces (GUIs) are pretty nice and convenient; they're intuitive and easy to find stuff in. A CLI has a steeper learning curve in comparison, but as you learn how the command line works, you'll find that it is fast and efficient.
- It takes less clicks to get to the utility you want to use. In fact, if you're clicking anything, you're just clicking to get into the CLI anyway - actually accessing the utility just involves typing a command.
- Since CLIs don't really involve graphics, they're less resource-intensive, making them useful for older hardware and cloud-based environments.
- CLIs are useful for automation - just create a batch file with the commands and utilites you need to run and have it run repeatedly.
- You can access CLIs over the network via SSH - this allows you to remotely manage devices, even on slower networks and on systems with fewer resources.

In this particular room, we'll be using Windows Command Prompt to do some basic information-gathering, troubleshooting, and system management. The VM will have to be accessed over SSH as done in previous rooms.

An understanding of Windows and AD fundamentals is expected.

**[Task 1, Question 1] What is the default command line interpreter in the Windows environment?** - `cmd.exe`

## [Task 2] Basic System Information

The commands covered here are as follows:
- `set`: We're only allowed to issue commands (i.e., run utilities) from the Windows path. You can use the `set` command to see what that entails - the relevant line of output will be `Path=`.
- `ver`: Used to display the OS version.
- `systeminfo`: Used to display various system information, including OS info, system details, processor/memory information.
- `more`: You can pipe a command through `more` if the output gets too long - for instance, `systeminfo | more` would allow you to view the `systeminfo` output a line at a time by pressing Enter, or one page at a time by pressing Space. When done, press CTRL+C.
- `help`: Provides help on how to use a command.
- `cls`: Clears the Command Prompt screen.

If we run `ver` in the machine, we see this output:

![image](https://github.com/user-attachments/assets/b0af01c4-47ac-4394-88ff-42f7be08e640)

This gives us the OS version.

**[Task 2, Question 1] What is the OS version of the Windows VM?** - 10.0.20348.2655

And if we run `systeminfo` and scroll to the top of the output, we see the hostname:

![image](https://github.com/user-attachments/assets/9286f4b2-fd05-4382-a8c4-5bd90804c5ee)

**[Task 2, Question 2] What is the hostname of the Windows VM?** - WINSRV2022-CORE

## [Task 3] Network Troubleshooting

Network configuration and troubleshooting can be performed in the GUI, but the command line has many features to this end, too.
- `ipconfig`: This allows you to check your network configuration, including IP addresses, subnet masks, and default gateways. For more information, run `ipconfig /all`.
- `ping (DOMAIN_OR_IP)`: A useful troubleshooting tool that can be used to see if a machine can access a particular server on the Internet. ICMP packets are sent to the server, and we see if we can get a response. If we do, we'll be notified in the Command Prompt, and we'll get additional information, including how long the process took on average.
- `tracert (DOMAIN_OR_IP)`: Also known as trace route. This traces the network route traversed to get to the target. It relies on routers telling us when they've dropped a packet due to the time-to-live (TTL) value being 0.
- `nslookup (DOMAIN)`: This looks up a host or domain and returns its IP address. On its own, `nslookup` uses the default name server. However, you can also specify a name server to use - `nslook (DOMAIN) (ADDRESS_OF_NAME_SERVER)`.
- `netstat`: This displays current network connections and listening ports. On its own, it shows established connections, though there are other options available too.
  - `netstat -h`: Displays help on how to use the command
  - `netstat -a`: All established connections and listening ports
  - `netstat -b`: Shows program associated with each listening port and connection
  - `netstat -o`: Shows the PID associated with the connection
  - `netstat -n`: Uses a numerical form for addresses and port numbers
  - If you use the flag `-abon`, it combines the a, b, o, and n switches above into one output. This gives a long output, but it can be useful if you need detailed information.
 
**[Task 3, Question 1] Which command can we use to look up the server's physical address (MAC address)?** - `ipconfig /all`

We can see what processes are listening by running `netstat -abon`. We're looking for a process with a local address `0.0.0.0:3389`... this seems to be the one:

![image](https://github.com/user-attachments/assets/90207f30-703e-472e-aa60-93b54b085cc8)

**[Task 3, Question 2] What is the IP address of your gateway?** - TermService

And, of course, we can get gateway information by running `ipconfig`:

![image](https://github.com/user-attachments/assets/12796253-aa96-45b9-af86-543b4d3060b2)

We'll want to pull the value set for Default Gateway.

**[Task 3, Question 3] What is the IP address of your gateway?** - `10.10.0.1`

## [Task 4] File and Disk Management

Now let's learn how to browse directories and interact with files from the Windows Command Line.
- `cd`: Displays the current drive and directory, if ran on its own.
- `dir`: View a listing of directories, like the Linux `ls` command.
- `dir /a`: Displays hidden and system files as well, like `ls -a` in Linux.
- `dir /s`: Displays files in the current directory and in all subdirectories.
- `tree`: Visually represents the child directories and subdirectories.
- `cd (TARGET_DIRECTORY)`: Allows you to change to a different directory.
- `cd ..`: Move back up one level of directories.
- `mkdir (DIRECTORY_NAME)`: Creates a directory.
- `rmdir (DIRECTORY_NAME)`: Removes a directory.
- `type`: Prints the contents of a text file to the screen, like `cat` would in Linux.
- `copy (FILE_TO_COPY) (DESTINATION)`: Copies a file from one location to another.
- `move (FILE_TO_MOVE) (DESTINATION)`: Moves a file from one location to another.
- `del (FILE)` or `erase (FILE)`: Deletes a file.

Note that the `*` wildcard can be used to reference multiple files. For instance, `*.md` will allow you to interact with all files that have the `.md` extension at the end.

Let's try out these commands. We need to get to `C:\Treasure\Hunt`. We can start by running `cd ..` until we get back to `C:\`. The Command Prompt tells you where you are in the filesystem at any time; just look to the left of where you type your commands.

From here, we can run `cd Treasure` and then `cd Hunt` to get to the proper directory. Then we can run `dir` to see what files are inside. The only file in there is `flag.txt`, so we can run `type flag.txt` to see what's inside.

![image](https://github.com/user-attachments/assets/015ff6db-f775-44a0-b42e-f82af9e60073)

**[Task 4, Question 1] What are the file's contents in `C:\Treasure\Hunt`?** - `THM{CLI_POWER}`

## [Task 5] Task and Process Management

In the Windows Fundamentals rooms, we covered Task Manager, which is a utility that allows us to manage and kill non-responsive processes. We can do much the same from Command Prompt.
- `tasklist` allows you to list running processes, as well as how much memory is being used.
- `tasklist /?` gives you help on filters you can use for `tasklist`. The output in `tasklist` is very long, so it is helpful to know how to filter it.
- `tasklist /FI (FILTER)` allows you to set a certain filter. If we wanted to find a specific image name, we can use the filter `"imagename eq (IMAGE_NAME)"`.
- `taskkill /PID (TARGET_PID)`: If we know the PID, we can terminate a task by running this command.

**[Task 5, Question 1] What command would you use to find the running processes related to `notepad.exe`?** - `tasklist /FI "imagename eq notepad.exe"`

**[Task 5, Question 2] What command would you use to kill the process with PID 1516?** - `taskkill /PID 1516`

## [Task 6] Conclusion

These are some of the more practical commands for accessing and interacting with a networked system on the command line. There are a few other commonly-used commands that you should look into; these are primarily helpful for troubleshooting:
- `chkdsk`: Checks the filesystem and disk volumes for errors and bad sectors.
- `driverquery`: Displays a list of installed device drivers
- `sfc /scannow`: Scans system files for corruption and repairs them if possible

If you're not sure on how to use a certain command, you can use `/?` with most commands to get a help page. You can also check online documentation, if you have access to it.

Also, an aside: You can use `more` to display the contents of text files, too. Just run it as `more (FILE_TO_DISPLAY)`.

Now we know the Windows Command Line fundamentals. Windows offers another command line for some more advanced functionality. In the next room, we'll be covering PowerShell.

Here are some additional questions to make sure we know how to use `/?`. We're told that `shutdown /s` is used to shut a system down. We'd like to find more information on the `shutdown` command, so let's run `shutdown /?` and see what we can find. After scrolling through, we see an option that can be used to just restart a computer.

![image](https://github.com/user-attachments/assets/28459e96-fe79-40ce-8e0f-f9036a08013d)

**[Task 6, Question 1] The command `shutdown /s` can shut down a system. What is the command you can use to restart a system?** - `shutdown /r`

Similarly, we see this on how to abort a system shutdown.

![image](https://github.com/user-attachments/assets/88332203-16f5-449f-a585-81ff466ad89c)

**[Task 6, Question 2] What command can you use to abort a scheduled system shutdown?** - `shutdown /a`
