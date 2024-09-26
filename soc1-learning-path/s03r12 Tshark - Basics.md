# Tshark: The Basics

## [Task 1] Introduction

TShark is an open-source traffic analyzer and is accessed exclusively through the command line. It's made by the same folks who put together Wireshark, and so this tool has most of the features of that program. It can also be used like tcpdump.

## [Task 2] Command-Line Packet Analysis Hints | TShark and Supplemental CLI Tools

TShark is a text-based tool useful for data carving, in-depth packet analysis, and automation. This is partly thanks to the fact that you can apply different CLI tools to the output, and pipe output to other utilities. We've discussed most of these tools in previous rooms in this section, but we'll briefly review them here:
- `capinfos` is a program that can be used to get the details for a capture file. You can use this to get a summary of file before starting an investigation.
- `grep` is used to search for plaintext data.
- `cut` is used to cut parts of lines out from a specific data source.
- `uniq` filters out repeated lines and values
- `nl` helps you view the number of shown lines
- `sed` is a stream editor that allows you to parse and transform text
- `awk` is a scripting language used with pattern searching and processing

**[Task 2, Question 1] View the details of the demo.pcapng file with "capinfos." What is the RIPEMD160 value?** - `6ef5f0c165a1db4a3cad3116b0c5bcc0cf6b9ab7`

## [Task 3] TShark Fundamentals I | Main Parameters I

There are some built-in options and parameters that you need to be able to use to control the output and not be flooded with information. Here are some common parameters.
- The `-h` flag displays the help page with the most common features.
- The `-v` flag displays version information.
- The `-D` flag displays available sniffing interfaces.
- The `-i` flag allows you to select an interface to capture live traffic on.
- If you run `tshark` with no flags, it sniffs traffic like `tcpdump` would.

Tshark does let you sniff traffic over a chosen interface. The sniffing functionality requires superuser privileges. To view available interfaces, use `sudo tshark -D`. Then, if you want to use tshark over an interface, run `tshark -i (INTERFACE NUMBER)`. If you don't provide an interface number, Tshark will use the first available interface.

**[Task 3, Question 1] What is the installed TShark version in the given VM?** - 3.2.3

**[Task 3, Question 2] List the available interfaces with TShark. What is the number of available interfaces in the given VM?** - 12

## [Task 4] TShark Fundamentals I | Main Parameters II

There are some additional parameters to keep in mind:
- `-r` lets you read a capture file: `tshark -r (PCAP)`
- `-c` will run tshark until a certain number of packets has been captured, filtered, or read: `tshark -c (NUMBER)`.
- `-w` will write the sniffed traffic to a file: `tshark -w (PCAP FILE TO WRITE TO)`
- `-V` will run tshark in verbose mode, providing information for each packet, not unlike Wirshark's packet details pane.
- `-q` will run tshark in silent mode, suppressing packet output in the terminal.
- `-x` will display the packet bytes, showing packet details in hex and ASCII for each packet.

We use the above parameters for the following use cases:
- For reading capture files, we use `-r` and perhaps `-c`  to limit the packets displayed.
- To write sniffed or filtered packets to a file, we'd use `-w`. This may be helpful if we want to separate specific packets from the file/traffic for further analysis or for sharing with upper-level investigators.
- We may need to get more information about a packet, namely what's found in the actual data. It helps to use `-x`, though it may be be best to reduce the number of packets under consideration first.
- You may also want to get more verbose output to see more details for each packet. This can be done with `-v`. Since there's a lot of information in these packets, you should try to use the option for a specific packet. Verbosity is best applied once you've filtered the packets of interest.

**[Task 4, Question 1] Read the "demo.pcapng" file with TShark. What are the assigned TCP flags in the 29th packet?** - PSH, ACK

**[Task 4, Question 2] What is the ACK value of the 25th packet?** - 12421

**[Task 4, Question 3] What is the "Window size value" of the 9th packet?** - 9660

## [Task 5] TShark Fundamentals II | Capture Conditions

We can configure tshark to count packets and stop at specific point, or we can run it in a loop. Here are some common parameters.

The purpose of the `-a` parameter is to define capture conditions for a single run or loop. It stops after completing the condition (aka auto-stopping):
- We can specify a duration by using `-a duration:(SECONDS)`. After x seconds pass, tshark stops sniffing.
- We can specify the maximum filesize for a capture file with `-a filesize:(KB)`. Once the file is x KB, sniffing stops.
- We can specify the maximum number of output files with `-a files:(FILES)`. This can be used in conjunction with the filesize parameter to generate a certain number of equally-sized capture files.

