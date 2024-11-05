# CAPA: The Basics

## [Task 1] Introduction

A key challenge in analyzing potentially malicious software is that our machine might get compromised when we run it, unless we've got a sandbox or a completely isolated environment. When we do analysis of malware, we can either perform _dynamic_ or _static_ analysis. Our focus here will be static analysis with a tool called CAPA.

CAPA - the Common Analysis Platform for Artifacts - is a tool developed by FireEye's Mandiant team. It's used to identify capabilities present in portable executables (PE), ELF binaries, .NET modules, shellcode, and even sandbox reports. It analyzes files and applies rules that describe common behaviors. The end goal in using this program is to figure out what a piece of software/malware is _capable of_... can it communciate in the network? Does it mess with files? Could it inject itself into processes?

CAPA effectively encapsulates years of reverse engineering knowledge into an automated tool, which makes it particularly useful for folks who don't have a lot of expertise in malware reverse engineering. This can be useful for analysts and security professionals, letting them quickly understand what a piece of malware can do without reverse-engineering everything. This is helpful in malware analysis and threat hunting exercises, where knowing what malware can do is critical for response and defense.

Ideally you would be familiar with MITRE ATT&CK before beginning work in this room, but it's not necessary. The attached VM contains all the tools and resources you'll need for this room. It does tkae a long time for CAPA to operate on a file, so for the questions we will be working with pre-processed CAPA reports. All of this can be found in `C:\Users\Administrator\Desktop\capa`.

## [Task 2] Tool Overview: How CAPA Works

Running CAPA is simple:
1. Open PowerShell and navigate to where the CAPA executable is stored.
2. Run `capa.exe (PATH_TO_THING_YOU_WANT_TO_INVESTIGATE)`.
3. You'll need to wait a while for CAPA to finish processing everything; this can take several minutes.

Some helpful parameters to know when working with CAPA include:
- `-h` or `--help`, which provides a help message.
- `-v` or `--verbose`, which provides more detailed results.
- `-vv` or `--vverbose`, which provides even more detaied results. Note that the more verbosity you provide, the longer it will take for CAPA to process stuff.

CAPA will spit out a bunch of information about what the malware can do in terms of various metrics, such as objectives, malware behaviors, and more.

As an aside, the files we'll be looking at in the forthcoming tasks are provided in this task - you can download the task files to investigate them yourself on your own machine. If you're using PowerShell to investigate the contents of a file, you can run `Get-Content cryptbot.txt` for all the information. Since this is a text file, you _could_ also just open it in a text editor for your perusal.

**[Task 2, Question 1] What command line-option would you use if you need to check what other parameters you can use with the tool? Use the shortest format.** - `-h`

**[Task 2, Question 2] What command-line options are used to find detailed information on the malware's capabilities? Use the shortest format.** - `-v`

**[Task 2, Question 3] What command-line options do you use to find very verbose information about the malware's capabilities? Use the shortest format.** - `-vv`

**[Task 2, Question 4] What PowerShell command will you use to read the content of a file?** - `Get-Content`

## [Task 3] Dissecting CAPA Results Part 1: General Information, MITRE, and MAEC

CAPA splits off all of its results into blocks, and so we'll discuss the results per block and topic. The first block contains general information about the file, including:
- Cryptographic algorithms and the resulting hashes, including `md5` and `sha1/256`.
- The `analysis` field tells us how CAPA analyzed the file - in our case it should just be `static`.
- The `os` field tells us what operating system (OS) the identified capabilities apply to.
- The `arch` field tells us if we're working with a binary in the `x86` architecture or not.
- The `path` tells us where the analyzed file was located.

MITRE ATT&CK, or the Adversarial Tactics, Techniques, and Common Knowledge framework, is a repository that documents tactics and techniques employed by threat actors at every stage of a cyberattack. It can be thought of as a playbook, outlining the methods used by attackers (e.g., gaining initial access, maintaining presence, escalating privileges, evading defenses, moving laterally, etc.). CAPA uses this format for the output:
- ATT&CK Tactic::ATT&CK Technique::Technique Identifier --> e.g., Defense Evasion::Obfuscated files or Information::T1027
- ATT&CK Tactic::ATT&CK Technique::ATT&CK Sub-Technique::Technique Identifier.Sub-Technique Identifier --> e.g., Defense Evasion::Obfuscated Files or Information::Indicator Removal from Tools::T1027.005

