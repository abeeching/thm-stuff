# FlareVM: Arsenal of Tools

## [Task 1] Introduction

FLARE - Forensics, Logic Analysis, and Reverse Engineering - is a comprehensive and carefully-curated collection of specialized tools uniquely designed to meet the specific needs of reverse engineers, malware analysts, incident responders, forensic investigators, and pentesters. This was developed by FireEye.

You should be familiar with technical terms relating to Windows and processes. This is covered in the Pre Security path.

The VM takes a while to build and install, but you can access it in the room with the attached VM. You may also use RDP to access the room. The files will be found in `C:\Users\Administrator\Desktop\Sample` folder.

Note that the FlareVM machine contains malicious sample files and has no internet access. Do not download or execute the files ouside of the VM, and do not distribute them under any circumstances. This can lead to harm to the system or network.

## [Task 2] Arsenal of Tools

Here is the wide variety of tools offered by FlareVM:
- Reverse engineering and debugging (taking apart malware to understand how it works, identifying errors, correcting code):
  - Ghidra: NSA-developed open-source reverse engineering suite
  - x64dbg: Open-source debugger for binaries, x64 and x32 formats
  - OllyDbg: Debugger for reverse engineering at the assembly level
  - Radare2: Sophisticated open-source platform for reverse engineering
  - Binary Ninja: Disassemble and decompile binaries
  - PEiD: Packer, cryptor, compiler detection
- Disassemblers and decompilers (breaking malware behavior, logic, control flow into more understandable formats):
  - CFF Explorer: PE editor for analyzing/editing portable executable files
  - Hopper Disassembler: Debugger, disassembler, decompiler
  - RetDec: Open-source decompiler for machine code
- Static and dynamic analysis (methods for examining malware; static involves code inspection, dynamic involves observing behavior):
  - Process Hacker: Sophisticated memory editor and process watcher
  - PEview: Portable executable (PE) file viewer for analysis
  - Dependency Walker: Displays executable DLL dependencies
  - DIE (Detect It Easy): Packer, compiler, cryptor detection tool
- Forensics and incident response (collection, analysis, preservation of digital evidence from devices; containment, eradication, recovery from attacks)
  - Volatility: RAM dump analysis framework
  - Rekall: Framework for memory forensics in incident response
  - FTK Imager: Disc image acquisition
- Network analysis (studying, analyzing networks to uncover patterns, optimize performance, understand underlying structure):
  - Wireshark: Protocol analyzer for traffic recording and examination
  - Nmap: Vulnerability detection and network mapping tool
  - Netcat: Read and write data across network connections
- File analysis (examine files for potential security threats and ensure proper file permissions are in place)
  - File Insight: Program for looking through, editing binary files
  - Hex Fiend: Light and quick hex editor
  - HxD: Binary file viewing, editing with hex viewer
- Scripting and automation (using scripts such as PowerShell and Python to automate repetitive tasks/processes):
  - Python: Automation-focused modules and tools available
  - PowerShell Empire: Framework for post-exploitation with PowerShell
- Sysinternals (collection of advanced system utilities designed to help IT folks manage, troubleshoot, dignose systems):
  - Autoruns: Shows what executables are configured to run in system bootup
  - Process Explorer: Information about running processes
  - Process Monitor: Monitor, log real-time process and thread activity

There are, of course, more tools than those listed here.

**[Task 2, Question 1] Which tool is an open-source debugger for binaries in x64 and x32 formats?** - x64dbg

**[Task 2, Question 2] What tool is designed to analyze and edit portable executable (PE) files?** - CFF Explorer

**[Task 2, Question 3] Which tool is considered a sophisticated memory editor and process watcher?** - Process Hacker

**[Task 2, Question 4] Which tool is used for disc image acquisition and analysis for forensic use?** - FTK Imager

**[Task 2, Question 5] What tool can be used to view and edit a binary file?** - HxD

## [Task 3] Commonly Used Tools for Investigation: Overview

