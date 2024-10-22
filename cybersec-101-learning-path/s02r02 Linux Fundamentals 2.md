# Linux Fundamentals 2

## [Task 1] Introduction

This is the second part of the Linux Fundamentals sequence, where we'll apply knowledge from the first part to do some more advanced filesystem tasks. Oftentimes in TryHackMe rooms (and in real-life engagements) you will be forced to connect to a machine remotely instead of using Split Screen View, so we'll start by learning how to use SSH to connect to machines.

Once in the machine, we'll build upon the commands we learned previously by introducing flags and arguments, and then use this knowledge to do more useful tasks in the filesystem, including copying and moving files. From there, we'll learn how Linux handles file/folder access, and then we'll use that knowledge to run some scripts and executables.

## [Task 2] Accessing Your Linux Machine Using SSH (Deploy)

The in-browser functionality that we had in the previous room makes use of the same protocol that we'll be using here: Secure Shell, also known as SSH. This is a protocol often used for connecting to and interacing with a machine remotely. It encrypts whatever input we provide and sends it to the remote machine, allowing interaction to occur. One of the THM rooms will give more details on the protocol, but this is all we really need to know for now.

To access this machine, start by clicking Start Machine, as always. The top of the room will display the Target IP Address that you need to connect to - this will be different for people. From here, go up to the top of the room and click Start AttackBox.

The AttackBox is a Linux machine hosted in the cloud and can be used to interact with machines you start in various THM rooms, like this one! Once we get into the AttackBox, you'll need to provide the following information to connect to a machine with SSH:
- The IP address of the remote machine
- Credentials for a valid account on the remote machine

Let's open up a terminal and get to work. Our credentials will be `tryhackme` for the username and `tryhackme` for the password. To begin connecting with SSH, the general command is `ssh (USERNAME)@(IP)`. In our case, we'll want to run the command `ssh tryhackme@(IP)`, where `(IP)` is the Target IP Address you noted down earlier.

WHen you run this command, you will be asked if you want to trust the host - you can type `yes` and then press ENTER. Then you'll be prompted to enter a password. When you're prompted to type a password (and this is the case whether you're logging on to a Linux terminal remotely or if you're using a local terminal), Linux will not display any symbols on the screen. None. It's still accepting input, though - that's just there to stop any eavesdroppers from getting information about how long your password is. Type the password, and then hit Enter to log in.

## [Task 3] Introduction to Flags and Switches

Now that we're logged in, we can get to work. Many commands allow us to provide arguments, which are typically identified with a hyphen followed by a keyword. The keyword is commonly known as a flag or a switch. Some commands don't accept flags and switches; we'll discuss how to identify those commands later.

For now, let's focus on commands that can accept flags and switches. When you use a command on its own, it will perform whatever its default behavior is. The `ls` command lists the contents of the working directory by default, without listing hidden files. But what if we want to find those hidden files? We can use the `--all` flag, or the `-a` switch, to list those hidden files. Any hidden files on Linux are preceded by a dot, e.g. `.hiddenfolder` won't show up unless you use `ls -a`.

Commands that accept switches will have a `--help` option, which lists the possible options that the command accepts, along with a description and example of how to use it. The `--help` option is a formatted output of man pages, which we discussed in the Search Skills room...

...But let's recap. Manual pages give us information about system commands, functions, and applications in a Linux machine. They can be accessed within Linux itself, or they can be found online. To access documentation in Linux, you can use `man (COMMAND)` to get documentation for a specific command. For instance, `man ls` gives us information about the `ls` command. You can use the up and down arrow keys to read through the man page.

**[Task 3, Question 1] What directional arrow key would we use to navigate down the manual page?** - down

This next question refers to the `ls` command, so let's see the man pages for it. Running `man ls` and scrolling through, we see this:

![image](https://github.com/user-attachments/assets/8ebb611d-503e-491d-b348-f03bd1057b32)

It appears we've found our flag! Note that we could also find this if we ran `ls --help`. When you're done reading the man pages, press `q` to exit them.

**[Task 3, Question 2] What flag would we use to display the output in a "human-readable" way?** - `-h`

## [Task 4] Filesystem Interaction, Continued

So far, we've learned how to list the contents of a folder, find specific files, and navigate the filesystem. Now let's learn how to create, move, and delete files. Briefly:
- The `touch` command creates files.
- The `mkdir` command makes directories.
- The `cp` command copies files and folders.
- The `mv` command moves files and folders.
- The `rm` command removes files and folders.
- The `file` command determines the type of a file.

In all of these commands, you can simply give the name of a file in the working directory with these commands, or if you know where a certain file of interest is, you can give the absolute path to that file.

Now, let's start by creating files and folders.
- To create a file, use the `touch` command. This takes exactly one argument: the filename. If we wanted to create a file called `example`, we run `touch example`. This creates a blank file called `example`; if we want to add contents to this file, we'll need to make use of a text editor or use `echo` and shell operators.
- To make a folder, use the `mkdir` command. Again, this takes the directory name as a single argument. The command `mkdir example_dir` creates an empty directory called `example_dir`.

But what if we want to remove files and folders?
- To remove a file, simply use `rm` and provide the filename as an argument. The command `rm example` will remove the example file disussed in previously.
- To remove a folder, you also use `rm`...but you must also provide the `-R` flag to remove any contents inside the folder as well as the folder itself. If I ran `rm -R example_dir`, I'd remove the `example_dir` directory and anything that may be inside of it.

Let's say we want to copy and move files around.
- To copy a file, use `cp`. This takes two arguments: the name of the existing file, and the name we want to assign the new file when copying, e.g. `cp (FILE) (COPY_FILE)`. This copies the contents of (FILE) into (COPY_FILE). Note that you can give an absolute path for either of these arguments.
- To move a file, you also need to provide two arguments - the file to move and the destination: `mv (FILE) (DESTINATION)`. Note that you may use `mv` to rename a file or folder, given that it already exists. Simply use `mv (FILE_TO_RENAME) (NEW_NAME)`.

Lastly, to determine the file type, use `file (FILE_NAME)`. It can be hard to tell what kind of file a file is - some files may have extensions to make it easier to figure out what kind of file you're working with. However, this need not be the case.

**[Task 4, Question 1] How would you create the file named `newnote`?** - `touch newnote`

For this question, we'll want to use the `file` command. We use `file unknown1`, and then we see what the output says about the file:

![image](https://github.com/user-attachments/assets/863e2536-a2db-43bd-981d-7f10bb6a7957)

**[Task 4, Question 2] On the deployable machine, what is the file type of `unknown1` in `tryhackme`'s home directory?** - ASCII text

**[Task 4, Question 3] How would we move the file `myfile` to the directory `myfolder`?** - `mv myfile myfolder`

We can go ahead and move the file and change to the directory:

![image](https://github.com/user-attachments/assets/265e6a5c-23df-48a7-a653-85b1599d5920)

And then we can use `cat` to read it:

![image](https://github.com/user-attachments/assets/9ba051a6-0a5b-435d-9e32-00abd0ab1a39)

**[Task 4, Question 4] What are the contents of this file?** - `THM{FILESYSTEM}`

## [Task 5] Permissions 101

As it would turn out, some users are not allowed to access certain files or folders. We can figure out what permissions are available for a given file by using `ls -l` (or `ls -lh`). When we run this listing, we'll see something like `-rw-r--r--` at the left of each entry in the list. This represents the permissions available for different people. The three permissions are
- Read (`r`)
- Write (`w`)
- Execute (`x`)

The first `rw-` in the string above means that the owner of the file can `r`ead and `w`rite to it, but not execute it. Linux permissions are granular, so you can set different permissions for a group of users. They may have the same permissions as the owner of a file, or they may have different permissions. A web hosting company will want customers to upload files to the webserver, without giving them all the permissions of the webserver system user.

To switch between users on a Linux install, you can use the `su` command. You must have the following information: the username of the user you want to switch to, and the user's password. Note that if you're the root (high-level administrator) user, you don't need this information to switch between accounts.

The `su` command can accept a few switches of relevance. You can use it to execute a command once you log in, or you can specify a specific shell to use. That's all in the man pages for the command, but you should at least be aware of the `-l` switch. This lets you start a shell that is similar to that of the actual user's, i.e., what they would use when they logged into the system. This gives you access to a lot properties of the user, such as their environment variables.

As an example of the difference, the `su` command on its own will log you into the other user's account in your home directory. If you use `su` with `-l` you can log into the other user's account in their home directory.

If we run `ls -lh` we can see the list of permissions and files in the home directory on the VM. The third column of this output is the owner's username.

![image](https://github.com/user-attachments/assets/e15b1338-f61e-4a0a-ae9f-621dd7f22841)

**[Task 5, Question 1] On the deployable machine, who is the owner of `important`?** - `user2`

**[Task 5, Question 2] What would the command be to switch to the user `user2`?** - `su user2`

If we tried to read the `important` file, we would be given an error saying "Permission Denied." If we run the `su user2` command, we can switch to the owner's account and see what the contents are. Note that the password for `user2` is `user2`.

![image](https://github.com/user-attachments/assets/91432603-7640-4031-8a65-bec32112224c)

**[Task 5, Question 3] Output the contents of `important`. What is the flag?** - `THM{SU_USER2}`

## [Task 6] Common Directories

Let's wrap up by discussing a few key directories in Linux:
- `/etc`: This is a root directory where system files are stored to be used by the OS. Critical files such as `sudoers` (which specifies who can run what commands as root), `passwd` (user information), and `shadow` (user passwords) are stored here.
- `/var`: This is a directory for variable data, which is simply data that is frequently accessed or written to by services and apps on the system. Log files from running services and applications are stored in `/var/log`, for instance. This will likely not include data associated with a specific user.
- `/root`: This is the root user's home directory. You may have assumed that the root user's home directory is `/home/root` based on our work so far, but that is not the case.
- `/tmp`: This is a volatile directory and is used to store data that is only needed once or twice. This is like your RAM on a computer - once you restart it, the contents of this folder are cleared out. In penetration testing, this can be a location where you can store scripts for enumeration.

**[Task 6, Question 1] What is the directory path that we would expect logs to be stored in?** - `/var/log`

**[Task 6, Question 2] What root directory is similar to how RAM on a computer works?** - `/tmp`

**[Task 6, Question 3] Name the home directory of the root user.** - `/root`

## [Task 7] Conclusions and Summaries

Now we've covered more of the fundamentals for Linux, including connecting to machines remotely, using flags to use additional functionality in certain commands, and learning additional commands for filesystem interaction. We briefly introduced how Linux handles file permissions and how to switch users, and we went over some of the important directories in an Ubuntu install.

## [Task 8] Linux Fundamentals Part 3

At this point, we can terminate the machine from earlier, and move on to the third and final part of the sequence.
