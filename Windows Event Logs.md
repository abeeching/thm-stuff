# Windows Event Logs

## Purpose of this Room
Understanding how events are logged in Windows is essential for understanding what's happening in a system and diagnosing problems with the system. Logs provide clues that can help us understand how problems happened and how we can sollve them. The OS, by default, writes messagtes to these logs. Security Information and Event Management (SIEM) systems combine logs from multiple sources to help identify correlations between seemingly-unrelated events on different servers. In larger environments, this is the way to go.

## [Task 2] Event Viewer

Event Logs are not text files that can be viewed with your standard text editor. The raw data can be translated into XML, though, using the Windows API. The events in these files are stored as a .evt or .evtx file. Log files with .evtx extensions are stuck in `C:\Windows\System32\winevt\Logs`.

Windows has a standardized way of logging information. The categories for logs are as follows:
- System Logs: Events associated with operating system stuff - hardware changes, device drivers, system changes, other activities
- Security Logs: Events associated with login/log-off activities. Audit policy specifies the events to be logged.
- Application Logs: Events associated with applications installed on a system - warnings, errors, and events.
- Directory Service Events: Active Directory-related junk, particularly on Domain Controllers.
- File Replication Service Events: Events associated with Windows Servers during sharing of Group Policies and logon scripts to domain controllers, from where they may be accessed by the users through the client servers.
- DNS Event Logs: DNS servers use these logs to record domain events and to map out
- Custom Logs: Events logged by apps that require custom data storage. This lets apps control log size or other parameters (e.g. ACLs) for security reasons.

Events can show up as one of five types:
- Error: A significant problem, such as loss of data or functionality. E.g. service fails to load
- Warning: Event that isn't necessarily significant, but could indicate a possible future problem. E.g. disk space is low.
- Information: Event that describes successful operation of app, driver, or service. E.g. network driver loading correctly
- Success Audit: Records audited security access attempt that is successful. E.g. successful login
- Failure Audit: Records audited security access attempt that fails. E.g. failed access to network drive

Three ways to access event logs: Event Viewer, Wevtutil.exe, and Get-WinEvent. We first discuss Event Viewer, which is a Microsoft Management Console (MMC) snap-in. This can be launched by right-clicking the Windows icon and selecting Event Viewer. You can also type eventvwr.msc into the command line.

Event Viewer comes with three panes:
1. The left pane: A hierarchical tree listing of the event log provviders
2. The middle pane: A general overview and summary of events specific to a selected provider
3. The right pane: Actions

Standard logs defined earlier are found under Windows Logs. Following this, we have the Applications and Services Logs. We can check the PowerShell logs here by going to Microsoft --> Windows --> PowerShell --> Operational. Right-click on Operational, then click Properties. This tells you the log location, size, and when it was last created/modified/accessed. You can set maximum log size and what action to take once criteria are met - this allows for log rotation. The settings set here will vary depending on your circumtsances. You can also just straight-up clear the log if you want, and while there are legitimate reasons to do so, adversaries can do this to cover up their traces.

In the middle pane, you will be shown events specific to a given provider, in this case, PowerShell/Operational. This tells you the event provider's name and the number of events logged. Each column in the pane gives you different information:
- Level: This highlights the log recorded type based on the event types listed above.
- Date and Time: When the event was logged.
- Source: The name of the software that logs the event.
- Event ID: A predefined number that maps to a specific operation or event based on the log source. Event IDs are not unique -- if you see the same number in two different logs, they have different meanings.
- Task Category: Highlights the Event Category. This helps you organize events so Event Viewer can filter them.

The middle pane has a split view - more information is shown in the bottom half of the middle pane for any event you click on. There are two tabs - General and Details.

The Actions Pane gives you plenty of options. This incloudes opening saved logs, changing views, saving events, clearing and disabling logs, and so on. Create Custom View and Filter Current Log are identical, but in Filter Current Log, you don't get to choose By log or By source. You can use these options to filter out certain events with certain IDs, for intsance.

You can view event logs from another computer by right-clicking Event Viewer (Local) --> Connect to Another Computer...

Opening Event Viewer in the attached virtual machine, and going to Microsoft --> Windows --> PowerShell --> Operational, we can answer the first question by sorting by Date and Time in the top half of the pane. The very first event is as follows:

