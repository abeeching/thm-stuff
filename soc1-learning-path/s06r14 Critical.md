# Critical

## [Task 1] Introduction

A user noted strange behavior on their computer: some PDF files were encrypted, including a critical document for the company named `important_document.pdf`. It was suspected that some credentials were stolen, and now the DFIR teams are involved and got some evidence.

We'll be learning how to extract essential information and artifacts in a memory forensics task in a compromised machine using Windows. We'll learn some basic concepts before setting up the working environment, as well as how to search for suspicious activity. We'll extract some interesting data at the end to help us identify potential malicious actors.

It's good to have an understanding of volatile memory analysis, the Volatility utility, and the difficulties and challenges of incident response before moving on. These are rooms provided by THM.

## [Task 2] Memory Forensics

Let's recap:
- Memory forensics: A subset of computer forensics analyzing volatile memory, typically on a compromised machine
- Typically corresponds to Random Access Memory (RAM) that gets flushed with every reboot/shutdown
- One of the tasks usually done at the beginning of an incident investigation
- Differs from disk analysis - gives information on what's on the target but also provides information about processes/apps running at a particular time.
- Gives detailed information on execution flow on a system that we may not see in regular storage or app logs

Generally memory analysis can help us get a snapshot of an application or a timestamp of an attacker's actions. Evidence collected in memory forensics can become invaluable in creating a chronology of events.

Memory forensics occurs in two steps: memory acquisition and memory analysis. In memory acquisition, we copy the live memory to a file (known as a dump), and then we perform analysis on this file. This helps avoid the risk of losing data from an inadvertent reboot on the compromised system.

**[Task 2, Question 1] What type of memory is analyzed during a forensic memory task?** - RAM

**[Task 2, Question 2] In which phase will you create a memory dump of the target system?** - Memory acquisition

## [Task 3] Environment and Setup

There are a few ways to acquire memory from the target machine if needed - the choice of tool depends on personal preference and the OS involved in the imaging task. Some tools include
- FTK imager and WinPmem for Windows systems,
- LIME for Linux systems, and
- `osxpmem` for macoS.

In this scenario we used FTK Imager to grab the memory dump of the compromised machine, then copied the dump to a Linux machine for analysis. In the attached VM, the `/home/analyst/memdump.mem` file is what we managed to get off of the compromised computer. To analyze the dump, we'll use Volatility 3. In this VM, you can simply run `vol` to make use of it. See `vol -h` for more information on how to use the utility. Since we're looking at a Windows dump, we'll need to run `vol windows -h` to see a list of all available plugins for Windows.

Some of the most relevant plugins we'll be using are as follows:
- `windows.cmdline`: Lists process command line arguments
- `windows.drivermodule`: Determines if any loaded drivers were hidden by a rootkit
- `windows.filescan`: Scans for file objects present in a particular Windows memory image
- `windows.getsids`: Prints the SIDs owning each process
- `windows.handle`: Lists process open handles
- `windows.info`: Shows OS and kernel details of the memory sample
- `windows.netscan`: Scans for network objects present in a memory image
- `windows.netstat`: Traverses network tracking structures in a memory image
- `windows.mftscan`: Scans for Alternate Data Streams
- `windows.pslist`: Lists processes present in a particular memory image
- `windows.pstree`: Lists processes in a tree based on their parent process ID

**[Task 3, Question 1] Which plugin can help us to get more information about the OS running on the target machine?** - `windows.info`

**[Task 3, Question 2] Which tool referenced above can help us take a memory dump on a Linux OS?** - LIME

**[Task 3, Question 3] Which command will display the help menu using Volatility on the target machine?** - `vol -h`

## [Task 4] Gathering Target Information

Getting information about the target is helpful since it allows us to make sure that we're analyzing in the correct context and environment of the evidence. This helps us understand the specific architecture and operating system, which in turn helps ensure our findings' accuracy, relevance, and legitimacy. We can get information about the target with the `-f` switch, followed by the file we want to analyze, followed by the plugin we want to use. For general information, we run `vol -f memdump.mem windows.info`.

The output from the `windows.info` plugin shows information that helps us identify the machine we're working on, down to the architecture, processors, and version. This helps us correlate information and data with other analyses performed on separate hardware of the compromised machine.

Let's try it! Running the command from earlier earlier yields the following information:

![image](https://github.com/user-attachments/assets/d7af3f36-9b5f-4809-92fc-9662d2e286a2)

The output above can be used to answer all of the questions in this task. We can determine the architecture type by checking the `Is64Bit` value.

**[Task 4, Question 1] Is the architecture of the machine x64 (64-bit)?** - Y

And we can check the version of Windows by looking at `NtMajorVersion`.

**[Task 4, Question 2] What is the Version of the Windows OS?** - 10

And lastly the kernel base address can be found in the `Kernel Base` value.

**[Task 4, Question 3] What is the base address of the kernel?** - 0xf8066161b000

## [Task 5] Searching for Suspicious Activity

Now let's identify suspicious activity - this can be technical anomalies that are present in a system, such as unexpected processes, unusual network connections, and registry modifications. These may signal potential security threats such as malware attacks and data breaches.

One place to start would be to look for unusual activity. We can use `windows.netstat` to investigate this. Remote access connections and access to suspicious sites are something to look out for in this regard. If we run `vol -f memdump.mem windows.netstat`, we'll see this information. The room specifically calls out the connection from `192.168.182.139` over port 3389 from February 24, 2024; this may be an attacker's initial access.

We can also use `windows.pstree` to see what's going on with the processes. Running `vol -f memdump.mem windows.pstree` yields a list of the processes in a hierarchical view. When checking processes, we need to be on the lookout for unusual process names; attackers may try to use names to disguise the execution of malware. Common Windows processes, and their hierarchy, include:
- System Idle Process
  - System
    - `smss.exe`
  - `csrss.exe`
  - `wininit.exe`
    - `services.exe`
      - `svchost.exe`
        - `RuntimeBroker.exe`
        - `taskhostw.exe`
      - `lsaiso.exe`
      - `lsass.exe`
- `winlogon.exe`
- `explorer.exe`

The room calls out the presence of `critical_updat` (a truncated name), which has a child process of `updater.exe`. These are not normally present in the Windows OS. These could be malicious, so we should note their PID, PPID, timestamp, and memory offset.

If we run `vol -f memdump.mem windows.netscan`, we can see the IP addresses and associated programs that tried to get in on port 80. Scrolling through we see this output - the first IP and port number represents the local address/pot, and the second IP and port number represents the foreign address/port. In this case we're looking for the foreign IP address, since it's working over port 80.

![image](https://github.com/user-attachments/assets/8c96118d-1d44-4fc4-a4ba-1f37721746c0)

**[Task 5, Question 1] Using the plugin `windows.netscan`, can you identify the IP address that established a connection on port 80?** - `192.168.182.128`

The program that was used to access this port is also shown above. It's Microsoft Edge.

**[Task 5, Question 2] Using the plugin `windows.netscan`, can you identify the program (owner) used to access through port 80?** - `msedge.exe`

If we run `vol -f memdump.mem windows.pstree`, we see this with regards to `critical_updat`:

![image](https://github.com/user-attachments/assets/b1bb8a6f-3581-4d0b-b960-555f55df4dbe)

For `critical_updat`, the child process is `updater.exe`. Its PID can be found by looking at the first number in the row - in this case, 1612.

**[Task 5, Question 3] Analyzing the process present on the dump, what is the PID of the child process of `critical_updat`?** - 1612

The timestamp can be found in the screenshot provided above; for `critical_updat`, the timestamp is at the very end of the output (it kinda gets shoved to the next row since I'm running this in Split Screen View).

**[Task 5, Question 4] What is the timestamp for the process with the truncated name `critical_updat`?** - `2024-02-24 22:51:50.000000`

## [Task 6] Finding Interesting Data

Let's see what's going on with `critical_updat` and `updater`. We'll first start by figuring out where the `updater` file was saved on disk - we can use `windows.filescan` for this, since it'll show us what files were accessed from the memory dump. We should redirect the output of this to a file: `vol -f memdump.mem windows.filescan > filescan_out`. Then we can run `cat filescan_out | grep updater` to look for anything pertinent.

When we do this, we see that the files are stored in `C:\Users\user01\Documents\updater.exe`. We might want to get more detailed information about when the file was accessed or modified; in this case, we'd run `vol -f memdump.mem windows.mftscan.MFTScan > mftscan_out`. Then we run `cat mftscan_out | grep updater` to get the file creation, last modified, last updated, and last accessed timestamps.

Let's learn more about the process now. We can use Volatility to dump the memory region associated with `updater.exe` and examine it. In this case, we'll use `windows.memmap`. We'll need to specify an output directory with the `-o` switch. We'll then use the `--dump` and `--pid (PID)` flags to dump the memory for `updater.exe`.

The full command, `vol -f memdump.mem -o . windows.memmap --dump --pid 1612`, produces a file with the extension `.dmp` in the current working directory. This file is tricky to examine, so you should use `strings pid.1612.dmp` to examine the readable contents; perhaps pipe it to `less` to make the output more manageable. You should be keeping an eye out for key patterns. For instance, you can find a string referencing `important_document.pdf`, indicating that this process interacted with the critical document from earlier.

If you look deeper, say for `http` patterns, you'll see that it tries to access something from `hxxp[://]key[.]critical-update[.]com/encKEY[.]txt` (defanged). We can use the `-B 10 -A 10` flags in `grep` to look for all lines surrounding this URL, and we see that there is an HTTP resquest for the contents of `encKEY.txt`. This HTTP request contains data with the value `cafebabe`, which could be the key used to encrypt the PDF file.

Running `vol -f memdump.mem windows.filescan > filescan_out` and then running `cat filescan_out | grep critical_updat`, we see the following:

![image](https://github.com/user-attachments/assets/4a432ca5-e277-4b6c-a0c2-316c01c4f87c)

**[Task 6, Question 1] Analyzing the `windows.filescan` output, what is the full path and name for `critical_updat`?** - `C:\Users\user01\Documents\critical_update.exe`

Running `vol -f memdump.mem windows.mftscan.MFTScan > mftscan_out` and then running `cat mftscan_out | grep "important_document.pdf"` yields

![image](https://github.com/user-attachments/assets/ab1fa3aa-b4e8-4f32-aafe-5e8a84abf24f)

All of the timestamps are the same, so there's nothing to worry about there. However, if we did care about a particular timestamp, the order of timestamps in the output are Created, Modified, Updated, and Accessed.

**[Task 6, Question 2] Analyzing the `windows.mftscan.MFTScan` output, what is the timestamp for the created date of `important_document.pdf`?** - `2024-02-24 20:39:42.000000`

Lastly, we run `vol -f memdump.mem -o . windows.memmap --dump --pid 1612`. This generates a file called `pid.1612.dmp`, and we can check for HTTP requests and responses. HTTP responses will list the server used in, fittingly, the "Server" field, so we can try running `strings pid.1612.dmp | grep "Server:"` -- note that I left the colon inside the grep commmand.

![image](https://github.com/user-attachments/assets/29c578f3-0483-4b9d-b3d3-dd4fb631e569)

**[Task 6, Question 3] Analyzing the `updater.exe` memory output, can you observe the HTTP request and determine the server used by the attacker?** - SimpleHTTP/0.6 Python/3.10.4
