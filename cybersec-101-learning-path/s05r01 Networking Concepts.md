# Networking Concepts

## [Task 1] Introduction

In this section, we're going to cover some vital networking concepts and important networking protocols. Our goal is to figure out how the major components of the modern Internet work - including how IPs are used to get on the Internet and communicate with other machines. You should have some slight exposure to networking terminology already, e.g. "IP address" and "port." This is covered in the Pre-Security pathway in case you need to get caught up.

## [Task 2] OSI Model

The Internet is often represented in one of two models - we'll first cover the model developed by the International Organization for Standardization (ISO). They came up with the Open Systems Interconnect (OSI) model as a way to explain how network communications work. This is more of a theoretical thing, but folks in IT will speak in these terms, one way or another, so it's good to get familiar with them.

The seven layers of the OSI model are:
1. Physical
2. Data Link
3. Network
4. Transport
5. Session
6. Presentation
7. Application

Folks will often remember this with an mnemonic of some kind - "Please Do Not Throw Spinach Pizza Away" being one of them. There's others, and you should develop your own system for remembering this structure. You should also be aware of the nuemrical order these layers show up in. Folks will prefer to say "Layer 7 Firewall" over, say, "Application-layer firewall." The latter term isn't wrong, per se, but if you don't know what people mean when they give a layer number, you'll have a hard time understanding certain network and security concepts/issues.

Now we'll dive into the layers in more detail. At the Physical Layer (1), the only thing we consider are the physical connections between devices, regardless of whether or not they're wired. This will include cabling (e.g., electrical and optical wires/connections) and wireless connectivity (e.g., radio bands that Wi-Fi runs on, such as the 2.4 GHz, 5 GHz, and 6 GHz bands). Also at this layer, binary digits (bits) are defined and used.

At the Data Link Layer (2), we then consider how two devices (formally, nodes) communicate on the same part of the network. The "part of the network" is known as a network segment, and that's just a group of networked devices using the same medium or channel to transfer information. This might be a series of computers connected to a switch over Ethernet (aka IEEE 802.3) or via Wi-Fi (IEEE 802.11).

Addresses at the link layer are six bytes in length, and we call them Media Access Control (MAC) addresses. MAC addresses are six bytes in length, each byte being expressed in hexadecimal format, e.g. `AA:BB:CC:DD:EE:FF`. A colon separates each two bytes. The first three bytes (`AA:BB:CC`) represent the vendor who built the interface, and the last three (`DD:EE:FF`) represent the unique address of the network interface. In network communications, there should pretty much always be a destination MAC address and a source MAC address.

At the Network Layer (3), we now focus on how to send data between two networks - here, we're just concerned with the mechanics of sending data over, such as logical addressing and routing. The Network layer may be used to connect multiple office buildings together into the same network, and it will define the route that packets take to get from one network to the other. We start dealing with Internet Protocol (IP), Internet Control Message Protocol (ICMP), and Virtual Private Network (VPN) protocols such as IPsec and SSL/TLS here.

At the Transport Layer (4), we focus on how the communication happens between applications on different hosts. This entails flow control, segmentation of incoming data, and error correction during transmission. The two main protocols at this layer are Transmission Control Protocol (TCP) and User Datagram Protocol (UDP).

At the Session Layer (5), we're concerned with maintaining synchronous communication between applications on different hosts. To establish a session we would start a connection between two applications and negotiate the required parameters for that connection to take place. Data synchronization ensures that data is transmitted in the correct order. If it's out of order, then there's a way to recover from that error. At the Session Layer we'd start dealing with the Network File System (NFS) and Remote Procedure Call (RPC), among other things.

At the Presentation Layer (6), we're trying to take incoming data and make sure it's put together in a way that the receiving applicaiton can understand. This involves data encoding (e.g., ASCII and Unicode character encoding), compression, and encryption. Many standards are used here, including file types (such as JPEG, GIF, and PNG), and things like Multipurpose Internet Mail Extensions (MIME) for email.

Finally, at the Application Layer (7), we use the data in end-user applications. This is where all the usual stuff happens on the Internet - you use HTTP to access web pages, for instance. There are other protocols here, such as FTP, DNS, POP3, SMTP, and IMAP.

