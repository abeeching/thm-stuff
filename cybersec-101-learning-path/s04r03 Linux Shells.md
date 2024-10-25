# Linux Shells

## [Task 1] Introduction to Linux Shells

As we have seen plenty of in this section of rooms, CLIs are very useful for doing things more efficiently and without all the resource overhead. With command lines, you get more power and control over how you want to carry out tasks. In this room, we'll dive more deeply into the Linux command line, including a discussion of the various shells and how to write scripts with them.

A familiarity with Linux fundamentals is nice to have here.

**[Task 1, Question 1] Who is the facilitator between the user and the OS?** - shell

## [Task 2] How to Interact with a Shell

The provided VM will start in split-screen view; otherwise, you may use a machine connected to the TryHackMe VPN or the AttackBox, and then SSH into the VM proper. Once you get into the VM, the shell prompt will be there, awaiting commands to be input.

Generally, Linux distributions use the Bourne Again Shell (better known as `bash`) as the default shell. This will depend on the exact distribution, of course, and there's other shells out there.

There are a few important commands that you should be familiar with:
- `pwd`, which allows you to print your current working directory to the screen
- `cd`, which allows you to change directories
- `ls`, which is used to list off the files and subdirectories in a given directory
- `cat (FILE)`, which allows you to display the contents of a file, among other things
- `grep (THING_TO_SEARCH_FOR) (FILE)`, which allows you to search a file for a string or pattern of text

**[Task 2, Question 1] What is the default shell in most Linux distributions?** - Bash

**[Task 2, Question 2] Which command utility is used to list down the contents of a directory?** - `ls`

**[Task 2, Question 3] Which command utility can help you search for anything in a file?** - `grep`

## [Task 3] Types of Linux Shells

Linux has a selection of shells available for you to use; you can see which shell is currently in use by running `echo $SHELL`. To see a list of available shells, you can run `cat /etc/shells` - the `/etc/shells` is a file that contains the list. To switch between shells, simply type the name of it into the prompt. To permanently change the default shell for your account, run `chsh -s /usr/bin/zsh`.

The Bourne Again Shell (bash) is the default shell in most cases, and it'll be there whenever you open a terminal in Linux. There were some shells beforehand, such as sh, ksh, and csh; bash supersedes all of these and borrows some functionality from them. Some key features are as follows:
- Bash is widely used and has scripting capabilities; more on this in a moment
- Bash allows you to tab-complete, meaning that you can press tab while you're in the middle of typing a command to see suggested completions (e.g., if you're in the middle of typing a filename, pressing tab cycles through files that begin with what you've already written down).
- Bash keeps a history file and logs all commands, and you can access them by pressing the Up and Down arrow keys. You can use the `history` command to see a list of all previous commands.

The Friendly Interactive Shell (fish) is not default in many Linux distributions; its focus is on user-friendliness, when compared with other shells.
- Fish provides a simple syntax which might make it easier to use for newer users.
- Bash has auto-spell correction for the commands you write.
- You can customize the command prompt with themes.
- Fish has syntax highlighting and will color different parts of a command based on their roles, improving readability and helping with spotting errors.
- Fish provides scripting, tab completion, and command history as does bash.

The Z Shell (zsh) is not installed by default in most distributions, but it is considered a modern shell nonetheless that combines functionalities of previous shells.
- Zsh provides advanced tab completion, and it is possible to write scripts in it.
- Like fish, zsh provides auto-spell correction for commands.
- Zsh provides extensive customization options, though this may make it slower to use.
- Tab completion, command history, and other functionalities from the previously mentioned shells are provided.

Here's a full summary of the three different shells:
- Bash
  - AKA "Bourne Again Shell"
  - Offers widely compatible scripting, and has a lot of documentation out there
  - Basic tab completion
  - Basic customization options
  - Less user-friendly than the others, but is traditional and widely used, so you'll probably find someone who knows what to do
  - No syntax highlighting
- Fish
  - AKA "Friendly Interactive Shell"
  - Has limited scripting capabilities when compared to the other two shells
  - Advanced tab completion, offering suggestions based on previously-run commands
  - Good customization options via interactive tools
  - Very user-friendly
  - Syntax highlighting built-in
