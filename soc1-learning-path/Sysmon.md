# Sysmon

## Purpose of this Room
Sysmon is a tool that can be used to monitor and log events on Windows and is commonly used by enterprises as part of their monitoring and logging solutions. This is part of Windows Sysinternals and acts like Windows Event Logs... but with more detail and granular control. It's highly recommended to do the Windows Event Log room, but it also helps to do the Blue and Ice boxes.

## [Task 2] Sysmon Overview
Okay, here we go! Sysmon is a Windows system service and device driver that, once installed on a system, remains resident across system reboots to monitor and log system activity to the Windows event log. It provides detailed info about process creation, network connections, changes to file creation time, yadda yadda yadda. You can collect events using Windows Event Collection or SIEM agents and analyze them to identify malicious or anomalous activity and understand how intruders and malware operate on your network.

Sysmon gathers detailed and high-quality logs as well as event tracing that assists in identifying anomalies in your environment. Sysmon is most commonly used in conjunction with SIEM systems or other log parsing solutions that aggregate, filter, and visualize events. When installed on an endpoint, Sysmon starts early in the Windows boot process. Ideally, the events would be forwarded to a SIEM for further analysis. However, here, we'll focus on Sysmon itself and view events with Windows Event Viewer.

Events with Sysmon are stored in Applications and Srevices Logs/Microsoft/Windows/Sysmon/Operational.

Sysmon requires a configuration file that tells the binary how to analyze the events that it is receiving. You can create your own config or you can download one, such as SwiftOnSecurity's Sysmon-Config. There are 29 different types of Event IDs, all of which can be used in the config to specify how the events should be handled and analyzed. Some of the most important IDs will be discussed shortly. A majority of rules in Sysmon-Config exclude events, rather than include events. This helps filter out normal activity in your environment, decrasing the number of events and alerts to search and audit. Other rulesets, such as the ION-Storm sysmon-config fork, have a lot of include rules. Modify configuration files and figure out what you prefer. What you choose will likely depend on the SOC team.

Event 1, Process Creation: Looks for any processes that have been created, which can be used to look for known suspicious processes or processes with typos that would be considered anomalous. This uses the CommandLine and Image XML tags. The code snippet below specifies the Event ID to pull from as well as what condition to look for. Here, it excludes svchost.exe from the event logs:

```xml
<RuleGroup name="" group Relation="or">
   <ProcessCreate onmatch="exclude">
     <CommandLine condition="is">C:\Windows\system32\svchost.exe -k appmodel -p -s camsvc</CommandLine>
   </ProcessCreate>
</RuleGroup>
```

Event 3, Network Connection: This looks for events that occurs remotely, including files and sources of suspicious binaries and opened ports. This uses the Image and DestinationPort XML tags. There are two ways to identify suspicious network connection activity with the snippet below - identify files transmitted over open ports (e.g. nmap.exe) and identify open ports (e.g. 4444, commonly associated with Metasploit). If the condition is met, an event will be created and ideally trigger an alert for the SOC to investigate.

```Xml
<RuleGroup name="" groupRelation="or">
  <NetworkConnecct onmatch="include">
    <Image condition="image">nmap.exe</Image>
    <DestinationPort name="Alert,Metasploit" condition="is">4444</DestinationPort>
  </NetworkConnect>
</RuleGroup>
```

Event 7, Image Loaded: This looks for DLLs loaded by processes, which can be good for hunting DLL injection and hijacking attacks. Exercise caution with this ID, since this can cause high system load. This uses the Image, Signed, ImageLoaded, and Signature XML tags. The snippet below looks for DLLs that have been loaded within \Temp\, which could be anomalous.

```Xml
<RuleGroup name="" groupRelation="or">
  <ImageLoad onmatch="include">
    <ImageLoaded condition="contains">\Temp\</ImageLoaded>
  </ImageLoad>
</RuleGroup>
```

