# Blue

## Purpose of this Room

## [Task 1] Recon

**[Task 1, Question 1] How many ports are open with a port number under 1000? - 3**

**[Task 1, Question 2] What is this machine vulnerable to? (Answer in the form of: ms??-???, ex: ms08-067) - ms17-010**

## [Task 2] Gain Access

**[Task 2, Question 1] Find the exploitation code we will run against the machine. What is the full path of the code? (Ex: exploit/........) - exploit/windows/smb/ms17_010_eternalblue**

**[Task 2, Question 2] Show options and set the one required value. What is the name of this value? (All caps for submission) - RHOSTS**

## [Task 3] Escalate

**[Task 3, Question 1] If you haven't already, background the previously-gained shell (CTRL + Z). Research online how to convert a shell to a meterpreter shell in metasploit. What is the name of the post module we will use? (Exact path, similar to the exploit we previously selected) - post/multi/manage/shell_to_meterpreter**

**[Task 3, Question 2] Select this (use MODULE_PATH). Show options. What option are we requir3ed to change? - SESSION**

## [Task 4] Cracking

**[Task 4, Question 1] Within our elevated meterpreter shell, run the command `hashdump`. This will dump all of the passwords on the machien as long as we have the correct privileges to do so. What is the name of the non-default user? - Jon**

**[Task 4, Question 2] Copy this password hash to a file and research how to crack it. What is the cracked password? - alqfna22**

## [Task 5] Find flags!

**[Task 5, Flag 1] (This flag can be found at the system root.) - flag{access_the_machine}**

**[Task 5, Flag 2] (This flag can be found at the location where passwords are stored within Windows. Note that Windows doesn't like the location of this flag and CAN delete it; if this happens, that means you'll havfe to restart the machine. Lame, right?) - flag{sam_database_elevated_access}**

**[Task 5, Flag 3] (This flag can be found in an excellent location to loot. After all, Administrators usually have pretty interesting things saved.) - flag{admin_documents_can_be_valuable}
