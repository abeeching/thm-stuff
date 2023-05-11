# Nmap

## [Task 2] Introduction

Generally speaking, the more knowledge you have about a system, the better. Thus, it's imperative to carry out proper enumeration before exploiting anything. If we have an IP (or IPs) to perform an audit on, we want to know what's up with the "landscape" before we attack. We must establish which services are running on the targets.

The first stage in establishing this map of the landscape is to perform port scanning. When a computer runs a network service, it opens a port - a network construct - to receive the connection. Ports are needed to make multiple network requests or have multiple services available. When loading multiple webpages into multiple tabs, connections are established to remote webservers with different ports. For a server to run multiple services, you must direct traffic to the appropriate service using ports. Network connecctions are made between two ports - an open port listening on the server and a randomly-selected port on your computer.

All computers have a total of 65535 ports; that being said, many of these are registered as standard ports. HTTP is typically port 80, HTTPS is typically port 443. NETBIOS is typically on port 139 and SMB on port 445. Note that, especially in CTFs, *these standard ports may be altered.*

It's nigh-impossible to attack a target without knowing which ports are open, so that's why we're going to talk about Nmap. This is a tool that is usually used for port scanning purposes. It can perform many kindsn of port scans, but the basic theory is that nmap will connect to each port of the target in turn. Depending on how the port responds, nmap determines if it's opened, closed, or filtered (e.g. via firewall). Once we know what the open ports are, we can enumerate the services running on each port. One *could* do this manually, but we also have Nmap so...

We use Nmap since this is currently the industry-standard tool for port scanning. No other scanning tool comes close to matching its functionality, though some other tools are matching it in speed. It's very powerful, especially when taking into account its scripting engine, which can be used to scan for vulnerabilities (and even perform exploits).

With this information in mind, we can answer the first two questions of the task:

**[Task 2, Question 1] What networking constructs are used to direct traffic to the right application on a server? - Ports**

**[Task 2, Question 2] How many of these are available on any network-enabled computer? - 65535**

Well... we know that there are well-known ports, based on our answer to Task 2, Question 1. Taking to Google and looking up well-known ports, we find that there are 1024 ports that are well-known. Nice!

**[task 3, Question 3] (Research) How many of these are considered "well known"? These are the "standard" numbers mentioned in the task. - 1024**

## [Task 3] Nmap Switches

So! There are a boatload of nmap switches. It's worth taking the time to investigate them. We need to use `nmap -h` to open the help menu. Pop open the Aatack Box or Kali Linux and look around yourself! The answers to all of these questions can be found there.

**[Task 3, Question 1] What is the first switch listed in the help menu for a Syn Scan? More on this later. -sS**

**[Task 3, Question 2] Which switch would you use for a UDP scan? -sU**

**[Task 3, Question 3] If you wanted to detect which operating system the target is running on, which switch would you use? -O**

**[Task 3, Question 4] Nmap provides a switch to detect the version of the services running on the target. What is this switch? -sV**

**[Task 3, Question 5] The default output provided by nmap often does not provide enough information for a pentester. How would you increase the verbosity? -v**

