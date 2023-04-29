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



**[Task 4, Question 1] How many event ID 3 events are in C:\Users\THM-Analyst\Desktop\Scenarios\Practice\Filtering.evtx? - 73,591**

**[Task 4, Question 2] What is the UTC time created of the first network event in C:\Users\THM-Analyst\Desktop\Scenarios\Practice\Filtering.evtx? - 2021-01-06 01:35:50.464**

## [Task 5] Hunting Metasploit

## [Task 6] Detecting Mimikatz

## [Task 7] Hunting Malware

## [Task 8] Hunting Persistence

## [Task 9] Detecting Evasion Techniques

## [Task 10] Practical Investigations

**[Task 10, Question 1] What is the full registry key of the USB device calling svchost.exe in Investigation 1? - ```HKLM\System\CurrentControlSet\Enum\WpdBusEnumRoot\UMB\2&37c186b&0&STORAGE#VOLUME#_??_USBSTOR#DISK&VEN_SANDISK&PROD_U3_CRUZER_MICRO&REV_8.01#4054910EF19005B3&0#\FriendlyName```**

**[Task 10, Question 2] What is the device name when being called by RawAccessRead in Investigation 1? - \Device\HarddiskVolume3**

**[Task 10, Question 3] What is the first exe the process executes in Investigation 1? - rundll32.exe**

**[Task 10, Question 4] What is the full path of the payload in Investigation 2? - C:\Users\IEUser\AppData\Local\Microsoft\Windows\Temporary Internet Files\Content.IE5\S97WTYG7\update.hta**

**[Task 10, Question 5] What is the full path of the file the payload masked itself as in Investigation 2? - C:\Users\IEUser\Downloads\update.html**

**[Task 10, Question 6] What signed binary executed the payload in Investigation 2? - C:\Windows\System32\mshta.exe**

**[Task 10, Question 7] What is the IP of the adversary in Investigation 2? - 10.0.2.18**

**[Task 10, Question 8] What back connect port is used in Investigation 2? - 4443**

**[Task 10, Question 9] What is the IP of the suspected adversary in Investigation 3.1? - 172.30.1.253**

**[Task 10, Question 10] What is the hostname of the affected endpoitn in Investigation 3.1? - DESKTOP-O153T4R**

**[Task 10, Question 11] What is the hostname of the C2 server connecting to the endpoint in Investigation 3.1? - empirec2**

**[Task 10, Question 12] Where in the registry was the payload stored in Investigation 3.1? - HKLM\SOFTWARE\Microsoft\Network\debug**

**[Task 10, Question 13] What PowerShell launch code was used to launch the payload in Investigation 3.1? - ```"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -c "$x=$((gp HKLM:Software\Microsoft\Network debug).debug);start -Win Hidden -A \"-enc $x\" powershell";exit;```**

**[Task 10, Question 14] What is the IP of the adversary in Investigation 3.2? - 172.168.103.188**

**[Task 10, Question 15] What is the full path of the payload location in Investigation 3.2? - c:\users\q\AppData:blah.txt**

**[Task 10, Question 16] What was the full command used to create the scheduled task in Investigation 3.2? - ```"C:\WINDOWS\system32\schtasks.exe" /Create /F /SC DAILY /ST 09:00 /TN Updater /TR "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -NonI -W hidden -c \"IEX ([Text.Encoding]::UNICODE.GetString([Convert]::FromBase64String($(cmd /c ''more < c:\users\q\AppData:blah.txt'''))))\""```**

**[Task 10, Question 17] What process was accessed by schtasks.exe that would be considered suspicious behavior in Investigation 3.2? - lsass.exe**

**[Task 10, Question 18] What is the IP of the adversary in Investigation 4? - 172.30.1.253**

**[Task 10, Question 19] What port is the adversary operating on in Investigation 4? - 80**

**[Task 10, Question 20] What C2 is the adversary utilizing in Investigation 4? - Empire**
