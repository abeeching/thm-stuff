# REMnux: Getting Started

## [Task 1] Introduction

Malware analysis can be daunting, especially when part of an ongoing incident. Results are expected to be as accurate as possible. Analysts use different tools, machines, and even environments to accomplish this. One of these is the REMnu VM. This is a specialized distribution of Linux that includes tools like Volatility, YARA, Wireshark, oledump, and INetSim. There's also a sandbox-like environment that you can use to dissect potentially malicious software without risking your primary system. You don't need to do a whole bunch of manual installations - you just set up the lab and go.

Familiarity with CyberChef is helpful but not required.

## [Task 2] Machine Access

To access the machine, you can start the attached VM in this task. You will need to start the AttackBox for one of the later tasks. All files to be used in this room are in the `Desktop/tasks` directory.

## [Task 3] File Analysis

Here we will use `oledump.py` to perform static analysis on a potentially malicious Excel document. `oledump.py` is a Python tool that analyzes OLE2 files, or Structured Storage / Compound File Binary Format. OLE stands for Object Linking and Embedding, which is a proprietary tool developed by Microsoft. OLE2 files can be used to store many data types, such as documents, spreadsheets, and presentations, in a single file. This particular tool lets you extract and examine the content of these files.

To use the tool, we simply run `oledump.py (FILE)`. In this case, we'll need to run `oledump.py agenttesla.xlsm`. The output of this particular command tells us there might be a VBA script in the docuument inside `x1/vbaProject.bin` - `oledump` marks this with an index of A. You will also get some information about data streams - these are noted with a letter (index) followed by some numbers in the output, e.g. A1 to A6. If `oledump` marks any of the streams with a capital M, then there's likely a macro worth investigating.

To investigate a data stream, you can run `oledump.py (FILE) -s (DATA_STREAM_NUMBER)`. This will look at the actual data stream of interest and print the contents in a hex dump. While it's not impossible to read the hex dump output, it's a lot easier to run `oledump.py (FILE) -s (DATA_STREAM_NUMBER) --vbadecompress`, which prints the output in a more readable format.

From here, you can investigate the contents of the data stream, which may include PowerShell commands among other things. You can use CyberChef to remove any unwanted characters; for instance, if there are `*`s and `^`s between each character of the command, you can use the Find / Replace operation to replace all instances of them with blank strings. This might make it easier to understand what's going on.

The PowerShell command in the file we're investigating has a few interesting points:
- `-WindowStyle hidden` means that the PowerShell window will not be visible to the user.
- `-executionpolicy bypass` will bypass the default script execution restriction that PowerShell has. This means any script can run without issue.
- `Invoke-WebRequest` is commonly used to download files off the Internet. It can take URLs with the `-Uri` command, and you can specify where to download the file to with the `-OutFile` flag.
- `Start-Process` is used to execute the downloaded file, which in this case is `Doc-3737122pdf.exe`, stored in `$TempFile`.

**[Task 3, Question 1] What Python tool analyzes OLE2 files, commonly called Structured Storage or Compound File Binary Format?** - `oledump.py`

**[Task 3, Question 2] What tool parameter we used in this task allows you to select a particular data stream of the file we are using it with?** - `-s`

**[Task 3, Question 3] During our analysis, we were able to decode a PowerShell script. What command is commonly used for downloading files from the Internet?** - `Invoke-WebRequest`

**[Task 3, Question 4] What file was being downloaded using the PowerShell script?** - `Doc-3737122pdf.exe`

**[Task 3, Question 5] During our analysis of the PowerShell script, we noted that a file would be downloaded. Where will the file being downloaded be stored?** - `$TempFile`

For these last two questions, we'll need to make use of the VM proper. Let's run `oledump` on `/home/ubuntu/Desktop/tasks/agenttesla/possible_malicious.docx` and see what we get:

