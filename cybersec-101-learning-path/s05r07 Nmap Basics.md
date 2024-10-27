# Nmap: The Basics

## [Task 1] Introduction

Say you're connected to a network, and what if you want to figure out what other devices on the network? If you are able to figure that out, how can you tell what's running on them?

One way to answer these questions is to use manual tools, like `ping` or `arp-scan`. Say we want to figure out what devices are live on the `192.168.0.1/24` network... we would have to analyze 254 addresses! That's a lot! Also, the tools mentioned earlier have limitations. The `ping` tool doesn't quite work if ICMP traffic is blocked, and `arp-scan` doesn't work if the device isn't connected to the same network.

We could also try using manual solutions and scripts, but those may not work so well, particularly if set up inefficiently. We could use `telnet` to try one port after the other, but there's around 65 thousand ports to try on one system, so that becomes inefficient _regardless_.

This is where `nmap` comes in - this is an open-source network scanner first published in 1997. It's powerful and flexible and can be adapted to various scenarios and setups.

You should be familiar with the TCP/IP model, basic networking concepts, and protocols.

## [Task 2] Host Discovery: Who is Online?

So, let's use Nmap to answer questions about what devices are online. It has some sophisticated ways to discover live hosts. Note that there are many ways to specify hosts in Nmap:
- You can use an IP range with `-`: For instance, if you wanted to scan all IPs from `192.168.0.1` to `192.168.0.10`, you would use `192.168.0.1-10` as your input into `nmap`.
- You can scan subnets with `/`: For instance, you could scan `192.168.0.1/24` to scan all IPs from `192.168.0.0` to `192.168.0.255`.
- You can specify hostnames instead of IPs altogether.

If you wanted to discover online hosts on a network, you can use the `-sn` option. This will act like a ping scan, without actually being limited like ping is.

Note that you should either be running Nmap as root or with sudo privileges, since otherwise Nmap will be limited in what it can do.

To scan a "local" network - one that you are directly connected to - you can run `nmap -sn (LOCAL_IP_OR_SUBNET)`. This allows you to get MAC addresses of devices, allowing you to figure out network card vendors. Nmap will scan with ARP requests. If a device responds, it'll mention that the "host is up."

If you want to scan a "remote" network - a network that is separated by at least one router from our system - we'll have to adopt a different approach since ARP requests won't work. You can still provide a remote IP or subnet, though, as above. The difference is that Nmap will shoot off ICMP echo/ping requests, ICMP timestamp requests, TCP packets with the SYN flag set, and TCP flags with the ACK flag set. Essentially, it tries many different options to see if a host is up. If you were to look at the communications in Wireshark, you may see "ICMP Destination Unreachable" packets if a host cannot be reached.

You do have more control over how Nmap discovers live hosts. This is out of the scope of the room, though you may want to examine the `-PS[portlist]`, `-PA[portlist]`, and `-PU[portlist]` options. These allow you to set options for TCP SYN, TCP ACK, and UDP discovery for given ports.

If you want to see what targets will be scanned when running a command _without actually scanning them_, you can use the `-sL` option to create a scan list. For instance, running `nmap -sL 192.168.0.1/24` will list the 256 targets that will be scanned, allowing you to confirm if these are the targets you want to scan.

The `-sn` scan is ultimately helpful if you want to discover devices on a network without causing noise. This doesn't give us a lot of information about what's on the system, though. You'll need to use more noisy options to get that information.

In this room, you'll have to make use of a VM and the AttackBox. You will be running all Nmap commands via the AttackBox.

For this first question, unless you know how to work around subnets well, you'll probably want to make use of the Nmap scan list option: `nmap -sL 192.168.0.1/27`.

