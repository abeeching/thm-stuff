# Linux Fundamentals 3

## [Task 1] Introduction

This is the final part of the Linux Fundamentals trilogy. So far, we've gone over fundamentals and important commands; we'll wrap up by going over some useful utilities and applications that you may use in your day-to-day work. This includes automation methods, package management solutions, and service/application logging.

## [Task 2] Deploy Your Linux Machine

You will deploy and get into the Linux machine as you did in the previous room:
1. Click on Start Machine.
2. Note the Target IP Address.
3. Start the AttackBox and open a terminal there.
4. Run `ssh tryhackme@(IP)`, replacing `(IP)` with the Target IP Address.
5. Type `yes` to trust the remote machine.
6. Type the password `tryhackme`.

## [Task 3] Terminal Text Editors

So far, we've only stored text in files by using the `echo` command and the `>` and `>>` operators. That's not very efficient - surely we can do better than this, right?

As it would turn out, we can! There are many text editors (in fact, there was even a whole war over which editor is better), but we'll look at two of them here: `nano` and `vim` (aka `vi`).
- To edit a file with Nano, simply type `nano (FILENAME)`. Once you execute the command, you can edit the file. Use the up and down arrow keys to get to where you want to go, or press Enter on the keyboard to create a new line. There are features such as searching, copying/pasting, jumping to a line number, and finding out what line number you're on. These features can be accessed by pressing CTRL (which Linux represents as `^`).
- Vim is a more advanced text editor, and while it takes longer to familiarize yourself with it, you can get some pretty crazy work done. You can customize it, making the keyboard shortcuts whatever you want. It automatically hihglights syntax which is helpful when you're working with code. It also works on all terminals; `nano` may not be installed on certain terminals. You can refer to resources such as cheatsheets for more information how to use `vim`. There's a THM room for it too.

We can try messing with `nano` now. If we run the command `nano task3` from the home directory in the VM, we see this:

![image](https://github.com/user-attachments/assets/ae417375-b319-45db-b9aa-8e34e18573af)

There's not much going on here, but had this been a larger document, we could have used the arrow keys to navigate the file or used `CTRL+W` to find the `THM` prefix used in the flag. When we're done, we can use `CTRL+X` to close it.

**[Task 3, Question 1] Edit `task3` located in `tryhackme`'s home directory with Nano. What is the flag?** - `THM{TEXT_EDITORS}`

## [Task 4] General/Useful Utilities

There are a handful of utilities you may find useful as you work with Linux and THM.
- `wget`: This allows you to transfer files, e.g. downloading programs, scripts, you name it. This will download files from the web over HTTP, as if you were accesing the file from a browser. Just give it the address of the resource you want to download, e.g. `wget (LINK_TO_FILE)`.
- `scp`: Also known as Secure Copy, this is a way of securely copying files between two remote computers over SSH. It works in a source-destination structure, and as long as you know the usernames and passwords for your system and a remote system, you can send files to and from with ease.
  - To copy to a remote system: `scp (FILE_ON_LOCAL_SYSTEM) (REMOTE_USERNAME)@(IP):(DESTINATION)`
  - To copy from a remote system: `scp (REMOTE_USERNAME)@(IP):(REMOTE_FILE_PATH) (DESTINATION)`
- `python3 -m http.server`: Linux machines come with Python 3, which comes with a lightweight HTTP Server module. This turns your computer to a simple web server that can be used to serve files to other machines. Other machines, if they're connected to your machine, can download files via `wget` or `curl`. Here's how you would use it:
  - Say you want to host a web server so another machine can download a certain file. Navigate to where that file is using `cd`, then run `python3 -m http.server`. This will automatically run a server over port 8000, though you may specify other ports as needed.
  - The other machine can then use `wget http://(IP):8000/(FILE)` to download the file.
 
A few things on the Python 3 HTTP server:
- It will take up the terminal. You may want to background it, or you can open another terminal and do work there. When you're done, press `CTRL+C` to close it.
- This module does not have indexing capabilities, meaning you must know the exact name and location of the file you want to use. Other lightweight web servers exist with that kind of functionality, but the Python HTTP server will do the job just fine in most cases.

Let's interact with the Python webserver. Running the command `python3 -m http.server` in the attached VM over SSH will start the HTTP server; we can access files from this server via the AttackBox proper, since we're connected to it:

![image](https://github.com/user-attachments/assets/77ff834e-65a4-44ed-b1de-dc757a8554b1)

We don't have much control over the SSH VM while the web server is running, so let's go open a new terminal on the AttackBox. We can either open a new one outright, or we can go to File -> Open Tab to open a new terminal tab where we can actually do work. I prefer to do it this way.

Once we do this, we can run the command `wget http://(MACHINE_IP):8000/.flag.txt` and download the flag to our AttackBox. Then we can use `cat` to read off the file.

![image](https://github.com/user-attachments/assets/b0ce5492-4ca2-44f2-9f85-70f860c0e16d)

**[Task 4, Question 1] Download the file `http://(MACHINE_IP):8000/.flag.txt` onto the TryHackMe AttackBox. Remember, you will need to do this in a new terminal. What are the contents?** - `THM{WGET_WEBSERVER}`

Once we're finished with the webserver, we can hit CTRL+C to close out the web server and go back to the remote machine terminal.

## [Task 5] Processes 101

Processes are just programs that are running on a machine. These are managed by the kernel, which assigns each process an ID, known as the Process ID (PID). The PID increments for the order in which the process starts, so the 60th process will get the PID 60. There are a few commands worth noting when it comes to dealing with processes:
- `ps`: This command lets you see a list of the running processes in your current session. This includes some information like the status code, the session that's running it, how much time it's using on the CPU, and the name of the program ebing executed.
- `ps aux`: This command lets you see proecsses run by other users and those that don't run from a session (i.e., system processes).
- `top`: This command gives you real-time statistics abou running processes instead of a one-time view. This information refreshes every ten seconds and when you use arrow keys to browse the output.
- To terminate processes, you may want to issue signals to tell the process to stop running. The `kill (PID)` command will stop a process with the given PID. There are a few signals that we can send to a process when it is killed:
  - SIGTERM: Kill the process. Do some cleanup beforehand, though.
  - SIGKILL: Kill the process. Don't cleanup.
  - SIGSTOP: Stop and suspend a process.
 
It is worth considering how processes start. The operating system will use namespaces to split up resources available on the computer, such as CPU, RAM, and priority, to processes. Processes are isolated with namespaces, and each set of processes has a slice of computing resources accessible to them.

The process with PID 0 is the process that starts when the system boots, known as the system's init or `systemd` in Ubuntu; this process provides a way to manage user processes and it sits between the OS and the user. Any program that we start will start as a child process of `systemd`. It shares its resources, but otherwise runs as its own process.

Some apps can be set to start on system boot, including web servers, database servers, and file transfer servers. To let this happen, we use `systemctl`, which allows us to interact with the `systemd`. The command for this is `systemctl (OPTION) (SERVICE)`. There are various options we can apply:
- `start` to start a service
- `stop` to stop a service
- `enable` and `disable` to have it start/stop on boot

Processes can run in two states: the background and the foreground. Commands that are run in the foreground are those that are actively doing something on the terminal; for instance, `echo` runs in the foreground which is why you see output displayed in the terminal. If you run a command and add the `&` operator, you're given the PID of the process and it does its job in the background.

Note that you can send a process to the background by using `CTRL+Z`. It can be used to "pause" a script's or command's execution.

If you ever need to send a process back to the foreground, you can use the `fg` command to do so. This command will take whatever is in the background and start running it in the foreground.

**[Task 5, Question 1] If we were to launch a process where the previous ID was "300," what would the ID of this new process be?** - 301

**[Task 5, Quesiton 2] If we wanted to cleanly kill a process, what signal would we send it?** - SIGTERM

If we want to view all processes as well as some extra info regarding each, we can run `ps aux`. This will give us a flag in the remote machine:

![image](https://github.com/user-attachments/assets/d754a3dd-5d2a-4d52-9c22-65a7b94d58f1)

**[Task 5, Question 3] Locate the process that is running on the deployed instance. What flag is given?** - `THM{PROCESSES}`

**[Task 5, Question 4] What command would we use to stop the service `myservice`?** - `systemctl stop myservice`

**[Task 5, Question 5] What command would we use to start the same service on the boot-up of the system?** - `systemctl enable myservice`

**[Task 5, Question 6] What command would weu se to bring a previously backgrounded process back to the foreground?** - `fg`

## [Task 6] Managing Your System: Automation

Sometimes you might want to schedule certain tasks and actions to take place once a system has booted. This includes running commands, backing up files, and launching programs. Linux offers functionality for this in the form of the `cron` process and, more specifically, with `crontabs`. The `cron` process is launched at boot and helps manage cron jobs, or scheduled tasks. Crontabs are files that we can create with special formatting to tell `cron` what we want to schedule.

The values required for a crontab are
- MIN: The minute to execute at
- HOUR: The hour to execute at
- DOM: The day of month to execute at
- MON: The month of the year to execute at
- DOW: The day of the week to execute at
- CMD: The command to execute

If we had, say, a backup script, and we wanted to run it every 12 hours or so, we might use this formatting: `0 */12 * * * (BACKUP_SCRIPT)`. You'll notice the inclusion of the asterisk wildcard - this means that we don't care what value goes into that particular field. We just care that it runs every 12 hours.

It can be pretty tricky to get used to, so there are websites such as Crontab Generator and Cron Guru.

Crontabs can be edited using `crontab -e`, where you can select an editor (e.g. Nano) to edit your crontab.

Let's do that now in the remote VM. Running `crontab -e` in the remote machine opens up the Cron file in Nano, and scrolling down tells us that a script will run, and the time frame is "@reboot". This suggests that the script will automatically run at, well, reboot.

![image](https://github.com/user-attachments/assets/2c85609b-4430-48fc-bb90-fa610db18407)

**[Task 6, Question 1] When will the crontab on the deployed instance run?**

## [Task 7] Managing Your System: Package Management

When developers want to submit software to the community, the software will be submit into an `apt` repository. If approved, then the programs and tools will be released for the wider public. Operating systems such as Linux will come with their own software repositories, though you can add other community repositories to the list via the `add-apt-repository` command.

The `apt` command is more generally used for installing software onto the OS. It comes with a set of tools for managing packages and sources of software, as well as installing and removing software. To add repositories, we could use `add-apt-repository`, but we could also do so manually.

Software can be installed with package installers such as `dpkg`, though if we use `apt`, we can keep everything up-to-date whenever we decide to update the system as a whole. When it comes to adding software, integrity is assured with GPG (GNU Privacy Guard) keys; this is a way for developers to assure people that they created the software that's being distributed. If the keys don't match, the software doesn't get downloaded.

The first step for manually geting a repository is to grab the GPG key:
1. Downlaod the GPG key. You could use `wget -q0 - (LINK_TO_GPG_KEY) | sudo apt-key add -`. The `|` is called a "pipe," and will send the output of the first command (`wget`) to the second command (`apt-key`). In this case, we grab the GPG key, and then we use `apt-key` to trust it.
2. We can add the repository of the software we want to download to the apt sources list. We do so by creating a new file in `/etc/apt/sources.list.d` (e.g., `sublime-text.list`).
3. Then we enter the information into the file with a tool like Nano. We could write a line `deb (DOWNLOAD_URL) apt/stable`. You may want to refer to instructions provided by the developers to fill out this file.
4. Once we've added the entry, we need to upgrade apt so it recognizes the entry; this can be done with `apt update`.
5. Once updated, we can install the software that we've trusted and added to `apt` with `apt install (SOFTWARE)`.

If you ever need to remove a package, you can run `add-apt-repository --remove ppa:(PPA_Name)/ppa` or manually delete the file that we added. Once we've done that, we can run `apt remove (SOFTWARE)`.

## [Task 8] Managing Your System: Logs

In earlier parts of this sequence we coered log files and where they can be found. Typically these files can be found in `/var/log`, and they contain information about applications and services running on the system. The OS can handle and manage these logs pretty well.

The logs are a good way of monitoring system health as well as looking for any suspicious events. For instance, a web server will log information about every single request, so it can be used to monitor for issues or attempted intrusions. We may want to look at access and error logs to this end.

The OS itself maintains logs about how well the OS is running, as well as actions that are performed by its users.

We'll want to cd into `/var/log/apache2` on the remote machine and start investigating. We're looking at files that were accessed by users, so we'll want to check the `access.log` files. We can only read the `access.log.1` file, so we'll start there.

![image](https://github.com/user-attachments/assets/c0eb29a6-0cbc-401e-9b0f-cc1e121a891d)

There is only one line in this log. The very first thing in the output here is an IP address, which is the IP address the user is trying to access from.

**[Task 8, Question 1] What is the IP address of the user who visited the site?** - `10.9.232.111`

And the file they accessed can be found in the output starting with "GET".

**[Task 8, Question 2] What file did they access?** - `catsanddogs.jpg`

As an aside, if you ever need to access a file you don't have permission to see, you can put `sudo` in front of the command. As an example, `sudo cat access.log` will display the contents of the log file as long as you have permissions to do so (from the `sudoers` file briefly discussed in the previous room).

## [Task 9] Conclusions and Summaries

And that's all there is for the fundamentals! Getting familiar with Linux will take some time - it really does take practice. But once you get a good handle on how Linux works, you'll be able to do some pretty powerful things pretty easily. We covered how to use text editors, downloading/serving contents via web servers, how processes work, and how to maintain and automate the system. There's still more you can learn about scripting and other advanced features.
