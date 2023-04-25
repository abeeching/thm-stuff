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

The next commands come from the online documentation for Get-WinEvent. Doing these on the Virtual Machine should return the following information.

**[Task 4, Question 2] Execute the command from Example 8. Instead of the string `*Policy*` search for `*PowerShell*`. What is the name of the 3rd log provider? - Microsoft-Windows-PowerShell-DesiredStateConfiguration-FileDownloadManager**

**[Task 4, Question 3] Execute the command from Example 9. Use Microsoft-Windows-PowerShell as the log provider. How many event IDs are displayed for this event provider? - 192**

**[Task 4, Question 4] How do you specify the number of events to display? - -MaxEvents**

**[Task 4, Question 5] When using the FilterHashtable parameter and filtering by level, what is the value for Informational? - 4**

### More on Get-WinEvent

### Hash Tables

### More on FilterHashtable

## [Task 5] XPath Queries

**[Task 5, Question 1] Using Get-WinEvent and XPath, what is the query to find WLMS events with a System Time of 2020-12-15T01:09:08.940277500Z? - ```Get-WinEvent -LogName Application -FilterXPath '*/System/Provider[@Name="WLMS"] and */System/TimeCreated[@SystemTime="2020-12-15T01:09:08.940277500Z"]'```**

**[Task 5, Question 2] Using Get-WinEvent and XPath, what is the query to find a user named Sam with a Logon Event ID of 4720? - ```Get-WinEvent -LogName Security -FilterXPath '*/EventData/Data[@Name="TargetUserName"]="Sam" and */System/EventID=4720'```**

**[Task 5, Question 3] Based on the previous query, how many results are returned? - 2**

**[Task 5, Question 4] Based on the output from Question #2, what is Message? - A user account was created**

**[Task 5, Question 5] Still working with Sam as the user, what time was Event ID 4724 recorded? (MM/DD/YYYY H:MM:SS [AM/PM]) - 12/17/2020 1:57:14 PM**

**[Task 5, Question 6] What is the Provider Name? - Microsoft-Windows-Security-Auditing**

### More on XPath

## [Task 6] Event IDs

### Links to Resources

## [Task 7] Putting Theory into Practice

**[Task 7, Question 1] What event ID is to detect a PowerShell downgrade attack? - 400**

**[Task 7, Question 2] What is the Date and Time this attack took place? - 12/18/2020 7:50:33 AM**

**[Task 7, Question 3] A Log clear event was recorded. What is the 'Event Record ID'? - 27736**

**[Task 7, Question 4] What is the name of the computer? PC01.example.corp**

**[Task 7, Question 5] What is the name of the first variable within the PowerShell command? - $Va5w3n8**

**[Task 7, Question 6] What is the Date and Time this attack took place? - 8/25/2020 10:09:28 PM**

**[Task 7, Question 7] What is the Execution Process ID? - 6620**

**[Task 7, Question 8] What is the Group Security ID of the group she enumerated? - S-1-5-32-544**

**[Task 7, Question 9] What is the event ID? - 4799**

## Extra Resources: PowerShell and Blue Teaming

## Extra Resources: Tampering with Windows Event Tracing
