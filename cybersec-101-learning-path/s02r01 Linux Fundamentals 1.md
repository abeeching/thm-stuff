# Linux Fundamentals 1

## [Task 1] Introduction

This is the first in a series of rooms designed to get you familiar with the fundamentals of the Linux operating system. It's likely that you're using Windows or mac - you'll notice that there are differences between the two in terms of how they are designed and how they work. Linux is no different in this regard; while it's built up a reputation of being kinda obtuse to use, it's no different from any other operating system.

Our focus for now will be to introduce a few of the important commands, use them in an interactive environment, and learn how to interact with the filesystem from the command line.

## [Task 2] A Bit of Background on Linux

There's actually quite a decent chance that you've used Linux before. Linux tends to be more lightweight than, say, Windows, so it often shows up in places you wouldn't expect it to:
- Certain websites that you visit may be running off of Linux machines.
- Car entertainment systems and control panels may be using Linux as a base.
- Point-of-sale (POS) systems, like those you'd see in checkout at a store, can make use of Linux.
- Critical infrastructure even runs on Linux, like those that control traffic lights and industrial sensors.

(And, of course, if you're using an Android, that itself is a sort of Linux. Even macOS is Linux-esque!)

"Linux" is an umbrella term for many operating systems that are based on UNIX, which is another (much older, and more foundational) operating system. Since Linux is open-source, there are many variants of Linux out there that can satisfy different needs. Ubuntu and Debian are both common Linux distributions (variants) because they're extensible - if you need a server, they're a good place to start. If you need something to use as a desktop computer OS, those are a good start too! We'll use Ubuntu in these rooms - do note that Ubuntu Server can run only on systems with at least 512 MB of RAM.

The history of Linux goes back quite a bit, especially if you take UNIX into account. You should certainly do some research into it if you're interested, but the development of Linux proper begins with Linus Torvalds' creation of its kernel.

**[Task 2, Question 1] What year was the first release of a Linux operating system?** - 1991

## [Task 3] Interacting With Your First Linux Machine (In-Browser)

You can start a Linux machine as you did in the Intro to Offensive Security room by clicking the Start Machine button. This automatically opens in Split-Screen view. Once you're done with the machine, you can terminate it as you have before.

## [Task 4] Running Your First Few Commands

Ubuntu, again, is pretty lightweight. However, upon opening this VM, you might feel that it's a little too lightweight. There is no graphical user interface (GUI) to speak of - it's just a command prompt, known as a terminal. This is how you'll be interacting with the system in this room. It may seem intimidating at first, but with some practice, you'll get the hang of it.

Our goal is to figure out how to do basic file system tasks, like getting to files, showing their contents, and creating new files. That said, we'll start with a few basic commands.
- The `echo` command will output any text that we provide. For instance, `echo "Hello World"` will put "Hello World" on the command prompt.
- The `whoami` command will tell us what user we're logged in as.

Note that you don't need to put quotation marks in the `echo` command if you're just `echo`-ing one word.

**[Task 4, Question 1] If we wanted to output the text `TryHackMe`, what would our command be?** - `echo TryHackMe`

If we type `whoami` into the terminal and press Enter, we get some output - this is the username that we're logged in as.

![image](https://github.com/user-attachments/assets/516b4e55-69fc-4d5d-8b3a-6a7e7bbb0e69)

**[Task 4, Question 2] What is the username of who you're logged in as on your deployed Linux machine?** - `tryhackme`

## [Task 5] Interacting With the Filesystem!

The commands we've learned aren't all that useful on their own (though they may be useful in conjunction with other advanced commands or in certain contexts), so let's see how we can actually interact with the filesystem. We'd like to get familiar with some basic filesystem navigation and with how to display files.
- `ls` is the "listing" command, and will list files and directories (folders).
- `cd` is the "change directory" command, and will allow you to move to a different directory.
- `cat` is the "concatenate" command, and will display the contents of files to the terminal.
- `pwd` is the "print working directory" command, and tells you where you currently are in the filesystem.

We can start with using `ls` to see what's even there for us to explore. Running `ls` on its own should just give us a list of directories and files present in the "working directory" - more on this in a moment. If you want to find out what's in a different directory, you can run `ls (DIRECTORY)` in your terminal, replacing (DIRECTORY) with the actual directory name.

If we want to move to a different directory, we can use `cd (DIRECTORY)`. This will move us into the directory of our choosing.

If we want to view the contents of a file, we'll want to use `cat`. There are other methods that we can use to view the contents of files (we'll discuss how to transfer files off of a machine in a later room), but this is a decent place to start. If we run the command `cat (FILE)`, we can display the contents of any file we want. You can use `ls` to figure out what files are there, and then `cat` to display the contents of a given file.

If you know where the file you're interested in is located, you can give the "full path" (formally an _absolute path_) to the file. For instance, `cat /home/ubuntu/(FILE)` will display the contents of a file located in the ubuntu home directory. This will be useful since certain Linux files will always be located in certain spots.

The `cat` command becomes particularly useful once you realize that a file can contain usernames, configurations, flags, and even passwords.

As you navigate the filesystem, you'll notice that the directory name will appear in your terminal. It's easy to lose track of just where you are in the filesystem, so you can use `pwd` to get an idea of where you are. This prints the working directory, which you can think of as your current location in the filesystem. You might know that you're in the `Documents` directory, but where is it in the filesystem? You can use the `pwd` command to figure out where it is - it gives the "full"/absolute path described earlier.

For this first question, we'll want to `ls` in the machine that we have open:

![image](https://github.com/user-attachments/assets/56ba1a9c-9f5a-42a5-87b1-29e59871e65a)

There are four folders. Notice how they're color-coded!

**[Task 5, Question 1] On the Linux machine that you deploy, how many folders are there?** - 4

Now we need to figure out what directory has a file. We can list the contents of other directories by running `ls (DIRECTORY)`. If you don't see anything pop up on the terminal, that means the directory is empty and has no files.

![image](https://github.com/user-attachments/assets/75251272-49c5-4480-8eb0-1c2ce638526b)

**[Task 5, Question 2] Which directory contains a file?** - `folder4`

Let's navigate to `folder4` and see what's in that file. We can run `cd folder4` to get there, and then run `cat note.txt` to see what's inside.

![image](https://github.com/user-attachments/assets/3802795a-d981-4629-a3d6-f53bf0df7654)

**[Task 5, Question 3] What is the contents of this file?** - Hello World!

And lastly, let's see the full working directory that we're currently in, just to round things out. Running `pwd` gives us this:

![image](https://github.com/user-attachments/assets/360202ca-ce7e-4760-a6af-9a2671767bd1)

**[Task 5, Question 4] Use the `cd` command to navigate to this file and find out the new current working directory. What is the path?** - `/home/tryhackme/folder4`

## [Task 6] Searching for Files

Linux can be pretty efficient if you know how to use it well - again, with practice (and general use - you will be using those previous commands pretty often), the basic commands will come to you naturally. But there are other commands that make certain tasks much easier! Want to find where a given file is on the system? No need to `ls` and `cd` around...we can just use `find`!

The `find` command is useful since it can be as complex as you need it to be. The simplest use of `find` would be to use it to find a given file that you know the name of. In this case, you'd run the command `find -name (FILE)`. If it finds the file, it will tell you where the file is located so you can work with it.

But say you don't know the filename, or maybe you need to find all files ending in a certain extension, like `.txt`. The `find` command accepts wildcards for this purpose. If we ran `find -name *.txt`, we would basically be asking `find` to look for any files that have a `.txt` at the end.

Similarly to `find`, we can use `grep` to find specific values or contents in a given file. Some files (particularly logs and things of that sort) are way too large to read through with `cat`. If we know what we're looking for, we can use `grep` to search for it. The command is `grep "(THING YOU WANT TO LOOK FOR)" (FILE)`. Let's make use of it here.

We're going to need to find `access.log` and find a flag that has the `THM` prefix. If we're picking up from the previous task, we're going to be in `/home/tryhackme/folder4`. We'll want to go back to the `tryhackme` directory. There's a few ways to do this:
- `cd /home/tryhackme` will do just fine.
- `cd ..` will take you back up one level, so this will take us to `/home/tryhackme` just as well.
- `cd ~` can be used. The `~` is a short-hand for your home directory, so this will take us to `/home/tryhackme`.

We see that `access.log` is in the home directory if we run `ls`. So let's use `grep` -- if we use `grep "THM" access.log`, it should give us what we're looking for.

![image](https://github.com/user-attachments/assets/a8b1f5d9-ebb5-4cda-9da2-59c0802e774e)

**[Task 6, Question 1] Use `grep` on `access.log` to find the flag that has a prefix of "THM." What is the flag?** - `THM{ACCESS}`

## [Task 7] An Introduction to Shell Operators

To wrap up, we'll cover some operators that we can use in the terminal to do some more advanced stuff. Here are the key operators that we'll be looking at:
- `&`, which runs commands in the background
- `&&`, which runs multiple commands
- `>`, which takes the output from one command and sends it somewhere else. This replaces whatever's in the destination.
- `>>`, which does the same as `>` but appends output rather than replaces.

The `&` operator is useful if you have a long command that you need to run. You can use `&` to send it to the background, allowing you to work in the terminal while the other command is hard at work. Without it, you'd be stuck staring at a terminal without being able to do anything until the command is done!

It's worth bringing that operator up because it differs completely from `&&`. This allows you to run two commands together, i.e. `(COMMAND1) && (COMMAND2)`. Note that `COMMAND2` only runs if `COMMAND1` was successful. This is analogous to how you may use `&&` if you were, say, programming.

The `>` operator is an output redirector, meaning it takes the output from one command and redirects it to somewhere else. Typically, all output gets sent to standard output (a.k.a. your terminal), but you can use this operator to send it to different files. This is where `echo` can be useful - if we wanted to send "Hello" to a file, we may run `echo "Hello" > (FILE)`. Be aware that this will overwrite anything in the target file, if it exists and if there's something in there.

If you didn't want to overwrite anything, though, you should use `>>`. This will append the output to the bottom of the file.

**[Task 7, Question 1] If we wanted to run a command in the background, what operator would we want to use?** - `&`

**[Task 7, Question 2] If I wanted to replace the contents of a file named `passwords` with the word `password123`, what would my command be?** - `echo password123 > passwords`

**[Task 7, Question 3] Now if I wanted to add `tryhackme` to this file named `passwords`, but also keep `passwords123`, what would my command be?** - `echo tryhackme >> passwords`

You should practice these commands in the provided machine.

## [Task 8] Conclusions and Summaries

That should bring us up to speed with the most fundamental functions we need to be able to use in Linux. You'll be using these commands very often as you continue to work with Linux, so you'll get the hang of them eventually! You should spend some time playing around with the commands discussed in this room before moving onward.

## [Task 9] Linux Fundamentals Part 2

When you're done, you can terminate the machine you started, and then move on to Part 2 of the Linux Fundamentals room, where you'll learn some more advanced commands.
