# Secret Recipe

## [Task 1] Introduction

Here's our scenario: A coffee shop's owner keeps the original copy of her secret recipe on her laptop. An employee from the IT department was called in to fix up her laptop, and it's suspected that he copied the secret recipe off of her machine. No one could spot anything on his machine, but there are some important registry artifacts that are worth examining.

The attached VM starts in Split Screen View. The `Artifacts` folder contains the registry hives we need to examine; the `EZ tools` folder contains all the required tools for analyzing these artifacts.

If we're going to use Registry Explorer, there will be some delays in loading it. It takes time to parse the hives.

Checking the Artifacts folder, we have six hives to investigate:

![image](https://github.com/user-attachments/assets/7c8f74ed-8889-4def-a81f-207293d876f6)

**[Task 1, Question 1] How many Files are available in the Artifacts folder on the Desktop?** - 6

## [Task 2] Windows Registry Forensics

In brief, the Registry contains lots of forensically-useful information about the user, system, activities performed, processes executed, files accessed/deleted, and more. We'll need to examine the Registry hives to figure out what's going on in this system. The hives we have access to are
- SYSTEM
- SECURITY
- SOFTWARE
- SAM
- NTUSER.DAT
- UsrClass.dat

I'll be using Registry Explorer to do the work in this room, and if other tools are needed then I will point them out.

First we need to identify the computer name. To do this, we must open the SYSTEM hive and go to `SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName`. In this case we'll need to go to `ControlSet001` instead of `CurrentControlSet`. Doing so gives us the following information:

![image](https://github.com/user-attachments/assets/b88ac5b1-7c7f-4e3b-82cb-19bf7f6216c9)

**[Task 2, Question 1] What is the Computer Name of the Machine found in the registry?** - JAMES

Next, we need to figure out when the Administrator account was created on the machine. We'll have to load up the SAM hive and go to `SAM\Domains\Account\Users`. Doing so and looking at the values for the Administrator account gives us this information:

![image](https://github.com/user-attachments/assets/bd1da97b-a4d2-4f9c-9840-94de8563e5bc)

The full Created On value is `2021-03-17 14:58:48`.

**[Task 2, Question 2] When was the Administrator account created on this machine? (Format: `yyyy-mm-dd hh:mm:ss`)** - `2021-03-17 14:58:48`

Next up, let's grab the Administrator's RID. This is the same as User Id in Registry Explorer:

![image](https://github.com/user-attachments/assets/e437e3c3-eb97-4c7e-8a39-1bc370a3b03e)

**[Task 2, Question 3] What is the RID associated with the Administrator account?** - 500

Now let's see how many user accounts there were in the machine. In the screenshot for Question 2, in the bottom left, we see that there are 7 total rows. This corresponds to 7 total accounts.

**[Task 2, Question 4] How many User accounts were observed on this machine?** - 7

Most of the accounts in this list appear to be the defaults (Administrator, Guest, DefaultAccount, etc.), with two accounts appearing to be relatively normal. However, we're looking for an account set up as a backdoor, and in light of that, this one seems pretty suspect:

![image](https://github.com/user-attachments/assets/5f1ccef8-6d33-4d7d-825d-1ecb16a97c3c)

Also, this has the required RID value of 1013, as specified by the question.

**[Task 2, Question 5] There seems to be a suspicious account created as a backdoor with RID 1013. What is the Account Name?** - `bdoor`

Next up, we'll need to investigate network connections performed on the machine. We're looking for a VPN connection. A lot of the network connection-related information will be found in the SOFTWARE hive, so let's load it. Going to `SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList`, we see the following:

![image](https://github.com/user-attachments/assets/26f398ff-d1e2-40c4-9010-3244c330efcc)

**[Task 2, Question 6] What is the VPN connection this host connected to?** - ProtonVPN

This registry key also happens to contain the first connection time to the VPN.

![image](https://github.com/user-attachments/assets/aa5d5a43-18d1-4375-b7e1-5f9440b86473)

**[Task 2, Question 7] When was the first VPN connection observed?** - `2022-10-12 19:52:36`

Now we'll need to figure out what kind of shared folders were accessed. One good place to start might be to look for the available shares on the machine, which can be found in `SYSTEM\ControlSet001\Services\LanmanServer\Shares`. Here are the available shares.

![image](https://github.com/user-attachments/assets/eb397b7a-cc41-4df4-9ec6-0d5fcd59602f)

The RESTRICTED FILES share just seems interesting by virtue of its name. Checking its value, we see that its path is `C:\RESTRICTED FILES`.

**[Task 2, Question 8] There were three shared folders observed on his machine. What is the path of the third share?** - `C:\RESTRICTED FILES`

Next up: We need to figure out the last DHCP IP assigned to the host. We can actually check this by going to `SYSTEM\ControlSet001\Services\Tcpip\Parameters\Interfaces` and looking at the `DHCPIP Address` column. We're told to look for addresses that start with 172 in the hint, and lo and behold, we've got something:

![image](https://github.com/user-attachments/assets/c6893c76-7b3b-4174-a445-4f3c221e3f48)

**[Task 2, Question 9] What is the Last DHCP IP assigned to this host?** - `172.31.2.197`

Now we'll need to start answering questions regarding file access - these can be answered with the information found in `NTUSER.DAT`. Specifically, we can look at `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs` and look at the values:

![image](https://github.com/user-attachments/assets/71e79db0-d1d3-445d-8746-345387c9948d)

Given the scenario, that `secret-recipe.pdf` file seems important.

**[Task 2, Question 10] The suspect seems to have accessed a file containing the secret coffee recipe. What is the name of the file?** - `secret-recipe.pdf`

Now we'll have to look at commands executed in the Run window; one place to start looking would be `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU`. Here are the commands run:

![image](https://github.com/user-attachments/assets/cf8c3884-99c0-48ac-bafe-7c182e96dc26)

If the suspect is trying to enumerate network interfaces, the command with `enum-interfaces` in it is probably what we're interested in.

**[Task 2, Question 11] The suspect ran multiple commands in the run windows. What command was run to enumerate the network interfaces?** - `pnputil /enum-interfaces`

The suspect also tried to look for a network utility for transferring files. We'll want to check `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery` for anything the suspect has searched for. This yields:

![image](https://github.com/user-attachments/assets/d448e3de-f49a-4827-a955-81c7c290257c)

Netcat is a tool popularly used for transferring files over the network.

**[Task 2, Question 12] In the file explorer, the user searched for a network utility to transfer files. What is the name of that tool?** - `netcat`

Now let's go back to recently-opened files. We're looking specifically for a text file, so let's check `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs\.txt` and see what crops up:

![image](https://github.com/user-attachments/assets/619437ed-432b-438e-a2c5-5a41fae26b8b)

**[Task 2, Question 13] What is the recent text file opened by the suspect?** - `secret-code.txt`

We can check for how often something got executed by looking in `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist\{CEBFF5CD...}\Count`. This contains a list of of all ran programs and how often they were ran. This includes PowerShell:

![image](https://github.com/user-attachments/assets/8a473a6e-eacc-478d-b8e3-33c093ad00bf)

**[Task 2, Question 14] How many times was PowerShell executed on this host?** - 3

If we keep scrolling through this output, we see that the user has been running Wireshark, which is a network monitoring tool:

![image](https://github.com/user-attachments/assets/d5cb529c-ac01-4c16-8bbb-44e784e81dbb)

**[Task 2, Question 15] The suspect also executed a network monitoring tool. What is the name of the tool?** - Wireshark

Incidentally, the `UserAssist` key contains the amount of time a program was in focus for. So, for instance, we can look at this and see that ProtonVPN has been run for 5 minutes and 43 seconds, or 343 seconds in all:

![image](https://github.com/user-attachments/assets/82c9b841-e988-40d7-8131-6b8b8aebb348)

**[Task 2, Question 16] Registry Hives also notes the amount of time a process is in focus. Examine the Hives. For how many seconds was ProtonVPN executed?** - 343

Lastly, we're told about a program that can search for files on a Windows machine: `Everything.exe`. We want to figure out the full path from which this was executed. This very same registry key, once again, helps us.

![image](https://github.com/user-attachments/assets/3376463a-ba6c-47c4-b932-c4f89b4b03d6)

**[Task 2, Question 17] Everything.exe is a utility used to search for files in a Windows machine. What is the full path from which everything.exe was executed?** - `C:\Users\Administrator\Downloads\tools\Everything\Everything.exe`
