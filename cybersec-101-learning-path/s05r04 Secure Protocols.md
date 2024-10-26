# Networking: Secure Protocols

## [Task 1] Introduction

The previous room gave an overview of the protocols used to browse the web, access email, and do other common tasks. They work pretty well, but come with one significant flaw: they are all insecure. Someone could watch the traffic on the network connection and grab usernames and passwords, because it's all sent in the clear! Even worse, a crafty adversary may be able to modify the traffic for their own gain, which can lead to even worse issues including _not actually talking to the server you wanted to talk to._

It quickly became apparent that this was going to be a major issue, so the Internet folks developed Transport Layer Security (TLS), which is used to provide the necessary security for Internet traffic. The major protocols discussed in the previous task (HTTP, IMAP, POP3, SMTP) have secure versions that run on TLS. These secure versions are marked with an S, e.g. HTTPS, POP3S, SMTPS, IMAPS.

Also, it was mentioned in the Networking Concepts room that Telnet is insecure. That's because of the same reasons described above - all information is sent out in the clear for folks to read. That's why we use Secure Shell (SSH) instead, and SSH can even be used to provide secure functionality in other protocols like FTP.

Evidently, you should be familiar with the Core Protocols room; though having an understanding the entirety of the section so far is preferred.

## [Task 2] TLS

Back in the day, all someone had to do to collect sensitive traffic in a network was to run their network cards in _promiscuous mode_, which captures ALL traffics on the network, including those not intended for them. Then they would go through the packet captures and obtain login credentials from there. A user couldn't stop their credentials from being sent in the clear. Thankfully, times have changed, and now most services offer some form of protection for users' data.

In the early 90s, Netscape Communications would develop the Secure Sockets Layer, or SSL, in response to the increasing demand for secure communications. In 1995, SSL 2.0 was released as the first public version of SSL. In 1999, the Internet Engineering Task Force (IETF) developed Transport Layer Security, TLS, which was the successor to SSL with better security. By 2018, TLS saw many overhauls and so TLS 1.3 was released. A lot of time has been spent into perfecting the protocols used to protect data.

TLS is a cryptographic protocol that operates at the Transport Layer. It allows a client and a server to communicate securely over an inherently insecure network. TLS makes it so that no one can read or modify exchanged data. Now many protocols have TLS-enhanced versions, including the ones mentioned earlier, DoT (DNS over TLS), MQTTS, and SIPS.

