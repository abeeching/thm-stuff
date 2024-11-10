# Volatility

## [Task 1] Introduction

In this room, we'll be taking a look at some memory dumps from other TryHackMe rooms and samples from the Volatility foundation. You should be familiar with the material in the Core Windows Processes room. Files are attached to this task if you'd like to do this on your own machine, though a VM is available in Task 3 with all of the required files and setup all done.

## [Task 2] Volatility Overview

Volatility is a widely-used framework for extracting digital artifacts from volatile memory (RAM) samples. The extraction techniques are performed completely independent of the system being investigated, but offer visibility into the runtime state of the system. This can help demonstrate some techniques for extracting digital artifacts from volatile memoory, and can provide a platform for further work into memory forensics.

Volatility uses many programs to obtain information from the memory dump. You must first identify the image type (and there are many ways to do so), and once you have that figured out you can set up your plugins and analyze the dump.

Volatility has two main repositories: one for Python 2 and one for Python 3 - we'll be using the Python 3 version, known as Volatility3. Some writings on Volatility may still use the Volatility2 syntax; note that the syntax has been changed for Volatility3.

## [Task 3] Installing Volatility

The steps for installing Volatility are simple and can be applied to any OS, given that it's all written in Python. It's recommended to start in Linux, but the process should generally be the same on the other systems. The AttackBox in TryHackMe already has Volatility present.

You can make use of a pre-packaged executable (`.whl` file) that will work the same and require no dependencies; this is only available for Windows. You can also run it directly from Python. To use the pre-packaged executable, you can grab the most recent version from the Volatility Foundation's releases page on GitHub. To run the project from source, you'll need Python 3.5.3 or later and Pefile 2017.8.1 or later. You can get the latter from `pypi.org`.

You can also download some optional dependencies that are recommended for this room in particular: yara-python 3.8.0 or later and capstone 3.0.0 or later.

Once all is said and done, clone the repository from GitHub with `git clone https://github.com/volatilityfoundation/volatility3.git`. To test your installation, simply run `python3 vol.py -h`.

For Linux and Mac memory files, you may need to download the symbol files from the Volatility GitHub page (`symbol-tables`).

The attached VM already has Volatility and Volatility 3 present in the `/opt` directory, along with the memory files that we'll be using. It'll open in Split Screen View should you choose to use it.

## [Task 4] Memory Extraction

Here are a few tools that can be used to extract a memory dump; the choice of tool depends on the requirements of your investigation.
- FTK Imager
- Redline
- `DumpIt.exe`
- `win32dd.exe` and `win64dd.exe`
- Memoryze
- FastDump

When using an extraction tool on a bare-metal host, you'll have to wait a considerable amount of time. Take this into consideration; in some investigations, time might be of the essence.

Most of the tools mentioned above will output a `.raw` file, with the exception of Redline that can use its own agent and session structure.

For virtual machines, you can simply grab the virtual memory file from the host machine's drive. This file can change depending on the hypervisor used. Here are some virtual memory files you may encounter:
- `.vmem` for VMWare
- `.bin` for Hyper-V
- `.mem` for Parallels
- `.sav` for VirtualBox; note that this is only a partial memory file

Exercise caution when attempting to extract or move memory from both bare-metal and virtual machines.

## [Task 5] Plugins Overview

In previous verisons of Volatility, you would have to identify a specific OS profile exact to the operating system and build version of the host; this can be hard to find, and while there were plugins to help, there were false positives. Volatility3 does away with the profiles and now automatically identifies the host and build of the memory file.

The naming structure for plugins has changed. Previously the convention was to simply use the name of the plugin, and the names were universal for all operating systems. With Volatility3, you must specify the operating system prior to specifying the plugin to be used, e.g. `windows.info` and `linux.info`. This is because profiles have been removed; each plugin has been set up differently to work with each operating system. After all, they have different ways of dealing with memory. You'll see plugins with the following syntax for different operating systems:
- `.windows`
- `.linux`
- `.mac`

There are several plugins available with Volatility along with some third-party plugins. We'll be covering a small number of these.

You can use the Help menu to get used to the different plugins. There are fewer plugins in Volatility 3 compared to its Python 2 counterpart, but there should be enough to do what you need to do.

## [Task 6] Identifying Image Info and Profiles

Volatility is equipped to handle all Windows profiles from Windows XP to Windows 10 by default. Image profiles can be difficult to determine if you don't know the exact version and build the machine was. You may very well end up with a memory file with no context whatsoever; you have to figure out what to do from there. Volatility comes with the `imageinfo` plugin, which takes the memory dump and identifies a list of the best possible OS profiles. This is really only relevant to us if we use Volatility2.

