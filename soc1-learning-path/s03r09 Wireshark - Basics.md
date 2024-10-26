# Wireshark: The Basics

*Also the 5th room in the 4th section of the Cyber Security 101 path*

## [Task 1] Introduction

Wireshark is a packet analyzer tool that allows you to sniff and investigate traffic, as well as investigate packet captures. Understanding fundamental network concepts helps when working through these rooms. The questions in this task reinforce which files you are working with.

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

Make sure you're looking at `Exercise.pcapng` for the questions in this room. To view the capture file comments, you'll have to examine the Capture File Properties, accessible from the Statistics menu. The bottom textbox contains the capture file comments, and the flag is located at the bottom of the comment:

![image](https://github.com/user-attachments/assets/fae4065c-54dc-47be-8ba9-dd98007b36a3)

**[Task 2, Question 1] Read the "capture file comments". What is the flag?** - `TryHackMe_Wireshark_Demo`

We can see this information in the capture file properties above, though we can also check the Status Bar at the bottom of the screen to see how amny packets are in the file in total.

![image](https://github.com/user-attachments/assets/7d3a17e1-cdab-49b6-8c06-1494fa56df1e)

**[Task 2, Question 2] What is the total number of packets?** - 58620

The SHA256 hash is also given in the Capture File Properties, located near the top -- see the screenshot for the first question.

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

For this task, we'll just scroll down in the pcap file until see Packet #38, and then we'll select it. To see the markup language, all we have to do is look at the Packet Details Pane:

![image](https://github.com/user-attachments/assets/3d114b91-d0d4-4ff2-a6e7-04ab12fc2950)

**[Task 3, Question 1] View packet number 38. Which markup language is used under the HTTP protocol?** - eXtensible Markup Language

To see the arrival date of this packet, we'll want to investigate the Hypertext Transfer Protocol details in the Packet Details Pane. The date is given below:

![image](https://github.com/user-attachments/assets/b08feadd-eff3-44c3-bfe6-c2ccd0492be4)

**[Task 3, Question 2] What is the arrival date of the packet?** - 05/13/2004

TTL, or time-to-live, is the number of times a packet can "hop" through a network. If a router picks up a packet and sees that its TTL is 0, it will simply discard the packet. Since this has everything to do with routing, we'll want to investigate the Network layer information. This can be found in the Internet Protocol Version 4 details:

![image](https://github.com/user-attachments/assets/2f04c996-1ed1-42a9-88cd-ef457f758c2d)

**[Task 3, Question 3] What is the TTL value?** - 47

We'll want to investigate the Transmission Control Protocol information. The information of interest can be found in the "TCP Segment Len" entry:

![image](https://github.com/user-attachments/assets/3c16c4ca-0180-4800-860f-743d64292ee8)

**[Task 3, Question 4] What is the TCP payload size?** - 424

The e-tag is an HTTP-related value, so you'll need to check the Hypertext Transfer Protocol information. See the screenshot for the second question above.

**[Task 3, Question 5] What is the e-tag value?** - `9a01a-4696-7e354b00`

## [Task 4] Packet Navigation

Wireshark will calculate the number of investigated packets for you, and it will identify each packet with a unique number, making it easy to work with big captures and to go back to a specific point in time. If you know the number of the packet you're looking for, you can use the Go menu or the toolbar to go directly to that packet.

If you don't know what packet number you're looking for, then you can find packets by their content via Edit -> Find Packet. This helps with finding specific intrusion patterns, among other things. You can use different types of strings in your search (filter, string, hex, regex), and searches are case-insensitive by default. You can also perform searches in the three different panes.

You can point to specific packets nad mark them by using Edit -> Mark/Unmark Packet(s), or by right-clicking the packets you want to mark and selecting the option there. Marked packets are shown in black regardless of the original color. Marked packets do not save after closing the capture.

You can also add packet comments - that is, comments for particular packets to help further the investigation or point out important and suspicious points for other analysts. These comments stay in the capture file until the operator removes them.

You may export specific packets from a file, which can help if you only want to share the suspicious packets. This can be done by going into File -> Export Selected Packets. You can also extract objects - files - that have been transferred by going into File -> Export Objects. This can only be done with certain protocol streams, e.g. DICOM, HTTP, IMF, SMB, TFTP.

Wireshark provides timestamps for packets, though they are timed in seconds as each packet comes in (timing starts when the capture starts). It may be better to change the time display format via View -> Time Display Format. You can change it to something like UTC format, which is commonly used.

Wireshark will also provide some expert information on certain protocols to help analysts spot possible anomalies and problems. Expert information can provide information in multiple different categories/severities:
- Chat (Blue color): Information about usual workflows, e.g. connection ending or requests
- Note (Cyan color): Notable events, like application error codes
- Warn (Yellow color): Unusual error codes, problem statements, and the like
- Error (Red color): Severe problems, e.g. malformed packets

For expert information, there are different groups that Wireshark provides to categorize information:
- Checksum: Typically used for checksum errors
- Deprecated: Usage of a deprecated protocol
- Comment: Packet comment detection
- Malformed: Malformed packet detection

To access the Expert Information, you can click the bottom-left button (the circle), or you can go into Analyze -> Expert Information.

Since we want to look for a given string, we'll want to use the Edit -> Find Packet feature. From there, we type `r4w` into the search field that pops up (see top right of the image below). Searching for this string will take us to packet #33790, and we can find the artist name if we investigate the packet details (see bottom left).

![image](https://github.com/user-attachments/assets/8412f493-e6dc-4dd4-8cb1-9807cb2932f7)

**[Task 4, Question 1] Search the "r4w" string in packet details. What is the name of artist 1?** - `r4w8173`

Now that we're all the way down at packet #33790, we can go straight to packet #12 via Go -> Go to Packet. Simply type in the number 12 into the field that appears, then click "Go to packet." From there, we right-click the packet and we click on "Packet Comments." We're told that this isn't a flag, and scrolling down gives us a question to answer:

![image](https://github.com/user-attachments/assets/38438cc3-0de2-473d-973a-d88bbd0b0498)

So, we'll do just that. We go to packet #39765 via the Go -> Go to Packet feature again. Then we right-click the JPEG heading in the Packet Details Pane and click Export Packet Bytes. We'll save this somewhere we can easily get to - such as the user's desktop. Then we can get the MD5 hash by checking the image's properties, or by using md5sum:

![image](https://github.com/user-attachments/assets/55c3d949-fd92-41d4-afaa-96bcd1df32b1)

**[Task 4, Question 2] Go to packet 12 and read the comments. What is the answer?** - `911cd574a42865a956ccde2d04495ebf`

To find text files, we can use the `File -> Export Objects` feature. We can specifically look for files transmitted over HTTP. If we sort the object list by filename (that is, clicking the Filename column name a few times), we'll see a text document fly up to the top:

![image](https://github.com/user-attachments/assets/8309bde4-10f7-4dc3-a707-1def840e9724)

Clicking the entry will display the packet in Wireshark, but we can also save the file to our desktop and investigate it there. It might be a little hard to tell, but our alien friend's name is `PACKETMASTER` (by the way, I'm digging the art there).

**[Task 4, Question 3] There is a ".txt" file inside the capture file. Find the file and read it; what is the alien's name?** - PACKETMASTER

Lastly, we'll want to look at Analyze -> Expert Information. We want to see how many warnings there are. There is only one warning, and we can see how many times that warning has been observed in the packet capture:

![image](https://github.com/user-attachments/assets/a809855f-93a6-4426-aec2-096b68c18729)

**[Task 4, Question 4] Look at the expert info section. What is the number of warnings?** - 1636

## [Task 5] Packet Filtering

Wireshark comes with a packet filtering engine, helping analysts narrow down traffic to focus on events of interest. There are two approaches to filtering with Wireshark: using capture filters to capture only certain packets, and using display filters to view only certain packets. We'll focus on display filters for now.

Filters are queries designed for the protocols that Wireshark can work with. You can create queries manually, or you can leverage Wireshark's GUI to get queries for basic tasks -- all you have to do is right-click.

The most basic way to filter traffic is to right-click a given field (or click Analyze at the top) -> Apply as Filter. Wireshark generates the requir3ed filter query, applies it, and shows the relevant packets. You can always see how many packets are being displayed by checking the status bar.

The Apply as Filter method is good for when you want to investigate a certain value in packets. However, if you want to investigate packets that are linked together (i.e., focusing on IP addressees and port numbers), you can use the Conversation Filter, also accessible from the right-click or Analyze menus. This will display all packets that are related in a given conversation.

If you don't want to filter out all other packets, you may want to consider using the Colorize Conversation feature, accessible from the right-click menu or the View menu. This feature highlights all linked packets without applying display filters, and it overrides any applied color rules. To undo this, you can go to View -> Colorize Conversation -> Reset Colorization.

Instead of using Apply as Filter, you may want to use Prepare as Filter. This also lets Wireshark develop queries for you, but it doesn't automatically apply them. It will either wait for the user to execute the query, or it will accept additional filters (e.g. "...and Selected", "...or Selected" ...). This is helpful for when you want to add to a query - that is, when Apply as Filter doesn't exactly do the job.

If you want to add columns to the packet list pane, you can use the right-click menu in the Packet Details Pane or the Analyze menu, and click on Apply as Column. This allows you to add further information to the packet list pane, which can be helpful in examining the appearance of specific values and fields across all available packets. You can enable or disable columns by clicking on the top of the packet list pane.

Wireshark will display everything in terms of packets, but sometimes it may be more beneficial to see the raw traffic, as seen from the Application layer. Maybe you want a better understanding of what happened in a given event, or maybe you want to view unencrypted protocol data. Wireshark lets you do this via the Follow Stream feature. Right-click a given packet or go to the Analyze menu -> Follow - > TCP Stream, UDP Stream, HTTP Stream, etc...

Streams appear in a dialog box of their own. Packets that originate from the server are highlighted in blue, those originating from the client are higlighted in red. Wireshark handles the filters needed to view the specific stream; however, you'll have to remove the filter when you're done investigating the stream.

Heading up to packet #4, we'll want to click it so it appears in the Packet Details Pane, and then we want to right click the Hypertext Transfer Protocol heading. From here, we can click Apply as Filter -> Selected.

![image](https://github.com/user-attachments/assets/3e2fe096-cb05-48a7-abfc-a5936a81f85d)

And from there, we look at the display filter at the top:

![image](https://github.com/user-attachments/assets/8343b99b-4458-499c-a5ef-ff0b914bad4f)

**[Task 5, Question 1] Go to packet number 4. Right-click on the "Hypertext Transfer Protocol" and apply it as a filter. Now, look at the filter pane. What is the filter query?** - `http`

Since we have a display filter applied, the number of packets that will be displayed changes. If we check the Status Bar at the bottom...

![image](https://github.com/user-attachments/assets/da8827a6-5a73-4e5b-8e72-a1da5e449d6d)

This makes sense - we're only seeing the HTTP packets.

**[Task 5, Question 2] What is the number of displayed packets?** - 1089

The packet is an HTTP packet, so we can simply use the Go -> Go to Packet feature to jump to packet #33790. From there, we right-click the packet and click Follow -> HTTP Stream (we found some artist information in an earlier question by checking HTTP data, so we'll check out the HTTP information here, too). From there, we can examine the HTTP stream to see if we can find the number of artists. You can scroll through and try to find the information that way, but we know that the artist in an earlier question was denoted with `artists.php?artist=1`. It may be helpful to look for instances of the string `artist` -- or perhaps even `artist=1`.

![image](https://github.com/user-attachments/assets/e8b430fc-7f93-4bae-92f3-c25b507a6d91)

We see that `artist=1` is highlighted. If we read the surrounding HTTP code, we see that there are only three artists.

**[Task 5, Question 3] Go to packet number 33790 and follow the stream. What is the total number of artists?** - 3

And the second artist's name can be found by reading the code, as well.

**[Task 5, Question 4] What is the name of the second artist?** - Blad3
