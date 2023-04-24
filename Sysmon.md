# Sysmon

## [Task 2] Sysmon Overview

## [Task 3] Installing and Preparing Sysmon

## [Task 4] Cutting Out the Noise

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