![image](https://github.com/user-attachments/assets/e643006d-7df3-484a-8d41-97e616e1fdda)

The last address is the last one that will be scanned should we run the command.

**[Task 2, Question 1] What is the last IP address that will be scanned when your scan target is `192.168.0.1/27`?** - `192.168.0.31`

## [Task 3] Port Scanning: Who is Listening?

A simple procedure for scanning open TCP ports on a system is to try and directly connect to each port with a TCP protocol, such as Telnet. This would involve attempting to complete the TCP three-way handshake over each port; if the handshake completes correctly, then you know that the port is open. Nmap does something very similar, if you run it with the `-sT` command.

Nmap runs through the handshake on each TCP port with this option. If it succeeds, then Nmap sends a `RST-ACK` packet to close the connection. If Nmap _receives_ the `RST-ACK` packet from a server over a given port, it knows that the port must not be accepting connections (i.e., it is closed).

You can also run Nmap in stealth mode with the `-sS` flag. In this mode, Nmap attempts to send just a TCP SYN packet to each host without actually completing the three-way handshake. This should leads to fewer logs on the target host since the connection is never established. In terms of the types of packets sent, if a service responds to the SYN packet with a SYN-ACK packet, Nmap will simply send off an RST packet to close the connection and mark the service as being up. Otherwise, if a service sends an RST-ACK packet, then Nmap knows the port must be closed.

There are UDP ports to consider as well. Certain protocols operate over UDP, such as DNS, DHCP, the Network Time Protocol (NTP), the Simple Network Management Protocol (SNMP), and Voice over IP (VoIP). You do not need to establish a connection and close it afterwards in UDP, and it is often best for real-time communications.

You can use the `-sU` option to scan for UDP services. The only thing Nmap can tell for certain when running such a scan is whether the port is closed. If it is closed, then Nmap will receive an ICMP Destination Unreachable packet. It's possible for UDP ports to be filtered via firewalls, and Nmap cannot distinguish between these and actually open UDP ports, since there's no connection-establishment process.

Nmap will scan the top 1000 common ports by default, though there are other options you can employ if this isn't what you're looking for:
- `-F` will scan in Fast mode, looking at the top 100 common ports only.
- `-p[range]` lets you specify a range of ports to scan, e.g. `-p10-1024` to scan ports 10-1024.
- `-p-25` lets you scan all ports from 1 to 25. The `-p-` option on its own lets you scan all ports and is equivalent to scanning ports 1-65535. This is the best option if you're looking to be thorough.

Here are the important options covered in this task:
- `-sT` runs a TCP Connect scan and attempts to complete the three-way handshake over the target ports
- `-sS` runs a TCP SYN scan and only attempts the first step of the three-way handshake
- `-sU` runs a UDP scan
- `-F` runs a scan in fast mode, only scanning the top 100 common ports
- `-p[range]` specifies a range of port numbers to scan, with `-p-` allowing you to scan all possible ports

Let's try these in the AttackBox. First we want to figure out how many TCP ports are open on the target system we started in Task 2. To do so, we run `nmap -sT (MACHINE_IP)` or `nmap -sS (MACHINE_IP)`, whichever you prefer. Running either will give you something like this as output:

![image](https://github.com/user-attachments/assets/869b2ba5-e9a6-4c54-ad8f-3e67b76b9d67)

You'll notice that there are six ports open.

**[Task 3, Question 1] How many TCP ports are open on the target system at `(YOUR_MACHINE_IP)`?** - 6

You'll notice that one of these is an HTTP server - port 8008. You can actually navigate to this on your AttackBox! Simply go to `http://(MACHINE_IP):8008/` in the browser. You should see a flag:

![image](https://github.com/user-attachments/assets/64f31f62-756e-473d-93d1-84df4d02eae5)

**[Task 3, Question 2] Find the listening web server on `10.10.67.125` and access it with your browser. What is the flag that appears on the main page?** - `THM{SECRET_PAGE_38B9P6}`

## [Task 4] Version Detection: Extract More Information

There are some options we can use if we wish to learn more about the system than just what ports are open.
- The `-O` option can be used to perform OS detection, which uses various indicators to guess what system is running on the target. This is not going to be 100% accurate though!
- The `-sV` option can be used to perform service/version detection for the ports. This lets us get more information about what services are running, e.g. what version of what software is being used to provide the service.
- `-A` can be used to combine both the options above into one convenient little option. This also provides traceroute output, among others.

If the target host does not reply during the host discovery phase (perhaps because ICMP packets won't get a response from the target, among other things), then Nmap will mark the host as down and it won't launch a port scan against it. The `-Pn` option trets all hosts as online and port scans every host, including those that didn't respond during host discovery.

We'll practice with some of these options. Namely, for this question, we'll want to run an Nmap scan with the `-sV` option to see what version of the web server is being run. Running `nmap -sT -sV (YOUR_MACHINE_IP)` should give results, though you may append the other options as you wish. This may take a while to do, but eventually you'll get this:

![image](https://github.com/user-attachments/assets/de1bbe82-589c-412c-9f9d-7e501e4d9f23)

**[Task 4, Question 1] What is the name and detected version of the web server running on `10.109.67.125`?** - `lighttpd 1.4.74`

## [Task 5] Timing: How Fast is Fast?

Nmap allows you to control the scan speed and timing; running at a normal speed can trip an intrusion detection system (IDS) or other security solution. So, you would want to adjust how fast the scans go. Nmap lets you do this by specifying a number or a keyword/name. The timing template can be chosen by using `-T(NUMBER)` or `-T (NAME)`, e.g. `-T0` or `-T paranoid`. The following timing options are available, and the room provides some guidance on how long each scan took on their lab setup. The scans were run with the `-sS` and `-F` flags. Your results may vary:
- T0, paranoid: Takes roughly 9.8 hours
- T1, sneaky: Takes roughly 27.53 minutes
- T2, polite: Takes roughly 40.56 seconds
- T3, normal: Takes roughly 0.15 seconds
- T4, aggressive: Takes roughly 0.13 seconds
- T5, insane: Wasn't actually tested but probably takes even less time. Be wary that this is most likely going to get picked up by an IDS.

Nmap runs at normal/T3 speed by default.

You may want to adjust the number of parallel service probes via `--min-parallelism (NUMBER_OF_PROBES)` and `--max-parallelism (NUMBER_OF_PROBES)`. This gives you the ability ots et the minimum and maximum number of TCP/UDP port probes active simultaneously for a host group. Nmap automaticaly controls these numbers, so if the network performs worse it may choose to only use one; if the network performs better, it may choose to use hundreds.

Similarly, you may want to use `--min-rate (PACKET_RATE)` and `--max-rate (PACKET_RATE)` to control the minimum and maximum rate at which Nmap sends packets - this is calculated as packets per second. The specified rate applies to the whole scan and not just a single host.

Lastly, you can use `--host-timeout (TIME)`. This gives you the maximum time you are willing to wait, and it's suitable for slower hosts or hosts with slow network connections.

To summarize:
- `-T(0-5)` lets you control the speed of the scan, with 0 being the slowest and 5 being the fastest.
- `--min-parallelism (NUMBER_OF_PROBES)` and `--max-parallelism (NUMBER_OF_PROBES)` gives the minimum and maximum number of parallel probes
- `--min-rate (NUMBER)` and `--max-rate (NUMBER_OF_PROBES)` gives the minimum and maximum rate in packets per second
- `--host-timeout` gives the maximum amount of time to wait on a target host

**[Task 5, Question 1] What is the non-numeric equivalent of `-T4`?** - `-T aggressive`

## [Task 6] Output: Controlling What You See

Sometimes the scan can take a very long time to finish, and you won't even see any output produced on the screen for a while. You might also want to know what's going on while a scan is in progress. You can get updates about the scan by running Nmap with the `-v` flag. The amount of details can be pretty useful, including information about what stage of the scan Nmap is on. Typically `-v` will be enough for verbosity, though you can run with `-vv` and `-vvvv` to increase the verbosity for more details. You can also run with `-v2` and `-v4` to achieve the same effect, or even press `v` in the middle of a scan to increase verbosity mid-scan.

If you need even more information, you can use `-d` for debugging output. You can increase the debugging level by adding `d`'s or specifying the debug level directly as above. You can go up to `-d9`, at which point you will literally get thousands of lines of output and information.

There are a few different ways to save scan results. Some of the most useful options include
- `-oN (FILENAME)` for normal output (human-friendly)
- `-oX (FILENAME)` for XML output
- `-oG (FILENAME)` for `grep`-able output, which is useful for `grep` and `awk`
- `-oA (BASENAME_FOR_ALL_FILES)` for output in all major formats.

**[Task 6, Question 1] What option must you add to your `nmap` command to enable debugging?** - `-d`

## [Task 7] Conclusion and Summary

Nmap is a useful tool to have to discover live hosts on a network, as well as to identify open services and their versions. You can control how fast Nmap operates and how results get output/saved to the computer. You should try to run Nmap with `sudo` privileges where you can; otherwise you will be limited to connect scans as a local user. Crafting certain packets, like the TCP SYN packet that is required for SYN scans, requires root privileges.

There are multiple rooms in TryHackMe dedicated to Nmap, so we're really only scratching the surface here. These are the most important options covered in this room:
- `-sL` to list targets without scanning
- `-sn` to perform a ping scan and discover hosts
- `-sT` to perform a TCP connect scan, which completes the handshake
- `-sS` to perform a SYN/stealth scan, which only does the first step of the handshake
- `-sU` to perform a UDP scan
- `-F` to run Nmap in fast mode, only scanning the top 100 common ports
- `-p[range]` to scan a range of port numbers, with `-p-` being used to specify all ports
- `-Pn` to treat all hosts as online and scan those that appear to be down
- `-O` to perform OS detection
- `-sV` to perform service version detection
- `-A` to do OS, service version detection among other things
- `-T(0-5)` to change the timing/speed of the scan, with 0 being the slowest and 5 being the fastest
- `--min-` and `--max-parallelism (NUMBER_OF_PROBES)` to specify the number of probes used in parallel
- `--min-` and `--max-rate (NUMBER)` to specify the rate of packets per second sent
-  `--host-timeout` to specify the maximum amount of time to wait on a target host
-  `-v` to increase verbosity level; add more v's to increase verbosity
-  `-d` to increase debugging level; add more d's to increase debug output
-  `-oN (FILENAME)` to save results in normal, human-friendly output
-  `-oX (FILENAME)` to save results in XML output
-  `-oG (FILENAME)` to save results in grep-able output
-  `-oA (BASENAME_FOR_ALL_FILES)` to save results in all major formats

**[Task 7, Question 1] What kind of scan wil Nmap use if you run `nmap (MACHINE_IP)` with local user privileges?** - Connect Scan
