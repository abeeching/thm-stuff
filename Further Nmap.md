# Nmap

## [Task 2] Introduction

**[Task 2, Question 1] What networking constructs are used to direct traffic to the right application on a server? - Ports**

**[Task 2, Question 2] How many of these are available on any network-enabled computer? - 65535**

**[task 3, Question 3] (Research) How many of these are considered "well known"? These are the "standard" numbers mentioned in the task. - 1024**

## [Task 3] Nmap Switches

**[Task 3, Question 1] What is the first switch listed in the help menu for a Syn Scan? More on this later. -sS**

**[Task 3, Question 2] Which switch would you use for a UDP scan? -sU**

**[Task 3, Question 3] If you wanted to detect which operating system the target is running on, which switch would you use? -O**

**[Task 3, Question 4] Nmap provides a switch to detect the version of the services running on the target. What is this switch? -sV**

**[Task 3, Question 5] The default output provided by nmap often does not provide enough information for a pentester. How would you increase the verbosity? -v**

**[Task 3, Question 6] Verbosity level one is good, but verbosity level two is better! How would you set the verbosity level to two? (Note: It's highly advisable to always use *at least* this option.) -vv**

**[Task 3, Question 7] What switch would you use to save the nmap results in three major formats? -oA**

**[Task 3, Question 8] What switch would you use to save the nmap resutls in a "normal" format? -oN**

**[Task 3, Question 9] A very useful output format: how would you save results in a "grepable" format? -oG**

Sometimes, the results we get aren't enough. Maybe we don't care about how loud we are, so we might want to enable "aggressive mode" to active service detection, OS detection, traceroute, and common script scanning.

**[Task 3, Question 10] How would you active this setting? -A**

**[Task 3, Question 11] How would you set the timing template to level 5? -T5**

**[Task 3, Question 12] How would you tell nmap to only scan port 80? -p 80**

**[Task 3, Question 13] How would you tell nmap to scan ports 1000-1500? -p 1000-1500**

**[Task 3, Question 14] How would you tell nmap to scan *all* ports? -p-**

**[Task 3, Question 15] How would you activate a script from the nmap scripting library? Lots more on this later. --script**

**[Task 3, Question 16] How would you activate all of the scripts in the "vuln" category? --script=vuln**

## [Task 4] (Scan Types) Overview

## [Task 5] (Scan Types) TCP Connect Scans

**[Task 5, Question 1] Which RFC defines the appropriate behavior for the TCP protocol? - RFC 793**

**[Task 5, Question 2] If a port is closed, which flag should the server send back to indicate this? - RST**

## [Task 6] (Scan Types) SYN Scans

**[Task 6, Question 1] There are two other names for a SYN scan. What are they? - Half-Open, Stealth**

**[Task 6, Question 2] Can Nmap use a SYN scan without Sudo permissions (Y/N)? - N**

## [Task 7] (Scan Types) UDP Scans

**[Task 7, Question 1] If a UDP port doesn't respond to an Nmap scan, what will it be marked as? - open|filtered**

**[Task 7, Question 2] When a UDP port is closed, by convention the target should send back a "port unreachable" message. Which protocol would it use to do so? - ICMP**

## [Task 8] (Scan Types) NULL, FIN, and Xmas

**[Task 8, Question 1] Which of the three shown scan types uses the URG flag? - xmas**

**[Task 8, Question 2] Why are NULL, FIN, and Xmas scans generally used? - Firewall Evasion**

**[Task 8, Question 3] Which common OS may respond to a NULL, FIN, or Xmas scan with a RST for every port? - Microsoft Windows**

## [Task 9] (Scan Types) ICMP Network Scanning

**[Task 9, Question 1] How would you perform a ping sweep on the 176.16.x.x network (Netmask: 255.255.0.0) using Nmap? (CIDR notation) - nmap -sn 172.16.0.0/16**

## [Task 10] (NSE Scripts) Overview

**[Task 10, Question 1] What language are NSE scripts written in? - Lua**

**[Task 10, Question 2] Which category of scripts would be a VERY bad idea to run in a production environment? - intrusive**

### On NSE Usage

## [Task 11] (NSE Scripts) Working with the NSE

**[Task 11, Question 1] What optional argument can the `ftp-anon.nse` script take? - maxlist**

### NSE doc from Nmap

## [Task 12] (NSE Scripts) Searching for Scripts

**[Task 12, Question 1] Search for "smb" scripts in the `/usr/share/nmap/scripts` directory using either of the demonstrated methods. What is the filename of the script which determines the underlying OS of the SMB server? - smb-os-discovery.nse**

**[Task 12, Question 2] Read through this script. What does it depend on? - smb-brute**

## [Task 13] Firewall Evasion

**[Task 13, Question 1] Which simple (and frequently relied upon) protocol is often blocked, requiring the use of the `-Pn` switch? - ICMP**

**[Task 13, Question 2] (Research) Which Nmap switch allows you to append an arbitrary length of random data to the end of packets? --data-length**

### On Switches for Firewall Evasion with Nmap

## [Task 14] Practical

**[Task 14, Question 1] Does the target respond to ICMP (ping) requests (Y/N)? - N**

**[Task 14, Question 2] Perform an Xmas scan on the first 999 ports of the target -- how many open ports are shown to be open or filtered? - 999**

**[Task 14, Question 3] There is a reason given for this -- what is it? (Note: The answer will be in your scan results. You have to think carefully about which switches to use!) - No Response**

**[Task 14, Question 4] Perform a TCP SYN scan on the first 5000 ports of the target -- how many ports are shown to be open? - 5**

**[Task 14, Question 5] Deploy the `ftp-anon` script against the box. Can Nmap login successfully to the FTP server on port 21? (Y/N) - Y**

## Additional Resources