Event 8, CreateRemoteThread: This monitors for processes injeccting code into other processes. This function can be used for legitimate tasks and apps, however it could be used by malware to hide malicious activity. The event uses SourceImage, TargetImage, StartAddress, and StartFunction tags. The snippet below shows two ways of monitoring for CreateRemoteThread - looking at the memory address for a specific ending condition which could be an indicator of the Cobalt Strike beacon, or looking for injected processes with no parent processes.

```Xml
<RuleGroup name="" groupRelation="or">
  <CreateRemoteThread onmatch="include">
    <StartAddress name="Alert,Cobalt Strike" condition="end with">0B80</StartAddress>
    <SourceImage condition="contains">\</SourceImage>
  </CreateRemoteThread>
</RuleGroup>
```

Event 11, File Created: This logs events when files are created or overwritten in the endpoint. This can be used to identify filenames and signatures of files written to disk. This uses the TargetFilename XML tags. The snippet below is an example of a ransomware event monitor, and is not indicative of all the ways you can use Event ID 11.

```Xml
<RuleGroup name="" groupRelation="or">
  <FileCreate onmatch="include">
    <TargetFilename name="Alert,Ransomware" condition="contains">HELP_TO_SAVE_FILES</TargetFilename>
  </FileCreate>
</RuleGroup>
```

Events 12-14, Registry Event: This looks for changes or modifications to the registry. Malicious activity within the registry can include persistence and credential abuse. This uses the TargetObject XML tags. The below snippet looks for registry objects in the Windows\System\Scripts directory, as this is a common directory for adversaries to place scripts to establish persistence.

```Xml
<RuleGroup name="" groupRelation="or">
  <RegistryEvent onmatch="include">
    <TargetObject name="T1484" condition="contains">Windows\System\Scripts</TargetObject>
  </RegistryEvent>
</RuleGroup>
```

Event 15, FileCreateStreamHash: This looks for any files created in an alternate data stream, which is commonly used by adversaries to hide malware. This uses the TargetFilename XML tags. The snippet below looks for files with the .hta extension that have been placed in an alternate data stream:

```Xml
<RuleGroup name="" groupRelation="or">
  <FileCreateStreamHash onmatch="include">
    <TargetFilename condition="end with">.hta</TargetFilename>
  </FileCreateStreamHash>
</RuleGroup>
```

Event 22, DNS Event: This logs all DNS queries and events for analysis. The most common way to deal with this stuff is to EXCLUDE all trusted domains that you know will be common noise in the environment. Once you get rid of the noise, you can then look for DNS anomalies. This uses the QueryName XML tags. The snippet below excludes any DNS events with the .microsoft.com query, getting rid of some noise:

```Xml
<RuleGroup name="" groupRelation="or">
  <DnsQuery onmatch="exclude">
    <QueryName condition="end with">.microsoft.com</QueryName>
  </DnsQuery>
</RuleGroup>
```

There are a LOT of ways and tags that can be used to customize configuration files. We're using the ION-Storm and SwiftOnSecurity files in this room, but definitely experiment with some other config files!

## [Task 3] Installing and Preparing Sysmon

To install Sysmon, just download the binary from Microsoft's website. You can also download all of the Sysinternals tools with the PowerShell command `Download-SysInternalsTools (DIRECTORY)`. You should use a Sysmon config file along with Sysmon to get more detailed and high-quality event tracing. Remember, we're using SwiftOnSecurity and ION-Storm in this room.

To start Sysmon, open a PowerShell or Command Prompt as Administrator, then run the following command to execute the Sysmon binary, accept the EULA, and use the SwiftOnSecurity config file.
`Sysmon.exe -accepteula -i ..\Configuration\swift.xml`

Once Sysmon is started, you can look at the Event Viewer to monitor events. Remember, the log is located under Applications and Services Logs/Microsoft/Windows/Sysmon/Operational. You can change the configuration file used by uninstalling or updating the current configuration and replacing it with a new configuration file. Sysmon's help menu can help here. The room has a virtual machine with Sysmon set up, and thus it will be used for the rest of the room.

## [Task 4] Cutting Out the Noise

