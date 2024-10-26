# Tcpdump: The Basics

## [Task 1] Introduction

In studying network protocols, you don't often get to see the actual conversations taking place. All of the complexity is hidden behind friendly and elegant user interfaces. You don't need to see or interact with an ARP query to access resources, and you don't need to access Internet services without seeing the three-way handshake. If you want to better understand how networks work, you'd be better off using a tool like Wireshark or tcpdump.

Tcpdump, and the libpcap library, were released for Unix systems in the 80s and 90s, and were stable and optimal for capturing packets, filtering, and displaying packets. libpcap has become a cornerstone of many networking tools today, and has even been ported to Windows under `winpcap`. You should be familiar with the basics of networking by this point - see the Networking Concepts, Essentials, Core ProtocolS, and Secure Protocol rooms if not. You will need to follow along in the attached VM.

**[Task 1, Question 1] What is the name of the library that is associated with tcpdump?** - libpcap

## [Task 2] Basic Packet Capture

It's possible to run `tcpdump` without providing arguments, though this is used just to make sure that it's installed. In a real scenario, you need to tell it what you want to listen to, what to write, and what to display.

To decide what network interface to listen on, you'll want to use `-i (INTERFACE)`, where `(INTERFACE)` could be `any` or a specific network interface, such as `eth0`. If you want to see a list of network interfaces, run `ip address show` or `ip a s`.

When capturing packets, you're usually doing so in order to check them again later. To save them, you want to provide the argument `-w (FILE)`, where `(FILE)` is typically a filename ending with the `.pcap` extension. Saved packets can then be inspect later, and you can even do so using another tool such as Wireshark. This optiohn does not let you see the packets scroll on by as they come through.

To read captured packets from a file, provide the argument `-r (FILE)`. This lets you learn about protocol behavior. You can capture network traffic over time to inspect a specific protocol, and then read the file while applying filters to display whatever packets you might be interested in. Packet captures can contain evidence of an attack taking place, so understanding how to read and filter through packets is key.

To limit the number of packets you want to capture, specify the count by using `-c (COUNT)`. Otherwise, `tcpdump` will continue capturing packets until you stop it via CTRL+C. YOu may well only need a limited number of packets.

`tcpdump` will resolve IP addresses and print friendly domain names when possible. You might not want these lookups to happen, though; sometimes you want to see IP addresses. In this case, run with the `-n` argument. Similarly, if you don't even want port numbers to be resolved to their actual names (e.g. `http` instead of `80`), run with the `-nn` argument to resolve neither IP addresses nor port numbers.

If you need more details about packets, use `-v` to produce verbose output. You can go further and get more output by running with the `-vv` argument, and _even more output_ if you run with `-vvv`.

In summary, these are the arguments that you can use with `tcpdump`:
- `tcpdump -i (INTERFACE)` to capture packets over a specific interface
- `tcpdump -w (FILE)` to write captured packets to a file
- `tcpdump -r (FILE)` to read a packet capture
- `tcpdump -c (COUNT)` to capcutre a number of packets
- `tcpdump -n` to display IP addresses without resolving them, i.e. in numerical form
- `tcpdump -nn` to display IP addresses and port numbers without resolving them
- `tcpdump -v` to provide verbose output, with increased verbosity provided in `-vv` and `-vvv`

Of course, these may be combined as needed. Note that to capture packets you must either be logged in as `root` or use `sudo`.

**[Task 2, Question 1] What option can you add to your command to display addresses only in numeric format?** - `-n`

## [Task 3] Filtering Expressions

You can run `tcpdump` without providing filtering expressions, but this makes the output way too cumbersome to read. So we'll want to figure out something specific to look for, and look only for that.

If you want IP packets that have only been exchanged with a specific server or device, you can use the `host (IP)` or `host (HOSTNAME)` filters. You can apply this filter to saving packets and reading them. If you want to limit packets to those coming from a particular source, use `src host (IP_OR_HOSTNAME)`. Simialrly, you can limit packets coming to a particular destination with `dst host (IP_OR_HOSTNAME)`.

If you want to filter by port, you can use `port (PORT_NUMBER)`. You can also specify source/destination ports with `src port (PORT_NUMBER)` and `dst port (PORT_NUMBER)`. You can use this to filter for specific protocols as long as you know the port number (hopefully you remembered them from the protocols rooms).