![image](https://user-images.githubusercontent.com/56741029/234171066-493ca742-16dd-488d-98f2-75f19cc55cd5.png)

**[Task 2, Question 1] What is the Event ID for the first recorded event? - 40961**

You can use the Filter Current Log action to filter the log for Event ID 4104. Doing so gives us a bunch of events to work with. The second event that was logged is clearly whoami!

![image](https://user-images.githubusercontent.com/56741029/234171502-7062ae0a-9bc1-4cdb-8807-3979e6cbef6f.png)

**[Task 2, Question 2] Filter on Event ID 4104. What was the 2nd command executed in the PowerShell session? - whoami**

Similarly, you can check the Task Category in the middle pane.

![image](https://user-images.githubusercontent.com/56741029/234171555-806b863d-8e0f-4e65-899b-cc540400630b.png)

**[Task 2, Question 3] What is the Task Category for Event ID 4104? - Execute a Remote Command**

Event ID 800 doesn't show up in the regular Event Viewer logs for PowerShell; however, it does show up in the merged event log on the VM's desktop. Opening that and just scrolling down a little bit gives us some instances of Event ID 800. The Task Category is clearly shown here:

![image](https://user-images.githubusercontent.com/56741029/234172577-08954269-8161-4aaa-ad7e-002e859308d1.png)

**[Task 2, Question 4] Analyze the Windows PowerShell log. What is the Task Category for Event ID 800? - Pipeline Execution Details**

## [Task 3] wevtutil.exe

This is a tool that allows you to get information about event logs and publishers. It can also be used to install and uninstall manifests, run queries, and export/archive/clear logs. This can help automate the process of sifting through event logs because, oh man, there's a lot of logs to look through.

To get a list of help commands, simply run `wevtutil.exe /?`. You get a brief example of how to use the program, as well as a list of common options that one may use. Since wevtutil has commands that you can use in addition, you can use `wevtutil (COMMAND) /?` to get help for a specific command.

To get the number of log names that are on the machine, you can use `wevtutil.exe el | Measure-Object` in PowerShell.

**[Task 3, Question 1] How many log names are in the machine? - 1071**

You can get the information for this question by running `wevtutil.exe qe /?` in PowerShell.

**[Task 3, Question 2] What event files would be read when using the query-events command? - event log, log file, structured query**

In the output of `wevtutil.exe qe /?`, you're also told what option you need to use to give a path to a log file.

**[Task 3, Question 3] What option would you use to provide a path to a log file? - /lf:true**

Also found in the help for wevtutil's qe command.

**[Task 3, Question 4] What is the VALUE for /q? - Xpath query**

The next three questions can be answered by examining the command `wevtutil qe Application /c:3 /rd:true /f:text`. Use the help pages on wevtutil as needed.

**[Task 3, Question 5] What is the log name? - Application**

**[Task 3, Question 6] What is the /rd option for? - Event read direction**

**[Task 3, Question 7] What is the /c option for? - Maximum number of events read**

### More on wevtutil

More parameters/commands:
- gl: Gets configuration information about a log, e.g. whether it is enabled, maximum size limit, path to file where log is stored
- sl: Modifies configuration of a log
- ep: Displays event publishers
- gp: Displays configuration information about an event publisher
- im: Installs event publishers and logs from a manifest.
- um: Uninstalls event publishers and logs from a manifest.
- gli: Displays status information about an event log or log file.
- epl: Exports events from an event log, log file, or structured query to the specified file.
- al: Archives the specified log file in a self-contained format, which can be read to later regardless of whether or not the publisher is installed.
- cl: Clears events from the specified event log; use /bu to backup cleared events.

Options:
- /f: Formatting output, either XML or text
- /e: Enables or disables a log.
- /i: Sets log isolation mode. Can be system, application, or ucstom. Determines whether or not a log shares a session with other logs in the same class. For instance, system isolation means that the target log can share write permissions with the System log at least.
- /lfn: Defines the log file name.
- /rt: Sets log retention mode. Determines behavior of Event Log service when log reaches its maximum size - does it retain or overwrite existing events?
- /ab: Specifies auto-backup policy. If true, will be backed up automatically upon reaching maximum file size.
- /ms: Sets maximum size of log in bytes.
- /l: Defines level filter of the log - can remove by setting level to 0
- /k: Specifies keywords filter of the log - any valid 64-bit keyword mask.
- /ca: Sets access permission for an event log. Accepts channels as input, which is a whole other can of worms.
- /c: Specifies the path to a configuration file. Cannot specify logname parameter if this is used; the log name is read from the file.
- /ge: Gets metadata information for events that can be raised by this publisher.
- /gm: Displays actual message instead of numeric message ID.
- /sq: Specifies that events should be obtained from structured query.
- /q: Defines Xpath query to filter events that are read or exported.
- /bm: Specifies path to a file that contains bookmark from a previous query.
- /sbm: Specifies path to a file that is used to save a bookmark of this query. Should be an XML file.
- /l: Defines a locale string that is used to print event text in a specific locale. Only available for /f:text.
- /e: Includes a root element when displaying events in XML. A string that you want within the root element.
- /ow: Specifies that the export file should be overwritten.
- /bu: Specifies path to a file where the cleared events will be stored, must include .evtx.
- /r: Runs the command on a remote computer.
- /u: Specifies a different user to log onto a remote computer, only applicable if /r is used.
- /p: Specifies a password for the user. Only applicable if /u is used; if omitted, user is prompted to enter password.
- /a: Defines authentication type for connecting to a remote computer - default, negotiate, Kerberos, NTLM.
- /uni: Displays output in Unicode.

## [Task 4] Get-WinEvent

The PowerShell cmdlet. It gets information on event logs and event tracing log files on local and remote computers, and one can combine numerous events from many sources into a single command and filter with XPath queries, structured XML, and hash table queries. This replaces the Get-EventLog cmdlet.

Example 1 - Get all logs from a comptuer: `Get-WinEvent -ListLog *`. Obtains all event logs locally, starting with classic logs and going up to new Windows Event logs. The Record Count may be zero or null.

Example 2 - Get event log providers and log names: `Get-WinEvent -ListProvider *`. Results in the event log providers and their associated logs getting spit out at you. The Name is the provider, and the LogLinks is the log that is written to.

Example 3 - Log filtering: `Get-WinEvent -LogName Application | Where-Object { $_.ProviderName -Match 'WLMS' }`. This allows us to filter by the provider name WLMS.

With larger event logs, it's inefficient to send objects down the pipeline to a Where-Object command. It's better to use the FilterHashtable parameter in this case. We can achieve the same results as above by running the following command:

```
Get-WinEvent -FilterHashtable @{
  LogName='Application'
  ProviderName='WLMS'
}
```

A hash table looks like this: `@{ <name> = <value>; [<name> = <value>] ...}`. Guidelines for creating a hash table are...
- Begin the hash table with an @ sign.
- Enclose it in braces, {}.
- Enter one or more key-value pairs for the content of the hash table. If entering many key-value pairs, you don't need to use semicolons if you're entering each pair on a new line.
- Use an equal sign to separate each key from its value.

Microsoft suggests you make the hash table one key-value pair at a time. Event Viewer can help give information on what you need to build a hash table. If I wanted to get events relating to MsiInstaller, event ID 11707, then...

```
Get-WinEvent -FilterHashtable @{
  LogName='Application'
  ProviderName='MsiInstaller'
  ID=11707
}
```

For the first three questions, we can just run the commands as they are, replacing things as needed.

**[Task 4, Question 1] Execute the command from Example 1 (as is). What are the names of the logs related to OpenSSH? - OpenSSH/Admin,OpenSSH/Operational**

The next commands come from the online documentation for Get-WinEvent. Some of these examples are provided in the supplemental material here for this task. Doing these on the Virtual Machine should return the following information.

**[Task 4, Question 2] Execute the command from Example 8. Instead of the string `*Policy*` search for `*PowerShell*`. What is the name of the 3rd log provider? - Microsoft-Windows-PowerShell-DesiredStateConfiguration-FileDownloadManager**

**[Task 4, Question 3] Execute the command from Example 9. Use Microsoft-Windows-PowerShell as the log provider. How many event IDs are displayed for this event provider? - 192**

The information can be found in the Microsoft documentation, some of which I have included as supplementary material here.

**[Task 4, Question 4] How do you specify the number of events to display? - -MaxEvents**

**[Task 4, Question 5] When using the FilterHashtable parameter and filtering by level, what is the value for Informational? - 4**

### More on Get-WinEvent

Example 4 - Get event logs from a server. `Get-WinEvent -ListLog * -ComputerName localhost | Where-Object { $_.RecordCount }`. ComputerName, localhost, is where the logs are obtained from.

Example 5 - Getting logs from multiple servers.
```PowerShell
$S = 'Server01', 'Server02', 'Server03'
ForEach ($Server in $S) {
  Get-WinEvent -ListLog Application -ComputerName $Server | Select-Object LogMode, MaximumSizeInBytes, RecordCount, LogName, @{name='ComputerName'; expression={$Server}} | Format-Table -AutoSize
}
```

Example 6 - Getting event log providers and log names: `Get-WinEvent -ListProvider *`

Example 7 - Get all event log providers that write to a specific log: `(Get-WinEvent -ListLog Application).ProviderNames`

Example 8 - Get event log provider names that contain a specific string: `Get-WinEvent -ListProvider *Policy*`

Example 9 - Get Event IDs that an event provider generates: `(Get-WinEvent -ListProvider Microsoft-Windows-GroupPolicy).Events | Format-Table Id, Description`

Example 10 - Get log information from event object properties:
```PowerShell
$Event = Get-WinEvent -LogName 'Windows PowerShell'
$Event.Count
$Event | Group-Object -Property Id -NoElement | Sort-Object -Property Count -Descending # counts by ID
$Event | Group-Object -Property LevelDisplayName -NoElement # counts by level
```

Example 11 - Get error events that have a specified st ring in their name: `Get-WinEvent -LogName *PowerShell*, Microsoft-Windows-Kernel-WHEA* | Group-Object -Property LevelDisplayName, LogName -NoElement | Format-Table -AutoSize`

Example 12 - Get events from an archived log: `Get-WinEvent -Path 'C:\Test\Windows Powershell.evtx'`

Example 13 - Get specific number of events from an archived event log: `Get-WinEvent -Path 'C:\Test\PowerShellCore Operational.evtx' -MaxEvents 100`

There's plenty more examples, too. Other parameters include
- Credential: User account that has permission to perform the action
- FilterXml: Specifies a structured XML query that the cmdlet selects events with
- Force: Gets debug anda nalytic logs in addition to other event logs
- MaxEvents: Specifies the maximum number of events that are returned
- Oldest: Gets events in oldest-first order

### Hash Tables

Hashtables are dictionary/associative arrays that store key-value pairs. This might be stuff like... IP addresses and computer names. In PowerShell, each hashtable is a Hashtable. In a hashtable, the order of keys is not determined.

The [ordered] attribute can be used to create an Ordered Dictionary object in PowerShell, where the keys always appear in the order you list them in.

Keys and values in hashtables are .NET objects - they can have any object type. You can create nested hashtables, in which the value of a key is another hashtable.

You can display hashtables by simply typing in the variable name, e.g. $hash. You can also display just the keys ($hash.keys) or the values ($hash.values) as needed. To iterate over keys and values, you can use a foreach block (foreach key in keys), or perhaps you can use ForEach-Object (hash.keys | ForEach-Object). To add keys and values, do `hash[key] = value`. To remove a key-value pair, use `hash.Remove(key)`.

### More on FilterHashtable

Key names include
- LogName
- ProviderName
- Path
- Keywords: These are numbers that PowerShell uses to represent certain strings.
- ID
- Level: Levels are enumerated values. 5 = Verbose, 4 = Informational, 3 = Warning, 2 = Error, 1 = Critical.
- StartTime
- EndTime
- UserID
- Data
- (named-data): This is a named event data field. This might be, say, 'Service' in Perflib event 1008.

## [Task 5] XPath Queries

XPath, or XML Path Language, is a standardized set of syntax and sematnics that can be used to address parts of an XML document and manipulate strings/numbers/booleans. XPath event queries start with * or Event. Event Viewer can help us construct these queries.

![image](https://user-images.githubusercontent.com/56741029/234389493-9c8b000a-50b9-4810-8700-09f182af5e80.png)

Starting from the first tag, you have...
- `<Event xmlns...>` --> `*`
- `<System>` --> `*/System/`
- `<EventID Qualifiers>` --> `*/System/EventID=100`

So, our final command would either be
`Get-WinEvent -LogName Application -FilterXPath '*/System/EventID=100'`, OR
`wevtutil.exe qe Application /q:*/System[EventID=100] /f:text /c:1`

To query for, say, the provider, we want to use the Name attribute of Provider:
`*/System/Provider[@Name="WLMS"]`

To combine two queries, we would use `and`:
`Get-WinEvent -LogName Application -FilterXPath '*/System/EventID=101 and */System/Provider[@Name="WLMS"]'`

Alternatively, we could query for elements in EventData:

![image](https://user-images.githubusercontent.com/56741029/234390962-40c5054b-0448-43d8-9e85-2f1b073733b6.png)

To query for TargetUserName, we'd have
`Get-WinEvent -LogName Security -FilterXPath '*/EventData/Data[@Name="TargetUserName"]="System"'`

For the first question, we need to filter for WLMS events (thus, we'd use the Provider example provided earlier), and we want to filter for a specific System Time. To get the System Time events, we want to query for `*/System/TimeCrated[@SystemTime=...]`.

**[Task 5, Question 1] Using Get-WinEvent and XPath, what is the query to find WLMS events with a System Time of 2020-12-15T01:09:08.940277500Z? - ```Get-WinEvent -LogName Application -FilterXPath '*/System/Provider[@Name="WLMS"] and */System/TimeCreated[@SystemTime="2020-12-15T01:09:08.940277500Z"]'```**

Here, we're looking at EventData. We're specifically looking at the Target User Name, Sam, and a specific Event ID. Combining the work we've done in the examples above, we get this as our query:

**[Task 5, Question 2] Using Get-WinEvent and XPath, what is the query to find a user named Sam with a Logon Event ID of 4720? - ```Get-WinEvent -LogName Security -FilterXPath '*/EventData/Data[@Name="TargetUserName"]="Sam" and */System/EventID=4720'```**

Running the above query on the virtual machine:

![image](https://user-images.githubusercontent.com/56741029/234391621-fab76993-0c96-417c-b4cf-31614262cae1.png)

**[Task 5, Question 3] Based on the previous query, how many results are returned? - 2**

**[Task 5, Question 4] Based on the output from Question #2, what is Message? - A user account was created**

We can change our query from Question #2 to search for Event ID 4724:

![image](https://user-images.githubusercontent.com/56741029/234391858-4e38e053-8495-4c54-a79f-a8d0fc18ebbf.png)

**[Task 5, Question 5] Still working with Sam as the user, what time was Event ID 4724 recorded? (MM/DD/YYYY H:MM:SS [AM/PM]) - 12/17/2020 1:57:14 PM**

We can change the query from Question #2 again so that we're looking for `*/System/Provider`. This gives us the name of the provider at the very top:

![image](https://user-images.githubusercontent.com/56741029/234392154-b1e90500-d6ed-48b7-94f5-ff0351be52f3.png)

**[Task 5, Question 6] What is the Provider Name? - Microsoft-Windows-Security-Auditing**

### More on XPath

There's a whole manual for XPath on Microsoft's website.
https://learn.microsoft.com/en-us/previous-versions/dotnet/netframework-4.0/ms256115(v=vs.100)

## [Task 6] Event IDs

A couple of examples:
- Detecting if a new service is installed - check Event ID 7045 in System
- Monitoring if a firewall rule was detected from the host - check Event ID 2006, 2033 in Windows Firewall with Advanced Security/Firewall
- Monitoring events that correlate with changes to account objects/permissions on system and domain - check Event IDs 4738, 4728, 4670.
- See Links to Resources for more information.

Some events aren't generated by default, and certain features will need to be enabled and configured such as PowerShell logging. For this case, check the Group Policy/Registry: Local Computer Policy --> Computer Configuration --> Administrative Templates --> Windows Components --> Windows PowerShell

### Links to Resources

Windows Logging Cheat Sheet - https://static1.squarespace.com/static/552092d5e4b0661088167e5c/t/580595db9f745688bc7477f6/1476761074992/Windows+Logging+Cheat+Sheet_ver_Oct_2016.pdf
Spotting the Adversary with Windows Event Log Monitoring - https://apps.nsa.gov/iaarchive/library/reports/spotting-the-adversary-with-windows-event-log-monitoring.cfm
MITRE Attack - https://attack.mitre.org/
Events to Monitor - https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/plan/appendix-l--events-to-monitor
Security Auditing and Monitoring Reference - https://www.microsoft.com/en-us/download/confirmation.aspx?id=52630

### More on Logging

It's possible to use Protected Event Logging in Windows 10 and onward, allowing participating applications to encrypt sensitive data written to the event log to be decrypted later on a more secure machine. The public key can be shared widely to multiple machines, while a select few computers should be given the private key. This can be enabled in Group Policy, and if you have the private key, you can decrypt logs with a PowerShell script.

## [Task 7] Putting Theory into Practice

First up, we need to use the resources provided in Task 6 to figure out what event ID we're looking for. In this case, it's event ID 400, thanks to snooping around on MITRE for downgrade attack information.

![image](https://user-images.githubusercontent.com/56741029/234467787-9dedb26d-188d-4bbf-823b-a141617f19eb.png)

**[Task 7, Question 1] What event ID is to detect a PowerShell downgrade attack? - 400**

Filtering this log, we see that the attack appeared to have finished on 12/18/2020 at 7:50:33 AM. We can see this in Event Viewer.

![image](https://user-images.githubusercontent.com/56741029/234468012-7f629ece-0049-455c-b02f-36a343afe5b0.png)

**[Task 7, Question 2] What is the Date and Time this attack took place? - 12/18/2020 7:50:33 AM**

Filtering for Event ID 104 - which is one of the possible log clear events - yields a single event indicating the System log file was cleared. We need to look at the XML View in order to get the Event *Record* ID.

![image](https://user-images.githubusercontent.com/56741029/234471531-ef2f0083-6cd4-4c0d-90a7-c52beb87b934.png)

**[Task 7, Question 3] A Log clear event was recorded. What is the 'Event Record ID'? - 27736**

In the log mentioned above, scrolling down a little bit tells us the computer name:

![image](https://user-images.githubusercontent.com/56741029/234471595-4ed8aff1-0c79-46c1-a26f-e19d1c3ec99f.png)

**[Task 7, Question 4] What is the name of the computer? PC01.example.corp**

Instead of rummaging through Event Viewer for this, we can use Get-WinEvent to get the oldest PowerShell command. We open PowerShell and navigate to the Desktop, and we remember that Event ID 4104 is used to indicate PowerShell script blocks being created. Therefore, we give this as our command:
`Get-WinEvent -Path .\merged.evtx -FilterXPath '*/System/EventID=4104' -Oldest -MaxEvents 1 | Format-List`

Doing so gives us this output, which helps us answer the next few questions!

![image](https://user-images.githubusercontent.com/56741029/234473541-09b99fc7-c7b1-4a6e-9a23-02de89b8c99a.png)

**[Task 7, Question 5] What is the name of the first variable within the PowerShell command? - $Va5w3n8**

**[Task 7, Question 6] What is the Date and Time this attack took place? - 8/25/2020 10:09:28 PM**

Fortunately for us, now that we know exactly what we're looking for, we can just open this stuff up in Event Viewer again and look at the XML View to get the Execution Process ID:

![image](https://user-images.githubusercontent.com/56741029/234473864-3ccbe6ac-2762-4d56-b8b3-a149da091e7b.png)

**[Task 7, Question 7] What is the Execution Process ID? - 6620**

One could look up the Event ID associated with Group Security ID enumeration, which is what I did. Microsoft's official website suggests you filter for Event ID 4799 (which, hey, that's the answer to the last question). Doing so and switching to XML View gives us the Group Security ID that was enumerated -- the TargetSid.

![image](https://user-images.githubusercontent.com/56741029/234475126-bc57e3cb-44ca-4d64-827f-2e2adadd8856.png)

**[Task 7, Question 8] What is the Group Security ID of the group she enumerated? - S-1-5-32-544**

**[Task 7, Question 9] What is the event ID? - 4799**

## Extra Resources: PowerShell and Blue Teaming

Assuming a breach happened, how can PowerShell assist in investigating and dealing with it? As of KB 3000850 for PowerShell v4 and, to a larger extent, PowerShell v5, new features help with investigating these attacks.

Transcription - just getting their commands and the output of those commands - is one of the quickest ways to figure out what's happening in a PowerShell session. `Start-Transcript` was a command already built-in to allow this to happen, but it was very complex and error-prone since it needed to be urn on every system startup, and it wouldn't catch commands done by remoting hosts and non-console sessions.

In the new iteration of PowerShell, the transcript adds new information to the header. The filename of the transcript includes the computer that generated the transcript, a hash breaker to prevent transcript collisions, and increased granularity in the transcript start time. The -OutputDirectory parameter has been added to help with controlling the output path and file name. In the header, Username is the username of the connected user and RunAs User is the account being impersonated, if that is the case. PowerShell transcribes what it can of console commands that manipulate the buffer and can be enabled in non-console sessions, e.g. PowerShell ISE. To more directly associate commands withoutput, -IncludeInvocationHeader can be used which adds an additional header for each command that is invoked. PowerShell transcription includes emulated transcription for hosts that don't have an interface; it shows you what you would have seen. OutputDirectory lets you collect transcripts to a central location for later review. Make sure you prevent users from reading previously-written transcripts.

PowerShell script blocks - the base level of executable cocde - can be logged deeply. When enabling script block logging, PowerShell records the content of all script blocks being processed. If a script block uses dynamic code generation, PowerShell will log the invocation of the generated script block as well to help with detection evasion attempts. This applies to any application that hosts the PowerShell engine. To turn this on, enable Turn on PowerShell Script Block Logging in Group Policy (Windows Components --> Administrative Templates --> Windows PowerShell). Configuration settings are stored under HKLM\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging.

PowerShell only logs sccript blocks the first time they are used. You can change this so PowerShell logs start and stop events for every time a script block is invoked, but this can lead to a huge volume of events being logged. PowerShell also automatically logs script blocks when they have content often used by malicious scripts as a reccord of last report. Script block logging can be disabled via Group Policy or the registry. When script block logging is enabled, Event ID 4104 (Creating Scriptblock text) is logged to Microsoft-Windows-PowerShell/Operational. The text embedded in the message is the text of the script block compiled. The ScriptBlock ID is a GUID retained for the life of the script block. When script block invocation logging is enabled, Event ID 4105 (Started invocation of ScriptBlock) is logged. Protected Event Logging might be used to prevent disclosure of sensitive data. Get-WinEvent may be used to retrieve information about structured ETW properties. PowerShell can break a script into multiple parts if ETW can't hold it all. Event log collection should be set up to prevent a flood of logs by adversaries.

The Antimalware Scan Interface (AMSI) is integrated with PowerShell. All script content is submitted to the antimalware engine, including additional calls for scripts that employ obfuscation or layer dynamic code evaluation.

PowerShell v5 and v4 KB 3000850 introduces support for protection of content using the Cryptographic Message Syntax (CMS) format. This supports encryption and decryption of content using IETF stnadard format for protecting messages as described by RFC 5652: Get-CmsMessage, Protect-CmsMesage, Unprotect-CmsMessage. This implements public key cryptography where keys are used to encrypt (public key) content and decrypt (private key) content. If any content is encrypted with the public key, the private key decrypts it. A unique key usage identifier, EKU, is required by encryption certificates to be recognized in PowerShell. Identifiers can be provided in the following formats:
- Actual certificate, as retrieved from the certificate provider
- Path to a file containing the certificate
- Path to a directory containing the certificate
- Thumbprint of the certificate, used to look in the certificate store
- Subject name of the certificate, used to look in the certificate store

To view document encryption certificates in the certificate provider, use the -DocumentEncryptionCert dynamic parameter for Get-ChildItem (dir).

Since CMS is an IETF standard, PowerShell supports the decryption of content generated by other conforming tools, and the content it generates can be decrypted by other conforming tools. One popular implementation is the OpenSSL library and the command-line toolchain. It's easy to add and remove the text-based headers that work with OpenSSL - it expects that the content is an email message body in the P7M format.

In PowerShell, a common source of code injection vulnerabilities is from attacker-controlled input sent into the Invoke-Expression command. Invoke-Expression should almost always be avoided, since PowerShell has ways to handle situations more securely. If you need to generate PowerShell scripts, then v5 and KB 3000850 introduces APIs to support secure generation of scripts that may contain attacker input. If placing attacker-controlled input within a string, you can put it in a single-quoted string and use EscapeSingleQuotedStringContent to ensure that stuff is escaped safely. This is supported within blcok comments, format strings, and variable names.

There's ways to reduce capabilities of PowerShell to prevent attacks from taking off.
- Antivirus and antimalware: These can limit the execution of malware knwon to the AV industry. Without it, attackers can write and run any code, custom C++ apps, internet tools, and more. The issue is that this can be disabled by administrators, and AV signatures can be evaded by recompiling and modifying an application.
- Applocker in Deny Mode: This can limit the execution of malware knwon to your organization. Attackers can write and run any code, custom C++, applications, and more as long as they aren't using well-known attack tools or exploits otherwise. The issue is that this can be disabled by admins, and this only blocks known evil/undesirable malware and can be bypassed by making slight changes to the app.
- Applocker in Allow Mode: This can prevent the execution of unknown and unapproved applications. Without it, an attacker can write arbitrary custom apps as long as they aren't detected by AV or Applocker Deny rules. This can be disabled by admins, and attackers can still leverage in-box tools such as VBScript, Office macros, HTA apps, local web pages, PowerShell, and more.

In v5, PowerShell reduces its functionality to Constrained Mode for both interactive input and user-authored scripts when it detects that PowerShell scripts have an Allow Mode policy applied to them. The language mode is limited to Constrained Language, first introduced in Windows RT. This is designed to limit the extended language features that can lead to unverifiable code execution, e.g. direct .NET scripting, invocation of APIs via Add-Type, and interaction with COM objects. Scripts allowed by the AppLocker policy are not subject to Constrained Language.

One way to enable this functionality in PowerShell is to indicate that certain files in a folder can be run. If the folder in question has sufficient permissions granted to people, this policy doesn't really offer any protection since users can just place files into that folder and go. Also, if AppLocker doesn't limit executables, this also offers no protection, since an executable may be run to bypass that policy. Note that AppIDSvc needs to be running for AppLocker to do its job. This should be only applied to non-Admin accounts.

Protected Event Logging allows apps to encrypt sensitive data as they write it to the event log. You can decrypt and process these logs once you've moved them to a more secure and centralized log collector. A common technique is to use Windows Event Forwarding, but other options include System Center Operations Manager or commercial SIEM systems. PowerShell is the only app that participates in Protected Event Logging. This is provided via IETF CMS.

## Extra Resources: Tampering with Windows Event Tracing
Whoops. It appears that I've lost what I wrote here because my browser crashed. Sooo... I'm just gonna skip to the tampering methods. (Source - https://blog.palantir.com/tampering-with-windows-event-tracing-background-offense-and-defense-4be7ac62ac63)

If an attacker wants to subvert event logging, ETW provides a stealthy mechanism to affect logging without itself generating an event log trail. Plenty of techniques exist to tamper with ETW - they're either persistent, requiriing a reboot, or they're ephemeral, not requiring a reboot. The methods are, as follows:
- Autologger provider removal (persistent, requiring reboot): Requires Admin permissions and can be detected by noting deletion of HKLM\SYSTEM\CurrentControlSet\Control\WMI\Autologger\AUTOLOGGER_NAME\PROVIDER_GUID. This basically involves the removal of a provider entry from a configured autologger, causing events to cease to flow to the respective trace session.
- Provider "Enable" Property Modification (persistent, requiring reboot): Requires Admin permissions and can be detected by noting deletion of HKLM\SYSTEM\CurrentControlSet\Control\WMI\Autologger\AUTOLOGGER_NAME\PROVIDER_GUID - EnableProperty (REG_DWORD). (You should generally investigate modifications to the PROVIDER_GUID registry key.) This involves alerting the Enable keyword of an autologger session. One could replace EVENT_ENABLE_PROPERTY_ENABLE_KEYWORD_0 with EVENT_ENABLE_PROPERTY_IGNORE_KEYWORD_0, which doens't log events with keyword 0... which is what PowerShell uses.
- ETW Provider Removal from a Trace Session (ephemeral): Requires SYSTEM permissions. No artifacts are associated with this events. A common example is using `logman.exe` to achieve this, but this can be obfuscated with direct use of the Win32 APIs, WMI, DCOM, PowerShell, and so on. This involves removing an ETW provider from a trace session, cutting off its ability to supply a targeted event log with events until a reboot occurs, or until the attacker restores the provider. Defenders might not notice this if they rely on event logs for threat detection. Alternative detection ideas may include checking for Event ID 12 (though you don't get the provider name or GUID modified), or you could craft a permanent WMI event to log all provider removals from a trace session. Generally, try to log execution of `logman.exe`, `wpr.exe`, and PowerShell.