**[Task 3, Question 6] Verbosity level one is good, but verbosity level two is better! How would you set the verbosity level to two? (Note: It's highly advisable to always use *at least* this option.) -vv**

**[Task 3, Question 7] What switch would you use to save the nmap results in three major formats? -oA**

**[Task 3, Question 8] What switch would you use to save the nmap resutls in a "normal" format? -oN**

**[Task 3, Question 9] A very useful output format: how would you save results in a "grepable" format? -oG**

Sometimes, the results we get aren't enough. Maybe we don't care about how loud we are, so we might want to enable "aggressive mode" to active service detection, OS detection, traceroute, and common script scanning.

**[Task 3, Question 10] How would you activate this setting? -A**

**[Task 3, Question 11] How would you set the timing template to level 5? -T5**

**[Task 3, Question 12] How would you tell nmap to only scan port 80? -p 80**

**[Task 3, Question 13] How would you tell nmap to scan ports 1000-1500? -p 1000-1500**

**[Task 3, Question 14] How would you tell nmap to scan *all* ports? -p-**

**[Task 3, Question 15] How would you activate a script from the nmap scripting library? Lots more on this later. --script**

**[Task 3, Question 16] How would you activate all of the scripts in the "vuln" category? --script=vuln**

## [Task 4] (Scan Types) Overview

There are three basic types of scans in nmap: TCP connect scans, SYN half-open scans, and UDP scans. There are some less common port scan types which may or may not be useful in certain cases, including TCP Null, TCP FIN, and TCP Xmas scans. The way that each of these scan types work will differ from scan to scan.

## [Task 5] (Scan Types) TCP Connect Scans

To understand the TCP Connect scan, `-sT`, you must first understand the TCP three-way handshake. In brief, the handshake works like this:
1. You want to connect to something over TCP. You send a TCP request to the target server, with the SYN flag set.
2. The server receives this and acknowledges it, sending back an ACK flag.
3. We acknowledge that the handshake has worked by shooting back a TCPC request with the ACK flag set.

So, in terms of nmap, all we do is run this process with each target port on a machine. Nmap attempts to connect and determines if the port is open based on what it gets back.

According to RFC 793, if a connection does not exist -- that is, if it's closed -- a RESET is sent in response to any incoming segment excluding another reset. This is how SYNs sent to nonexistent connections are handled. If nmap sees a RST flag, then it knows it's closed. However, if the port is open, then it will have gotten a TCP packet with SYN/ACK flags set.

There is one possibility worth considering though. What if the port is behind a firewall? Firewalls are configured to _drop_ incoming packets. Nmap will shoot a TCP SYN request to the port, and it will get nothing back. In this case, nmap considers the port to be filtered.

(NOTE: The filtered response isn't exactly foolproof. You can run a command to make a firewall shoot an RST TCP packet back. Using Linux's IPtables, you just type `iptables -I INPUT -p tcp --dport <PORT> -j REJECT --reject-with tcp-reset`)

Using the above information, we can answer the next questions.

**[Task 5, Question 1] Which RFC defines the appropriate behavior for the TCP protocol? - RFC 793**

**[Task 5, Question 2] If a port is closed, which flag should the server send back to indicate this? - RST**

## [Task 6] (Scan Types) SYN Scans

When run with `-sS`, nmap can scan the TCP port range of a target (or targets), however, this kind of scan is considered to be a half-open/stealth scan. Why is that?

Well, in terms of the three-way handshake, after a target server sends a SYN/ACK packet, a client actually sends an RST packet in response. This stops the server from repeatedly trying to make the request. Why is this useful?
- This is helpful for bypassing older intrusion detection systems (IDS), since they look for a full three-way handshake. Modern IDS can detect this though...
- SYN scans are not logged by applications listening on open ports, since it's common to log a connection once it has been fully established.
- SYN scans are faster than a TCP Connect scan, since we don't have to complete and subsequently disconnect from a server for every port.

There are still disadvantages to SYN scans, though.
- You need sudo privileges, since SYN scans require the ability to create raw packets, which only the root user can do. (NOTE: You can make these work by giving Nmap the CAP_NET_RAW, CAP_NET_ADMIN, CAP_NET_BIND_SERVICE capabilities, though this might prevent NSE scripts from running properly.)
- Unstable services can be brought down by SYN scans, which is a huge issue if a production environment is used for the test.

SYN scans are run by default if nmap is run with sudo permissions; otherwise, the TCP connect scan is used. 

The same rules for identifying closed and filtered ports applies from the TCP connect scan. If a port's closed, then it gets a RST packet. If a port's filtered by a firewall, then the packet is dropped or spoofed with a TCP reset.

The information provided above can be used to answer the questions.

**[Task 6, Question 1] There are two other names for a SYN scan. What are they? - Half-Open, Stealth**

**[Task 6, Question 2] Can Nmap use a SYN scan without Sudo permissions (Y/N)? - N**

## [Task 7] (Scan Types) UDP Scans

UDP connections are _stateless_ - this means that instead of initiating a connection with a back-and-forth handshake, UDP connections rely on sending packets a target port and (basically) hoping they get there. Thus UDP is useful for situations where speed is more important than quality. The lack of acknowledgement though makes UDP a slower scan. We use -sU to scan with UDP.

If a packet is sent to an open UDP port, there will be no response. Thus, nmap calls this `open|filtered`. It thinks the port is open, but it could be firewalled. If it gets a UDP response - which is unusual - the port is marked as open. When a packet is sent to a closed UDP port, the target responds with an ICMP (ping) packet telling nmap the port is unreachable, and so nmap marks it as closed.

Usually nmap sends completely empty requests - raw UDP packets. If ports are occupied by well-known services, it will try to send a protocol-specific payload which is more likely to elicit a response.

**[Task 7, Question 1] If a UDP port doesn't respond to an Nmap scan, what will it be marked as? - open|filtered**

**[Task 7, Question 2] When a UDP port is closed, by convention the target should send back a "port unreachable" message. Which protocol would it use to do so? - ICMP**

## [Task 8] (Scan Types) NULL, FIN, and Xmas

These are less-commonly used than SYN, TCP Connect, and UDP scans. They tend to be stealthier, though, than a SYN stealth scan.
- NULL scans (-sN): TCP request is sent with no flags at all. Per RFC, the target host should respond with RST if the port is closed.
- FIN scans (-SF): TCP request sent with the FIN flag, usually used to gracefully closed an active connection. If closed, nmap should get an RST.
- Xmas scan (-sX): malformed TCP packet sent and expects RST for closed ports. It sets PSH, URG, and FIN all at once. It apparently looks like a blinking tree when run in Wireshark.

For open ports, nmap does not expect there to be a response -- it's a malformed packet after all. That being said, this is also what nmap expects to see if a port is protected by a firewall. Thus, these scans only report open|filtered, closed, or filtered as statuses for any given port.

Also note: RFC 793 mandates that network hosts respond to malformed packets with an RST TCP packet for closed ports and does not respond at all for open ports. This doesn't always happen - Microsoft Windows and a lot of Cisco devices are known to respond with an RST to _any_ malformed TCP packet regardless of whether the port is open or closed. Thus, all ports show up as closed in this case.

The goal in using these scans is to evade firewalls. Many firewalls are configured to drop incoming TCP packets to blocked ports with SYN set, blocking new connection initiation requests. We can send requests that _don't_ have the SYN flag set to bypass this kind of firewall. Note that IDS are aware of these types of scans, so they may not be 100% effective for modern systems.

**[Task 8, Question 1] Which of the three shown scan types uses the URG flag? - xmas**

**[Task 8, Question 2] Why are NULL, FIN, and Xmas scans generally used? - Firewall Evasion**

**[Task 8, Question 3] Which common OS may respond to a NULL, FIN, or Xmas scan with a RST for every port? - Microsoft Windows**

## [Task 9] (Scan Types) ICMP Network Scanning

If we are given a black-box environment, we want to first get a map of the network structure - which IP addresses contain active hosts and which do not? We can do this by using a ping sweep.

Nmap sends an ICMP packet to each possible IP for the specified network. When it gets a response, it marks the IP that responded as being alive. This isn't always accurate, but it gives us a baseline.

To use this scan, we simply supply `-sn` as the switch as well as IP address ranges given in hyphenated or CIDR notation: `192.168.0.1-254` or `192.168.0.0/64`.

This tells nmap not to scan any ports - it just relies on ICMP echo packets, or ARP requests on a local network, if run with sudo or as root, to identify targets. This will also send a TCP SYN request to port 443 of the target AND a TCP ACK (or TCP SYN if not run as root) to port 80.

**[Task 9, Question 1] How would you perform a ping sweep on the 176.16.x.x network (Netmask: 255.255.0.0) using Nmap? (CIDR notation) - nmap -sn 172.16.0.0/16**

## [Task 10] (NSE Scripts) Overview

NSE - Nmap Scripting Engine - is a powerful addition for Nmap that extends its functionality. Scripts are written in the Lua language and can be used to do all sorts of junk: scan for vulnerabilities, automate exploits, yadda yadda yadda. Categories include:
- safe: Stuff that doesn't affect the target
- intrusive: Stuff that is likely to affect the target
- vuln: Scan for vulnerabilities
- exploit: Attempt to exploit a vulnerability
- auth: Attempt to bypass authentication for running services, e.g. logging into an FTP server anonymously
- brute: Attempt to bruteforce credentials for running services
- discovery: Attempt to query running services for further information about the network, e.g. query an SNMP server

**[Task 10, Question 1] What language are NSE scripts written in? - Lua**

**[Task 10, Question 2] Which category of scripts would be a VERY bad idea to run in a production environment? - intrusive**

### On NSE Usage

Additional categories include
- broadcast: Discovery of hosts not listed in the command line, obtained via network broadcasting
- default: The default set of scripts run when using -sC and -A.
- dos: Scripts that may cause denial-of-service. Can be used to test vulnerabilities, but can bring down a service.
- external: Scripts that send data to a 3rd-party database or another network resource, e.g. whois-ip
- fuzzer: Scripts that send server software unexpected or randomized fields in each packet.
- malware: Scripts that test if a server is infected by malware or backdoors.
- version: Extensions of the version detection feature, only ran if -sV is used.

Determining when a script is set to default:
- Speed: quick scans are preferred, so any brute-force crackers, web spiders, etc. aren't set as default
- Usefulness: scans that produce valuable and actionable information are preferred to those that don't. If it can't easily be explained why the output of one script is useful, then it's not set to default.
- Verbosity: scripts that aren't readable or concise should not be marked as default. If a script finds vulnerabilities, it should only give output once the vulnerability is discovered.
- Reliability: if a script (mainly one that uses heuristics and fuzzy signature matching) is frequently wrong, do not set it as default.
- Intrusiveness: if a script is capable of using significant resources on a remote system or just straight-up crashing it, it probably shouldn't be set to default.
- Privacy: some scripts divulge information to 3rd parties by their nature. Probably shouldn't set these as defaults.

Types and Phases:
1. Prerule scripts run before any of Nmap's scan phases, so these don't have a lot of info to work off of.
2. Host scripts run during Nmap's scanning process after host discovery, port scaning, version detection, OS detection
3. Service scripts run against specific services on a target host
4. Postrule scripts run after Nmap has scanned its targets.

## [Task 11] (NSE Scripts) Working with the NSE

We can give categories to the --script switch, e.g. `--script=vuln` and `--script=safe`.

To run a specific script, we would give `--script=<NAME>`, e.g. `--script=http-fileupload-exploiter`. Multiple scripts can be run by separating them via commas.

Some scripts require arguments; these need to be given by the `--script-args` switch. Arguments are separated by commas, and they are connected to corresponding scripts with periods, such as `<NAME>.<ARG>`.

Nmap scripts get built-in help menus which may be accessed using `nmap --script-help <NAME>`. If working locally, this can help. If you can, though, you should check the Nmap documentation (linked below).

Using the nmap documentation, we see that `ftp-anon.maxlist` is the one script argument that can be given here. This indicates the number of files to return in a directory listing.

**[Task 11, Question 1] What optional argument can the `ftp-anon.nse` script take? - maxlist**

### NSE doc from Nmap

https://nmap.org/nsedoc/

## [Task 12] (NSE Scripts) Searching for Scripts

Two options to take; you should use these in conjunction with each other if possible.
1. Use the Nmap documentaiton above, or
2. Check the local storage on the attacking machine: `/usr/share/nmap/scripts`.

To search for installed scripts, you can
1. Check the `/usr/share/nmap/scripts/script.db` file, or
2. Use the `ls` command: `ls -l /usr/share/nmap/scripts/*ftp*` --> spits out scripts with ftp in their names

Rememember, you can use grep if you're checking the database file directly: `grep "ftp" /usr/share/nmap/scripts/script.db` --> returns scripts with ftp in their names

You can use these techniques to look for categories of scripts, as discussed earlier.

If a script is missing, you can run `sudo apt update && sudo apt install nmap` to reinstall these missing scripts. You can also use wget to download the scripts directly from nmap: `sudo wget -O /usr/share/nmap/scripts/<NAME>.nse https://svn.nmap.org/nmap/scripts/<NAME>.nse`. You must also use `nmap --script-updatedb` after this. Also note that if you want to make your own script, you would have to use the updatedb command too.

You can use any of the options discussed here. It's most beneficial to open up the AttackBox and just look around that way. Grepping for smb and looking at the results gives you the answer right away for the first question.

**[Task 12, Question 1] Search for "smb" scripts in the `/usr/share/nmap/scripts` directory using either of the demonstrated methods. What is the filename of the script which determines the underlying OS of the SMB server? - smb-os-discovery.nse**

You will have to look at the script itself in order to get the answer to the question:

![image](https://github.com/abeeching/thm-stuff/assets/56741029/7534b444-0d32-4d75-904b-777ba9ff6d0f)

**[Task 12, Question 2] Read through this script. What does it depend on? - smb-brute**

## [Task 13] Firewall Evasion

There are techniques for bypassing firewalls that we've discussed, but there's also another super-common configuration we should know about. The typical Windows host blocks all ICMP packets - this means Nmap won't be able to properly register this kind of host. To get around this, we can use -Pn to tell it to not bother pinging the host before scanning. This means that Nmap considers all hosts alive; however, this can tkae a VERY long time to scan. Nmap can also use ARP requests to determine host activity IF you are already on the network.

Switches of note:
1. The `-f` switch fragments the packets into smaller pieces, making it less likely for them to be detected by a firewall or IDS.
2. The `--mtu <NUMBER>` switch provides more control over the size of the packets. This has to be a multiple of 8.
3. The `--scan-delay <TIME>ms` switch adds a delay between packets sent, which helps if the network is unstable and/or if the network uses time-based firewall/IDS triggers.
4. The `--badsum` switch generates invalid checksums for packets. A real TCP/IP stack would drop this packet, but firewalls can potentially respond automatically, without bothering to check the checksum. This can be used to determine the presence of firewalls/IDS.

**[Task 13, Question 1] Which simple (and frequently relied upon) protocol is often blocked, requiring the use of the `-Pn` switch? - ICMP**

Reading up on the Nmap documentation (copied below) should give you all the information you need for the next question.

**[Task 13, Question 2] (Research) Which Nmap switch allows you to append an arbitrary length of random data to the end of packets? --data-length**

### On Switches for Firewall Evasion with Nmap

Other switches include:
- `-D` to cloak a scan with decoys
- `-S` to spoof an IP address
- `-e` to change an interface
- `--source-port` to exploit a common misconfiguration - trusting traffic based on port number alone.
- `--data` to append custom binary data to sent packets. The switch requires a hex string as an argument.
- `--data-string` to append a custom string to a sent packet.
- `--data-length` to append random data to sent packets. Accepts a number.
- `--ip-options` to send packets with specified IP options.
- `--ttl` to set the IP time-to-live field
- `--randomize-hosts` to randomize the target host order
- `--spoof-mac` to spoof MAC addresses
- `--proxies` to relay TCP connections through a chain of proxies
- `--adler32` to use the deprecated Adler32 system instead of CRC32C for SCTP checksums.

## [Task 14] Practical

Alright! Let's scan the machine provided in the task.

First up, does it accept ping requests? We can check with `-ping <IP>`:

![image](https://github.com/abeeching/thm-stuff/assets/56741029/ecebfef8-7a79-490f-966b-2cc9439e5db5)

It's been like this for a while. Pretty sure the answer is no. (You can use CTRL+C to confirm that no packets have been received)

**[Task 14, Question 1] Does the target respond to ICMP (ping) requests (Y/N)? - N**

We can run `nmap -sX <IP> -p 1-999 -T5 -vv` to get some answers. According to the verbose output (which you should generally always use -- in this case, I used `-vv`), we see that all ports are open/filtered, since there was No Response:

![image](https://github.com/abeeching/thm-stuff/assets/56741029/dd58aada-6954-4bbc-87bf-e64fb22d8812)

**[Task 14, Question 2] Perform an Xmas scan on the first 999 ports of the target -- how many open ports are shown to be open or filtered? - 999**

**[Task 14, Question 3] There is a reason given for this -- what is it? (Note: The answer will be in your scan results. You have to think carefully about which switches to use!) - No Response**

After running `nmap -sT <IP> -p 1-5000 -T5`, we have:

![image](https://github.com/abeeching/thm-stuff/assets/56741029/15edcbd7-b4fb-4ffd-a714-a5c27f085fb9)

There are five open ports.

**[Task 14, Question 4] Perform a TCP SYN scan on the first 5000 ports of the target -- how many ports are shown to be open? - 5**

After applying the `--script=ftp-anon` switch to the command, we can see that anonymous FTP login is allowed, indeed. Here it shows that the directory listing couldn't be pulled since it timed out, but I'm going to assume this means Nmap was able to get in.

![image](https://github.com/abeeching/thm-stuff/assets/56741029/e2a6d70f-c90b-4226-a29a-e2c419a07d0d)

**[Task 14, Question 5] Deploy the `ftp-anon` script against the box. Can Nmap login successfully to the FTP server on port 21? (Y/N) - Y**

## Additional Resources

Simply put... check the Nmap documentation!! https://nmap.org/book/
