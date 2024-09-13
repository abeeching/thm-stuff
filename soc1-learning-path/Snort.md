# Snort (introduction)

## [Task 1] Introduction

## [Task 2] Interactive Material and VM

**[Task 2, Question 1] Navigate to the Task-Exercises folder and run the command "./.easy.sh" and write the output** - Too Easy!

## [Task 3] Introduction to IDS/IPS

**[Task 3, Question 1] Which snort mode can help you stop the threats on a local machine?** - HIPS

**[Task 3, Question 2] Whcih snort mode can help you detect threats on a local network?** - NIDS

**[Task 3, Question 3] Which snort mode can help you detect the threats on a local machine?** - HIDS

**[Task 3, Question 4] Which snort mode can help you stop the threats on a local network?** - NIPS

**[Task 3, Question 5] Which snort mode works similar to NIPS mode?** - NBA

**[Task 3, Question 6] According to the official description of snort, what kind of NIPS is it?** - full-blown

**[Task 3, Question 7] NBA training period is also known as...** - baselining

## [Task 4] First Interaction with Snort

**[Task 4, Question 1] Run the Snort instance and check the build number.** - 149

**[Task 4, Question 2] Test the current instance with "/etc/snort/snort.conf" file and check how many rules are loaded with the current build.** - 4151

**[Task 4, Question 3] Test the current instance with "/etc/snort/snortv2.conf" file and check how many rules are loaded with the current build.** - 1

## [Task 5] Operation Mode 1: Sniffer Mode

## [Task 6] Operation Mode 2: Packet Logger Mode

**[Task 6, Question 1] What is the source port used to connect port 53?** - 3009

**[Task 6, Question 2] Read the snort.log file with Snort; what is the IP ID of the 10th packet?** - 49313

**[Task 6, Question 3] Read the "snort.log.1640048004" file with Snort; what is the referer of the 4th packet?** - `http://www.ethereal.com/development.html`

**[Task 6, Question 4] Read the "snort.log.1640048004" file with Snort; what is the Ack number of the 8th packet?** - 0x38AFFFF3

**[Task 6, Question 5] Read the "snort.log.1640048004" file with Snort; what is the number of the "TCP port 80" packets?** - 41

### The Berkeley Packet Filter (BPF)

## [Task 7] Operation Mode 3: IDS/IPS

**[Task 7, Question 1] What is the number of the detected HTTP GET methods?** - 2

## [Task 8] Operation Mode 4: PCAP Investigation

**[Task 8, Question 1] What is the number of the generated alerts?** - 170

**[Task 8, Question 2] Keep reading the output. How many TCP Segments are Queued?** - 18

**[Task 8, Question 3] Keep reading the output. How many "HTTP response headers" were extracted?** - 3

**[Task 8, Question 4] What is the number of the generated alerts?** - 68

**[Task 8, Question 5] What is the number of the generated alerts?** - 340

**[Task 8, Question 6] Keep reading the output. What is the number of the detected TCP packets?** - 82

**[Task 8, Question 7] What is the number of the generated alerts?** - 1020

## [Task 9] Snort Rule Structure

**[Task 9, Question 1] Write a rule to filter IP ID "35369" and run it against the given pcap file. What is the request name of the detected packet?** - TIMESTAMP REQUEST

**[Task 9, Question 2] Create a rule to filter packets with Syn flag and run it against the given pcap file. What is the number of detected packets?** - 1

**[Task 9, Question 3] Write a rule to filter packets with Push-Ack flags and run it against the given pcap file. What is the number of detected packets?** - 216

**[Task 9, Question 4] Create a rule to filter packets with the same source and destination IP and run it against the given pcap file. What is the number of packets that show the same source and destination address?** - 7

**[Task 9, Question 5] Case Example - An analyst modified an existing rule successfully. Which rule option must the analyst change after the implementation?** - rev

## [Task 10] Snort2 Operation Logic: Points to Remember
