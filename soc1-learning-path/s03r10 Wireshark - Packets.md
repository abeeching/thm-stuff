# Wireshark: Packet Operations

## [Task 1] Introduction

This is a continuation of the Wireshark Basics room, focusing on how to investigate events of interest at the pacet level. We cover advanced features regarding Wireshark's statistical capabilities, along with filters, operators, and functions.

## [Task 2] Statistics | Summary

The Statistics menu provides many statistics options that can help analysts investigate the traffic scope, available protocols, endpoints, and conversations, along with details specific to certain protocols. The summary information can be useful when trying to develop hypotheses for an investigation. Here's the provided information:
- Resolved Addresses: Identify IP addresses and DNS names available in the capture file, with hostname information being pulled from DNS answers. Can be helpful in identifying accessed resources. Access by going to Statistics -> Resolved Addresses.
- Protocol Hierarchy: Breaks down avaialble protocols and displays them in a tree view based on packet counters and percentages. Helps with understanding usage of ports and services. You can right-click and filter for events of interest. Access by going to Statistics -> Protocol Hierarchy.
- Converastions: Displays traffic between specific endpoints, listed in terms of ethernet (MAC) addresses, IPv4, IPv6, TCP, and UDP. Helpful for identifying all contact endpoints and conversations. Access by going to Statistics -> Conversations.
- Endpoints: Similar to Conversations. Provides unique information for a single information type (e.g. Ethernet/MAC Address, IPv4, IPv6, TCP, UDP). You can identify unique endpoints in the capture file with this. Access by going to Statistics -> Endpoints.
  - If you check the Name resolution button, you can convert the MAC addresses to manufacturer names. This works only if the manufacturers are well-known enough.
  - Wirshark also lets you do IP-geolocation mapping. This is not enabled by default and requires a GeoIP database. Wireshark supports MaxMind databases, and it has a MaxMind database resolver. You still need the database files, though, and you can point to them with Edit -> Preferences -> Name Resolution -> MaxMind database directories.

**[Task 2, Question 1] Investigate the resolved addresses. What is the IP address of the hostname that starts with `bbc`?** - `199.232.24.81`

**[Task 2, Question 2] What is the number of IPv4 conversations?** - 435

**[Task 2, Question 3] How many bytes (k) were transferred from the "Micro-St" MAC address?** - 7474

**[Task 2, Question 4] What is the number of IP addresses linked with "Kansas City"?** - 4

**[Task 2, Question 5] Which IP address is linked with "Blicnet" AS Organization?** - `188.246.82.7`

## [Task 3] Statistics | Protocol Details

As mentioned earlier, you can get information about specific protocol.
- IPv4 and IPv6: You can narrow down the statistics on packets for specific IP versions. Access by going to Statistics -> IPv4 Statistics, or IPv6 Statistics.
- DNS: You can 

**[Task 3, Question 1] What is the most used IPv4 destination address?** - `10.100.1.33`

**[Task 3, Question 2] What is the max service request-response time of the DNS packets?** - `0.467897`

**[Task 3, Question 3] What is the number of HTTP requests accomplished by `rad[.]msn[.]com`?** - 39

## [Task 4] Packet Filtering | Principles

## [Task 5] Packet Filtering | Protocol Filters

**[Task 5, Question 1] What is the number of IP packets?** - 814230

**[Task 5, Question 2] What is the number of packets with a TTL value less than 10?** - 66

**[Task 5, Question 3] What is the number of packets which uses TCP port 4444?** - 632

**[Task 5, Question 4] What is the number of HTTP GET requests sent to port 80?** - 527

**[Task 5, Question 5] What is the number of type A DNS queries?** - 51

## [Task 6] Advanced Filtering

**[Task 6, Question 1] Find all Microsoft IIS servers. What is the number of packets that did not originate from port 80?** - 21

**[Task 6, Question 2] Find all Microsoft IIS servers. What is the number of packets that have version 7.5?** - 71

**[Task 6, Question 3] What is the total number of packets that use ports 3333, 4444, or 9999?** - 2235

**[Task 6, Question 4] What is the number of packets with even TTL numbers?** - 77289

**[Task 6, Question 5] Change the profile to "Checksum Control." What is the number of Bad TCP Checksum packets?** - 34185

**[Task 6, Question 6] Use the existing filtering button to filter the traffic. What is the number of displayed packets?** - 261

## [Task 7] Conclusion
