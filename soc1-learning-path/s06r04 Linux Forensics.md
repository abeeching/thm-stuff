# Linux Forensics

## [Task 1] Introduction

In previous rooms, we've spent time performing forensics on Windows machines. Windows is still a commonly-used desktop OS (particularly in the enterprise), but Linux is still used quite often. Linux is commonly used in servers that host different services for enterprises, either in endpoints or via public-facing servers. Thus, it's important to understand how to conduct forensics on Linux machines.

## [Task 2] Linux Forensics

Linux is an open-source operating system that comes in different flavors - distributions. It's generally lightweight and can run on low resources, and it can be customized to meet certain requirements. For more background information on Linux, you should view the Linux Fundamentals sequence of rooms.

The key thing to note for now is that distributions have minor differences between them. Sometimes these differences may be cosmetic, other times these differences may be more pronounced. Some commonly-used distributions include:
- Ubuntu
- Red Hat
- Arch Linux
- OpenSUSE
- Linux Mint
- CentOS
- Debian

For our purposes, we will be focusing on the Ubuntu distribution. It should be possible to perform similar tasks in other distributions.

## [Task 3] OS and Account Information

Our methodology will be similar to what we did for Windows forensics. Our first step, as before, is to identify the system we're on and get basic information about the system. The Registry in Windows gave us this information, but in Linux, we can usually find this information scattered throughout files. To identify forensic artifacts, we must know the locations of these files, and we must know how to read them.

To find OS release information, we can use `cat` to read the file `/etc/os-release`. If you need a refresher on how `cat` works, you can review the prerequisite rooms or read the man pages by running `man cat`.

User accounts are stored in `/etc/passwd`, and this file can also be read with `cat`. This file contains seven colon-separated fields, describing the username, password information, user ID (UID), group ID (GUID), user description, home directory information, and the default shell that is executed when the user logs in. User-created accounts, as in Windows, have UIDs of 1000 or higher.

If the file is too hard to read with just `cat`, we can issue the command `cat /etc/passwd | column -t -s :` to make it a little easier to read.

You'll notice that the password information for each user is probably going to be `x`; this means that the password information is actually stored in `/etc/shadow`. The description will often mention full names/contact information/other relevant info.

In case we need to obtain information about groups, we can `cat /etc/group`. This file contains information about different user groups present on the host. The format is Group Name : Password Information : Users in Group.

In Linux, privileged commands can only be executed with `sudo`, and this is only granted to users present on the sudoers list. This information can be read by running `sudo cat /etc/sudoers` - note that you have to elevate permissions to see the contents of this file.

Linux will log login attempts, whether they were successful or not. The file `/var/log/btmp` stores information about failed logins, while the file `/var/log/wtmp` stores information about successful logins. These are binary files, so you'll have to read them with the `last` utility insttead of `cat`. For instance, `sudo last -f /var/log/wtmp` will give you the contents of `/var/log/wtmp`.

Every user that auhtenticates on a Linux host will be logged in the auth log, which is a file found at `/var/log/auth.log`. This file can be read with `cat`, though due to its size it may be more helpful to use `tail`, `head`, `more`, or `less`. This file can give us information about users levating privileges, sessions that were opened and closed, and so on.

Let's start investigating the VM. In the first question, we'd like to find the `audio` group information. The command to get this information is `cat /etc/group`, though we can narrow down the output further by running `cat /etc/group | grep audio`. This yields the following output:

![image](https://github.com/user-attachments/assets/01a1e395-4884-448a-8805-ad5e7baae3c1)

**[Task 3, Question 1] Which two users are members of the group `audio`?** - `ubuntu`,`pulse`

Similarly to the above question, we can run the command `cat /etc/passwd | grep tryhackme`. This pulls the information on the users in the system and specifically filters for the `tryhackme` user. The first number in the output is the UID.

![image](https://github.com/user-attachments/assets/f14056fa-bdc5-4cd4-89a0-8744f7a9e79c)

**[Task 3, Question 2] In the attached VM, there is a user account named `tryhackme`. What is the UID of this account?** - 1001

If we want to see how long sessions are run for, we would have to check `/var/log/wtmp`. Remember that this isn't a text file, so we'll want to run `sudo last -f /var/log/wtmp`. Investigating the output, we see a session that has started on April 16th at 20:10 -- this is at the bottom of the output. The data in parentheses tells us how long the session ran for.

![image](https://github.com/user-attachments/assets/221c93e5-eead-476b-b268-4d8309d370ea)

**[Task 3, Question 3] A session was started on this machine on Sat Apr 16 20:10. How long did this session last?** - 01:32

## [Task 4] System Configuration

Once we've identified the OS and relevant accounts, a next step might be to look into how the system was configured.

One place to start here would be to `cat /etc/hostname`. This simply stores the hostname of the machine and can be helpful to make sure that you're working with the correct host.

If you recall from earlier rooms, the timezone is of vital importance. This gives an indicator of where the device has been used, or a timeframe in which the device might be used, and it generally helps with constructing a timeline of events. This information can be found by running `cat /etc/timezone`.

To find information about configured network interfaces, you can run `cat /etc/network/interfaces`. To find information about MAC and IP addresses for different interfaces, you can use the `ip` utility - you can see more information on how to use it by reading its man pages (`man ip`). For intsance, the command `ip address show` will display IP/MAC address information, though this is only helpful if you're on a live system.

On a live system, knowing what network connections are active can help provide additional context to the investigation. The `netstat` utility can be used to find active network connections on a Linux host, and you can learm more about how it works by running `man netstat`. If you'd like to find what processes are actively communicating over the network, the command `netstat -natp` can help with that.

It can also help to check what processes are running on a live machine. The `ps` utility gives us information about running processes; as always, you can run `man ps` for more information on how to use it. If you run `ps aux`, you can get a list of what porocesses are running on the system, and what commands were ran.

DNS name assignment information can be found in `/etc/hosts`. You can learn more about the hosts file by running `man hosts`. Information about the DNS servers that a Linux host talks to for DNS resolution is stored in `/etc/resolv.conf`. You can use `cat` to read both of these files.

Now let's get the hostname of this VM. Remember that we can view the hostname by running `cat /etc/hostname`.

![image](https://github.com/user-attachments/assets/e0940d6e-9353-445d-9ffd-5448fbe4d180)

**[Task 4, Question 1] What is the hostname of the attached VM?** - Linux4n6

Then we can check the timezone by running `cat /etc/timezone`.

![image](https://github.com/user-attachments/assets/1ccfa7db-ebc9-4cf1-be82-7ac01cadbae1)

**[Task 4, Question 2] What is the timezone of the attached VM?** - Asia/Karachi

If we run `netstat -natp`, we can see what programs are communicating over the network. We're looking for the program listening on the local address `127.0.0.1:5901`. It's at the very top of the tabular output in this instance.

![image](https://github.com/user-attachments/assets/eef8a336-a7b3-4565-9359-63ebd80726a9)

**[Task 4, Question 3] What program is listening on the address `127.0.0.1:5901`?** - Xtigervnc

We know that Xtigervnc is currently running on the system. We can expect to see something relating to it in `ps aux`, so let's run `ps aux | grep Xtigervnc` to see what information we can find.

![image](https://github.com/user-attachments/assets/1f3b1a7f-2100-4b5b-ac9f-e1c042217f90)

We'll notice that the full path of the program is given in the output -- it is `/usr/bin/Xtigervnc`.

**[Task 4, Question 4] What is the full path of this program?** - `/usr/bin/Xtigervnc`

## [Task 5] Persistence Mechanisms

The commands given in the previous tasks should be helpful in figuring out what the environment actually is in your investigation. From there, we can move onto other things, like figuring out what persistence mechanisms exist on the Linux host. These mechanisms are ways that a program can survive after a system reboot; malware authors make use of these to maintain access into the system, even if it is rebooted.

Cron jobs are commands that run periodically after a ste amount of time. A Linux host maintains a list of cron jobs found in `/etc/crontab`. This can be read with `cat`. This file will contain information about the time interval after which the command has to run, the username that runs the command, and the command itself. It can also contain scripts to run, where the script that needs to be run will be placed on the disk, and the command to run it.

Linux, like Windows, makes use of services that are set to start and run in the background after system boot. These services can be found in `/etc/init.d`, and we can check its contents by running `ls /etc/init.d`.

If a bash shell is spawned, it will start running commands stored in `.bashrc`. This file can be thought of as a startup list of actions to be performed, so it might be a good place to look for persistence. System-wide settings can be found in `/etc/bash.bashrc` and `/etc/profile`.

We'll want to investigate the `.bashrc` file for the `ubuntu` user. We can run `cat .bashrc` from `ubuntu`'s home directory (to get to it easily, you can run `cd ~`). We'll want to look out for the `HISTFILESIZE` variable. This gives us the size of the history file.

![image](https://github.com/user-attachments/assets/47176825-6e5a-40cc-9deb-c2f5f8ec5abd)

**[Task 5, Question 1] In the `bashrc` file, the size of the history file is defined. What is the size of the history file that is set for the user `ubuntu` in the attached machine?** - 2000

## [Task 6] Evidence of Execution

Knowing what programs have been executed on a host is one of the main purposes of forensic analysis. On a Linux host, we can find evidence of execution from the following sources.

All commands that are run on Linux using `sudo` will be stored in the auth log, discussed in Task 3. The `grep` utility can be used to filter out only the required information from the audit log, e.g. `cat /var/log/auth.log* | grep -i COMMAND | tail`.

Any commands other than ones run using `sudo` are stored in the bash history. Every user's bash history is stored separately in their home folder. Thus, when examining bash histories, we should get the `.bash_history` file from each user's home directory, including the root user (so we can make a note of all commands run as root). The command `cat ~/.bash_history` will work well for this purpose. Note that `~` can be replaced with another user's home directory as needed.

Lastly, we may want to consider what files were accessed with text editors. For the case of Vim, this information is stored in the home directory, under `.viminfo`. This contains command line history, search string history, and more for the opened files. We can view the contents of this file by running `cat ~/.viminfo`, replacing `~` with another user's home directory as needed.

Now, let's see. We'll want to see what commands the `tryhackme` user issued to install a package. To do so, we should check their bash history. We would have to run `sudo cat /home/tryhackme/.bash_history` and check through the commands. The command at the bottom is what we're looking for:

![image](https://github.com/user-attachments/assets/3d6439bb-85c4-4ac0-8d38-2e95085c8ad3)

**[Task 6, Question 1] The user `tryhackme` used `apt-get` to install a package. What was the command that was issued?** - `sudo apt-get install apache2`

To figure out where `net-tools` was installed from, we'll want to run the command `cat /var/log/auth.log.1 | grep -i COMMAND | less`. You could use `auth.log*` but I've noticed it acts a little funky. If you look through the output, you can find where the command was being issued from - it'll be noted under `PWD`.

![image](https://github.com/user-attachments/assets/664cb474-4b8d-4ca7-a1ad-a183dc83bc26)

**[Task 6, Question 2] What was the current working directory when the command to install `net-tools` was issued?** - `/home/ubuntu`

## [Task 7] Log Files

One of the most important sources of information on the activity of a Linux host is the log files. These maintain a history of activity performed on the host. The amount of logging depends on the logging level defined by the system. Here's some important log sources -- we can generally find logs in `/var/log`.

Syslog: This contains messages recorded by the host about system activity. The detail which is recoded in these messages is configurable via the logging level. We can run `cat /var/log/syslog` to view the contents of this log. This is a huge file, so you should use `tail`, `head`, `more`, or `less` to make it readable.

The terminal can show the system time, system name, the process that sent the log (process ID), and the details of the log. This m ay include cron jobs, rotated logs (i.e. logs that were rotated into other files for storage's sake). We may need to use the asterisk (*) wildcard to search through all syslog files.

Auth logs: These were discussed in previous tasks, and are used to monitor information about users and authenticaiton-related logs. This may include creation of users, groups, and addition of users into groups.

Third-party logs can be created for different third-party applications, like web servers, databases, and file share servers. They are typically found in `/var/log` - you may need to run `ls /var/log` to see the available log types.

If we want to see how the hostname was changed, we could examine log files. It turns out that `syslog` can help us here. The command `cat /var/etc/syslog* | grep hostname` will work; we just have to sift through the output some. We see these lines here:

![image](https://github.com/user-attachments/assets/15af012c-4908-4fe9-a9aa-f579e08711ee)

These happen before the hostname is set to `Linux4n6`, so this is probably what we're looking for!

**[Task 7, Question 1] Though the machine's current hostname is the one we identified in Task 4, the machine earlier had a different hostname. What was the previous hostname of this machine?** - `tryhackme`
