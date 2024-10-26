# Networking Essentials

## [Task 1] Introduction

In this room, we'll be diving into how a computer dynamically sets up its network settings when you turn it on and connect to a network, as well as how packets reach their destinations and more about how this IP addressing thing works. An understanding of the OSI and TCP/IP models is assumed, as well as some understanding of Ethernet, IP, and TCP. All of this was covered in the Networking Concepts room that precedes this.

## [Task 2] DHCP: Give Me My Network Settings

When you turn on your computer, you probably didn't need to type any IP addresses or anything to get on the Internet - you just opened up your browser and went straight to where you needed to go. You may be wondering how your computer managed to do this. Generally, when we want to get into a network, we need the following information at minimum:
- The IP address, plus a subnet mask
- Router/gateway information
- DNS server information

When connecting to a new network, the information above must be set. We can manually configure these settings if we wished, and in some settings - such as servers/domain controllers - this is the preferable route. However, in most cases, it's better to automate this process for mobile phones and computers. The big risk in configuring all of these devices manually is introducing _IP address conflicts_, where two devices have the same IP address. The Dynamic Host Configuration Protocol, DHCP, helps with all of this. It's a UDP-based protocol, listening on port 67. Clients send DHCP-related things over UDP port 68. DHCP is the default in phones and computers.

There are four steps in the DHCP configuration process, known as DORA:
1. DHCP Discover: The client broadcasts a message seeking the local DHCP server, if one exists.
2. DHCP Offer: The DHCP server offers an IP address for the client to accept.
3. DHCP Request: The client requests (i.e., indicates that it has accepted) the IP address.
4. DHCP Acknowledge: The DHCP server responds with an acknowledgement that the IP address has been asssigned to the client.

At the first two steps of the DHCP configuration process, the client computer sends DHCP information out of the IP `0.0.0.0` to the broadcast address `255.255.255.255`. When this information needs to be sent out via the Link Layer, the client sends to the broadcast MAC address `FF:FF:FF:FF:FF:FF`. When information needs to be sent back to the client, the DHCP server knows to send it out to the client's MAC address.

Upon completion, the DHCP process provides the leased IP address to access network resources, the gateway to route packets outside the local network, and a DNS server that can be used to resolve domain names (which will be discussed in a little bit).

**[Task 2, Question 1] How many steps does DHCP user to provide network configuration?** - 4

**[Task 2, Question 2] What is the destination IP address that a client uses when it sends a DHCP Discover packet?** - `255.255.255.255`

**[Task 2, Question 3] What is the source IP address that a client uses when trying to get IP network configuration over DHCP?** - `0.0.0.0`

## [Task 3] ARP: Bridging Layer 3 Addressing to Layer 2 Addressing

We know that two hosts communicate over a network, and an IP packet is encapsulated in a data link frame as it travels over Layer 2 - either via Ethernet or Wi-Fi. If a host needs to communicate with another host on the same Ethernet or Wi-Fi network, then it must send the IP packet within a link layer frame. It probably knows the IP address of the target host, but in order for a proper frame to be created, we must know the MAC address. We already know that a MAC address looks something like `7C:DF:A1:D3:8C:5C` - it's a 48-bit/12-octet number. Each two octets are separated by a colon.

The devices on the same Ethernet network don't necessarily need to know each other's MAC addresses at all times - they only need to know this when communication happens. Communication across networks requires knowing certain IP addresses; some of these are set up when DHCP does its magic. However, if two devices on the network need to talk, they can't do much without knowing their MAC address.

A typical Ethernet frame header contains the destination and source MAC addresses, and the type of the packet encapsulated (e.g., IPv4).

The Address Resolution Protocol (ARP) makes it possible to find the MAC address of another device in the network. It does so by sending an ARP Request to the broadcast MAC adress - `FF:FF:FF:FF:FF:FF`. This request basically asks "which MAC address has this IP address associated with it? Tell the device at this IP/MAC address!" An ARP reply is simply the answer to that question sent back to the requesting device. None of the ARP traffic gets encapasulated in any fashion; it'll be encapsulated directly in an Ethernet frame.

ARP is considered a component of Layer 2, since it deals with MAC addresses. You may argue that it's a part of Layer 3 since it supports IP addresses. All we need to know is that ARP acts as the bridge between Layers 3 and 2.

**[Task 3, Question 1] What is the destination MAC address used in an ARP request?** - `FF:FF:FF:FF:FF:FF`

The question refers to example images posted in the room.

**[Task 3, Question 2] In the example above, what is the MAC address of `192.168.66.1`?** - `44:DF:65:D8:FE:6C`

## [Task 4] ICMP: Troubleshooting Networks