- Zsh
  - AKA "Z Shell"
  - Has extensive scripting capabilities, combining those of bash with other features
  - Tab completion can be extended with plugins
  - Advanced customization available via the oh-my-zsh framework
  - Can be made very user-friendly with proper customization
  - Syntax highlighting via plugins
 
The best shell will be based on what tasks you need to do, and also just on personal preference.

**[Task 3, Question 1] Which shell comes with syntax highlighting as an out-of-the-box feature?** - fish

**[Task 3, Question 2] Which shell does not have auto spell correction?** - bash

**[Task 3, Question 3] Which command displays all the previously executed commands of the current session?** - `history`

## [Task 4] Shell Scripting and Components

At its most fundamental level, a shell script is a series of commands. While you could enter these commands one at a time, that can get pretty tedious pretty quickly. So, you can instead throw them all together in a script, and then run the script as needed and even automate it. All shells mentioned previously have some level of scripting capabilities, though you don't have to use shells to make scripts. You can use programming languages as well to do some scripting (e.g., in Linux, folks may use Python to put together scripts and automate things). Programming languages are out of the scope of this room.

We'll go with the bash shell - this is the default one, and it's the one that's in use in many of the TryHackMe rooms.

When creating a script, the file must have a `.sh` extension at the end. So, when creating a script, you'll want to run a command like `nano (SCRIPT_NAME).sh`. Every script should start with a _shebang_, which defines what shell/program is interpreting it. For bash, the first line of any script will probably be `#!/bin/bash`. From here, we can start putting together some scripts using fundamental building blocks and constructs. You may be familiar with some of this if you've programmed before!

First, the variable. Variables just hold values, which in this case may even be URLs, file paths, and more. Instead of memorizing these values and typing them over and over again (doing so is risky, since this may introduce error), you can just store the value in a variable and throw the variable name around where you need it.

Recall that scripts in bash are just sets of commands. So you can start by writing some commands, line-by-line.

```bash
#!/bin/bash
echo "Hey, what's your name?"
read name
echo "Welcome, $name"
```

The `read name` line is used to take input from the user. `name` is the variable that the input gets stored in. When we want to print (or echo) it out later, we write it as `$name`.

To run such a script, you need to first give the script execution permissions. This can be done by running `chmod +x (SCRIPT)`. Once execution permissions have been given, you'll have to run the script as follows: `./(SCRIPT)`. We need to have `./` before the cript name because of how Linux handles things. Basically, when you run any command or script in bash, it'll look through the directories in the PATH environment variable to find it. It's very likely that bash doesn't have your current working directory in the PATH variable, so it'll fail to find it. The `./` basically just tells bash, "hey, look here!"

A _loop_ is a set of commands that will be repeated a certain number of times. Here's a simple example:

```bash
#!/bin/bash
for i in {1..10};
do
echo $i
done
```

The "for" contains the variable `i`, which will be set to every value between 1 and 10, inclusive. The `do` line indicates that the loop begins, and the `done` line indicates that the loop ends. Reading the code in this way tells us that the variable `$i` will be echoed to the screen, and that this happens every time the loop is ran. You can think of this snippet as doing the following:
- It assigns the numbers 1 through 10 to the variable `i` each time the loop is ran.
- Every time the loop is ran, the number is printed out to the screen.

A _conditional statement_ is used to execute specific code under certain conditions. You may want your script to do something if a condition is met, and something else if that condition is not met. Here's an example:

```bash
$!/bin/bash
echo "Please enter your name first:"
read name
if [ "$name" = "Stewart" ]; then
   echo "Welcome Stewart! Here is the secret: THM_Script"
else
   echo "Sorry! You are not authorized to access the secret."
fi
```

The conditional statement portion starts with `if`, and that line just checks to see if the user typed in the name "Stewart." If they did, then they're given the secret. If they didn't, then the script looks at the `else` line to see what to do - in this case, it tells the user that they're unauthorized. The `fi` ends the conditional statement section.