`tcpdump` allows you specify certain protocols to filter by, including `ip`, `ip6`, `udp`, `tcp`, `udp` and `icmp`. You can just type those as filters, e.g. `tcpdump -r (FILE) icmp` will grab only the ICMP packets in the capture file.

There are three logical operators that may be helpful to remember:
- `and`: Captures packets where both conditions are true, e.g. `tcpdump host (IP) and tcp` captures TCP traffic with a given IP.
- `or`: Captures packets where either condition is true, e.g. `tcpdump udp or icmp` will grab UDP or ICMP traffic.
- `not`: Captures packets when the condition is not true, e.g. `tcpdump not tcp` captures all traffic that isn't TCP-based.

Here's a summary of what we learned in this section:
- `tcpdump host (IP_OR_HOSTNAME)` filters packets by an IP address or hostname
- `tcpdump src host (IP_OR_HOSTNAME)` filters packets by a specific source host
- `tcpdump dst host (IP_OR_HOSTNAME)` filters packets by a specific destination host
- `tcpdump port (PORT_NUMBER)` filters packets by a port number
- `tcpdump src port (PORT_NUMBER)` filters packets by a source port unmber
- `tcpdump dst port (PORT_NUMBER)` fitlers packets by a destination port number
- `tcpdump (PROTOCOL)` filters packets by certain protocols, such as `ip` and `icmp`.

For this room, we'll be reading packets from the `traffic.pcap` file in the VM. Note that we can apply the filters from the previous task here, e.g. `tcpdump -r traffic.pcap -c 5 -n` will display the first five packets in the file without resolving IP addresses.

You can count lines by piping the output to `wc`. I prefer using `wc -l` since each line is a packet, and using that particular argument only lets me see how many lines there are in the output. For instance, `tcpdump -r traffic.pcap src host 192.168.124.1 -n | wc -l` should just output 910. You should use `-n` as needed to prevent delays in attempting to resolve IP addresses, and you won't need to use `sudo` to read a packet capture file.

Here's another example: `tcpdump -r traffic.pcap icmp -n | wc -l` gives us how many ICMP packets there are:

![image](https://github.com/user-attachments/assets/25ff08e1-b54c-46b1-8fc4-07c3a25fb16c)

**[Task 3, Question 1] How many packets in `traffic.pcap` use the ICMP protocol?** - 26

The next quetsion wants the IP address of the host that asked for the MAC address of `192.168.124.137`. This will require examining the `arp` protocol output. We can look for packets involving the host `192.168.124.137` - one of these packets should be a host _asking_ for this IP address. Thus, the command `tcpdump -r traffic.pcap arp and host 192.168.124.137 -n` will do us some good:

![image](https://github.com/user-attachments/assets/66872649-2797-48bc-aa6b-4e1d8581a978)

**[Task 3, Question 2] What is the IP address of the host that asked for the MAC address of `192.168.124.137`?** - `192.168.124.148`

We need to find the hostname/domain that appears in the first DNS query. To do so, we can use the `port 53` filter, and to only grab the first DNS query we can use the `-c 1` argument. Hence our command will be `tcpdump -r traffic.pcap port 53 -c 1 -n`:

![image](https://github.com/user-attachments/assets/66727b9d-0a7c-4131-b8e4-a0ebc14bcd22)

The requested domain is given at the end of the packet.

**[Task 3, Question 3] What hostname (subdomain) appears in the first DNS query?** - `mirrors.rockylinux.org`

## [Task 4] Advanced Filtering

There are more ways to filter packets, which can be helpful when dealing with the immense amount of packets usually present in a packet capture. If we wanted to limit the displayed packets to those larger or smaller than a given length, we would use `greater (LENGTH)` or `less (LENGTH)`. If you want more information, you can use `man pcap-filter` to look at the possible filters. We'll focus on filtering by TCP flags in this particular room.

It's worth first discussing binary operations, which are operations that work on bits (the zeroes and ones). An operation will take one or two bits and return a bit. Generally, these include
- `and (&)` which takes two bits and returns 0 unless both inputs are 1
- `or (|)` which takes two bits and returns 1 unless both inputs are 0
- `not (!)` which takes one bit and inverts it; a 0 becomes a 1 and a 1 becomes a 0.

Now let's talk about how to filter packets based on the contents of a header bytes - sometimes this will be necessary; maybe we need to find packets with certain TCP flags set, as an example.

To refer to the contents of any byte in a header, use `proto[expr:size]`.
- `proto` refers to the protoocl, such as `arp`, `ether` (for Ethernet), `icmp`, `ip`, `ip6`, `tcp`, and `udp`.
- `expr` indicates the byte offset. If set to `0`, this is the first byte.
- `size` indicates the number of bytes that interest us, which can be 1, 2, or 4. By default, it is `1`.

Here are some examples from the man pages:
- `ether[0] & 1 != 0` takes the first byte in the Ethernet header and the number 1 (0000 0001) and applies the `&` operation. It returns true if the result isn't 0 - that is, `ether[0] = 1`. This helps show packets sent to a multicast Ethernet address, which identifies a group of devices intended to receive the same data.
- `ip[0] & 0xf != 5` takes the first byte in the IP header and compares ita gainst the hexadecimal number `F` (0000 1111). It returns true if the result is not equal to 5, or `0000 0101`. This captures all IP packets with options.

These are more complex examples primarily included so you know that this feature exists - you don't need to use these to finish this task. Our focus will be on filtering TCP packets based on TCP flags, which has a pretty convenient notation in `tcpdump`: `tcp[tcpflags]`. These are the flags we can search for.
- `tcp-syn` for SYN/synchronize packets
- `tcp-ack` for ACK/acknowledge packets
- `tcp-fin` for FIN/finish packets
- `tcp-rst` for RST/reset packets
- `tcp-push` for PUSH packets

To search for TCP flags, use `tcp[tcpflags] == (TCP_FLAG)`. You can use binary operations, e.g. `tcpdump tcp[tcpflags] & tcp-syn != 0` captures only TCP packets with at least the SYN/synchronize flag set. You can also use `tcpdump "tcp[tcpflags] & (tcp-syn|tcp-ack) != 0"` to look for TCP packets with at least the SYN/synchronize or ACK/acknowledge flags set. Note that multiple flags may be set in a TCP packet as needed.

Let's try it - if we wanted to find the number of TCP Reset packets, we could run `tcpdump -r traffic.pcap "tcp[tcpflags] == tcp-rst" -n | wc -l`.

![image](https://github.com/user-attachments/assets/3ae4fb3d-a2b4-4da8-a807-73daf84fa067)

**[Task 4, Question 1] How many packets have only the TCP Reset (RST) flag set?** - 57

And if we want to find hosts that have sent packets larger than 15000 bytes, we should use `tcpdump -r traffic.pcap greater 15000 -c 5 -n` to take a look at some of the relevant packets out there.

![image](https://github.com/user-attachments/assets/928e6391-a605-41a9-b9cd-2cfbe4572b12)

In the output above, the first IP address is the source, and there's only one source in this filtered output...

**[Task 4, Question 2] What is the IP address of the host that sent packets larger than 15000 bytes?** - `185.117.80.53`

## [Task 5] Displaying Packets

There are five options that we can use to customize how packets are printed and displayed:
- `-q` can be used to display quick output and brief packet information. This shows the timestamp, along with the source/destination IP address and source/destination port numbers.
- `-e` prints the link-level header. This includes MAC adresses in the output and can be useful in examining ARP/DHCP functionality, as well as any unusual packets.
- `-A` shows packet data in ASCII. ASCII is used to represent text, so you can see all the bytes mapped to English letters, numbers, and symbols.
- `-xx` shows packet data in hexadecimal/hex format. If contents have undergone encryption or compression, this is likely how you'll want to print data; it works regardless of the format of the data.
- `-X` shows packet headers and data in hex and ACSII. This is the best of both worlds.

Let's try it on the `traffic.pcap` file. If we want to know what MAC address was involved in sending ARP requests, we can use `tcpdump -r traffic.pcap arp -e -n`.

![image](https://github.com/user-attachments/assets/75f0ea67-b8c4-43b7-b47f-6f1fbadf4d4a)

The first packet is what we're looking for - this is the device requesting a MAC address over ARP.

**[Task 5, Question 1] What is the MAC address of the host that sent an ARP request?** - `52:54:00:7c:d3:5b`

## [Task 6] Conclusion

And that'll do it for `tcpdump`. Along with Wireshark and Tshark (also covered in the SOC 1 path), `tcpdump` is good for understanding what's going on with networking protocols. It can be very powerful for sifting through many packets, though there are plenty of other options that haven't been covered here. It's worth looking into!
