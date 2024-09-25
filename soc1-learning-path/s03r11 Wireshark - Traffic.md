# Wireshark: Traffic Analysis

## [Task 1] Introduction

In this last Wireshark room, we will cover how to analyze traffic and detect suspicious activities with the tools we've picked up on in the previous two rooms. Our end goal here is to investigate and correlate information from the traffic capture to find anomalies and evidence of malicious activity.

## [Task 2] Nmap Scans

Nmap is a tool used for scanning networks, and can be used to identify live hosts and services that are available on them. Analysts should be familiar with the kind of traffic nmap creates. The most common scan types on Nmap are TCP connect scans, SYN scans, and UDP scans.

You should be familiar with how to look for TCP flags generally:
- To search for TCP or UDP packets in general, simply apply the filters `tcp` or `udp`
- If you're looking only for the SYN flag, use `tcp.flags == 2`. Alternatively, you can use `tcp.flags.syn == 1` -- this will look for whether the SYN flag is enabled, regardless of what's happening with the rest of the flags/bits.
- If you're looking only for the ACK flag, use `tcp.flags == 16`. Alternatively, you can use `tcp.flags.ack == 1`. The latter works similarly to `tcp.flags.syn == 1`.
- If you're looking for the SYN and ACK flags, use `tcp.flags == 18`, or you can use `(tcp.flags.syn == 1) and (tcp.flags.ack == 1)`. The latter command only focuses on the SYN and ACK flags, and not the rest of the bits.
- If you're looking for the RST flag, use `tcp.flags == 4` or `tcp.flags.reset == 1`.
- If you're looking for RST and ACK flags, use `tcp.flags == 20` or `(tcp.flags.reset == 1) and (tcp.flags.ack == 1)`.
- If you're looking for the FIN flag, use `tcp.flags == 1` or `tcp.flags.fin == 1`.

Nmap's TCP connect scan relies on the three-way handshake; that is, it focuses on whether or not it can complete the three-way handshake needed for a TCP connection to determine if a TCP port is open.
- Usually this is done by running `nmap -sT`.
- This does not require root privileges to run -- in fact, it is the only nmap scan that a non-root user can run.
- Usually a window-size larger than 1024 bytes is involved; the request will expect some data because of how TCP works.

The three-way handshake starts by sending a TCP packet with the SYN flag to the other endpoint. The destination endpoint responds with a SYN, ACK packet, and the source endpoint responds with an ACK. If nmap sees that this process is followed, it will determine the TCP port to be open. If at some point the destination endpoint sends an RST, ACK packet, then the TCP port is deemed closed. Note that it's OK for the source endpoint to end off with an RST, ACK packet.

With big capture files, it can be hard to spot these patterns. There are general filters you can apply to spot initial anomaly patterns, and thus focus on specific traffic points. For TCP connect scans, the filter `tcp.flags.syn == 1 and tcp.flags.ack == 0 and tcp.window_size > 1024` will do.

Now for SYN scans. Here, nmap starts the three-way handshake process but simply does not finish it. It is performed with the `nmap -sS` command, and can only be used by privileged users. Since the request isn't supposed to finish, the window size is less than 1024 bytes. If nmap gets a SYN, ACK packet back from the destination endpoint, it sends an RST packet to the destination endpoint, thus ending the handshake early. This means that the port is open. If nmap instead gets an RST, ACK packet back, it knows that the destination endpoint dropped the packet at that port, so the port must be closed.

To detect TCP SYN scans, you may want to use a filter like `tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size <= 1024`.