We'll be looking at a few programs that are useful for initial investigation, including:
- Procmon: Tracks system activity. Helpful for malware research, troubleshooting, forensic investigation
- Process Explorer: Lets you see process parent-child relationships, DLLs loaded, their path
- HxD: Examine and alter malicious files via hex editing
- Wireshark: Observing and investigating network traffic to look for unusual activity
- CFF Explorer: Generate file hashes for integrity verification, authenticate source of system files, validate them
- PEStudio: Static analysis, studying executable file properties without running files
- FLOSS: Extract and deobfuscate strings from malware programs with advanced static analysis techniques

Here's some more details on the utilities mentioned above. Process Monitor, or Procmon, lets you track system and Windows file activity in real-time. This lets you track filesystem, registry, and thread/process activity. For investigation, you can use it to spot oddities; for instance, `lsass.exe` is targeted by tools like Mimikatz for credential dumping, so you could be on the lookout for abnormal `lsass.exe` access patterns.

Process Explorer, or Procexp, gives in-depth insights into the active processes running on the computer, including the associated user accounts. This can be used to see what program is accessing a specific file or folder. This is useful if you want to see what processes are being spawned, e.g. processes that start after opening a Word document. This can be evidence of malicious activity.

HxD is a quick and flexible hex editor for editing files, memories, and even drives of any capacity. It can be used for forensic investigation, data recovery, debugging, and manipulation of binary data. You can view file and memory contents, edit, search, and compare hex data. You can see ASCII interpretation of the hexadecimal contents as well, and you can examine individual bytes with the data inspector. This tool can be useful for identifying file types, structures, and possible corruption, and you can even look for byte values (e.g., 4D 5A in little endian suggests a file is executable).

CFF Explorer lets you get file hashes, which can be useful for verifying integrity, authenticate the source of system files, and look for unusual alterations. This can be helpful in malware analysis, since dangerous code can be hidden in altered system files.

Wireshark is a useful tool for traffic analysis, allowing an investigator to hunt down dubious connections and spot possible attacks/exfiltration. You can see information about protocols, source/destination IPs, and other information. There's a sequence of rooms on Wireshark in the SOC Level 1 path.

PEStudio lets you perform static analysis, allowing you to investigate an executable file's properties without running anything. You can get a lot of information about a file without having to execute it, possibly putting your system at risk. You can gather information about a file's entropy (which can give information about whether a file was packed or encrypted), and you can even see if a file is capable of being run remotely.

Lastly, the FLARE Obfuscated String Solver (FLOSS) allows you to extract and deobfuscate all strings from malware programs. It can be used to enhance the static analysis process. FLOSS includes more Python scripts in the script's directory, and this can be used to load the output into other programs.

**[Task 3, Question 1] Which tool was formerly known as FLARE Obfuscated String Solver?** - FLOSS

**[Task 3, Question 2] Which tool offers in-depth insights into the active processes running on your computer?** - Process Explorer

**[Task 3, Question 3] By using the Process Explorer (procexp) tool, under what process can we find `smss.exe`?** - System

**[Task 3, Question 4] Which powerful Windows tool is designed to help you record issues with your system's apps?** - Procmon

**[Task 3, Question 5] Which tool can be used for static analysis or studying executable file properties without running the files?** - PEStudio

**[Task 3, Question 6] Using the tool PEStudio to open the file `cryptominer.bin` in the `Desktop\Sample` folder, what is the sha256 value of the file?** - `E9627EBAAC562067759681DCEBA8DDE8D83B1D813AF8181948C549E342F67C0E`

**[Task 3, Question 7] Using the tool PEStudio to open the file `cryptominer.bin` in the `Desktop\Sample` folder, how many functions does it have?** - 102

**[Task 3, Question 8] What tool can generate file hashes for integrity verification, authenticate the source of system files, and validate their validity?** - CFF Explorer

**[Task 3, Question 9] Using the tool CFF Explorer to open the file `possible_medusa.txt` in the `Desktop\Sample` folder, what is the MD5 of the file?** - `646698572AFBBF24F50EC5681FEB2DB7`