Note that this plugin is not always correct and may have varied results depending on the provided dump. Test multiple profiles and treat any output with caution.

If we need to figure out what the host is running from the memory dump, we can use `windows.info`, `linux.info`, and `mac.info`. These provide information about the host. The syntax for these commands will resemble the following: `python3 vol.py -f (FILE) windows.info`.

## [Task 7] Listing Processes and Connections

We'll be looking at five plugins that allow us to dump processes and network connections.
- `pslist` is used to get a list of processes from a list that keeps track of them in memory, such as Task Manager's process list. This includes all current and terminated processes, with exit times. The syntax is `python3 vol.py -f (FILE) windows.pslist` and similar.
- `psscan` locates processes by finding data structures that match `_EPROCESS`. This is useful for finding malware, like rootkits, that unlink themselves from the process list to hide themselves. This can help with evasion countermeasures but may also cause false positives. The syntax is `python3 vol.py -f (FILE) windows.psscan`.
- `pstree` lists all processes based on their parent process ID with the same methods as `pslist`. This can be useful for getting a full story of the processes and what may have been occurring at the time of extraction. The syntax is `python3 vol.py -f (FILE) pstree`.
- `netstat` is used to identify network connections present at the time of extraction on the host machine. It attempts to identify all memory structures with a network connection. The syntax is `python3 vol.py -f (FILE) windows.netstat`. Note that this command may be unstable, at least in the attached VM and particularly with older Windows builds. There are tools like `bulk_extractor` which can pull out a PCAP file from the memory file.
- `dlllist` is used to list all DLLs associated with processes at the time of extraction. This can be helpful when you've done enough analysis to determine that a specific DLL may be an indicator of malware on the system. The syntax is `python3 vol.py -f (FILE) windows.dlllist`.

## [Task 8] Volatility Hunting and Detection Capabilities

Volatility also has some plugins that can be used to aid in hunting and detection capabilities when working with system memory. It helps to have a basic understanding of evasion techniques and how they're employed by adversaries.
- `malfind` attempts to identify injected processes and their PIDs, along with the offset address and a hex, ASCII, and disassembly view of the infected area. This is mainly useful for hunting for code injection. This scans the heap and identifies processes that have the executable bit set `RWE or RX` and/or no memory-mapped file on disk (as is the case with fileless malware). The injected area will change depending on what `malfind` finds. MZ headers may indicate Windows executable files, and the injected area may point to shellcode, which may need further analysis. Syntax is `python3 vol.py -f (FILE) windows.malfind`.
- `yarascan` searches for strings, patterns, and compound rules against a rule set. You may use a YARA file as an arugment or list rules within the command line. The syntax is `python3 vol.py -f (FILE) windows.yarascan`.

## [Task 9] Advanced Memory Forensics

Memory forensics can get confusing when you start looking at system objects and how the malware interacts directly with the system, particularly if you don't have prior experience hunting some of the techniques used by malware (such as hooking and driver manipulation). When dealing with advanced malware, you'll probably have to dive into the drivers, mutexes, and hooked functions to figure out what's going on. Many modules exist in Volatility to help with this.

We'll look at the plugins used to spot hooking. There are five methods of hooking.
- System Service Descriptor Table (SSDT) hooking: The SSDT maps system calls (syscalls) to kernel function addresses. Modifying this lets an adversary redirect syscalls to things outside of the kernel.
- I/O Request Paket (IRP) hooking: IRPs allow Windows NT device drivers to communicate with each other and the OS. Functions involving IRPs may be hooked such that they redirect to malicious functions.
- Import Address Table (IAT) hooking: The IAT is a lookup table used in portable executables to find specific library functions, and these too can be hooked.
- Export Address Table (EAT) hooking: This is quite similar to IAT hooking, except we look at the File Exports section of a portable executable rather than the import tables.
- Inline Hooking: The process of directly modifying the code of the target function so that execution jumps to a custom function.

We'll take a look at methods for detecting SSDT hooking; this is one of the most common techniques used in malware evasion, and with the base Volatility plugins it is easy to manage.
- `ssdt` is used to search for hooking and output its results. Note that hooking can be done legitimately, so it is up to the analyst to identify what is malicious. There can be hundreds of entries dumped in SSDT; you will need to analyze the output further or compare against a baseline. Perhaps consider using after investigating the initial compromise and work off it as part of the lead investigation. Syntax is `python3 vol.py -f (FILE) windows.ssdt`.