You will want to exclude or filter out normal activity to get rid of the "noise." This makes life a lot easier, allowing you to quickly identify and investigate stuff. There are some best practices that you can use to make sure you're operating as effectively as possible:
- Exclude > Include: It is typically best to prioritize excluding events than including events. This prevents yuo from accidentally missing crucial events and only seeing the events that matter the most.
- Stick to the CLI: As is common with most apps, the CLI gives you the most control and filtering allowing for further granular control. You can either use Get-WinEvent or wevutil.exe to access and filter logs. When you incorporate Sysmon into SIEM or detection solutions, these tools are less needed.
- Know Your Environment: Know your environment BEFORE implementing any tool. Make sure you understand what is normal and what isn't, so that you can craft your rules effectively.

Event Viewer isn't the best for filtering events, and offers limited control over logs out of the box. The main filter you're using is the EventID filter and keywords. You could filter with XML, but this is clunky and doesn't scale well. To open the filter menu, select Filter Current Log from the Actions menu.

![image](https://user-images.githubusercontent.com/56741029/235310877-b9467b8d-ecbb-4d69-ab9a-5aa6499ea119.png)

To filter events with PowerShell, use Get-WinEvent along with XPath queries. We can use any XPath queries that can be found in XML View. We can use wevtutil.exe to view evets after they are filtered. The command line is typically used over Event Viewer, since it allows for further granular control and filtering, whereas the GUI doesn't. For this room, we're only going over a few basic filters.
- Filter by Event ID: `*/System/EventID=<ID>`
- Filter by XML Attribute or Name: `*/EventData/Data[@Name="<XML Attribute/Name>"`
- Filter by Event Data: `*/EventData/Data=<Data>`

We can put these filters together with various attributes and data to get the most control out of our logs. Here's an example of using Get-WinEvent to look for network connecctions coming from port 4444.
```PowerShell
Get-WinEvent -Path <PATH_TO_LOG> -FilterXPath '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=4444`
```

Well, if we just want to count the number of Event ID 3 entries in Filtering.evtx, we can just adopt the above command for that purpose! Assuming you've cd'd into Desktop\Scenarios\Practice, you can just give this command:

`Get-WinEvent -Path Filtering.evtx -FilterXPath '*/System/EventID=3' | Measure-Object`

This takes a while to calculate, but when it's finally done, we see...

![image](https://user-images.githubusercontent.com/56741029/235325723-b6f06664-d1f1-46eb-82f3-a523ef0c76c7.png)

**[Task 4, Question 1] How many event ID 3 events are in C:\Users\THM-Analyst\Desktop\Scenarios\Practice\Filtering.evtx? - 73,591**

If you remember from the Windows Event Logs room, we were able to use a command to get the first event recorded in a series of events. This is what we want to do here with the Event ID 3 logs, since those represent network connection events. We give this command:

`Get-WinEvent -Path Filtering.evtx -FilterXPath '*/System/EventID=3' -Oldest -MaxEvents 1 | Format-List`

And we get this as our output!

![image](https://user-images.githubusercontent.com/56741029/235325820-0ff19718-0b2b-4d72-9bf4-4329c318aa59.png)

**[Task 4, Question 2] What is the UTC time created of the first network event in C:\Users\THM-Analyst\Desktop\Scenarios\Practice\Filtering.evtx? - 2021-01-06 01:35:50.464**

## [Task 5] Hunting Metasploit

Now let's move onto Metasploit. It seems that, generally speaking, you want to try and hunt for network connections that originate from suspicious ports such as 4444 and 5555. Metasploit specifically defaults to 4444. If there is a connection to ANY IP, known or unknown, it's worth investigating. You can do so by looking at packet captures from the date of the log to begin looking for more information about the adversary. We can also look for suspicious processes created.

Here's a modified ION-Security configuration that checks for ports 4444 and 5555 in Event ID 3.
```Xml
<RuleGroup name="" groupRelation="or">
   <NetworkConnect onmatch="include">
      <DestinationPort condition="is">4444</DestinationPort>
      <DestinationPort condition="is">5555</DestinationPort>
   </NetworkConnect>
</RuleGroup>
```

If we open the Hunting Metasploit event log in the machine in Event Viewer, we get some information that's useful to us in the XML View, for instanceC:
- The Process ID is useful to record, e.g. 3660
- The Image could be indicative of malicious activity, such as C:\Users\THM-Threat\Downloads\shell.exe

We can hunt for open ports in PowerShell using Get-WinEvent and XPath queries. We can use the same XPath queries that we used in the rule to filter out events from NetworkConnect with DestinationPort. Reminder that the command line is generally better for this kind of work, since you get more granularity and filtering than Event Viewer has.

```PowerShell
Get-WinEvent -Path <PATH_TO_LOG> -FilterXPath '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=4444'
```

First, this command filters by Event ID 3, the network connection ID. Then it filters by the data name, the DestinationPort, as well as the specific port we want to filter.

## [Task 6] Detecting Mimikatz

Now let's look for Mimikatz. This is a well-known and commonly-used program to dump credentials from memory along with other post-exploitation stuff. Mimikatz is primarily known for dumping LSASS secrets. We can hunt for the file created, execution of the file from elevated processes, creation of remote threads, and procecsses that Mimikatz creates. Antiviruses will typically pick up on Mimikatz since the signature is VERY well known, but it's still possible for threat actors to obfuscate or use droppers to get the file onto the device. We use a custom configuration in this room to help us here.

Methods for detecting Mimikatz:
1. Looking for files with the name Mimikatz. This can help you find stuFf that has bypassed the antivirus. Most of the time, you'll want more advanced techniques (e.g. searching for LSASS behavior), but this technique can be useful. It's generally preferable to use other techniques.

```Xml
<RuleGroup name="" groupRelation="or">
   <FileCreate onmatch="include">
      <TargetFileName condition="contains">mimikatz</TargetFileName>
   </FileCreate>
</RuleGroup>
```

2. Use ProcessAccess events to hunt for abnormal LSASS behavior. This event, along with LSASS, would show potential LSASS abuse which typically connects back to Mimikatz or some other kind of credential dumping tool. If LSASS is accessed by a process other than svchost.exe, it should be considered suspicious behavior and investigated. You can use a filter to look only for processes besides svchost.exe. Sysmon can give us more information to help with the investigation, such as the file path the process originated from.

```Xml
<RuleGroup name="" groupRelation="or">
   <ProcessAccess onmatch="include">
      <TargetImage condition="image">lsass.exe</TargetImage>
   </ProcessAccess>
</RuleGroup>
```

We can refine this by checking the Hunting LSASS event log in the virtual machine to get an idea of how Mimikatz may be obfuscated. We want to specifically exclude svchost.exe as a SourceImage, since this generates a lot of noise.

```Xml
<RuleGroup name="" groupRelation="or">
   <ProcessAccess onmatch="exclude">
      <SourceImage condition="image">svchost.exe</SourceImage>
   </ProcessAccess>
   <ProcessAccess onmatch="include">
      <TargetImage condition="image">lsass.exe</TargetImage>
   </ProcessAccess>
</RuleGroup>
```

We can find abnormal LSASS behavior with PowerShell's Get-WinEvent and XPath queries. We can actually use the same XPath queries used in the rule to filter out the other processes from TargetImage. If we can include this with a well-built configuration file, that does a LOT of the heavy lifting for us; we only have to filter a little bit.

```PowerShell
Get-WinEvent -Path <PATH_TO_LOG> -FilterXPath '*/System/EventID=10 and */EventData/Data[@Name="TargetImage"] and */EventData/Data="C:\WIndows\system32\lsass.exe"'
```

## [Task 7] Hunting Malware

There's a LOT of malware out there, right? How are we gonna hunt for it? Hear me out...

Malware can be classified into broad categories. In this room, we will focus on RATs (Remote Access Trojans) and backdoors. These are similar to payloads that involve gaining remote access on a machine. RATs typically come with other AV and detection evasion techniques that make them different than other payloads (e.g., those generated with msfvenom). RATs typically also use a client-server model and comes with an interface for user administration. To detect malware, we must first identify the malware we want to look for and then identify ways we can modify configuration files to help with this. This is hypothesis-based hunting. There's a lot of ways to look for malware, but we're only going to focus on basic methods for detecting open back-connect ports.

RATs and C2 Servers - This is going to be similar to finding Metasploit usage. We can look through and create configuration files to hunt and detect suspicious ports open on the endpoint. We can use known suspicious ports and include those in our logs, look for logs to identify adversaries, then use packet captures or other strategies to investigate further. The ION-Storm configuration file will look for specific ports like 1034 and 1604, as well as exclude common network connections such as OneDrive.

Word of advice for configuration files: make sure you understand what it is that it's doing. The ION-Storm configuration file excludes port 53 as an event, but attackers and adversaries have begun using port 53 as part of their malware or payloads. This is clearly not good.

```Xml
<RuleGroup name="" groupRelation="or">
   <NetworkConnect onmatch="include">
      <DestinationPort condition="is">1034</DestinationPort>
      <DestinationPort condition="is">1604</DestinationPort>
   </NetworkConnect>
   <NetworkConnect onmatch="exclude">
      <Image condition="image">OneDrive.exe</Image>
   </NetworkConnect>
</RuleGroup>
```

As the Hunting Rats event log (in the VM) will show, there are RATs that can operate on port 8080, which is a common substitute for port 80! We need to be careful when excluding events in order to not miss potential malicious activity.

As before, we can use PowerShell's Get-WinEvent along with XPath queries to filter our events and gain granular control over our logs. We want to filter in the NetworkConnect ID and the DestinationPort attribute.

```PowerShell
Get-WinEvent -Path <PATH_TO_LOG> -FilterXPath '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=<PORT>`
```

## [Task 8] Hunting Persistence

Persistence is used by attackers to maintain access to a machine once it's compromised. There's a multitude of ways for an attacker to gain persistence on a machine. We will be focusing on registry modification as well as startup scripts. We can find this in Sysmon by using File Creation and Registry Modification events. SwiftOnSecurity does a pretty good job of specifically targeting persistence and techniques used. We can also filter by rule names to get rid of noise.

First, let's look for Startup Persistence. In SwiftOnSecurity, we look for files in \Startup\ or \Start Menu.

```Xml
<RuleGroup name="" groupRelation="or">
   <FileCreate onmatch="include">
      <TargetFilename name="T1023" condition="contains">\Start Menu</TargetFilename>
      <TargetFilename name="T1165" condition="contains">\Startup\</TargetFilename>
   </FileCreate>
</RuleGroup>
```

The T1023.evtx event log includes an instance of seeing a persistence executable being dropped in the Startup folder. This is normally not this obvious in practice, but changes to the Start Menu should be investigated. You can adjust the configuration file to be more granular and create alerts past just the File Created tag. We can also filter by Rule Name T1023. Once you spot a suspicicous bianry or app in startup, investigate it!

Next, we'll look at how SwiftOnSecurity handles registry key persistence. We're looking, here, for modifications that place a script inside CurrentVersion\Windows\Run and other registry locations.

```Xml
<RuleGroup name="" groupRelation="or">
   <RegistryEvent onmatch="include">
      <TargetObject name="T1060,RunKey" condition="contains">CurrentVersion\Run</TargetObject>
      <TargetObject name="T1484" condition="contains">Group Policy</TargetObject>
      <TargetObject name="T1060" condition="contains">CurrentVersion\Windows\Run</TargetObject>
   </RegistryEvent>
</RuleGroup>
```

In the T1060.evtx event log, we can see an example where a malicious executable was added to HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run\Persistence. You can also see where this executable is in the system! We can filter by RuleName T1060 to make finding this anomaly easier. To investigate this properly, we want to check the registry AND the file location itself.

## [Task 9] Detecting Evasion Techniques

Our last stop on this journey is the evasion techniques. There's a lot of techniques used by malware authors to evade AVs and detections. Techniques include (but are likely not limited to)
- Alternate Data Streams (ADS)
- Injections
- Masquerading
- Packing and Compression
- Recompiling
- Obfuscation
- Anti-Reversing Techniques

We only focus on the first two techniques in this room. ADS is used by malware to hide files from normal inspection by saving the file in a different stream apart from $DATA. Sysmon has an event ID to detect newly-created and accessed streams, allowing us to quickly detect and hunt malware that uses ADS. Injection techniques come in many different types:
- Thread hijacking
- PE injection
- DLL injection
... and more!

In this room, we only focus on DLL injection and DLL backdoors. This is done by taking an already-used DLL that is used by an application and overwriting or including malicious code in the DLL.

First, let's look for ADS. This can be done with Event ID 15. It hashes and logs any NTFS streams that are included within the Sysmon configuration file, which lets us hunt for malware that evades detection using ADS. We can use the SwiftOnSecurity configuration file for this.

```Xml
<RuleGroup name="" groupRelation="or">
   <FileCreateStreamHash onmatch="include">
      <TargetFilename condition="contains">Downloads</TargetFilename>
      <TargetFilename condition="contains">Temp\7z</TargetFilename>
      <TargetFilename condition="ends with">.hta</TargetFilename>
      <TargetFilename condition="ends with">.bat</TargetFilename>
   </FileCreateStreamHash>
</RuleGroup>
```

The Hunting ADS.evtx log in the VM gives an example of a file opened with an alternate data stream. This tells us where the file is as well as the contents of the file, which can be useful in investigations.

Next, let's find remote threads - these are used to evade detections in combination with other techniques. This is accomplished by using the Windows API CreateRemoteThread, and the thread can be accessed by using OpenThread and ResumeThread. This is used in multiple techniques, such as DLL injection, thread hijacking, and process hollowing. We're going to use Event ID 8 from SwiftOnSecurity to accomplish this.

```Xml
<RuleGroup name="" groupRelation="or">
   <CreateRemoteThread onmatch="exclude">
      <SourceImage condition="is">C:\Windows\system32\svchost.exe</SourceImage>
      <TargetImage condition="is">C:\Program Files (x86)\Google\Chrome\Application\chrome.exe</TargetImage>
   </CreateRemoteThread>
</RuleGroup>
```

The Detecting RemoteThreads event log gives an example of a process hollowing attack that abuses notepad.exe. As we can see, PowerShell.exe creates a remote thread and accesses notepad.exe. This can, in theory, be used to execute any other kind of executable or DLL. Specifically, we call this Reflective PE Injection.

Now let's use PowerShell to find these techniques. You already know where we're going with this by now, right? We can use the EventID filter on its own here, since the configuration file actually does a lot of the heavy lifting for us.

```PowerShell
Get-WinEvent -Path <PATH_TO_LOG> -FilterXPath '*/System/EventID=8'
```

## [Task 10] Practical Investigations

Okay! Now let's tackle some practical investigations. The first investigation involves a malicious file that has been dropped onto a host by a malicious USB! They've pulled the logs suspected, and now we gotta do an investigation. We first need to figure out the full registry key of the USB device calling svchost.exe. Based on the beginning of our discussion, we have three event IDs that we can look for: Events 12-14.

This entire task will assume that we have CD'd into the Investigations directory.

Some quick trial-and-error tells us we can filter Get-WinEvent with Event ID 13. Running the command `Get-WinEvent -Path Investigation-1.evtx -FilterXPath '*/System/EventID=13' | Format-List` shows us the following events:

![image](https://user-images.githubusercontent.com/56741029/235328143-b0324992-9cc0-4fa1-b357-33ef4c30cbd2.png)

We're specifically interested in the first event's key.

**[Task 10, Question 1] What is the full registry key of the USB device calling svchost.exe in Investigation 1? - ```HKLM\System\CurrentControlSet\Enum\WpdBusEnumRoot\UMB\2&37c186b&0&STORAGE#VOLUME#_??_USBSTOR#DISK&VEN_SANDISK&PROD_U3_CRUZER_MICRO&REV_8.01#4054910EF19005B3&0#\FriendlyName```**

A quick Google search tells us that RawAccessRead can be detected by Sysmon Event ID 9, so we filter for that: `Get-WinEvent -Path Investigation-1.evtx -FilterXPath '*/System/EventID=9' | Format-List`. This gives us a lot of events with the same image, so...

![image](https://user-images.githubusercontent.com/56741029/235328195-0ee65d44-4fb5-4af3-bcb3-d81654e8a698.png)

**[Task 10, Question 2] What is the device name when being called by RawAccessRead in Investigation 1? - \Device\HarddiskVolume3**

This is an instance of Sysmon Event ID 1 in use. Hence, we filter for that in Get-WinEvent and look at the very first instance. There wasn't a lot of events, so I didn't include the -Oldest and -MaxEvent stuff, but that's helpful to include if you're dealing with a loooot of stuff. Anyway, `Get-WinEvent -Path Investigation-1.evtx -FilterXPath '*/System/EventID=1 | Format-List` spits out the following:

![image](https://user-images.githubusercontent.com/56741029/235328243-74519685-2566-45d2-abe1-4b21c7120187.png)

**[Task 10, Question 3] What is the first exe the process executes in Investigation 1? - rundll32.exe**

Alright, that's one investigation down! The second investigation involves a suspicious file executing code despite being an HTML file. This is evading antivirus detection which, well, isn't quite good. Since we're looking for stuff being executed, Event ID 1 is a good place to start. Filtering for this with the command `Get-WinEvent -Path Investigation-2.evtx -FilterXPath '*/System/EventID=1 | Format-List` and looking at the most recent event tells us what we need for the next *three* questions. Wow!

![image](https://user-images.githubusercontent.com/56741029/235328300-852663b6-5a43-4aed-9c76-7e6c81ede9a9.png)

For this question, we look at the OriginalFileName.

**[Task 10, Question 4] What is the full path of the payload in Investigation 2? - C:\Users\IEUser\AppData\Local\Microsoft\Windows\Temporary Internet Files\Content.IE5\S97WTYG7\update.hta**

Then we look at the ParentImage.

**[Task 10, Question 5] What is the full path of the file the payload masked itself as in Investigation 2? - C:\Users\IEUser\Downloads\update.html**

Then we look at the Image.

**[Task 10, Question 6] What signed binary executed the payload in Investigation 2? - C:\Windows\System32\mshta.exe**

Now we need to look at remote connections, Event ID 3. We don't really need to add anything else to our filter for our purposes, so... `Get-WinEvent -Path Investigation-2.evtx -FilterXPath '*/System/EventID=3 | Format-List` gives us the information we need for Questions 7 and 8. Note that we are paying attention to the Destination IP and port.

![image](https://user-images.githubusercontent.com/56741029/235328365-fd962a0b-7cb5-4e24-a03f-a55fe3d196dd.png)

**[Task 10, Question 7] What is the IP of the adversary in Investigation 2? - 10.0.2.18**

**[Task 10, Question 8] What back connect port is used in Investigation 2? - 4443**

Alright, another investigation complete! Let's start looking at Investigation 3, which will be conducted with two logs. Apparently our adversary has established persistence on our endpoints, so we need to figure out how they got it. Alright, let's do this. The next three questions can be found by examining network connections, Event ID 3. Running `Get-WinEvent -Path Investigation-3.1.evtx -FilterXPath '*/System/EventID=3' | Format-List` yields multiple events with the same Destination and Source IP information, all of which is needed for the next three questions.

![image](https://user-images.githubusercontent.com/56741029/235328425-9413c6fd-7379-4675-91b7-89888d5950e7.png)

**[Task 10, Question 9] What is the IP of the suspected adversary in Investigation 3.1? - 172.30.1.253**

**[Task 10, Question 10] What is the hostname of the affected endpoint in Investigation 3.1? - DESKTOP-O153T4R**

**[Task 10, Question 11] What is the hostname of the C2 server connecting to the endpoint in Investigation 3.1? - empirec2**

Well, this time we have to examine Events 12-14 to investigate the registry. Checking Event ID 13 with `Get-WinEvent -Path Investigation-3.1.evtx -FilterXPath '*/System/EventID=13' | Format-List` yields this totally-not-suspicious registry modification.

![image](https://user-images.githubusercontent.com/56741029/235328547-8a4635ca-5f7a-4424-b03b-5659cd81b65f.png)

(this stuff keeps going for multiple lines)

I think it's safe to say something got thrown into that registry key.

**[Task 10, Question 12] Where in the registry was the payload stored in Investigation 3.1? - HKLM\SOFTWARE\Microsoft\Network\debug**

As it would turn out, in the same query, we have another registry event that tells us what was used to *launch* the payload. How kind of them!

![image](https://user-images.githubusercontent.com/56741029/235328690-2d0d00e7-6ab7-40a9-aeb3-19bd56a5f94e.png)

**[Task 10, Question 13] What PowerShell launch code was used to launch the payload in Investigation 3.1? - ```"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -c "$x=$((gp HKLM:Software\Microsoft\Network debug).debug);start -Win Hidden -A \"-enc $x\" powershell";exit;```**

Now onto Investigation 3.2! As before, we want to check network connections, so let's look at Event ID 3. You can follow the same format that we've used before to get the list of events. Filtering for this event ID yields multiple events, each with the same DestinationIsIpv6 entry!

![image](https://user-images.githubusercontent.com/56741029/235329043-678cb9f6-e565-47ed-b73d-3ad601b8ad1c.png)

**[Task 10, Question 14] What is the IP of the adversary in Investigation 3.2? - 172.168.103.188**

Now we're looking for something in a payload location. We can actually get this by investigating Event ID 1, process creation. Checking the registry doesn't do us any favors, so I think we're justified in assuming that a process has been used to put the payload somewhere. Lo and behold, it was!

![image](https://user-images.githubusercontent.com/56741029/235329099-13d7d0d1-940e-459e-b4a9-e9eccfd8074b.png)

**[Task 10, Question 15] What is the full path of the payload location in Investigation 3.2? - c:\users\q\AppData:blah.txt**

The scheduled task information can also be found in Event ID 1!

![image](https://user-images.githubusercontent.com/56741029/235329123-596880ee-a095-4151-adf4-fa627e8f2b35.png)

**[Task 10, Question 16] What was the full command used to create the scheduled task in Investigation 3.2? - ```"C:\WINDOWS\system32\schtasks.exe" /Create /F /SC DAILY /ST 09:00 /TN Updater /TR "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -NonI -W hidden -c \"IEX ([Text.Encoding]::UNICODE.GetString([Convert]::FromBase64String($(cmd /c ''more < c:\users\q\AppData:blah.txt'''))))\""```**

So, I'm not sure if I'm just ovverthinking stuff here, but uhhh... huh. You would think to check Event ID 10, based on our discussion about LSASS abuse with Mimikatz, but Get-WinEvent doesn't actually return any helpful information. However, we do know what to look for, so let's just peruse the log in Windows Event Viewer. After poking around, we see this funny little thing:

![image](https://user-images.githubusercontent.com/56741029/235329471-b965c5dd-1720-415a-8a8b-72acfa67415e.png)

In the industry, I believe they call this "suspect."

**[Task 10, Question 17] What process was accessed by schtasks.exe that would be considered suspicious behavior in Investigation 3.2? - lsass.exe**

Alright, one more investigation to go! We know that the adversary got onto our network, and now we're worried that they've set up C2 communications on some of the endpoints. Let's look into that.

A simple look at the logs with Event ID 3 tells us all we need to know for the remainder of the questions. We're looking specifically at a Destination IP with empirec2 in it because...well...it's got c2 in the name. At best, that's *weird*.

![image](https://user-images.githubusercontent.com/56741029/235329552-dfbe899d-eabc-4688-971a-6ff34de95f7e.png)

We specifically look at the destination information.

**[Task 10, Question 18] What is the IP of the adversary in Investigation 4? - 172.30.1.253**

**[Task 10, Question 19] What port is the adversary operating on in Investigation 4? - 80**

**[Task 10, Question 20] What C2 is the adversary utilizing in Investigation 4? - Empire**
