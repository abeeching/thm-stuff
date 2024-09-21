# Wireshark: The Basics

## [Task 1] Introduction

Wireshark is a packet analyzer tool that allows you to sniff and investigate traffic, as well as investigate packet captures. Understanding fundamental network concepts helps when working through these rooms.

## [Task 2] Tool Overview

There are many reasons to use Wir3eshark:
- Detecting, troubleshooting network problems, e.g. network load failure points, congestion
- Detecting security anomalies, e.g. rogue hosts, abnormal pot usage, suspicious traffic
- Investigating, learning protocol details, e.g. response codes, payload data

Wireshark is not meant to be used as an IDS - it simply allows analysts to discover and investigate packets in depth, and it doesn't modify packets; it just reads them.

Wireshark provides a GUI interface to accomplish these tasks. There's a few points of interest in the GUI:
- Toolbar: The main toolbar, which contains many menus and shortcuts for packet sniffing, processing. This includes filtering, sorting, summarizing, exporting, merging
- Display filter bar: The main query and filtering section
- Recent files: Recently-investigated files; you can revisit them by double-clicking
- Capture filter, interfaces: Capture filters, available sniffing points/network interfaces. An interface is a connection point between a computer and a network, and the software connection (lo, eth0, ens33, etc.) enables networking.
- Status bar: Tool status, profile, numeric packet info

To load pcap files, you can use the File menu, drag-and-drop, or double-click the file if it's already in Wireshark. Once you do, you can see the processed filename, number of packets, and packet details. There are different panes where you can view packet details.
- Packet list pane: Summary of packets (source/destination addresses, protocol, packet info), and you can click on the list to choose a packet for further investigation. Details will appear in other panes.
- Packet details pane: Gives a protocol breakdown for a selected packet
- Packet bytes pane: Hex, decoded ASCII representation of the selected packet. The packet field will be highlighted depending on what section/protocol is clicked in the packet details pane.

Wireshark will also color packets based on different conditions and protocols, which can make it easy to spot anomalies and protocols as needed. You can also create custom color rules to make it easier to spot events of interest. There are two coloring methods in Wireshark:
- Temporary rules: available during a program session only
- Permanent rules: Saved under preference file and available for future sessions

To investigate coloring rules, you can use the right-click menu or click View -> Coloring Rules to create permanent rules. You can also check the Colorize Packet List menu to activate and deactivate coloring rules. To perform temporary packet coloring, use the right-click menu or go to View -> Conversation Filter.

To perform traffic sniffing, you cna click the blue shark button. The red button stops the sniffing, and the green button restarts the sniffing process. The status bar also gives us information on the used sniffing interface and the number of packets collected.

To merge pcap files, you can go to the File -> Merge menu. When you choose the second file, Wireshark will show you the total number of packets in the selected file. You'll need to save the merged pcap file before working on it.

To view file details, such as hashes, capture time, capture file comments, interfaces, and statistics, you can go to Statistics -> Capture File Properties. You can also click the PCAP icon located on the status bar, at the bottom left.

**[Task 2, Question 1] Read the "capture file comments". What is the flag?** - `TryHackMe_Wireshark_Demo`

**[Task 2, Question 2] What is the total number of packets?** - 58620

**[Task 2, Question 3] What is the SHA256 hash value of the capture file?** - `f446de335565fb0b0ee5e5a3266703c778b2f3dfad7efeaeccb2da5641a6d6eb`

## [Task 3] Packet Dissection

To dissect packets, you investigate packet details by decoding protocols and fields. There are many protocols you can analyze with Wireshark, and you can even set up your own scripts. In the documentation, there is information on how to set up your own dissector.

Note that Wirshark uses OSI layers to break down layers for analysis, so an understanding of the OSI layer model is beneficial.

Click on a packet in the packet list pane to open its details - if you double-click you can open the details in a new window. Packets will have 5-7 layers based on the OSI model. Each time you click a detail in the details pane, it will highlight the corresponding part in the packet bytes plane.

In a given packet, you may have up to seven distinct layers:
1. The frame, Layer 1: Shows you what frame/packet you're looking at. Relevant to the OSI Physical Layer.
2. The source (MAC), Layer 2: Shows you source and destination MAC addresses. Relevant to the OSI Data Link Layer.
3. The source (IP), Layer 3: Shows you source and destination IPv4 addresses. Relevant to the OSI Network Layer.
4. The protocol, Layer 4: Shows you details of the transmission protocol used (TCP/UDP). Relevant to OSI Transport Layer.
5. Wireshark may also include specific segments from TCP that needed to be reassembled in a protocol errors section.
6. The application protocol, Layer 5: Shows details relevant to the app protocol. Relevant to OSI Application Layer.
7. Wireshark may also include the application-specific data in an application data section.

**[Task 3, Question 1] View packet number 38. Which markup language is used under the HTTP protocol?** - eXtensible Markup Language

**[Task 3, Question 2] What is the arrival date of the packet?** - 05/13/2004

**[Task 3, Question 3] What is the TTL value?** - 47

**[Task 3, Question 4] What is the TCP payload size?** - 424

**[Task 3, Question 5] What is the e-tag value?** - `9a01a-4696-7e354b00`

## [Task 4] Packet Navigation

Wireshark will calculate the number of investigated packets for you, and it will identify each packet with a unique number, making it easy to work with big captures and to go back to a specific point in time. If you know the number of the packet you're looking for, you can use the Go menu or the toolbar to go directly to that packet.

If you don't know what packet number you're looking for, then you can find packets by their content.

**[Task 4, Question 1] Search the "r4w" string in packet details. What is the name of artist 1?** - `r4w8173`

**[Task 4, Question 2] Go to packet 12 and read the comments. What is the answer?** - `911cd574a42865a956ccde2d04495ebf`

**[Task 4, Question 3] There is a ".txt" file inside the capture file. Find the file and read it; what is the alien's name?** - PACKETMASTER

**[Task 4, Question 4] Look at hte expert info section. What is the number of warnings?** - 1636

## [Task 5] Packet Filtering

**[Task 5, Question 1] Go to packet number 4. Right-click on the "Hypertext Transfer Protocol" and apply it as a filter. Now, look at the filter pane. What is the filter query?** - `http`

**[Task 5, Question 2] What is the number of displayed packets?** - 1089

**[Task 5, Question 3] Go to packet number 33790 and follow the stream. What is the total number of artists?** - 3

**[Task 5, Question 4] What is the name of the second artist?** - Blad3