Adversaries will use malicious driver files as part of their evasion; there are a few plugins used to list drivers.
- `modules` will dump a list of loaded kernel modules, which can be useful in identifying active malware, though if a malicious file is idly waiting or hidden the plugin may miss it. Useful when you have investigated and found potential indicators to use as input for searching and filtering.
- `driverscan` will scan for drivers present on the system at the time of extraction, which can be used to identify driver files that `modules` might have missed (or were otherwise hidden). A prior investigation is good to have, and it might be good to check `modules` first. The syntax is `python3 vol.py -f (FILE) windows.driverscan`

Other plugins that might be useful in hunting for advanced malware include:
- `modscan`: Scans for modules present in a particular Windows memory image
- `driverirp`: Lists the IRPs for drivers in a Windows memory image.
- `callbacks`: Lists kernel callbacks and notification routines.
- `idt`: (Likely) A Vol2 plugin rather than a Vol3 one. Can be used to determine if changes have been made to the Interrupt Descriptor Table (IDT), including any hooking.
- `apihooks`: Not present in vol3. Can be used to detect API hooking.
- `moddump`: Dumps a kernel driver to an executable file sample. Not in vol3.
- `handles`: Lists process open handles.

Note that some of these are only present on Volatility 2 or are a part of third-party plugins. You may need to use third-party or custom plugins to get the most out of Volatility!

## [Task 10] Practical Investigations

And here we are - the practical exercise(s) of this room. There are two scenarios we will be investigating:
1. We have a quarantined endpoint that may have been compromised by a banking trojan masquerading as an Adobe document; we need to perform memory forensics on the host. This particular IP, `41.168.5.140`, was found to be linked to the file... it's suspicious and may be worth noting. This memory file is in `/Scenarios/Investigations/Investigation-1.vmem`.
2. Our (fictional) corporation has been hit with ransomware, and while we were able to get the decryption key and recover files, we still need to figure out what actually happened. We have a raw memory dump at `/Scenarios/Investigations/Investigation-2.raw`.

Note that `vol.py` may be found in `/opt/volatility3/vol.py`. To start with, it'll be in our best interest to get information about the memory files. We can try running the `info` plugins to see what comes up. When we run Volatility on the first investigation file with `windows.info`, we get:

![image](https://github.com/user-attachments/assets/f3ec4220-5f54-41f7-b2af-f838ab1b3204)

That's a lot of stuff, but we're looking for `NTBuildLab` for this first question.

**[Task 10, Question 1] What is the build version of the host machine in Case 001?** - `2600.xpsp.080413-2111`

The time the memory file was acquired can be found by looking at `SystemTime` in the output above.

**[Task 10, Question 2] At what time was the memory file acquired in Case 001?** - `2012-07-22 02:45:08`

We know now that this is a Windows memory image, so we can make use of the Windows plugins. We need to look for a suspicious process, though we're not told much else about it in this question. We'll run with `windows.psscan` for this one, since any process that tries to hide itself can reasonably be considered suspicious. The output of this command is as follows:

![image](https://github.com/user-attachments/assets/87f26347-5e70-4c9a-83c8-2005d369de52)

Most of these processes are legit (`alg.exe` is the Application Layer Gateway and `wuauclt.exe` has to do with Windows Update...the rest are system processes that we've seen before). The exception is `reader_sl.exe` -- note that the underscore does not render in the VM for some reason - like the question suggests, you can copy and paste directly from the VM to keep all the characters present.

**[Task 10, Question 3] What process can be considered suspicious in Case 001? Note: Certain special characters may not be visible on the provided VM. When doing a copy-and-paste, it will still copy all characters.** - `reader_sl.exe`

We could figure out what the parent process is just by looking at the output above, but it'll be easier to see what's going on if we use `windows.pstree`. Running Volatility with this plugin yields the following:

![image](https://github.com/user-attachments/assets/c7ddc443-58d1-4f43-9117-71180165067a)

The asterisks `*` indicate that a process is part of a hierarchical tree. For instance, System spawns `smss.exe`, which spawns `csrss.exe` and `winlogon.exe`, and so on. At the bottom of the output we see that `explorer.exe` has spawned `reader_sl.exe`, the suspicious process from before.

**[Task 10, Question 4] What is the parent process of the suspicious process in Case 001?** - `explorer.exe`

Looking at the output above, the process ID (PID) is the first number in a row for a given process. So in this case, `reader_sl.exe` has a PID of 1640.

**[Task 10, Question 5] What is the PID of the suspicious process in Case 001?** - 1640

And the parent PID is the second number in the row, 1484.

**[Task 10, Question 6] What is the parent process PID in Case 001?** - 1484

We'll need to make use of a separate Volatility plugin we haven't covered yet, `memmap`. This is called out in the hint for this question. It simply dumps the memory of a given process so we can examine it. We can run it with the syntax `sudo python3 /opt/volatility/vol.py -f Investigation-1.vmem -o . windows.memmap.Memmap --pid 1640 --dump`. This will dump the contents of the suspicious process's memory for us to examine. Note that we need to run this with `sudo` due to permissions issues.

When the process completes, we can run `sudo strings pid.1640.dmp | grep -i "user-agent"` to look for User Agent strings in the process's memory, of which there is one:

![image](https://github.com/user-attachments/assets/74b239fd-3338-4174-84ef-526b7c5adc9e)

**[Task 10, Question 7] What user-agent was employed by the adversary in Case 001?** - Mozilla/5.0 (Windows; U; MSIE 7.0; Windows NT 6.0; en-US)

If we run `sudo strings pid.1640.dmp | grep -i "chase"`, we definitely see a bunch of references to Chase Bank (screenshot below is just a snippet of the output):

![image](https://github.com/user-attachments/assets/c01b6f41-a80a-4112-a714-0a36c878bf82)

**[Task 10, Question 8] Was Chase Bank one of the suspicious bank domains found in Case 001? (Y/N)** - Y

Now let's move onto Case 2. From here on we need to work with `Investigation-2.raw`. First, let's see what suspicious process is running at PID 740. We can use `windows.pslist` to accomplish this:

![image](https://github.com/user-attachments/assets/846c02d0-c4be-4df9-88af-3a3dcb62d9bd)

**[Task 10, Question 9] What suspicious process is running at PID 740 in Case 002?** - `@WanaDecryptor@`

To grab the full path of the binary, we'll want to use `windows.dlllist`. This will spit out a bunch of DLL files, the files they're associated with, as well as full paths. There's a lot of entries for the suspicious process itself, so we'll want to grep for it:

![image](https://github.com/user-attachments/assets/1fe0f534-3500-4691-b3b1-32d4a8b529f0)

Near the top of the output, we see the full path for the decryptor executable.

**[Task 10, Question 10] What is the full path of the suspicious binary in PID 740 in Case 002?** - `C:\Intel\ivecuqmanpnirkt615\@WanaDecryptor@.exe`

Again, we can use `windows.pstree` to determine the parent process of PID 740:

![image](https://github.com/user-attachments/assets/8b481966-9665-46d2-b303-5cccdb946412)

**[Task 10, Question 11] What is the parent process of PID 740 in Case 002?** - `tasksche.exe`

The suspicious parent process's PID can be found in the output above.

**[Task 10, Question 12] What is the suspicious parent process PID connected to the decryptor in Case 002?** - 1940

If you looked up the filenames and the folder paths, you can figure out what malware is present on the system. There's also a good chance that you're just familiar with this malware. After all, it played a role in a ransomware attack in 2017, perhaps one of the most prolific ransomware attacks in recent memory.

**[Task 10, Question 13] From our current information, what malware is present on the system in Case 002?** - Wannacry

From here on, we just need to do some research into Wannacry. Here's a snippet of a YARA rule that can be used to detect Wannacry, or at least its main ransomware dropper:

![image](https://github.com/user-attachments/assets/01054274-2d7b-4df3-bb97-ff60f65cdffa)

This suggests that `ws2_32.dll` is used by Wannacry for socket-related things. You can also find tables and information on all the DLLs used by this ransomware.

**[Task 10, Question 14] What DLL is loaded by the decryptor used for socket creation in Case 002?** - `Ws2_32.dll`

From the same YARA file:

![image](https://github.com/user-attachments/assets/bf083970-3677-44d0-8451-0faea67587e5)

It appears that it uses the `MsWinZonesCacheCounterMutexA` mutex.

**[Task 10, Question 15] What mutex can be found that is a known indicator of the malware in question in Case 002?** - `MsWinZonesCacheCounterMutexA`

This last question just requires a reading of the Volatility help menu. It looks like this would be our best bet if we wanted to see what files were loaded.

![image](https://github.com/user-attachments/assets/94ae2660-0d34-4923-ba17-d7ed21afde3e)

**[Task 10, Question 16] What plugin could be used to identify all loaded files from the malware working directory in Case 002?** - `windows.filescan`