Lastly, you should be familiar with commments. A comment is just a blurb we write in the code to help make things easier to understand. To write a comment, you type a `#` followed by a space; for instance, `# this would be a comment.` Comments do not impact how the script works; it's just there to help make things easy. You should include comments in your scripts, particularly in the major and complicated areas; if there's a section of code that seems self-explanatory to a reader, then you don't _need_ to have comments there.

There are other types of statements that can be used to achieve different tasks, and you're able to write multi-line comments, though this is all out of the scope of the room.

**[Task 4, Question 1] What is the shebang used in a Bash script?** - `#!/bin/bash`

**[Task 4, Question 2] Which command gives executable permissions to a script?** - `chmod +x`

**[Task 4, Question 3] Which scripting functionality helps us configure iterative tasks?** - Loops

## [Task 5] The Locker Script

Now we'll create our own little script. Say a user has a locker in a bank, and to secure the locker, we need to create a script that verifies the user before opening it. The user should be asked for their name, company name, and a PIN. If they enter the correct details (John, Tryhackme, and 7385), they're let into the locker. Otherwise, they're denied access.

The script would look like this:

```bash
# Define the interpreter
#!/bin/bash

# Define the variables
username=""
companyname=""
pin=""

# Define the loop
for i in {1..3}; do
# Define conditional statements
   if [ "$i" -eq 1 ]; then
      echo "Enter your username: "
      read username
   elif [ "$i" -eq 2 ]; then
      echo "Enter your company name: "
      read companyname
   else
      echo "Enter your PIN: "
      read pin
   fi
done

# Note that the variables - "i" in the section above, and the variables below, are quoted in the loop/conditional statements
# They're also written as $(VARIABLE_NAME), which is how you would reference a variable and its value in your script.

# Checking if the user entered correct credentials
# Note that "&&" can be used to check if multiple conditions are true at the same time
if [ "$username" = "John" ] && [ "$companyname" = "Tryhackme" ] && [ "$pin" = "7385" ]; then
   echo "Authentication successful. You can now access your locker, John."
else
   echo "Authentication denied."
fi
```

To execute this script, we would save the script to our machine, e.g. as `locker_script.sh`. Then we'd run `chmod +x locker_script.sh`, and then run `./locker_script.sh`.

To answer the question for this task, we'll want to check the script above.

**[Task 5, Question 1] What would be the correct PIN to authenticate in the locker script?** - 7385

## [Task 6] Practical Exercise

The attached VM contains a script in the `/home/user` directory. It's meant to search for a specific keyword in all the files with a `.log` extension in a specific directory. It's recommended that you run the command `sudo su` to switch to the root user account. The password for `user` is `user@Tryhackme`.

We can open the script with `nano flag_hunt.sh`. Note that in this VM, the underscores will not show up. Our goal is to fill in the blanks, denoted as empty quotation marks (`" "`):
- We want the flag to be `thm-flag01-script`.
- We want the directory to be `/var/log`.

We can fill in the first two blanks with the information provided above. The directory should be `/var/log` and the flag should be `thm-flag01-script`. The last blank is at the beginning of the `for` loop. We're looking for all log files in the `/var/log` directory, which is stored in the `$directory` variable. So we should fill that blank in with `$directory` for the loop to work. The script should look like this:

![image](https://github.com/user-attachments/assets/b89f9f98-38a9-425f-86dc-70fec09f306b)

When we're done, we can save our work and close nano. Then we can run the script by running `./flag_hunt.sh`:

![image](https://github.com/user-attachments/assets/fb429377-d563-4c28-97e5-297f4184d93a)

**[Task 6, Question 1] Which file has the keyword?** - `authentication.log`

We'll read the contents of this file by running `cat /var/log/authentication.log`. This yields:

![image](https://github.com/user-attachments/assets/4a33c1a4-c212-47f0-a524-1f2e8fce0b5d)

**[Task 6, Question 2] Where is the cat sleeping?** - under the table

## [Task 7] Conclusion

We spent our time in this room studying the different Linux shells out there, and we developed a basic understanding of how to write scripts in Linux. We're now able to write some scripts to help us automate some teidous tasks!

In the next section of the Cyber Security 101 path, we'll take a look at some fundamental networking concepts.