The TLS handshake is out of the scope of this room (it's discussed in the Network Security Protocols room). However, the general process looks like this:
1. A server that wants to identify itself as legitimate will grab a signed TLS certificate.
2. For a certificate to be made, the server administrator creates a Certificate Signing Request, or CSR, and submits it to a Certificate Authority (CA) for verification. Once verified, the CA issues a digital certificate.
3. The digital certificate is used to identify the client or server to others, who can confirm its validity.
4. To confirm the validity of a signature, the certificates of the signing authorities must be installed on the host.

Getting a certificate signed typically requires a fee, but some folks will do so for free (see Let's Encrypt). Some users will opt to use a self-signed certificate, which cannot prove the server's authenticity... so steer clear of those unless you have to trust such a certificate for some reason.

**[Task 2, Question 1] What is the protocol name that TLS upgraded and built upon?** - SSL

**[Task 2, Question 2] Which type of certificates should not be used to confirm the authenticity of a server?** - self-signed certificate

## [Task 3] HTTPS

We know HTTP runs on TCP port 80, and that it can be intercepted and read pretty easily. When an HTTP communication happens, the client must
1. Set up a TCP three-way handshake with the server, and
2. Communicate over HTTP using requests, among other things.

The TCP handshake consists of three packets. The first screenshot provided displays three packets for TCP connection termination, as well, and the packets that involve HTTP communication.

HTTPS, or Hypertext Transfer Protocol Secure, is HTTP over TLS. To request a page via HTTPS, the client must do the following:
1. Set up a TCP three-way handshake,
2. Establish a TLS connection, and
3. Communicate over HTTP.

Again, TCP is set up in the first three packets, then five packets are involved in the setup of the TLS communication (as seen in the second Wireshark screenshot). Then HTTP data can be exchanged. In Wireshark, HTTPS packets will simply be marked as "Application Data" - we can't see what's inside because it's protected by TLS! Specifically, TLS _encrypts_ the traffic so that if an attacker were to try and read it, they would be immediately greeted with gibberish. You need a _key_ to decrypt the gibberish and read what's inside.

It's possible (though, in a real scenario, extremely unlikely) to obtain the _private key_ needed to decrypt HTTPS traffic. If we were to get our hands on such a key though, not much would change in Wireshark's output. In fact, the only thing that would change is that all of the "Application Data" packets would actually have readable data in them.

Notice how TLS just enhances the security involved in HTTP - nothing much really changes in the HTTP protocol itself, nor the TCP and IP protocols.

For the first question, we are counting TCP and TLS setup packets.

**[Task 3, Question 1] How many packets did the TLS negotiation and establishment take in the Wireshark HTTPS screenshots above?** - 8

The packet numbers are located on the left side of the Wireshark screenshots. Not the big numbers - the smaller ones to their right. Each packet is numbered in Wireshark sequentially. Make sure you're looking at the decrypted packets!

**[Task 3, Question 2] What is the number of the packet that contain the `GET /login` when accessing the website over HTTPS?** - 10

## [Task 4] SMTPS, POP3S, and IMAPS

TLS provides similar functionality to the email protocols - they encrypt the traffic sent over them without really changing how they work. Our discussion above for HTTPS applies here, just for the email protocols. If you're using a secure version of these protocols, expect to see an "S" somewhere.

We will take the time to go over port numbers, since you should be familiar with them. All are TCP port numbers:
- HTTP: 80 -> HTTPS: 443
  - Secondary HTTP to HTTPS port numbers: 8080 -> 8443
- SMTP: 25 -> SMTPS: 465 and 587
- POP3: 110 -> POP3S: 995
- IMAP: 143 -> IMAPS: 993

**[Task 4, Question 1] If you capture network traffic, in which of the following protocols can you extract login credentials: SMTPS, POP3S, or IMAP?** - IMAP

## [Task 5] SSH

Telnet has been the main focus of this section of rooms so far, but again, all traffic involved with it is sent in the clear, so anyone can grab your login credentials by just examining packets. Tatu Ylönen responds to this issue in 1995 by releasing the first version of Secure Shell (SSH-1) as freeware to the public. A more secure version, SSH-2, was defined in 1996. In 1999, the OpenBSD folks released OpenSSH, which is an open-source implementation of SSH. You're likely going to be using OpenSSH to do SSH communication nowadays.

Here are some benefits to using OpenSSH:
- OpenSSH supports public key and two-factor authentication, on top of typical password authentication
- OpenSSH provides end-to-end encryption to protect against eavesdropping, and it informs you of new server keys to prevent man-in-the-middle attacks. This all protects the _confidentiality_ of your data.
- OpenSSH uses cryptography to protect the _integrity_ of traffic - that is, it makes sure no bad actors can modify the traffic and get away with it.
- It can also create a secure tunnel to route other protocols through SSH, sort of like a VPN if you're familiar with how those work. If not, stay tuned - that'll be discussed later.
- And SSH supports X11 Forwarding, which means if you connect to a Unix system over a GUI, SSH will let you use the graphical application on the network.

To connect to a system over SSH, you would run `ssh (USERNAME)@(HOSTNAME)`. If the username is the same as your logged-in username, run `ssh hostname`. Then you'll be asked for a password, unless you're able to get in via public-key authentication, in which case you'll just be logged in immediately.

To support GUIs, you will want to run `ssh (USERNAME)@(HOSTNAME) -X`. The local system will need a suitable GUI for this to work, of course.

SSH happens to listen on port 22.

## [Task 6] SFTP and FTPS

SFTP stands for SSH File Transfer Protocol, and it basically uses SSH to perform FTP things. It even runs on the same port as SSH - port 22. If it is set up in the OpenSSH server configuration, you can connect via `sftp (USERNAME)@(HOSTNAME)`, and then you can use `get (FILE)` and `put (FILE)` to download and upload files respectively. SFTP commands can differ from typical FTP commands; these are more like Unix commands that you've seen before.

This is NOT the same as FTPS, which stands for File Transfer Protocol Secure. It uses TLS to secure FTP traffic. While FTP uses port 21, SFTP uses pot 990. This does require some certificate setup, and it can be tricky to manage since separate connections are needed for control and data transfer. This can cause it to get blocked in stricter firewalls.

To summarize: SFTP is easy to set up; all you need to do is enable an option in your SSH server settings. FTPS uses TLS, but it requires certificates to be properly secured.

This task comes with a short quiz on port numbers. Here's a quick summary of all the ones covered:
- HTTP: 80 -> HTTPS: 443
  - Secondary HTTP to HTTPS port numbers: 8080 -> 8443
- SMTP: 25 -> SMTPS: 465 and 587
- POP3: 110 -> POP3S: 995
- IMAP: 143 -> IMAPS: 993
- FTP: 21 -> SFTP: 22 (same as SSH)
- FTP: 21 -> FTPS: 990

In the exercise, you drag the insecure protocol port numbers to their associated secure protocol port numbers.

**[Task 6, Question 1] Click on the View Site button to access the related site. Please follow the instructions on the site to obtain the flag.** - `THM{Protocols_secur3d}`

## [Task 7] VPN

One last topic: Say you want to connect a whole bunch of physical sites to a main network so any device can access shared resources, as if it were physically located within the main network. It's possible to do this with virutal private networks (VPNs).

TCP/IP was designed to ddeliver packets. Routers were set up so that they could change routes to send packets if, say, one router went down for whatever reason. TCP can detect un-acknowledged packets and resend them as needed to recover from errors. What we hadn't figured out was how to ensure that all data leaving/entering a computer is secured - that no one could read it or change it. Setting up a VPN helped with this, and companies took notice (particularly those with information they really needed to keep private). VPNs can be convenient and inexpensive to use - all you need is an Internet connection and a VPN server/client.

VPN clients in remote sites/networks could connect to VPN serversi n the main network - the VPN client encrypts the traffic and passes it along via an established VPN tunnel. VPN traffic is limited to what goes in and out the tunnel - outside of that, traffic is deecrypted and sent around. A VPN client may be used to connect with many devices or just a single one.

All Internet traffic, when put on a VPN, is routed through VPN tunnels. When accessing Internet services and web apps, they will only see the public IP address of the VPN server. This can be used, among other ways, to bypass geographical restrictions and browse the Internet uncensored. Servers are automatically designed to redirect folks to different versions of a service for different geographical regions.

Some VPNs don't route traffic through a tunnel - they may set up a private network for you, but they don't do routing. Some VPN servers also leak your actual IP address (but at least they route traffic over the VPN). You may want to run tests (e.g., DNS leak tests) to see what is actually protected in transit. You may also want to consult with local laws and regulations - some countries have outlawed the use of VPNs.

**[Task 7, Question 1] What would you use to connect the various company sites so that users at a remote office can access resources located within the main branch?** - VPN

## [Task 8] Closing Notes

We have employed three main approaches to securing network traffic:
1. Use TLS to encrypt traffic sent over insecure protocols
2. Use SSH as a tunnel for secure transmission, e.g. in FTP
3. Secure traffic with VPN connections, which can be useful when dealing with companies that have far-flung branches

This room concludes with a challenge. We have a browser that logs TLS session keys so we can examine the traffic in Wireshark. The command `chromium -ssl-key-log-file=~/ssl-key.log` dumps TLS keys to the `ssl-key.log` file. The relevant packet capture file we want to investigate is `randy-chromium.pcapng`, and it can be found ni the Documents folder. We'd like to open this capture and use Wireshark to investigate it, however, the TLS-related traffic in this file is naturally going to be encrypted. We have to decrypt it first. The procedure for doing so is as follows, once we open up Wireshark and load the file (double-click Wireshark on the Desktop; the file should be listed in the Open section on Wireshark's landing page - double click that):
1. Each row is a packet of some kind. Right-click a TLSv1.3 packet. Choose Protocol Preferences -> Transport Layer Security -> Open Transport Layer Security Preferences
2. Click "Browse" at the bottom of the screen - it should be in the section "(Pre)-Master-Secret log filename."
3. Look for `ssl-key.log` in the Documents directory. Select it and click OK.
4. Click OK in the Wireshark - Preferences dialog box, and Wireshark will decrypt all the traffic.

Our goal now is to find the packet with login credentials and obtain the password. We haven't really covered Wireshark in detail yet - this will be the focus of the next room in this section (see heads-up below) - though the hint suggests we should check packet #366. Packet numebers are given in the left column, so let's scroll down to 366. Click the row of packet #366, and look at the information at the very bottom of the screen:

![image](https://github.com/user-attachments/assets/19fdce18-3382-405f-b7ac-2cdaf33b4c6d)

In Wireshark, this is where the contents of the packet can be found and examined. Since the information has been decrypted, we should be able to read the packet fully. Each protocol is a section/header of the Wireshark output. Expand the HyperText Transfer Protocol 2 at the very bottom of this output, then expand the "HTML Form URL Encoded" section. This gives us the password that was input:

![image](https://github.com/user-attachments/assets/4c535ac8-33be-4178-9e93-5df21c658f7e)

**[Task 8, Question 1] One of the packets contains login credentials. What password did the user submit?** - `THM{B8WM6P}`

(As a heads-up, the Wireshark room is already covered in the SOC Level 1 Learning Path, discussed [here](https://github.com/abeeching/thm-stuff/blob/main/soc1-learning-path/s03r09%20Wireshark%20-%20Basics.md).)