**[Task 3, Question 10] Use the CFF Explorer tool to open the file `possible_medusa.txt` in the `Desktop\Sample` folder. Then, go to the DOS Header Section. What is the `e_magic` value of the file?** - 5A4D

## [Task 4] Analyzing Malicious Files!

Let's make use of these tools to investigate a potential threat: a file named `windows.exe`, which we have put in the `C:\Users\Administrator\Desktop\Sample` folder. Our initial approach for this investigation is to perform static analysis to get initial information from the binary.

We'll use PEStudio to start with. We can open the program, then open the file by going to the File menu in the top-left. We can get information about the program:
- Its MD5 hash is `9FDD4767DE5AEC8E577C1916ECC3E1D6`
- Its SHA1 hash is `A1BC55A7931BFCD24651357829C460FD3DC4828F` - we can check both of these hashes against VirusTotal and see if there's a wider campaign going on with this program.
- Checking the description, we see that this file claims to be connected to the Registry Editor (Regedit), but this appears to be the malware's way of deceiving the user. Usually the tool is found in `C:\Windows\System32`.
- Russian characters present in the file's metadata may be suspicious, particularly if the user/organization does not operate in a Russian environment
- The lack of a rich header implies the file could be packed or obfuscated to avoid detection with static analysis tools, typical of sophisticated malware
- In the function tab, we see API calls that have been imported by the file (the Import Address Table, or IAT, formally). By clicking the blacklist tab, we can see all the blacklisted functions at the top, which can help us understand what a piece of malware does once it compromises the host.
  - The `set_UseShellExecute` function lets the process use the operating system's shell to execute other processes.
  - `CryptoStream`, `RijndaelManaged`, `CipherMode`, and `CreateDecryptor` APIs indicate that the executable uses 

If we run `FLOSS.exe .\windows.exe > windows.txt` in the directory that contains `windows.exe`, we can see a list of all the decoded strings in the file. This should be the same as what we got in PEStudio.

We'll also use Process Explorer and Process Monitor to explore the network connectivity involved in `cobaltstrike.exe`, located in the same place as `windows.exe`. If we check Process Explorer after running it, we should note that `explorer.exe` is treated as the parent process of `cobaltstrike.exe`. To see what kind of connections are being made, we can note the process ID, then right-click the process and go to Properties. Under the TCP/IP tab, we see that it attempts to connect to `47[.]120[.]46[.]210`.

We should verify our results with Process Monitor. We can open it, and then click the Filter button (or press CTRL + L). From there, we can do the following:
1. Select "Process Name"
2. Select "contains"
3. Type any value relating to the process, e.g. "cobalt"
4. Click "include"
5. Click Add, then click Apply.
6. The conditions should be applied, and Process Monitor will show us all communications involving the `cobaltstrike.exe` file - it does indeed reach out to the IP discussed above.

**[Task 4, Question 1] Using PEStudio, open the file `windows.exe`. What is the entropy value of the file `windows.exe`?** - 7.999

**[Task 4, Question 2] Using PEStudio, open the file `windows.exe`, then go to manifest (administrator) section. What is the value under `requestedExecutionLevel`?** - `requireAdministrator`

**[Task 4, Question 3] Which function allows the process to use the operating system's shell to execute other processes?** - `set_UseShellEncode`

**[Task 4, Question 4] Which API starts with R and indicates that the executable uses cryptographic functions?** - RijndaelManaged

**[Task 4, Question 5] What is the Imphash of `cobaltstrike.exe`?** - `92EEF189FB188C541CBD83AC8BA4ACF5`

**[Task 4, Question 6] What is the defanged IP address to which the process `cobaltstrike.exe` is connecting?** - `47[.]120[.]46[.]210`

**[Task 4, Question 7] What is the destination port number used by `cobaltstrike.exe` when connecting to its C2 IP address?** - 81

**[Task 4, Question 8] During our analysis, we found a process called `cobaltstrike.exe`. What is the parent process of `cobaltstrike.exe`?** - `explorer.exe`

## [Task 5] Conclusion

And that's the basics of FLARE. This is a complete and customized environment for incident response, malware reverse engineering, and forensic analysis.