In the final output of CAPA, the program will reference tactics from the MITRE framework, which can help analysts and defenders map behavior to a playbook. This can also help with narrowing down the scope of the investigation during an incident. There's a TryHackMe room in the SOC 1 learning path that goes into more detail on MITRE.

MAEC - Malware Attribute Enumeration and Characterization - is a specializzed language designed to encode and communicate complex details concerning malware. It contains extensive attributes about a malware's behavior, artifacts, and interconnections with other pieces of malware. This provides a standardized system for tracking and analyzing the complicated complexities of malware. The most commonly-used values in CAPA are:
- Launcher: Enables behaviors that trigger actions specific to malware behavior, such as dropping payloads, activating persistence, connecting to command-and-control (C2) servers, and executing specific functions.
- Downloader: Exhibits behaviors where it downloads and executes other files, such as fetching payloads/resources from the Internet, pulling in updates, executing secondary stages, and retrieving configuration files.

Let's check out the report generated in the `capa` folder on the Desktop. Here's the general information, including the SHA256 hash:

![image](https://github.com/user-attachments/assets/eeeb916f-f02e-4432-9074-0396ad8ecb73)

**[Task 3, Question 1] What is the SHA256 of `cryptbot.bin`?** - `ae7bc6b6f6ecb206a7b957e4bb86e0d11845c5b2d9f7a00a482bef63b567ce4c`

The following questions can be answered with the informatio nprovided in the task.

**[Task 3, Question 2] What is the Technique Identifier of Obfuscated Files or Information?** - T1027

**[Task 3, Question 3] What is the Sub-Technique Identifier of Obfuscated Files or Information::Indicator Removal from Tools?** - T1027.005

**[Task 3, Question 4] When CAPA tags a file with this MAEC value, it indicates that it demonstrates behavior similar to, but not limited to, activating persistence mechanisms?** - Launcher

**[Task 3, Question 5] When CAPA tags a file with this MAEC value, it indicates that it demonstrates behavior similar to, but not limited to, fetching additional payloads or resources from the internet?** - Downloader

## [Task 4] Dissecting CAPA Results Part 2: Malware Behavior Catalogue

The next block contains information from the Malware Behavior Catalogue (MBC), which supports various aspects of malware analysis including the labeling of malware, similarity analysis, and standardized reporting. It is simply a collection of malware objectives and behaviors. MBC links to ATT&CK methods and all behaviors/code features discovered during malware analysis can be logged. MBC behavior names may not match those provided by MITRE - it simply is there to complement what you can find in ATT&CK. There are two formats for MBC information:
- `OBJECTIVE::Behavior::Method[Identifier]` --> e.g., ANTI-STATIC ANALYSIS::Executable Code Obfuscation::Argument Obfuscation [B0032.020]
- `OBJECTIVE::Behavior::[Identifier]` --> e.g., COMMUNICATION::HTTP Communication::[C0002]

The first format provides more information in the Method - you can think of it as a MITRE sub-technique.

Objectives are based on ATT&CK tactics in the context of malware behavior, though not all are covered. MBC has two objectives of its own - Anti-Behavioral and Anti-Static Analysis. These objectives are tailoed for malware analysis for characterizing malware. Here are the objectives used by MBC:
- Anti-Behavioral Analysis: Malware attempts to avoid detection by hindering behavioral analysis with tools like sandboxes and debuggers.
- Anti-Static Analysis: Malware attempts to obstruct or add complexity to malware analysis, making it more challenging to identify and understand malicious behaviors and intentions.
- Collection: Malware focuses on identifying and gathering information from the target machine/network.
- Command and Control: Malware establishes communication with compromised systems via a C2 server, peer-to-peer networks, or some other means. This lets the malware control the systems, and lets attackers execute commands, remove data, and carry out other malicious activities.
- Credential Access: Malware tries to steal account credentials, like usernames and passwords.
- Defense Evasion: Malware attempts to bypass and circumvent existing detection and security mechanisms in a system to avoid being detected or mitigated.
- Discovery: Malware tries to gather information about the configuration and setup of the system/network, including hardware, software, and network infrastructure.
- Execution: Malware is designed to execute malicious code or unauthorized commands withou the user's consent, including harmful activities ranging from stealing personal information to gaining unauthorized access.
- Exfiltration: Malware is designed to infiltrate computer systems and networks to steal and extract sensitive data. Includes personal info, financial details, and any other valuable data.
- Impact: Malware aims to manipulate, disrupt, or damage systems/data. It enters systems via infected emails and compromisd websites, among other means, and can cause financial loss, privacy breaches, and instability.
- Lateral Impact: Malware seeks to spread through the network via machine access or passive methods such as malicious emails.
- Persistence: Malware is intentionally designed with the capability to remain undetected and operational on a system for an extended period.
- Privilege Escalation: Malware aims to gain elevated permissions or control over a system, which can lead to sensitive information access among other things.

Micro-objectives are associated with micro-behaviors, actions exhibited by potentially malicious software that aren't necessarily malicious and may serve various objectives. CAPA will flag on this stuff since they are typically abused by attackers to do harm.
- PROCESS: Exhibit behaviors related to processes, including process creation, changing thread context, terminating processes, and checking mutex.
- MEMORY: Exhibit behaviors related to memory, including memory allocation, changing memory protection, and freeing memory.
- COMMUNICATION: Exhibit behaviors related to network traffic of all types, including DNS, FTP, HTTP, ICMP, SMTP
- DATA: Exhibit heaviors related to data, such as checking strings, compressing, decoding, and encoding data.

The objective and micro-objective are only shown under the Objective column in CAPA's output.

The MBC Behaviors column contains various behaviors and micro-behaviors with or without methods and identifiers. There's a list of all MBC behaviors provided by the MBC project itself, but here are some of the most common:
- ANTI-BEHAVIORAL ANALYSIS objective - Virtual Machine Detection behavior - B0009 - Malware checks to see if it is running in a virtual environment by examining user and system artifacts.
- ANTI-STATIC ANALYSIS objective - Executable Code Obfuscation behavior - B0032 - Executable code is intentionally obscurd to prevent static analysis, e.g. data and text sections aren't readable.
- EXECUTION objective - Command and Scripting Interpreter behavior - E1059 - Malware exploits command/script interpreters to run malicious commands/scripts/binaries, such as `cmd.exe`, PowerShell, Bash, Python, Perl, JS.
- DISCOVERY objective - File and Directory Discovery behavior - E1083 - Malware can search for specific files in particular locations by enumerating files and directories.
- ANTI-STATIC ANALYSIS, DEFENSE EVASION objectives - Obfuscated Files or information - E1027 - Malware can obfuscate files and information by encoding, encrypting, or doing something similar to make them hard to analyze. This might even include encoding or encryption of malware samples itself.

Micro-behaviors, or low-level behaviors, are those behaviors that aren't necessarily malicious but can be used in furtherance of various objectives. This may include stuff like the creation of TCP sockets in a messaging app binary. Just because a behavior is classified as a "micro-behavior" does NOT mean it isn't harmless! You need to consider what it's doing in context. This can be found in the Behavior column.
- MEMORY micro-objective - Allocate Memory micro-behavior - C0007 - Malware uses memory allocation as a strategy to unpack itself and execute malicious activities.
- PROCESS micro-objective - Create Process micro-behavior - C0017 - Malware creates a process with WMI or shellcode, and it may even create suspended processes.
- COMMUNICATION micro-objective - HTTP Communication micro-behavior - C0002 - Malware can initiate HTTP connections.
- DATA micro-objective - Check String micro-behavior - C0019 - Malware inspects a string to identify specific characteristics, like ASCII content and string length. May be used to find sensitive information.
- DATA micro-objective - Encode Data micro-behavior - C0026 - Malware can encode data with base64 and XOR.
- FILE SYSTEM micro-objective - Create Directory micro-behavior - C0046 - Malware can create a directory.
- FILE SYSTEM micro-objective - Delete File micro-behavior - C0047 - Malware can delete a file.
- FILE SYSTEM micro-objective - Read File micro-behavior - C0051 - Malware can read a file.
- FILE SYSTEM micro-objective - Write File micro-behavior - C0052 - Malware can write to a file.

Lastly, we may see methods. These are tied to behaviors, so you should see what behaviors are linked to a particular method for more information. Some examples of methods include:
- Executable Code Obfuscation behavior - Argument Obfuscation method - B0032.020 - Simple number/string arguments to API calls are calculated at runtime to make analysis more difficult.
- Executable Code Obfuscation behavior - Stack Strings method - B0032.017 - Build and decrypt strings on the stack at each use, then discard to avoid obvious references
- HTTP Communication behavior - Read Header method - C0002.014 - Reading the HTTP header
- Encode Data behavior - Base64 method - C0026.001 - Encode data with base64
- Encode Data behavior - XOR method - C0026.002 - Encode data with XOR
- Obfuscated Files or Information behavior - Encoding-Standard Algorithm method - E1027.m02 - Encoding malware samples, files, and other informatio nwith a standard algorithm such as base64.

CAPA may spit out "DATA" as an objective and "Encode Data::Base64 [C0026.001]" as a behavior. The "Encode Data" is the behavior, "Base64" is the method, and "C0026.001" is the identifier. This tells us that this particular piece of malware may perform base64 encoding.

The following questions can be answered with information provided in the task.

**[Task 4, Question 1] What serves as a catalogue of malware objectives and behaviors?** - Malware Behavior Catalogue

**[Task 4, Question 2] Which field is based on ATT&CK tactics in the context of malware behavior?** - Objective

**[Task 4, Question 3] What is the Identifier of "Create Process" micro-behavior?** - C0017

**[Task 4, Question 4] What is the behavior with an identifier of B0009?** - Virtual Machine Detection

**[Task 4, Question 5] Malware can be used to obfuscate data using base64 and XOR. What is the related micro-behavior for this?** - Encode Data

**[Task 4, Question 6] Which micro-behavior refers to "Malware is capable of initiating HTTP communications?"** - HTTP Communications

## [Task 5] Dissecting CAPA Results Part 3: Namespaces

The last block contains information on capabilities and namespaces. We'll start with namespaces first. A namespace format looks like this: `Capability(Rule Name)::TLN(Top-Level Namespace)/Namespace`, with an example being reference anti-VM strings::Anti-Analysis/anti-vm/vm-detection.

Namespaces are used to group items with the same purpose. Each top-level namespace contains a ruleset designed to detect something. Some top-level namespaces include:
- anti-analysis: Detect behaviors exhibited by malware to evade analysis, including obfuscation, packing, and anti-debugging
- collection: Detect behaviors exhibited by malware to enumerate and collect data for exfiltration.
- communication: Detect behaviors pertaining to malware communication, i.e., how it communicates with networks, receives/transmits data, how it interacts with C2s, and so on
- compiler: Detect behaviors employed by malware to recognize specific build environments. Basically the "signature" that identifies how the program was compiled
- data-manipulation: Governs behaviors involved in altering the data in executable files. Effectively any data transformation behavior such as string encryption and data encoding
- executable: Attributes in executable files, including PE sections and debug info
- host-interaction: How malware interacts with the host system, including reading/writing/modifying files, creating/deleting/modifying files and directories
- impact: Potential consequences/effects of a program's behavior, e.g. establishing remote access, exfiltrating data, destroying/modifying data
- internal: Rules contained within the system not intended for direct use by analysts or for reporting. Behind-the-scenes stuff.
- lib: Building blocks for other rules
- linking: Identify linking/dynamically loading of code/libraries during execution. Malware often needs other libraries/components to do stuff, and these can be a great way to learn about a malware's capabilities.
- load-code: Behaviors associated with dynamically loading/executing code during program execution - "runtime code injection" of malware and unauthorized introduction of code
- malware-family: Behaviors linked to specific families and groups of malware, used to accurately detect and classify a potential threat
- nursery: Staging ground with rules that aren't quite polished yet
- persistence: How malware maintains access and persistence in a compromised system
- runtime: Language/platform on which malware runs
- targeting: Behaviors exhibited by programs that interact with ATMs

Top-level namespaces will have other namespaces beneath them. For instance, here's what we have under Anti-Analysis:
- anti-vm/vm-detection: Behavior used to detect VM environments, identifying sttrings/patterns commonly used to determine if malware is on a VM. YAML files include `reference-anti-vm-strings-targeting-virtualbox.yml` and `reference-anti-vm-strings-targeting-virtualpc.yml`.
- obfuscation: Using obfuscation to make analysis more difficult, including string encryption, code obfuscation, packing, and anti-debugging tricks. Conceal or obscure true purpose of code. YAML files include `obfuscated-with-dotfuscator.yml` and `obfuscated-with-smartassembly.yml`.

There are still more namespaces under anti-analysis, and there's more information from the MBC project on all the TLNs out there.

**[Task 5, Question 1] Which top-level namespace contains a set of rules specifically designed to detect behaviors, including obfuscation, packing, and anti-debugging techniques exhibited by malware to evade analysis?** - anti-analysis

**[Task 5, Question 2] Which namespace contains rules to detect virtual machine (VM) environments? Note that this is not the TLN or Top-Level Namespace.** - anti-vm/vm-detection

**[Task 5, Question 3] Which Top-Level Namespace contains rules related to behaviors associated with maintaining access or persistence within a compromised system? This namespace is focused on understanding how malware can establish and maintain a presence within a compromised environment, allowing it to persist and carry out malicious activities over an extended period.** - persistence

**[Task 5, Question 4] Which namespace addresses techniques such as String Encryption, Code Obfuscation, Packing, and Anti-Debugging Tricks, which conceal or obscure the true purpose of the code?** - obfuscation

**[Task 5, Question 5] Which Top-Level Namespace is a staging ground for rules that are not quite polished?** - nursery

## [Task 6] Dissecting CAPA Results Part 4: Capability

Now let's dive into the capabilities. Here are some capabilities, their related TLNs and namespaces, and the associated YAML files. Note that the specific rules are not given - please check the MBC project's page for more information.
- reference anti-VM strings capability - anti-analysis TLN - anti-vm/vm-detection namespace - reference-anti-vm-strings.yml
- reference anti-VM strings targeting VMWare capability - anti-analysis TLN - anti-vm/vm-detection namespace - reference-anti-vm-strings-targeting-vmware.yml
- reference anti-VM strings targeting VirtualBox capability - anti-analysis TLN - anti-vm/vm-detection namespace - reference-anti-vm-strings-targeting-virtualbox.yml
- reference HTTP User-Agent string capability - communication TLN - http/client namespace - reference-http-user-agent-string.yml
- check HTTP status code capability - communication TLN - http namespace - check-http-status-code.yml
- reference Base64 string capability - Data Manipulation TLN - encoding/base64 namespace - reference-base64-string.yml
- encode data using XOR capability - Data Manipulation TLN - encoding/XOR namespace - encode-data-using-xor.yml
- contain a thread local storage (.tls) section capability - Executable TLN - pe/section/tls namespace - contain-a-thread-local-storage-tls-section.yml
- get common file path capability - Host-Interaction TLN - file-system namespace - get-common-file-path.yml
- create directory capability - Host-Interaction TLN - file-system/create namespace - create-directory.yml
- delete file capability - Host-Interaction TLN - file-system/delete namespace - delete-file.yml
- read file on Windows capability - Host-Interaction TLN - file-system/read namespace - read-file-on-windows.yml
- write file on Windows capability - Host-Interaction TLN - file-system/write namespace - write-file-on-windows.yml
- get thread local storage value capability - Host-Interaction TLN - process namespace - get-thread-local-storage-value.yml (note that this will also be found in the Nursery TLN)
- allocate or change RWX memory capability - Host-Interaction TLN - process/inject namespace - allocate-or-change-rwx-memory.yml
- create process on Windows capability - Host-Interaction TLN - process create namespace - create-process-on-windows.yml
- reference cryptocurrency strings capability - Impact TLN - impact/cryptocurrency namespace - reference-cryptocurrency-strings.yml (note that this will also be found in the Nursery TLN)
- link function at runtime on Windows capability - Linking TLN - runtime-linking namespace - link-function-at-runtime-on-windows.yml
- parse PE header capability - load-code TLN - load-code/pe namespace - parse-pe-header.yml and resolve-function-by-parsing-pe-exports.yml
- resolve function by parsing PE exports capability - load-code TLN - load-code/pe namespace - resolve-function-by-parsing-pe-exports.yml
- run PowerShell expression capability - load-code TLN - load-code/PowerShell namespace - run-powershell-expression.yml
- schedule task via at capability - persistence TLN - scheduled-tasks namespace - schedule-task-via-at.yml
- schedule task via schtasks capability - persistence TLN - scheduled-tasks namespace - schedule-task-via-schtasks.yml

Each of the YAML files above is used by CAPA to identify potentially malicious software and potentially malicious behavior. Notice how the YAML files have the same name as the capability, just with dashes.

One last example: CAPA may tell you that a malware has the "reference Base64 string" capability with the namespace "data-manipulation/encoding/base64."
- The capability "reference Base64 string" tells us that the malware can encode data with base64.
- The data-manipulation TLN tells us that the malware has behavior that involves transforming data.
- The encoding/base64 namespace tells us that CAPA found matches against rules regarding encoding/decoding of data with base64 and XOR. The match was probably in reference-base64-string.yml.

With this information, we can be rest assured that the file can use base64 encoding.

**[Task 6, Question 1] What rule yaml file was matched if the Capability or rule name is "check HTTP status code"?** - `check-http-status-code.yml`

**[Task 6, Question 2] What is the name of the Capability if the rule YAML file is `reference-anti-vm-strings.yml`?** - reference anti-VM strings

**[Task 6, Question 3] Which TLN or Top-Level Namespace includes the Capability or rule name "run PowerShell expression"?** - load-code

This final question asks us to look at the YAML file provided by the MBC project's GitHub, the contents of which are below:

![image](https://github.com/user-attachments/assets/9ef69ae6-606e-433d-b494-d9c4685adf33)

We want to pay special attention to "features" at the bottom - this gives us the two APIs that the rule looks for. Only one ends with `Ex`.

**[Task 6, Question 4] Check the conditions inside the `check-for-windows-sandbox-via-registry.yml` rule file. What is the value of the API that ends in `Ex` is it looking for?** - RegOpenKeyEx

## [Task 7] More Information, More Fun!

If we wanted to precisely figure out which rule was matched in CAPA, we can run it with `-vv`. This enables more verbosity than the usual `-v` flag, though CAPA will take longer to process the file as a result of using this flag. It may help to output the contents to JSON. We can do this by running `capa.bin -j -vv (FILE_TO_ANALYZE) > (OUTPUT).json`. Once we've done this, we can upload the file to CAPA Explorer Web, which is a tool that can be accessed online and offline. The local page may take a moment to load in the VM.

Once you reach this site, you can click "Upload from local" and upload the JSON file we created. This gives you a user-friendly interface to investigate the CAPA output. You can expand any of the rules noted and see what was referenced in the relevant YAML files. This can make the process of identifying malware capabilities a lot easier.

You can also use filter options and perform searches in the Global Search Box to find specific rules.

**[Task 7, Question 1] Which parameter allows you to output the result of CAPA into a `.json` file?** - `-j`

**[Task 7, Question 2] What tool allows you to interactively explore CAPA results in your web browser?** - CAPA Web Explorer

**[Task 7, Question 3] Which feature of this CAPA Web Explorer allows you to filter options or results?** - Global Search Box

## [Task 8] Conclusion

We have now seen how CAPA can be leveraged for analysis of potentially malicious/dangerous software and for threat-hunting via static analysis. It automates the process of detecting functionalities in an executable file and presents findings in an easily understandable format. This makes the process of analysis quick as well, which can help with incident response and defense.
