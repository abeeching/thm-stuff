# Windows Event Logs

## Purpose of this Room

## [Task 2] Event Viewer

**[Task 2, Question 1] What is the Event ID for the first recorded event? - 40961**

**[Task 2, Question 2] Filter on Event ID 4104. What was the 2nd command executed in the PowerShell session? - whoami**

**[Task 2, Question 3] What is the Task Category for Event ID 4104? - Execute a Remote Command**

**[Task 2, Question 4] Analyze the Windows PowerShell log. What is the Task Category for Event ID 800? - Pipeline Execution Details**

## [Task 3] wevtutil.exe

**[Task 3, Question 1] How many log names are in the machine? - 1071**

**[Task 3, Question 2] What event files would be read when using the query-events command? - event log, log file, structured query**

**[Task 3, Question 3] What option would you use to provide a path to a log file? - /lf:true**

**[Task 3, Question 4] What is the VALUE for /q? - Xpath query**

**[Task 3, Question 5] What is the log name? - Application**

**[Task 3, Question 6] What is the /rd option for? - Event read direction**

**[Task 3, Question 7] What is the /c option for? - Maximum number of events read**

### More on wevtutil

## [Task 4] Get-WinEvent

**[Task 4, Question 1] Execute the command from Example 1 (as is). What are the names of the logs related to OpenSSH? - OpenSSH/Admin,OpenSSH/Operational**

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