The Internet Control Message Protocol, ICMP, is used for network diagnostics and error-reporting. There are two major commands that make use of ICMP:
- `ping (TARGET)`, which uses ICMP to test connectivity to a system and measure the round-trip time (RTT).
- `traceroute (TARGET)` (`tracert` in Windows), which uses ICMP to find the route from the host to the target.

The `ping` command formally sends an ICMP Echo Request (or ICMP Type 8) message within an IP packet. The computer at the receiving end responds with an ICMP Echo Reply (or ICMP Type 0) message. You may not receive a reply for several reasons - the system may well be offline or shut down, though firewalls may be configured to block the packets needed for `ping` to work. There are valid security reasons for doing so.

When issuing the `ping` command, you can add the flag `-c (NUMBER)` to tell the command to stop after sending (NUMBER) packets. Otherwise it runs forever. You'll see whether packets were lost in the output, as well as the minimum, average, maximum, and standard deviation (mdev) RTTs.

The `traceroute` command can be used to make every router between the system and the target system known. Internet Protocol makes use of a Time-to-Live (TTL) field, which indicates the maximum number of routers a packet can travel through before it is dropped. TTL is decremented by one at each router the packet goes to. Once the TTL hits zero, the router drops the packet and sends back an ICMP Time Exceeded (or ICMP Type 11) message.

The output of a `traceroute` command normally includes the routers between the systems, though some routers won't respond with any ICMP messages. Routers that belong to our ISP may respond and reveal their private IP address, and some routers may display their public IP address, allowing us to investigate any relevant domain names and geographic locations. Of course, it's possible to block the ICMP Time Exceeded message as well. The output may change every time you run the command.

**[Task 4, Question 1] Using the example images above, how many bytes were sent in the `echo` (`ping`) request?** - 40

**[Task 4, Question 2] Which IP header field does the `traceroute` command require to become zero?** - TTL

## [Task 5] Routing

Now the key question is "how can we decide what's the best route from the source system to a packet's destination?" There are algorithms out there that help us answer this question; the specifics are out of the scope of this room.
- OSPF, Open Shortest Path First: This lets routers share information about network topology and calculate the most efficient path for data transmission. Routers update each other about their connected links and network, so each router ends up having a complete map of the network and can determine the best routes from there.
- EIGRP, Enhanced Interior Gateway Routing Protocol: This Cisco-proprietary protocol combines aspects of different routing algorithms to let routers share information about reachable networks and costs (bandwidth, delay, etc.) of taking paths through them. Routers use this to choose the best path.
- BGP, Border Gateway Protocol: This is the primary routing protocol used on the Internet, allowing different networks to exchange routing information and establish paths for data to travel between the networks.
- RIP, Routing Information Protocol: This is a simple protocol often used in small networks, in which routers share information about their reachable networks and how many hops/rouers are required to get there. This allows routers to build routing tables based on this information.

**[Task 5, Question 1] Which routing protocol discussed in this task is a Cisco proprietary protocol?** - EIGRP

## [Task 6] NAT

We found earlier that IPv4 can support a maximum of four billion computers. That may seem a lot, but think about how many devices are connected to the Internet nowadays: computers, smartphones, security cameras, and washing machines. Clearly, with all of these, we would run out of IPv4 addresses quickly. So how are we still working with them?

Network Address Translation, NAT, allows us to use one public IP address to provide Internet access to many private IP addresses. Routers that support NAT must track ongoing connections, and so generally they use a table to translate network addresses between internal and external networks. The internal network would use a private IP range, while the external network would use a public IP address.

Web servers, when communicating with a public IP, doesn't know what the internal IP address is - it just sees the public IP. When information is sent back into the internal network, the router is responsible for sending it off to the correct device, e.g. via keeping track of what ports are in use.

**[Task 6, Question 1] In the network diagram above, what is the public IP that the phone will appear to use when accessing the Internet?** - `212.3.4.5`

The answer to the next question relies on the fact that we only have so many TCP port numbers.

**[Task 6, Question 2] Assuming that the router has infinite processing power, approximately speaking, how many thousand simultaneous TCP connections can it maintain?** - 65

## [Task 7] Closing Notes

This room concludes with some questions about these concepts. Recall:
- Basic network settings, such as gateways, DNS settings, and more, are provided by DHCP.
- Other devices' MAC addresses on the network are discovered via ARP communication.
- NAT allows multiple private IP addresses to be set up behind a few public IP addresses.
- ICMP is used in troubleshooting tools, such as ping and traceroute.

**[Task 7, Question 1] Click on the View Site button to access the related site. Please follow the instructions on the site to obtain the flag.** - `THM{computer_is_happy}`