To summarize, because that's a lot, here's what goes on in the OSI model:
- Layer 7: Application Layer - services and interfaces for apps are used, including HTTP, FTP, DNS, POP3, and more.
- Layer 6: Presentation Layer - data is encoded, encrypted, compressed in preparation for other layers. Includes Unicode, MIME, JPEG, PNG, and more.
- Layer 5: Session Layer - sessions between two applications are created, maintained, and synced. Includes NFS, RPC.
- Layer 4: Transport Layer - data is segmented and end-to-end communications actually happen. Includes UDP and TCP.
- Layer 3: Network Layer - logical routing and addressing is set up here so data can be sent. Includes IP, ICMP, IPsec...
- Layer 2: Data Link Layer - data is transferred between adjacent nodes in the same network. Includes Ethernet, Wi-Fi
- Layer 1: Physical Layer - data is physically transmitted on a wire or other medium. Includes electrical and wireless signals.

**[Task 2, Question 1] Which layer is responsible for connecting one application to another?** - Layer 7

**[Task 2, Question 2] Which layer is responsible for routing packets to the proper network?** - Layer 3

**[Task 2, Question 3] In the OSI model, which layer is responsible for encoding the application data?** - Layer 6

**[Task 2, Question 4] Which layer is responsible for transferring data between hosts on the same network segment?** - Layer 2

## [Task 3] TCP/IP Model

The OSI model is a theoretical view of how networks should work. The TCP/IP model is a more practical, actually-implemented approach, courtesy of the United States Department of Defense (DoD). By treating the network in this kind of model, we can set things up so that portions of the network can continue to function should other portions get disrupted. The reason this works is because certain protocols are able to adapt to changes in the network's topology/setup.

Here's the top-down approach in the TCP/IP model.
- The Application Layer in the TCP/IP model combines the Appliation, Presentation, and Session Layers in the OSI model. Here, it basically encompasses anything that involves getting an application to work and do something on the network.
- The Transport Layer is basically the same as OSI's Layer 4 - its Transport Layer. It serves the same purpose.
- The Internet Layer is OSI's Network Layer, or Layer 3. It serves the same purpose otherwise.
- The Link Layer is OSI's Data Link Layer, or Layer 2. Again, it serves the same purpose.

Some authors will include a fifth layer in the TCP/IP model - the Physical Layer from earlier.

**[Task 3, Question 1] To which layer does HTTP belong in the TCP/IP model?** - Application Layer

**[Task 3, Question 2] How many layers of the OSI model does the Application Layer in the TCP/IP model cover?** - 3

## [Task 4] IP Addresses and Subnets

You may already be familiar with IP addresses - think `192.168.0.1` or something similar. That's an IP (version 4) address. Every host on a network needs a unique one of these. Without such an identifier, you wouldn't be able to find the host you want to communicate with, at least not without some ambiguity. Thus, when implementing TCP/IP, you need to actually implement _IP_ by assigning unique IP addresses to devices in the network. You can think of it as your home address.

We did make the distinction earlier about the IP address being an IP _version 4_ (IPv4) address. There is another version of the IP address that's slowly being rolled out called IP version 6 (IPv6) that solves some issues about the number of addresses we can actually assign. However, you'll most likely be seeing IPv4 addresses in networks, and when folks talk about "IP" they're usually thinking of IPv4.

An IP address has four octets, or 32 bits. One octet is 8 bits, and it can be used to represent a number from 0 to 255. An example IP address is `192.168.1.1`. No octet can be greater than 255.

It's worth noting that there are some reserved numbers/addresses.
- `0` is used for the network address, e.g. `192.168.1.0`.
- `255` is used for the broadcast address, e.g. `192.168.1.255`. Sending anything to this address will involve all hosts on the network.

