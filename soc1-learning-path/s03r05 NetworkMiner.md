# NetworkMiner

## [Task 1] Introduction

In brief, NetworkMiner is an open-source traffic sniffer, pcap handler, and protocol analyzer - specifically, it can be used for network forensic analysis.

It can detect information without putting any traffic on the network, and it can parse PCAP files for offline analysis and to regenerate transmitted files and certificates from PCAPs.

An understanding of basic network concepts (ports, protocols, traffic data) as well as Linux functionality is expected for this room. The exercise files can be located on the folder of the VM's desktop.

## [Task 2] Introduction to Network Forensics

Network forensics is focused on network traffic investigation - accessing what information was transmitted, investigating live traffic, and gathering evidence. One of the end goals is to establish the root cause of an event and provide sufficient information to detect malicious activities and other issues.

Typically when you are investigating, you aim to identify the following information. Ideally you collect enough network evidence systematically so you can get answers to the following.
- WHO communicated (source IP, port)
- WHAT was communicated (data, payload)
- WHERE the data was sent to (destination IP, port)
- WHEN the communication happened (time, date0
- HOW it happened

There are plenty of use cases for network forensics, including:
- Discovering things on the network, such as connected devices, rogue hosts, network load.
- Reassembling packets to investigate the traffic flow, particularly unencrypted flows.
- Detecting possible data leakage by examining packet transfer rates.
- Detecting anomalous and malicious activities by examining used ports, source/destination addresses, and data. You'd use this to correlate indicators and hypotheses.
- Assessing policy and regulation compliance by reviewing overall network behavior.

And there are also plenty of advantages of network forensics:
- Network traffic is easier to acquire than other types of evidence, such as logs and IoCs.
- It's easy to collect data and evidence without creating a whole bunch of noise. Sniffing doesn't generate a lot of noise, logs, and alerts, typically, and it's easier to work with network traffic than unfiltered EDR and log data.
- It's harder to destroy network evidence - you would literally have to remove the traffic itself, which is quite difficult. The best an attacker can do is just encrypt, tunnel, and manipulate packets.
- Network logs can be made readily available, thus supporting the investigation process.
- It's possible to gather evidence of memory and non-residential malicious activities, particularly if any network communications are involved.

But network forensics comes with its own challenges, too:
- You need to figure out what you want to do - that is, are you looking for threats? Doing work for an SOC? Performing incident response? This will guide how you conduct network forensics.
- Collecting a sufficient amount of data - It's easy to _acquire_ network traffic, but paradoxically this makes it tricky to capture sufficient evidence.
- Short data capture: Capturing all network activity isn't typically applicable or viable, but ideally you'd like to have captures that cover the timeframe before, during, and after an event.
- Unavailability of full packet captures on suspicious events: Capturing takes time and resources, so it's possible that there may be gaps between captures resulting in things being missed. NetFlow captures may be used in lieu of packet captures for better performance, though these don't provide payload/data details.
- Traffic can be encrypted, and in some cases, discovering the contents of encrypted data may well be impossible. However, you can still make inferences from things like the involvevd hosts and services.
- Compliance with regulations: Capturing a bunch of network traffic may raise some eyebrows in the eyes of GDPR, HIPAA, PCI DSS, FISMA, and more.
- Nonstandard port usage: Attackers may use nonstandard ports to make it trickier for forensic investigators to find low-hanging fruit, e.g. ports used for enumeration and exploitation, services, etc.
- Time zone issues: A common time zone is needed when doing forensic investigations, since you'll want to create a timeline to help keep track of what happened and to correlate events.
- Lack of logs: You will need logs from other sources to properly correlate events, such as logs on endpoint devices. Attackers often target these, erasing them or otherwise tampering with them.

Sources of network forensics evidence include TAPS, inline devices, span ports, hubs, switches, routers, DHCP servers, name servers, authentication servers, firewalls, web proxies, log servers, and logs themselves.

There are a few reasons why you'd want to do network forensics in the first place:
- If you're in a security operations center 9SOC), you may want to be on the lookout for system performance and health, user behavior, and security issues daily.
- If you're performing incident handing/response and threat hunting, you may be interested in understanding why the incident was able to happen, as well as anything in the packets or data that may indicate an incident has occurred.

We can capture a few different types of data in network forensics - live traffic, traffic captures (full packet captures and network flows), and log files. NetworkMiner handles the former two types of data.

NetworkMiner is predominantly focused on the overall flow or condition of a limited amount of traffic - it may not be as helpful for in-depth, live traffic investigations as other tools are, such as Wireshark.

## [Task 3] What is NetworkMiner?

In brief, NetworkMiner has the following capabilities:
- Traffic sniffing: Intercepting, sniffing, collecting, and logging packets that pass through the network
- Parsing pcap files: Parsing and showing the contents of packet files in detail
- Protocol analysis: Identifying used protocols from a parsed pcap file
- OS fingerprinting: Identifying the used OS by reading the pcap file, with the help of some tools such as Satori and p0f
- File extraction: Extracting images, HTML files, emails from a parsed pcap
- Credential grabbing: Extracting credentials from a parsed pcap
- Cleartext keyword parsing: Extracting keywords and strings from a parsed pcap file

There is a professional edition with more features, but the VM only comes equipped with the free verison of NetworkMiner.

There are two modes of operation for NetworkMiner:
1. Sniffer mode: Only available on Windows. NetworkMiner isn't primarily designed as a sniffer tool, but it can be used like one if the need arises.
2. Packet parsing/processing: Available on all platforms - NetworKMiner can parse captures to provide a quick overview and information on the investigated capture. Used to catch the low-hanging fruit.

The disadvantages of using NetworkMiner are that it isn't useful in active sniffing or large PCAP investigations, and there are limited filtering options. It's also not built for manual traffic investigation, but if you need to get the low-hanging fruit quickly, this is a good place to start.

NetworkMiner and Wireshark may be similar in many ways, but there are a few key differences:
- NetworkMiner's purpose is to proide a quick overview of the traffic, providing some quick mapping and data extraction. Wireshark is meant for in-depth analysis of traffic.
- NetworkMiner provides OS fingerprinting capabilities, but Wireshark doesn't.
- NetworkMiner automatically discovers parameters and keywords, and while Wireshark can do so, it can only do so manually.
- NetworkMiner has limited filtering options, whereas Wireshark has more options in this department.
- NetworkMiner also has limited packet decoding options, whereas Wireshark has more capabilities in this department.
- NetworkMiner doesn't do payload or statistical analysis all that well when compared with Wireshark.
- NetworkMiner provides host categorization capabilities whereas Wireshark does not.

## [Task 4] Tool Overview 1

When you start NetworkMiner, you are greeted with the landing page. At the top, there are a few menu options.
- The File menu lets you load a pcap file, or you can receive a pcap file over IP. You can also just drag a pcap file into NetworkMiner and open it that way too.
- The Tools menu allows you to clear the dashboard and delete captured data.
- The Help menu lets you check for updates and get information about the current version of the software.
- The Case panel displays a list of the investigated pcap files, and you can reload files, view metadata, and remove loaded files from this panel.
- The Hosts tab displays identified hosts in the pcap file, providing IP/MAC address info, OS type, open ports, sent and received pa ckets, incoming/outgoing sessions, and other host details. The Sort menu allows you to sort the identified hosts, and you can change the color of the hosts as well. Some features, like OSINT lookup, are available only in premium.
- The Sessions tab displays detected sessions in the pcap file, including info on frame numbers, client/server addresses, source/destination ports, protocols, and start times. You can look for keywords inside of frames with the filtering bar, and you can filter specific columns as well.
- The DNS tab displays DNS queries with details such as frame numbers, timestamps, clients/servers, source/destination ports, IP TTLs, DNS times, transaction IDs and types, DNS queries and answers, and Alexa Top 1M info (only available in premium).
- The Credentials tab displays extracted credentials and password hashes which you can then decrypt/crack with tools like Hashcat and John the Ripper. These include Kerberos and NTLM hashes, RDP and HTTP cookies, HTTP requests, IMAP, FTP, SMTP, and MS SQL credentials...

To figure this out, open `mx-3.pcap` in NetworkMiner and right-click it in the Case Panel. Click on "Show Metadata", and you'll find the information there. You may need to widen the columns.

![image](https://github.com/user-attachments/assets/f1875f63-697f-4851-a1e3-4efaf5400f30)

**[Task 4, Question 1] What is the total number of frames?** - 460

You can check the Hosts tab and sort the hosts by MAC address (ascending). The final two hosts/IPs will have the same MAC address.

![image](https://github.com/user-attachments/assets/ebb9f52d-f5d0-4f0c-a379-62bdd792d409)

**[Task 4, Question 2] How many IP addresses use the same MAC address with host `145.253.2.203`?** - 2

You can also find this in the Hosts tab - select the IP in question, and you'll see the info in the `Sent:` row.

![image](https://github.com/user-attachments/assets/fd7c3562-99b9-44ab-8c82-66138c4d5d11)

**[Task 4, Question 3] How many packets were sent from host `65.208.228.223`?** - 72

And we can get this if we expand the "Host Details" row on the Hosts tab.

![image](https://github.com/user-attachments/assets/5c1da690-b58b-4520-ad16-818b1fca6323)

**[Task 4, Question 4] What is the name of the webserver banner under host `65.208.228.223`?** - Apache

We can extract the information from both questions by loading in the `mx-4.pcap` file and investigating the Credentials tab. You can right click the relevant rows/fields to copy the usernames and passwords.

For the password, they are simply concerned with the hash.

![image](https://github.com/user-attachments/assets/2141a351-1240-44b3-9643-2d80956f1f24)

**[Task 4, Question 5] What is the extracted username?** - `#B\Administrator`

**[Task 4, Question 6] What is the extracted password?** -  `$NETNTLMv2$#B$136B077D942D9A63$FBFF3C253926907AAAAD670A9037F2A5$01010000000000000094D71AE38CD60170A8D571127AE49E00000000020004003300420001001E003000310035003600360053002D00570049004E00310036002D004900520004001E0074006800720065006500620065006500730063006F002E0063006F006D0003003E003000310035003600360073002D00770069006E00310036002D00690072002E0074006800720065006500620065006500730063006F002E0063006F006D0005001E0074006800720065006500620065006500730063006F002E0063006F006D00070008000094D71AE38CD601060004000200000008003000300000000000000000000000003000009050B30CECBEBD73F501D6A2B88286851A6E84DDFAE1211D512A6A5A72594D340A001000000000000000000000000000000000000900220063006900660073002F003100370032002E00310036002E00360036002E0033003600000000000000000000000000`

### Collecting a pcap over IP

The process of getting pcaps over IP is simple - instead of reading a pcap file from the filesystem, NetworkMiner will read a pcap file from a TCP socket. For instance, if you run `cat (PCAP) | nc (NETWORKMINER HOST IP) (PCAP-OVER-IP PORT)`, NetworkMiner will receive the pcap file, save it to disk, and parsedisplay the contents of these packets.

You can use this to do live network sniffing with more reliable tools without dropping packets, and if needed, you can capture traffic from a remote PC, device, or network. You can even receive multiple pcap streams at once.

NetworkMiner also allows you to encrypt pcap files over SSL/TLS (i.e., you can send over the file via a tool like Socat, which can handle this kind of encryption).

You should avoid sniffing the pcap-over-ip traffic by either using different interfaces or, if you're using the same interface to sniff traffic and send it over IP, by excluding the port number used for the pcap-over-IP transfer with BFP (`not PORT`).

## [Task 5] Tool Overview 2

The Files tab shows extracted files from the pcap, as well as frame numbers, filenames, extensions, sizes, source/destination addresses, source/destination ports, protocols, timestamps, reconstructed paths, and details. Some tools like OSINT lookups and sample submission are only avilable in premium mode.

The Images tab lets you recover extracted images from investigated pcaps. If you hover over an image you can get a file's detailed information, including the source and destination address and file path.

The Parameters tab shows extracted parameters from investigated pcaps, including parameter names, values, frame numbers, source/destination hosts and ports, timestamps, and details.

The Keywords tab shows extracted keywords from investigated pcaps, including frame numbers, timestamps, keywords, context, source/destination hosts and source/destination ports. You can filter keywords by adding new keywords and then reloading case files.

The Messages tab shows extracted emails, chats, and messages from investigated pcaps, including frame numbers, source/destination hosts, protocols, sender information, receiver information, timestamps, and sizes. You can also get more information on attachments and attributes for a given message.

The Anomalies tab displays detected anomalies in the processed pcap. This is kind of the functionality that an IDS would provide, but NetworkMiner isn't designed as one and is thus extremely limited in this regard.

For the first question involving `mx-7.pcap`, we can investigate the Files tab and filter by keyword `63075`. The OS is mentioned in the source host.

![image](https://github.com/user-attachments/assets/e2b9192a-5217-49dc-b472-3d1eedc22d89)

**[Task 5, Question 1] What is the name of the Linux distro mentioned in the file associated with frame 63075?** - CentOS

You can also search for the frame number `75942` in the Files tab. Right-click the `index.html` file and click `Open file`, and you'll see the webpage:

![image](https://github.com/user-attachments/assets/b0f3b57f-4f40-4865-8c4b-c36327f0cc3c)

**[Task 5, Question 2] What is the header of the page associated with frame 75942?** - Password-Ned AB

You can search for the file in the Files tab, right click the result, and click on File Details to see the source IP address. You could also look for it in the Images tab.

![image](https://github.com/user-attachments/assets/3a4a5b2a-02d0-4732-809c-42d796ecf232)

**[Task 5, Question 3] What is the source address of the image "ads.bmp.2E5F0FD9.bmp"?** - `80.239.178.187`

Check the Anomalies tab. Apparently there are two potential TLS anomalies, but the one we're interested in is `36255`.

![image](https://github.com/user-attachments/assets/1f5cbc20-8512-4f4d-824a-2e1fbecebabd)

**[Task 5, Question 4] What is the frame number of the possible TLS anomaly?** - 36255

The next two questions require us to investigate the Messages tab.

I couldn't find a precise password reset email, but looking through the emails, we see a lot of communications over a mailing list and an email from Facebook, so presumably that's where the password reset comes from:

![image](https://github.com/user-attachments/assets/a7f614c5-db84-4fee-a7b2-90193737aeaf)

**[Task 5, Question 5] Look at the messages. Which platform sent a password reset email?** - Facebook

If we investigate more emails, we see the email of the required user:

![image](https://github.com/user-attachments/assets/d07f61a9-0cbf-46a5-907b-48d22ea72b78)

**[Task 5, Question 6] What is the email address of Branson Matheson?** - `branson@sandsite.org`

## [Task 6] Version Differences

The VM in this room contains two versions of NetworkMiner: v1.6 and v2.7. The differences as noted in this room are:
- MAC address processing: v2.7 is capable of processing MAC address-specific correlation, which may be helpful in identifying MAC address conflicts.
- Sent/Received packet processing: v1.6 is capable of investigating sent/received packets in a more detailed fashion than in later versions.
- Frame processing: v1.6 is capable of handling frames, providing the nubmer of frames and essential details about the frames.
- Parameter processing: v2.7 can handle more parameters than v1.6 can.
- Cleartext processing: v1.6 can handle cleartext data in traffic.

**[Task 6, Question 1] Which version can detect duplicate MAC addresses?** - 2.7

**[Task 6, Question 2] Which version can handle frames?** - 1.6

**[Task 6, Question 3] Which version can provide more details on packet details?** - 1.6

## [Task 7] Exercises

For the first few questions, I opened `case1.pcap` in NetworkMiner 2.7.

And now we put what we've learned into practice. We can find the information for this question in the Hosts tab - select the IP, and then expand the OS: Windows row.

![image](https://github.com/user-attachments/assets/e15b2cdf-88f0-47e1-a2c0-448b4f22ce28)

**[Task 7, Question 1] What is the OS name of the host `131.151.37.122`? - Windows - Windows NT 4

For this question, in the same host, expand the Incoming sessions row followed by the "Server 131.151.37.122 (Windows) TCP 1065 row. Reading the information in this row, we see that 192 bytes were sent.

![image](https://github.com/user-attachments/assets/cbd4b4eb-e500-47bb-81ef-79a5f0aa9728)

**[Task 7, Question 2] How many data bytes were received from host `131.151.32.91` to host `131.151.37.122` through port 1065?** - 192

And we do a very similar thing to the previous question, just for the other Incoming session:

![image](https://github.com/user-attachments/assets/e4f8a212-fc07-4175-877b-6a2b1bfbefda)

**[Task 7, Question 3] How many data bytes were received from host `131.151.37.122` to host `131.151.32.21` through port 143?** - 20769

Now we'll need to make use of NetworkMiner 1.6. Open the pcap there, then go to the Frames tab and investigate the TCP information in Frame 9.

![image](https://github.com/user-attachments/assets/cad1b2ea-bbbc-4b00-9182-846096a9ccb1)

**[Task 7, Question 4] What is the sequence number of frame 9?** - 2AD77400

In NetworkMapper (2.7) you can look in the Parameters tab. I sorted by Parameter name and investigated the Content-Type entries. There are only two distinct entries here:

![image](https://github.com/user-attachments/assets/80c21de0-7007-4be1-b634-727ea11b9cbe)

**[Task 7, Question 5] What is the number of the detected "content types"?** - 2

Now we investigate `case2.pcap`. For this first question you can examine the Files and Images tabs to get an idea of what the person was looking up. You see a lot of things relating to ASIX in the images, along with a USB drive, which suggests that they might've been looking into ASIX for USB products.

![image](https://github.com/user-attachments/assets/a90dc273-a5e4-4fef-bccb-82727e7fa373)

**[Task 7, Question 6] What is the USB product's brand name?** - ASIX

If you dig around in the images long enough, you'll see a reference to `Lumia 535`:

![image](https://github.com/user-attachments/assets/969bd45d-2269-4e91-a7bd-c746deab6cad)

**[Task 7, Question 7] What is the name of the phone model?** - Lumia 535

We can look for fish images in the Files tab, and we see one result from the IP given below:

![image](https://github.com/user-attachments/assets/d8f175d8-bbff-4c16-a862-e7f4f32134b0)

**[Task 7, Question 8] What is the source IP of the fish image?** - `50.22.95.9`

You can check the Credentials tab and uncheck the Show Cookies button so you only get credentials. It's pretty easy to find!

![image](https://github.com/user-attachments/assets/7f6f7aab-0d3e-41c5-9c87-687efa784479)

**[Task 7, Question 9] What is the password of the "`homer.pwned.se@gmx.com`"? - `spring2015`

We can just check the DNS tab and look for frame number `62001`. The DNS query is...

![image](https://github.com/user-attachments/assets/63e9cb21-a34e-4b71-9b00-00aa6e111287)

**[Task 7, Question 10] What is the DNS Query of frame 62001?** - `pop.gmx.com`