Then we have a few options for ring buffer control via `-b` - this is effectively the capture conditions for multiple runs or infinite loops:
- We can specify a duration again with `-b duration:(SECONDS)`. Output is written to the file every X seconds.
- We can specify a maximum capture file size with `-b filesize:(KB)`, which creates a new file and writes output to it after reaching a certain filesize.
- We can specify a maximum number of input files with `-b files:(FILES)`. Once x files have been created, the first or oldest file is rewritten.

These parameters only work in the capturing/sniffing mode. Attempting to use these when you're reading a packet capture with `-r` will throw an error. You should save capture files in specific sizes for different purposes. If you're trying to extract packets from a specific capture file, you should use the techniques discussed in the previous task.

As a heads-up, you can use both `-a` and `-b` parameters; however, there must be one `-a` (or auto-stop) parameter present, unless you want tshark to run indefinitely.

**[Task 5, Question 1] Which parameter can help analysts create a continuous capture dump?** - `-b`

**[Task 5, Question 2] Can we combine autostop and ring buffer parameters with TShark?** - y

## [Task 6] TShark Fundamentals III | Packet Filtering Options: Capture vs. Display Filters

TShark allows you to filter packets caught live and to filter packets after they are captured. These can be combined with a predefined syntax or the Berkeley Packet Filter (BPF) syntax for filtering. Tshark supports both. Like Wireshark, tshark can handle filters for capturing and filtering packets:
- Capture Filters: Live filtering options that limit what, out of the traffic, gets saved. It is set before capturing traffic and unchangeable once capture begins.
- Display Filters: Post-capture filtering, aimed at helping with investigations by reducing the number of displayed packets. This can be changed during investigation.

Capture filters have limited usability for filtering, and they're just there to help limit the scope of traffic and ensuing investigations. This results in capture files with a decent filesize. Display filters, on the other hand, are used to investigate capture files without needing to modify anything about the packet. For a further review of packet filtering principles, see the Wireshark Packet Operations room.

The parameters for the different filtering types are:
- `-f` for capture filters, which can be specified with BPF and Wireshark capture filters.
- `-Y` for display filters, which can be specified with Wireshark display filters.

**[Task 6, Question 1] Which parameter is used to set Capture Filters?** - `-f`

**[Task 6, Question 2] Which parameter is used to set Display Filters?** - `-Y`

## [Task 7] TShark Fundamentals IV | Packet Filtering Options: Capture Filters

The basic syntax for capture/BPF filters is as follows (with additional information available in Wireshark's documentation). Note that Boolean operators can be used in this kind of filter (as with display filters).
- Type qualifiers: The target match type. You can filter IP addresses, hostnames, IP ranges, and port numbers. If you don't set a qualifier, the host qualifier is used by default.
  - The qualifiers are `host`, `net`, `port`, and `portrange`.
  - To filter a host, use `-f "host (IP)`.
  - To filter a network range, use `-f "net (CIDR)"`.
  - To filter a port, use `-f "port (PORT)"`.
  - To filter a port range, use `-f "portrange (STARTING PORT)-(ENDING PORT)"`.
- Direction qualifiers: Helps define target direction or flow. If you don't use a direction qualifier, bidirectional communication is assumed.
  - The qualifiers are `src` and `dst`.
  - To filter for a source address, use `-f "src host (IP)"`.
  - To filter for a destination address, use `-f "dst host (MAC)"`.
- To filter for specific protocols...
  - Use the qualifiers `arp`, `ether`, `icmp`, `ip`, `ip6`, `tcp`, or `udp`.
  - To filter for TCP, use `-f "tcp"`.
  - To filter MAC addresses, use `-f "ether host (MAC)"`.
  - You can also filter protocols with IP numbers assigned by IANA. For instance, to filter ICMP, you may want to use `-f "ip proto 1"`. A list of protoocl numbers can be found on IANA's website.



**[Task 7, Question 1] What is the number of packets with SYN bytes?** - 2

**[Task 7, Question 2] What is the number of packets sent to the IP address `10.10.10.10`?** - 7

**[Task 7, Question 3] What is the number of packets with ACK bytes?** - 8

## [Task 8] TShark Fundamentals V | Packet Filtering Options: Display Filters

**[Task 8, Question 1] What is the number of packets with a `65.208.228.223` IP address?** - 34

**[Task 8, Question 2] What is the number of packets with a TCP port 3371?** - 7

**[Task 8, Question 3] What is the number of packets with a `145.254.160.237` IP address as a source address?** - 20

**[Task 8, Question 4] Rerun the previous query and look at the output. What is the packet number of the "duplicate" packet?** - 37