![image](https://github.com/user-attachments/assets/5d9e9409-7fa8-487f-ae8f-014b1e604fac)

We see that there are 16 data streams here.

**[Task 3, Question 6] Using the tool, scan another file named `possible_malicious.docx` located in the `/home/ubuntu/Desktop/tasks/agenttesla/` directory. How many data streams were presented for this file?** - 16

And at data stream #8, we see the capital M that indicates there is a macro there.

**[Task 3, Question 7] Using the tool, scan another file named `possible_malicious.docx` located in the `/home/ubuntu/Desktop/tasks/agenttesla/` directory. At what data stream does the tool indicate a macro present?** - 8

## [Task 4] Fake Network to Aid Analysis

It is useful to observe the behavior of potentially malicious software, particularly its network activities. There are many ways to do this, but REMnux provides a tool inside the VM to create a simulated network: INetSim, or the Internet Services Simulation Suite. We'll simulate a real network in this task, and the AttackBox will be a part of it.

First, we need to configure the INetSim tool inside the REMnux VM. We need to check the IP address assigned to this VM, which we can do by looking at `ifconfig` or by looking at the IP address after the `ubuntu@` in the terminal. Then, we need to run `sudo nano /etc/inetsim/inetsim.conf` and look for `#dns_default_ip 0.0.0.0`. We want to remove the `#` from this to uncomment it, and then we change the value of the default IP to the machine's IP address. Save the output, close nano, and then make sure the changes have been made by running `cat /etc/inetsim/inetsim.conf | grep dns_default_ip`. Once all is said and done, we can run `sudo inetsim`. If all is well, then we should see "Simulation running" in the output. We can ignore the "http_80_tcp - failed!" error message.

In the AttackBox, we can navigate to the machine's IP by going to `https://(MACHINE_IP)`. We can click Advanced -> Accept the Risk and Continue if we get a warning about security issues. This will redirect us to the INetSim homepage.

Malware typically downloads another binary or script to do its work. We can mimic this by trying to grab a file from INetSim. We'll use the CLI in this case; we can run `sudo wget https://(MACHINE_IP)/(FILE) --no-check-certificates` to download the file from here. When we're done with INetSim, we can go back to the REMnux VM and stop INetSim (pressing CTRL + C). By default, it creates a report on any captured connections, and they're usually saved in `/var/log/inetsim/report/`. We can read the file by running `sudo cat /var/log/inetsim/report/(REPORT_FILE)`. The reports will show us what connections were made to the URL, the protocol, the method used, and the file(s) accessed.

Following the instructions provided in the task, we set up and start `inetsim` in the REMnux VM. From here, we can run the command specified in the first question of this task to retrieve the flag file. Doing this and reading the contents of the flag will give us the answer to the first question:

![image](https://github.com/user-attachments/assets/1c432e9b-187d-46c8-bb88-dc86c1504329)

**[Task 4, Question 1] Download and scan the file named `flag.txt` from the terminal using the command `sudo wget https://MACHINE_IP/flag.txt --no-check-certificate`. What is the flag?** - `Tryhackme{remnux_edition}`

Going back to the REMnux machine and stopping the simulation, we are told where to find a report. Opening this report yields the answer to the next question.

![image](https://github.com/user-attachments/assets/3f2898e4-4583-4710-966c-6bcd2b47ab83)

**[Task 4, Question 2] After stopping INetSim, read the generated report. Based on the report, what URL Method was used to get the file `flag.txt`?** - GET

## [Task 5] Memory Investigation: Evidence Preprocessing

A common task in digital forensics is to preprocess evidence, which means running tools and saving results in a text/json format. Analysts use tools like Volatility to deal with memory images as evidence, and Volatility just so happens to be in the REMnux VM already. Volatility commands can be used to identify and extract artifacts from forensic images, and the output can be saved to files for further examination.

We'll be using Volatility 3 with a bunch of different plugins. This will take some time, since running one plugin will take 2-3 minutes to show the output. You should run `sudo su` in the VM, and then navigate to `/home/ubuntu/Desktop/tasks/Wcry_memory_image/`. The commands we will use include:
- `windows.pstree.PsTree`, which lists processes in a tree based on their parent process ID.
- `windows.pslist.PsList`, which lists all currently active processes in the machine.
- `windows.cmdline.CmdLine`, which lists process command line arguments.
- `windows.filescan.FileScan`, which scans for file objects in a Windows memory image.
- `windows.dlllist.DllList`, which lists loaded modules in a Windows memory image.
- `windows.psscan.PsScan`, which scans for processes in a Windows memory image.
- `windows.malfind.Malfind`, which scans for process memory ranges that contain potentially injected code.

There are other plugins, but our focus will be on these guys. We can run all of these in bulk if we use a loop statement in bash, like this: `for plugin in windows.malfind.Malfind windows.psscan.PsScan windows.pstree.PsTree windows.pslist.PsList windows.cmdline.CmdLine windows.filescan.FileScan windows.dlllist.DllList; do vol3 -q -f wcry.mem $plugin > wcry.$plugin.txt; done`. This command creates a variable to hold the name of the plugin, runs Volatility in `-q` (quiet) mode, reads from the memory capture with `-f`, and runs each plugin and stores the output to separate files.

We can also use the Linux `strings` utility to extract ASCII (`strings` on its own), 16-bit little-endian (`strings -e l`), and 16-bit big-endian strings (`strings -e b`).

**[Task 5, Question 1] What plugin lists processes in a tree based on their parent process ID?** - PsTree

**[Task 5, Question 2] What plugin is used to list all currently active processes in the machine?** - PsList

**[Task 5, Question 3] What Linux utility tool can extract the ASCII, 16-bit little-endian, and 16-bit big-endian strings?** - `strings`

We'll go ahead and run the big command listed above and wait a while. This will generate all of the files we need to answer the remaining questions in this room. To find processes that have potentially injected code, we'll want to check the results of `malfind`, which are as follows:

![image](https://github.com/user-attachments/assets/3b1dd6e9-2c75-4c47-a838-fa08547b17ce)

**[Task 5, Question 4] By running `vol3` with the Malfind parameter, what is the first (1st) process identified suspected of having injected code?** - `csrss.exe`

The next result in this file is as follows:

![image](https://github.com/user-attachments/assets/76dc62a3-160e-4695-a744-b101089464a4)

**[Task 5, Question 5] Continuing from the previous question, what is the second (2nd) process identified suspected of having an injected code?** - `winlogon.exe`

And lastly, to determine where the file is located, we'll check the `dlllist` results, grepping for `@WanaDecryptor@.exe`:

![image](https://github.com/user-attachments/assets/fb1875f9-7aff-42e1-9cfc-e2250ace16d2)

As we can see, the file is located in `C:\Intel\ivecuqmanpnirkt615`.

**[Task 5, Question 6] By running `vol3` with the DllList parameter, what is the file path or directory of the binary `@WanaDecryptor@.exe`?** - `C:\Intel\ivecuqmanpnirkt615`

## [Task 6] Conclusion

That's a basic introduction of REMnux - this lets us use tools like `oledump`, Volatility (`vol3`), and `strings` to do some basic analysis, and we can even create fake networks with `INetSim`. There are many tools available here. Note that `REMnux` focuses on the analysis of potentially malicious programs, documents, files, memory, and similar objects. I believe it is discussed in brief in the malware analysis rooms on here.