Doing some math and excluding the reserved addresses, we find that we cannot have more than around 4 billion IPv4 addresses (to confirm, calculate 2^32, since we've got 32 bits to work with).

You can see your IP address configuration in Windows with `ipconfig`, or in Linux with `ifconfig`. We've discussed these in previous rooms in this path. Note that in Linux you can also sue `ip address show` (or `ip a s`) to see information about the network card itself. You'll find information about the host IP address, subnet mask, and broadcast address.

If you look at a subnet mask, or at any IP address given in the output for those commands above, you might see `/24` or something similar. That tells us the leftmost 24 bits (or leftmost 3 octets) in the IP address will not change, ever. For instance, in `192.168.66.89/24`, we know that the `192.168.66` will not change. The rest of the address could vary from `192.168.66.0` to `192.168.66.255`.

For practical reasons, there are two kinds of IP addresses: public IPs and private IPs. RFC 1918 (RFC meaning "Request for Comment" - these are documents that generally explain how network protocols and components are implemented) gives three ranges of private IP addresses:
- `10.0.0.0` to `10.255.255.255` (or `10/8`)
- `172.16.0.0` to `172.31.255.255` (or `172.16/12`)
- `192.168.0.0` to `192.168.255.255` (or `192.168/16`)

Private IP addresses cannot be reached from the outside world, at least not directly. For such an address to do so, it'll need something called Network Address Translation (NAT), which is something that will be discussed later. Essentially, if you see `10...` or `172.16...` or `192.168...`, you're probably seeing a private IP that can only be reached if you're in the same private network it is on.

A router will handle forwarding data packets to the proper network. Data packets will pass through many routers before they reach their final destination. This all happens at Layer 3: packets get inspected for their IP address and sent off to the best network/router.

Choices are shown in the room. Remember that private IPs start with `10...`, `172.16` up to `172.31`, or `192.168`.

**[Task 4, Question 1] Which of the following IP addresses is not a private IP address?** - `49.69.147.197`

Similarly, IP addresses cannot have a number larger than `255` in any octet.

**[Task 4, Question 2] Which of the following IP addresses is not a valid IP address?** - `192.168.305.19`

## [Task 5] UDP and TCP

So we have a way to reach a destination host on a network. Our main question now is how we can make processes communicate with each other on networked hosts. That's the job of the transport layer and its main protocols - TCP and UDP.

The User Datagram Protocol (UDP) is used to reach a specific process on the target host, and it is a conectionless protocol. This means that it does not need to establish a connection - in fact, it does not even know if the packet has been delivered correctly. While this may sound bad, it's pretty good if you're looking for fast communication. It doesn't have to wait for any confirmation before moving on.

If IP addresses identify the host, then we need some way of knowing what the sending/receiving process is. That's where port numbers come in. Port numbers use two octets, and so they can range from 1 to 65535 with port 0 being reserved (`2^16 - 1`).

The Transmission Control Protocol (TCP), on the other hand, is a connection-oriented transport protocol which uses mechanisms to ensure reliable data delivery sent by different processes on the networked hosts. Before we can send any data, we must establish a connection between processes.

Each data octet in TCP has a sequence number, which makes it easy to identify lost or duplicated data packets. The receiver acknowledges the reception of data with acknowledgement numbers - these are simply the last received octets.

TCP connections are established with the three-way handshake. Packets with the SYN and ACK flags are used for this purpose.
1. SYN: Client initiates connection by sending a SYN packet to the server, containing a randomly chosen initial sequence number.
2. SYN-ACK: Server responds with a SYN-ACK packet, adding the initial sequence number randomly chosen by the server.
3. ACK: Three-way handshake completes as the client sends an ACK packet to acknowledge reception of the SYN-ACK packet.

Like UDP, TCP uses port numbers to identify processes initiating or waiting/listening for a connection. It, too, uses port numbers from 1 to 65535.

**[Task 5, Question 1] Which protocol requires a three-way handshake?** - TCP

**[Task 5, Question 2] What is the approximate number of port numbers, in thousands?** - 65

## [Task 6] Encapsulation

One more key concept in networking is that of encapsulation. When something gets sent down the layers, each successive layer adds a header (and sometimes a trailer) to the unit of data. The encapsulated unit then gets sent off. This allows each layer to focus on its intended function. In TCP/IP, encapsulation works like this:
1. Connections start when the user inputs data they want to send into the application, e.g. an email or an instant message. The application formats the data and sends it according to the application protocol in use.
2. The trnasport layer adds the proper header information depending on what transpot-layer protocol is being used. In TCP, this results in a segment. In UDP, this results in a datagram. Whatever gets made here is sent to the network layer.
3. The network/Internet layer adds an IP header to the received segment or datagram, thus creating a packet.
4. The data link layer adds the proper Ethernet or Wi-Fi header/trailer to create a frame, which is then sent off.

Once the frame is sent over to the right network, the process is then reversed as the data makes its way to the intended recipient. This may be known as decapsulation.

Now that we know all of this, here's a very simplified view of what happens when you access a webpage.
1. You make your way to the webpage - this might be a result of clicking something or entering a search query.
2. The web browser (via HTTPS) prepares an HTTP request. This is the application layer at work. It is then sent to the transport layer.
3. At the transport layer, TCP is used to establish a connection between you and the web server. Once it is established, it can send the HTTP request with the search query. Each TCP segment is sent to the Internet layer.
4. At the Internet layer, the IP source address (your computer) and destination IP address (web server) is added to the packet. Now we just need this packet to reach the router to be sent off.
5. At the Data Link layer, the link layer header and trailer is added depending on how the frame will reach the router. Then it's sent off to the router.
6. The router removes the link layer header/trailer, inspects the IP destination and other fields, and routes the packet to the right link. This process is repeated router-by-router until the packet reaches its destination.
7. The steps are then reversed once the packet reaches the destination router.

**[Task 6, Question 1] On a Wi-Fi, within what will an IP packet be encapsulated?** - frame

**[Task 6, Question 2] What do you call the UDP data unit that encapsulates the application data?** - datagram

**[Task 6, Question 3] What do you call the data unit that encapsulates the application data sent over TCP?*8 - segment

## [Task 7] Telnet

Let's get some practice in how networks work in a practical setting. This will require starting the provided VM _and_ the AttackBox. Once everything has been set up, you can open the Terminal so we can get into Telnet.

The Telnet protocol (Teletype Network) is used for remote terminal connection. You would have used it in order to communicate with remote systems and send text commands. You can also use it to connect to any server listening on a TCP port number. The target VM has different services running, including
- The Echo server, which just echoes everything that is sent to it. This listens on port 7 by default.
- The Daytime server, which replies with the current date and time. This listens on port 13 by default.
- The Web (HTTP) server, which serves web pages. This listens on port 80 by default.

NOTE: The Echo and Daytime servers are security risks and should not be run. In fact: Telnet as a whole is a security risk, since information is transmitted without being properly secured. Nowadays we primarily use SSH for remote communication; this just happens to be a nice way of demonstrating how things work.

To connect to a machine over Telnet, we run `telnet (MACHINE_IP) (PORT_TO_CONNECT_TO)`. When you're done, you can press `CTRL + ]` simultaneously to close the connection. The daytime server connection closes once the current date and time are returned.

To request a web page with Telnet, run `telnet (MACHINE_IP) 80` and then run the command `GET / HTTP/1.1`, press Enter, and then type `Host: (HOST_NAME)`. In this case, we'll have to type `Host: telnet.thm`. Once we do so, we press Enter a few times, and we see the web page. The name and version of the HTTP server is given in the `Server:` line.

![image](https://github.com/user-attachments/assets/141541da-da9e-4f75-89ea-3a94a6a35ee4)

**[Task 7, Question 1] Use `telnet` to connect to the web server on `10.10.160.243`. What is the name and version of the HTTP server?** - lighttpd/1.4.63

And the flag is visible at the end of the telnet output:

![image](https://github.com/user-attachments/assets/f2898d04-a6dd-4647-80d9-12953934b4f3)

**[Task 7, Question 2] What flag did you get when you viewed the page?** - `THM{TELNET_MASTER}`

## [Task 8] Conclusion

And that does it for the brief introduction to networks - we covered IP addresses, subnets, and routing, as well as some important protocols and encapsulation. We also saw how a client can "communicate" with other hosts by using Telnet.