Lastly, nmap can perform UDP scans. UDP does not require a handshake process at all (there's no need to establish that the other host is actually available and capable of receiving packets), but this comes at the cost of not knowing whether a port is actually open. All the scan tells you is whether or not a port is closed -- this usually results in an ICMP error. These scans are conducted with the `nmap -sU` command.

If nmap sends off a UDP packet and gets no response, it assumes that the port is either open or filtered (and thus wouldn't get a response). If it gets an ICMP Type 3, Code 3 message (i.e. the Destination Unreachable, Port Unreachable) message, then nmap knows that the port is closed over UDP.

The tricky thing with detecting nmap UDP scans is that it can be hard to tell if a given ICMP error is normal in the first place. To deal with this, you should examine the ICMP packet that has the error and look into its data. ICMP error messages use the original request as encapsulated data to show the source or reason of the packet.

To look for UDP scans, consider using a filter like `icmp.type==3 and icmp.code==3`.

For this task, we'll look at `exercise-pcaps/nmap/Exercise.pcapng`. We're looking for the total number of TCP connect scans, so our filter will be `tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size > 1024`. Running this filter yields...

![image](https://github.com/user-attachments/assets/2c4e93b4-10ea-4e19-88eb-89a01fe95c7e)

**[Task 2, Question 1] What is the total number of the "TCP Connect" scans?** - 1000

Now, if we simply filter for `tcp.port==80`, we can see a pattern emerge in the first four packets. We have a SYN packet sent to the recipient, a SYN,ACK packet sent back, and an ACK sent to the recipient afterwards. Finally, a RST,ACK packet is sent from the source. This is indicative of a TCP connect scan:

![image](https://github.com/user-attachments/assets/cd8f8845-93fe-4444-81cd-2ef399d6760e)

**[Task 2, Question 2] Which scan type is used to scan the TCP port 80?** - TCP Connect

Here, we need to find the UDP close port messages. In other words, we're going to apply the filter that can be used to detect nmap UDP scans: `icmp.type == 3 and icmp.code==3`. Doing so gives us this many packets:

![image](https://github.com/user-attachments/assets/a4b4f8da-5d2f-4f16-814a-77da3876e770)

**[Task 2, Question 3] How many "UDP close port" messages are there?** - 1083

For the next question, it may help if we just examine the destination port range in question (nmap is checking to see what ports are open on the target, hence why we need the destination port). One way to do so would be to create a filter like `udp.dstport >= 55 && udp.dstport <= 70`. There's not a lot of packets, so we can go through the list and see what ports the source tried to communicate with. It seems like it only tried to communicate with ports 67-69, and we see two ICMP packets that indicate ports 67 and 69 were closed. So all we have left over is port 68.

![image](https://github.com/user-attachments/assets/0991edee-057b-420a-8e9a-775a5ac1dbbc)

**[Task 2, Question 4] Which UDP port in the 55-70 port range is open?** - 68

## [Task 3] ARP Poisoning and Man in the Middle!

ARP, or the Address Resolution Protocol, is responsible for letting devices identify themselves on a network. It's possible to perform ARP spoofing or poisoning (_or_ a man-in-the-middle attack). Such an attack entails jamming or manipulating the network in such a way that you can manipulate the IP-to-MAC address table and sniff the target host's traffic. This involves sending malicious ARP packets to the default gateway. Since the methodology doesn't change much, it's possible to detect such an attack with Wireshark.

In brief, ARP works on the local network and enables communication between IP addresses. It is insecure and not routable, and it doesn't come with any authentication features. Common ARP usage patterns include request-and-response, announcements, and gratuitous packets. To look for ARP packets in Wireshark, consider using these filters:
- You can search for ARP packets generally if you use `arp`.
- You can look for ARP requests with `arp.opcode == 1`. ARP responses are designated with `arp.opcode == 2`.
- If you're looking for potential ARP scanning, consider using `arp.dst.hw_mac==00:00:00:00:00:00`.
- If you're looking for potential ARP poisoning, consider `arp.duplicate-address-detected or arp.duplicate-address-frame`.
- If you're looking for potential ARP flooding, consider `((arp) && (arp.opcode == 1)) && (arp.src.hw_mac == target-mac-address)`.

Suspicious situations arise when you have two different ARP responses for a given IP address. Wireshark actually alerts on this with the Expert Info feature, however it only alerts on the _second_ occurrence of the duplicate value, thus highlighting the existence of a conflict. Actually determining if the malicious packet is the first or the second occurence is the analyst's job.

Understanding the network's architecture and the timeframe in which traffic has occurred can help here. You should note these things before moving on, since this can be critical for correlating findings later. That said, if you see a bunch of ARP packets being sent off to `ff:ff:ff:ff:ff:ff` you should probably suspect ARP flooding. At best, this is just a network problem.

If you find a suspicious MAC-to-IP address pairing, you could examine the IP address in Wireshark and investigate the nature of the traffic associated with it. It might help to add MAC addresses as columns to help correlate findings. Of course, this isn't the only strategy for investigating issues involving ARP, but this is one way to do it.

Now we move to `exercise-pcaps/arp/Exercise.pcapng`. We're looking for the number of ARP packets _crafted_ by the attacker, so this will involve the source MAC address. However, we don't know what MAC addresses are suspicious yet, so we'll run the filter `arp.duplicate-address-detected or arp.duplicate-address-frame`. This yields multiple potential instances of ARP poisoning originating from `VMware_e2:18:b4`.

![image](https://github.com/user-attachments/assets/7d590024-309f-421a-8fdc-1dd9bb06fd22)

Investigating one of these packets and checking the Ethernet section gives us a potential malicious MAC address, `00:0c:29:e2:18:b4`. We'll search for it with the filter `((arp) && (arp.opcode == 1)) && (arp.src.hw_mac == 00:0c:29:e2:18:b4)`. This yields all the packets that are potentially crafted by the attacker:

![image](https://github.com/user-attachments/assets/02bf608a-9247-4112-a4f3-77be25a43384)

**[Task 3, Question 1] What is the number of ARP requests crafted by the attacker?** - 284

Now we're looking for HTTP packets sent to the attacker. We have the attacker's MAC address, so our filter will be `http && eth.dst == 00:0c:29:e2:18:b4`. This yields...

![image](https://github.com/user-attachments/assets/0f81ee13-8b3f-436e-ba42-6eeda1bce35a)

**[Task 3, Question 2] What is the number of HTTP packets received by the attacker?** - 90

We would like to find sniffed username and password entries. We're assuming that these were sent to the malicious user who performed the ARP poisoning. If we investigate the results from the filter in the previous question, we see a few POST requests to an HTML form. These may be what we're looking for. Investigating one of these packets confirms this:

![image](https://github.com/user-attachments/assets/8f9cca71-ee21-4c35-9599-dbba4babed2b)

This is being posted onto a page with the URI `/userinfo.php`. So, we can adjust our filter from before to be `(http.request.uri contains "userinfo") && (eth.dst==00:0c:29:e2:18:b4)`. Then we can investigate the small number of packets that are returned. Two of these packets don't contain username and password information, so we're left with six packets that contain sniffed usernames and passwords.

**[Task 3, Question 3] What is the number of sniffed username and password entries?** - 6

We can find the password for the user Client986 by simply going through the packets returned by our previous query:

![image](https://github.com/user-attachments/assets/7ae95f72-a843-460b-b44a-27dd34eef6cf)

**[Task 3, Question 4] What is the password of the "Client986"?** - `clientnothere!`

We are looking for a comment posted by Client354, and this user does not appear in our previous queries. It might be worth adjusting our query so that we're looking for POST methods generally, so the filter `(http.request.method == "POST") && (eth.dst == 00:0c:29:e2:18:b4)` would work. Looking through we see something getting posted to `/comment.php`, and wouldn't you know it -- it's client354's comment!

![image](https://github.com/user-attachments/assets/b615737e-4c2f-4f0b-920a-f346823eb334)

**[Task 3, Question 5] What is the comment provided by the "Client354"?** - `Nice work!`

## [Task 4] Identifying Hosts: DHCP, NetBIOS, and Kerberos

Instead of IP and MAC addresses, a good starting point might be to consider relevant hostnames and users. If you can figure out which hosts and users are involved with malicious activity, it becomes easier to drill deeper into the event of concern. In the enterprise environment, folks will use predefined patterns and schemas for naming users and hosts. Thus, it is easy to identify users and hosts, but attackers can also easily blend into the enterprise network. The point is that being able to identify hosts and users is critical, and you can do this with DHCP, NetBIOS (NBNS), and Kerberos.

The Dynamic Host Configuration Protocol, DHCP, is responsible for managing automatic IP address assignment, as well as setting other communication parameters. To investigate DHCP with Wirshark, consider using these filters;
- To look for DHCP packets generally: `dhcp` or `bootp`
- To look for DHCP requests: `dhcp.option.dhcp == 3`. This can contain hostname information.
- To look for DHCP ACKs: `dhcp.option.dhcp == 5`. This contains accepted requests.
- To look for DHCP NAKs: `dhcp.option.dhcp == 6`. This contains denied requests.
- Note that option 53, the "request type" has predefined static values; in this case, you should filter by packet type first, and then filter out the remaining options with the Apply as Column or the `contains` / `matches` filters.
- If you're looking at DHCP requests generally, use `dhcp.option.hostname contains "keyword"`. Specific options of interest include 12 (hostname), 50 (requested IP address), 51 (requested lease time), and 61 (client MAC).
- If you're looking at DHCP ACKs generally, use `dhcp.option.domain_name contains "keyword"`. You may want to consider looking into option 15 (domain name) and option 51 (assigned IP lease time).
- If you're looking at DHCP NAKs generally, you should consider Option 56, which gives the message (typically including rejection details and reasons). The message may be unique according to the case or situation, so you should consider actively reading them instead of just filtering them out.

NetBIOS is responsible for letting applications on different hosts talk to each other. To investigate these packets with Wireshark, you can use the following filters:
- To look for NetBIOS packets generally: `nbns`
- Looking for queries: Consider including details like the name, time-to-live (TTL), and IP address details.
- To look for NetBIOS names: `nbns.name contains "keyword"`

Kerberos is the default authentication service for Microsoft Windows domains, and handles authenticating service requests between computers over an untrusted network. The goal with Kerberos is to prove an identity securely. To investigate such packets with Wireshark:
- To look for Kerberos packets generally: `kerberos`
- To look for user accounts: `kerberos.CNameString contains "keyword"`. Note that some packets may provide hostname information in this field, so you should filter out the `$` value like so: `kerberos.CNameString and !(kerberos.CNameString contains "$")`.
- To get the protocol version of Kerberos, use `kerberos.pvno == 5`. To get the domain name of the generated ticket, use `kerberos.realm contains...`. To get the service/domain name for the generated ticket, you should use `kerberos.SNameString == "krbtg"` or something similar. To look for client IP addresses and NetBIOS names, you'll want to pay attention to request packets.

For the first three questions we will be working in `exercise-pcaps/dhcp-netbios/kerberos/dhcp-netbios.pcap`. Looking for DHCP hostnames is as good of a place to start as any for this next question. The filter `dhcp.option.hostname contains "Galaxy"` returns three packets. Only one of these packets references a Galaxy A30. The Ethernet source is the MAC address that tried to communicate over DHCP.

![image](https://github.com/user-attachments/assets/8eff92d6-b544-4d4d-bb06-3ff662884e2b)

**[Task 4, Quesiton 1] What is the MAC address of the host "Galaxy A30"?** - `9a:81:41:cb:96:6c`

I'm sure there are a few ways to go about this, but I first ran the query `nbns.name contains "LIVALJM"` and investigated some of the packets. One of the packets indicated that a registration request has an opcode of 5, so I adjusted my query to be `nbns.name contains "LIVALJM" and nbns.flags.opcode==5`. This yields...

![image](https://github.com/user-attachments/assets/7bd4e477-3334-45e6-87dc-8e6183dc18c1)

**[Task 4, Question 2] How many NetBIOS registration requests does the "LIVALJM" workstation have?** - 16

A host requesting an IP address is bound to go through DHCP, so we'll want to look for DHCP packets. We know that DHCP option 50 refers to a requested IP address, so we could try to filter with that in mind. Turns out, though, that Wireshark provides filters for DHCP by their name, so `dhcp.option.requested_ip_address == 172.16.13.85` is sufficient for our purposes. Investigating the sole packet that is returned tells us the host that requested this IP:

![image](https://github.com/user-attachments/assets/fdb93483-2800-4fe4-8494-9aabb00e5e3d)

**[Task 4, Question 3] Which host requested the IP address `172.16.13.85`?** - Galaxy-A12

For the last two questions of this task, we'll instead look at the `kerberos.pcap` file. We can find the packets involving the user named `u5` with the query `kerberos.CNameString contains "u5"`. This returns a series of packets, though presumably the `AS-REQ` packets are the only ones that actually originate from the user.

![image](https://github.com/user-attachments/assets/15da314a-88c4-446b-aee2-7519b75167da)

**[Task 4, Question 4] What is the IP address of the user "u5"?** - `10[.]1[.]12[.]2`

As we learned in the task, the CName values that end with `$` are hostnames, so we can run the query `kerberos.CNameString contains "$"`. There's only one packet, and investigating the packet yields our answer:

![image](https://github.com/user-attachments/assets/9c09ac99-357d-455c-82e1-9048da4c23b7)

**[Task 4, Question 5] What is the hostname of the available host in the Kerberos packets?** - `xp1$`

## [Task 5] Tunneling Traffic: DNS and ICMP

When you tunnel traffic (or forward a port), you are transferring data and resources securely to a different network segment or zone. This is useful for transmitting data between private networks and the internet. The data is hidden with encapsulation, so transferred data won't look like it has anything important inside it at a glance. This helps provide anonymity and security, and is often used by enterprise networks, but this is also used by attackers to bypass security standards. All they have to do is hide data in traffic that may necessarily be trusted, e.g. DNS and ICMP.

The Internet Control Message Protocol (ICMP) is helpful for diagnosing and reporting network communication issues, and can be used for error reporting and testing purposes. That said, since folks tend to trust ICMP traffic, it is used by attackers...sometimes for performing denial-of-service, but also for data exfiltration and C2 tunneling.

ICMP tunneling is a problem that crops up once malware is executed or a vulnerability is exploited. Since ICMP packets can carry an additional data payload, attackers will simply put C2 commands and data to be exfiltrated in there. Fortunately, most enterprise networks have caught on, and so custom ICMP packets may be blocked or require administrative privileges. Still, ICMP tunneling is an issue to keep an eye on, so we should know how to spot it in network traffic.

Typically, if we see a lot of ICMP traffic at once, or if we see weird packet sizes, then we can suspect ICMP tunneling has occurred. Adversaries could just create custom packets that match the typical ICMP packet size (64 bytes), and in that case it's a bit more cumbersome to find. In this case, you should be looking out for other anomalies that may suggest C2 communication or data exfiltration, perhaps correlating your findings with ICMP data.

If you want to investigate ICMP, you can use the following Wireshark filters:
- `icmp` for performing a general search for ICMP packets, and
- `data.len > 64 and icmp` for looking for ICMP packets of an anomalous length. It may be worth investigating these packets for encapsulated protocols/data and the destination addresses for these packets.

Domain Name System (DNS) is used to convert domain addresses (e.g. URLs) to IP addresses, and is an essential part of web traffic. Thus, it's typically used and trusted, and it may even be easy to ignore in an investigation. Adversaries can very much use DNS for exfiltration and C2. DNS tunneling, like ICMP tunneling, crops up after malware is executed or a vulnerability is exploited. An adversary can create a domain address and set it up as a C2 channel. Commands to be executed will be sent to this domain via DNS queries.

These DNS queries tend to be longer than your usual DNS queries, and they are typically crafted for subdomain addresses. Further, these subdomains actually contain the encoded commands. When such a query is sent off, the server responds with the actual malicious commands. It's possible to miss these packets, since DNS queries and responses are a typical component to network traffic. Here are some filters you can use in Wireshark to investigate DNS:
- `dns` for generally looking for DNS packets. This may be helpful, in conjunction with the other filters, when it comes to performing statistical analysis on abnormal volumes of DNS requests.
- `dns contains "dnscat"` and similar filters can be used to look for known patterns.
- `dns.qry.name.len > 15 and !mdns` and similar filters can be used to look for query lengths, abnormal names in DNS addresses, long DNS addresses with encoded subdomains, and so on. The `!mdns` bit disables local-link device queries, which we're not interested in.

First, we'll investigate the file `exercise-pcaps/dns-icmp/icmp-tunnel.pcap`. We see a lot of ICMP requests. If we apply the filter `data.len > 64 and icmp`, we can narrow it down to the potentially anomalous ICMP packets. Remember that ICMP packets can contain payload data, so we can start by looking for anything weird in the payloads. It takes a little bit, but once you scroll down far enough, you'll see some interesting strings pop up in the payloads:

![image](https://github.com/user-attachments/assets/d6814707-d2d4-4d39-8a4e-e12e9588bbfe)

A lot of references to OpenSSH and SSH in general.

**[Task 5, Question 1] Investigate the anomalous packets. Which protocol is used in ICMP tunnelling?** - SSH

Now we switch to the `dns.pcap` file. We'll follow a similar strategy as in the previous question: first, we'll filter for the DNS packets that could be potentially anomalous. The filter `dns.qry.name.len > 15 and !mdns` works wonders here. Then we investigate the payload data. In quite a few of these packets, we see an interesting domain address with some weird subdomains. It looks pretty suspicious to me!

![image](https://github.com/user-attachments/assets/4d86fb68-1635-4a61-abd2-3e0e048bea0c)

**[Task 5, Question 2] Investigate the anomalous packets. What is the suspicious main domain address that receives anomalous DNS queries?** - `dataexfil[.]com`

## [Task 6] Cleartext Protocol Analysis: FTP

This and the next task will focus on investigating cleartext protocol traces. On paper, this may seem easy, but it's likely that you'll have to sift through a large network trace for incident analysis and response. You should be able to create and analyze statistics, as well as extracting key results from investigations.

We start with the File Transfer Protocol (FTP), which is used for - well - transferring files. It doesn't have great security, so usage of this protocol may lead to various attacks. This can range from credential stealing and data exfiltration to man-in-the-middle attacks and malware-planting.

Here are a bunch of filters that can be used:
- `ftp` can be used to look for FTP packets generally.
- You should be using `ftp.response.code` to get some low-hanging fruits regarding authentication, connection, and other general messages. Some FTP codes include
  - 200: Command OK
  - 211: System status
  - 213: File status
  - 220: Service is ready
  - 227: Entering passive mode (the client is the one to initiate the connection, as opposed to the server)
  - 228: Long passive mode (passive mode, but for variable-length addresses)
  - 229: Extended passive mode (the client is assumed to connect via the same IP address it was originally connected to. Works with IPv6)
  - 230: User login
  - 231: User logout
  - 331: Valid username
  - 430: Invalid username or password
  - 530: No login, invalid password
- You may also consider looking for specific FTP commands with `ftp.request.command == (CMD)`. This may be USER for username, PASS for password, CWD for current working directory, and LIST for list, among others.
- Similarly, `ftp.request.arg` may be used to find command usage as well.
- The results for `ftp.response.code == 530` may be indicative of bruteforcing, as well as `(ftp.response.code == 530) and (ftp.response.arg contains "username")`
- You may also see evidence of password spraying with the `(ftp.request.command == "PASS") and (ftp.request.arg == "password")` filter.

For this task, we will work in `exercise-pcaps/ftp/ftp.pcap`. If we're just trying to find bad login attempts, the filter `ftp.response.code==530` will do. This gives us...

![image](https://github.com/user-attachments/assets/2650c938-761e-47af-bfe0-8160f70e6d4e)

**[Task 6, Question 1] How many incorrect login attempts are there?** - 737

FTP reports file status information under the response code 213. So we filter for `ftp.response.code==213`, and we happen to find the only two packets in the entire capture that involve someone determining a file's size. Investigating these packets will give us the answer.

![image](https://github.com/user-attachments/assets/968bffa7-12f3-46cd-be64-5dab1950119c)

**[Task 6, Question 2] What is the size of the file accessed by the "ftp" account?** - 39424

There are a few ways to do this, but I decided to look and see if there are any response codes for uploading a file. It turns out that there are -- response code 226 indicates that a file was successfully transferred. I investigated one of these packets by following the TCP stream -- that way, I could see information regarding the file that was transferred. As it would turn out, the file happens to be a document:

![image](https://github.com/user-attachments/assets/60bf049a-ce25-4bbc-b188-3c4a6c93d56e)

**[Task 6, Question 3] The adversary uploaded a document to the FTP server. What is the filename?** - `resume.doc`

Then, still investigating the TCP stream from before, we actually see the command that the adversary attempted to run before getting stopped:

![image](https://github.com/user-attachments/assets/957435c8-bd01-4cbf-b971-cc2398707316)

**[Task 6, Question 4] The adversary tried to assign special flags to change the executing permissions of the uploaded file. What is the command used by the adversary?** - `CHMOD 777`

## [Task 7] Cleartext Protocol Analysis: HTTP

Hypertext Transfer Protocol, or HTTP, is cleartext-based and uses a request-response, client-server protocol. This is a very standard protocol used to serve and retrieve webpages, so it's not often blocked. Being able to perform HTTP analysis is critical for this very reason. Here are some filters that can help:
- `http` can be used for looking for HTTP packets generally. `http2` is similar, but for the HTTP2 revision, which has better performance.
- You may want to use `http.request` and `http.request.method` to look for specific requests, e.g. GET and POST.
- You may want to use `http.response.code` to look for specific responses.
  - 200: OK - request was successful
  - 301: Moved Permanently - resource is located at a new URL or path, permanently
  - 302: Moved Temporarily - resource is located at a new URL or path, temporarily
  - 400: Bad Request - server couldn't understand the request
  - 401: Unauthorized - need to log in
  - 403: Forbidden - client not allowed to access the URL
  - 404: Not Found - server could not find the requested resource
  - 405: Method Not Allowed - the HTTP method is not allowed, or is otherwise unsuitable
  - 408: Request Timeout - server wait time exceeded, request not completed
  - 500: Internal Server Error - request not completed due to some unexpected error
  - 503: Service Unavailable - request could not be completed, service is down
- You can use `http.user_agent` to look for specific user-agent strings, more on this in a minute
- You can use `http.request.uri` to look for requests/access to certain resources
- Alternatively, `http.request.full_uri` can be used to get the complete URI information.
- `http.server` can be used to find server/service names
- `http.host` can be used to get the server's hostname
- `http.connection` can be used to obtain the connection status at a given point, etc. Keep-Alive
- `data-text-lines` can be used to retrieve cleartext data provided by the server. You may also want to use this, and other filters, to try and find web form information

The user-agent string can be incredibly helpful for identifying anomalous traffic. It's possible for adversaries to modify this so it looks legitimate, but these tend to provide traces of certain tools being used, like Nmap. You can do some research online to figure out what a user-agent string correlates to, if you're unsure.
- As a reminder, `http.user_agent` can be used to find these strings.
- Though, you can use a filter like `(http.user_agent contains "sqlmap") or (http.user_agent contains "nmap") or ...` to look for the use of various scanners.
- You should be on the lookout for user-agents that are blatantly custom-made, user-agents with very subtle spelling differences, or even user-agents that contain payload data.

The room provides one more example of HTTP analysis - how it can be used to analyze a log4j attack.
- The attack starts with a POST request, so `http.request.method == "POST"` is a good place to start
- The attack involves known cleartext patterns, including `jndi:ldap` and `Exploit.class`, so filters like `(ip contains "jndi") or (ip contains "Exploit")` will help. You'll also see some JNDI syntax in HTTP user-agent strings, so `(http.user_agent contains "$") or (http.user_agent contains "==")` is good to use too.

Here, we'll investigate `exercise-pcaps/http/user-agent.cap`. For this first question, we're looking for the number of unique anomalous user-agents. This requires a manual investigation, so we'll simply filter `http.user_agent`, and then add the user-agent string as a column in the packet list. Here's what we see:
- Wfuzz
- "Nmap Scripting Engine" (scanner, also misspelled)
- A JNDI:LDAP command (indicative of a log4j exploit attempt)
- "Windows NT 6.4" (does not exist)
- Sqlmap
- One packet is specifically written "Moz*lil*a/5.0", whereas it should be written Moz*ill*a.

Here's the partial output from Wireshark.

![image](https://github.com/user-attachments/assets/437c536f-e285-4f2a-b75e-6429466fa25a)

**[Task 7, Question 1] Investigate the user agents. What is the number of anomalous "user-agent" types?** - 6

The packet in question is the one with "Mozlila" in the user-agent string.

![image](https://github.com/user-attachments/assets/071458c0-9b90-4875-8d6a-d9a023cd4e28)

**[Task 7, Question 2] WHat is the packet number with a subtle spelling difference in the user agent field?** - 52

Now we swtich to `http.pcapng`. We know that the log4j attack starts with a POST request, so we'll simply look for `http.request.method == "POST"`. Since I let the user-agent string be one of the columns in the previous two questions, one of these packets jumped right out at me:

![image](https://github.com/user-attachments/assets/7c0d3059-f29e-47a3-8850-1cea2202a01a)

**[Task 7, Question 3] Locate the "log4j" attack starting phase. What is the packet number?** - 444

We'll have to take the base64 command provided in the user-agent string and decode it. Putting the base64 code into an online decoder yields

![image](https://github.com/user-attachments/assets/414fd5de-5489-4534-8ee4-e71bdbed3752)

**[Task 7, Question 4] Locate the "log4j" attack starting phase and decode the base64 command. What is the IP address contacted by the adversary?** - `62[.]210[.]130[.]250`

## [Task 8] Encrypted Protocol Analysis: Decrypting HTTPS

Nowadays, traffic isn't always left in cleartext - you'll often run into traffic that is actually encrypted. This is courtesy of the HTTP Secure protocol (HTTPS), which provides security against spoofing and sniffing, among other things. The encryption is provided by Transpot Layer Security, or TLS. In order to view the traffic, you need to have the encryption-decryption key pairs.

As always, while this protocol can be used for secure purposes, it can also be leveraged by attackers to transmit sensitive data. Regardless of what's actually in the packet, you won't really know much until you decrypt the traffic.

TLS involves its own handshake process, much like TCP. The client sends a "Client Hello" message, and the server responds with a "Server Hello" message. Looking out for these can help you figure out what IP addresses are involved in a TLS handshake, and thus in an HTTPS communication.

Here are some filters you can use to get some basic, low-hanging fruit regarding HTTPS traffic.
- `http.request`, as mentioned earlier, can be used to list off requests.
- `tls` can be used to look for TLS packets generally.
- `tls.handshake.type == 1` can be used to find TLS client requests.
- `tls.handshake.type == 2` can be used to find TLS server responses.
- `ssdp` can be used to find Local Simple Service Discovery Protocol (SSDP) packets, which help with advertisement and discovery of network services.

Encryption key log files contain unique key pairs that can be used to decrypt encrypted traffic. These are created when a connection is established with an SSL/TLS-supported webpage. This process takes place within the browser, so it's possible to recover the key log files by going into your browser's settings; this way, the browser will dump keys to the file as you browse around. This is particularly important when it comes to traffic capture - if you want to be able to decrypt HTTPS traffic, you should be ready to dump keys.

To add and remove key log files, you will want to go to Edit -> Preferences -> Protocols, and in the dialog box that pops up, scroll down to TLS and select it. The (Pre)-Master-Secret log filename is where you'll point to the key log file. You can also access this by right-clicking Transport Layer Security in the packet details pane, going to Protocol Preferences, and (Pre)-Master-Secret log filename...

Once you add the log, the traffic changes to include HTTP and HTTP2 information. Decrypted information about the frames, TLS/SSL communications, headers, TCP communications, and more should be available.

For this task, we'll investigate `exercise-pcaps/https/Exercise.pcap`. To figure out the frame number of the "Client Hello" message sent to `accounts.google.com`, we should first apply the filter `tls.handshake.type == 1`. This filters only for the Client Hello messages. Then we need to investigate the packets. We can find the hostnames by checking the Transport Layer Security information -> Handshake Protocol: Client Hello -> Extension: server_name -> Server Name Indication extension information in the packet details pane. The sixteenth packet has this:

![image](https://github.com/user-attachments/assets/337421c6-c631-4036-8af1-949cec99eddd)

**[Task 8, Question 1] What is the frame number of the Client Hello message sent to `accounts.google.com`?** - 16

First, we'll need to go to Edit -> Preferences -> Protocols, and select TLS. THen we'll want to click Browse under the (Pre)-Master-Secret log filename, and point it to `/home/ubuntu/Desktop/exercise-pcaps/https/KeysLogFile.txt`.

![image](https://github.com/user-attachments/assets/e6cee402-186c-4303-aaf0-001a0d1fcb59)

When we do this, we should actually be able to see the http/2 packets. To get the number of HTTP2 packets, we simply run the filter `http2`.

![image](https://github.com/user-attachments/assets/01be3e40-f2f3-45e1-9106-ab864bf61ad6)

**[Task 8, Question 2] Decrypt the traffic with the `KeysLogFile.txt` file. What is the number of HTTP2 packets?** - 115

We can go to frame #332 and check the authority headers by going to the Hypertext Transfer Protocol 2 information in the packet details pane and scrolling down a bit. We see the authority here:

![image](https://github.com/user-attachments/assets/56869ea8-bc4a-4394-85a2-d14a1af6df05)

**[Task 8, Question 3] Go to Frame 322. What is the authority header of the HTTP2 packet?** - `safebrowsing[.]googleapis[.]com`

Now that we've decrypted the packets, we are able to interact with them as we would normal HTTP/2 packets. The hint suggests we try exporting objects, so we go to File -> Export Objects -> HTTP. From there, we see a text document that we can extract. Doing so and opening the text file gives us both the flag and some pretty neat ASCII art.

![image](https://github.com/user-attachments/assets/895fc657-769a-40f9-b049-493cf745a0be)

**[Task 8, Question 4] Investigate the decrypted packets and find the flag! What is the flag?** - `FLAG{THM-PACKETMASTER}`

## [Task 9] Bonus: Hunt Cleartext Credentials!

Wireshark provides a convenient way to locate cleartext credential entries: simply go to Tools -> Credentials. This only works in versions 3.1 and later of Wireshark. Wireshark's dissectors for certain protocols (including FTP and HTTP) are programmed to extract cleartext passwords automatically, and they will show up in this menu. You can click on the packet number to jump to the packet with the password, and you can click the username to jump to the packet containing the username information.

For this task, we'll go into `exercise-pcaps/bonus/Bonus-exercise.pcap`. We'll want to find the credentials that are using HTTP Basic Auth. We have one packet in which HTTP Basic Auth is used:

![image](https://github.com/user-attachments/assets/a424a5c2-a931-4f71-863d-5dad6fb7d3da)

**[Task 9, Question 1] What is the packet number of the credentials using "HTTP Basic Auth"?** - 237

From this point, we may investigate the rest of the packets to try to find a packet that submitted an empty password. In other words, we're looking for a packet that ran the command PASS without a request argument. By inspecting the packets, we find our answer:

![image](https://github.com/user-attachments/assets/e15a57d7-2308-4457-a2f6-3ee016dead12)

**[Task 9, Question 2] What is the packet number where "empty password" was submitted?** - 170

## [Task 10] Bonus: Actionable Results!

You may find yourself in a situation where you need to take action on anomalous traffic, and Wireshark is capable of producing firewall rules to deal with that kind of traffic. You can access this feature by going to Tools -> Firewall ACL Rules. In this menu, you'll see a variety of rules designed for different purposes.

Wireshark is capable of creating rules for Netfilter/iptables, Cisco IOS, IP Filter/ipfilter, IPFirewall/ipfw, Packet Filter/pf, and Windows Firewall.

Working in the same pcap as before, we go to packet #99, and then go to Tools -> Firewall ACL Rules. We make sure we switch the format to IPFirewall (ipfw) -- this can be done at the bottom of the dialog box. Then we can just look through the list of rules to get the one we're looking for:

![image](https://github.com/user-attachments/assets/d620857e-3bc9-43f9-be13-c90799542d55)

**[Task 10, Question 1] Select packet number 99. Create a rule for `IPFirewall (ipfw)`. What is the rule for denying the source IPv4 address?** - `add deny ip from 10.121.70.151 to any in`

And lastly, we do the same thing, but with packet #231. However, we must also make sure to uncheck the Deny box at the bottom right of the dialog box. At the very bottom, we have our rule:

![image](https://github.com/user-attachments/assets/6e45d849-130a-45e7-9cb5-93bfe72bd862)

**[Task 10, Question 2] Select packet number 231. Create "IPFirewall" rules. What is the rule for allowing the destination MAC address?** - `add allow MAC 00:d0:59:aa:af:80 any in`
