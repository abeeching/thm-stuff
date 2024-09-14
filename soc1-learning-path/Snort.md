# Snort (introduction)

## [Task 1] Introduction

Snort is an open-source rule-based network intrusion detection and prevention system (a NIDS or a NIPS). Rules are used to define malicious network activities and to match packets against them and generate alerts.

## [Task 2] Interactive Material and VM

The exercises are set up in a split-screen virtual machine on TryHackMe. The Task-Exercises folder contains the relevant files for each task. Two subfolders are provided:
- Config-Sample: Contains sample configuration and rule files, provided to show what the config files look like. Feel free to modify them.
- Exercise-Files: Contains separate folders for each task, each folder containing pcaps and log/rule files.

There is a script on the machine, traffic-generator.sh, that allows you to generate traffic to your snort interface. This script triggers traffic for the snort interface, and it will allow you to choose the exercise type and then automatically open another terminal to show you the output of the selected action.

When using this script, you start the snort instance and wait until the end of the script execution - don't stop the traffic unless you choose the wrong exercise. You run the script by executing it as sudo.

The exercise in this task is simply there to make sure you know where the relevant files are for this room.

**[Task 2, Question 1] Navigate to the Task-Exercises folder and run the command "./.easy.sh" and write the output** - Too Easy!

## [Task 3] Introduction to IDS/IPS

An intrusion detection system (IDS) is a _passive_ monitoring solution; it is only designed to detect possible malicious activities, abnormal patterns, and more. When these are detected, an alert is generated. The two types are IDS systems are:
- Network IDS (NIDS): Monitors traffic flow in the network, investigating the traffic on the subnet or local network.
- Host IDS (HIDS): Monitors traffic flow in a single endpoint, investigating the traffic to that particular device.

An intrusion prevention system (IPS) is an _active_ protection solution, designed to prevent possible malicious activities and abnormal behavior from happening. It stops or terminates the suspicious event once it is detected. There are multiple types of IPS systems:
- Network IPS (NIPS): Monitors traffic flow in the network, protecting traffic on the entire subnet or local network.
- Behavior-based IPS, or Network Behavior Analysis (NBA): Serves a similar function as a NIPS, but requires a training period ("baselining") to learn the difference between normal and malicious traffic. Making sure the baselining process is secure is critical.
- Wireless IPS (WIPS): Monitors traffic flow in a wireless network, protecting the wireless traffic.
- Host IPS (HIPS): Protects the traffic flow from a single endpoint device.

There are a few main techniques used in IPS and IDS solutions.
- Signature-based: Relies on rules that identify specific patterns of known behavior- good for detecting known threats
- Behavior-based: Identifies new threats with new patterns, comparing the known with the unknown- good for new threats
- Policy-based: Compare detected activities with system configuration and security policies- good for policy violations

In short, an IDS _detects_ threats but doesn't stop them, whereas an IPS _stops_ threats actively. Snort can serve as a NIDS, but it may also be deployed inline as a NIPS to stop malicious packets. It has the following modes of operation:
- Sniffer: Read IP packets, display them in console
- Packet logger: Log all IP packets - inbound and outbound - that are in the network
- NIDS and NIPS: Log and drop packets deemed as malicious, according to user rules.

The questions can be answered by reviewing the information provided in the room (which is summarized above).

**[Task 3, Question 1] Which snort mode can help you stop the threats on a local machine?** - HIPS

**[Task 3, Question 2] Whcih snort mode can help you detect threats on a local network?** - NIDS

**[Task 3, Question 3] Which snort mode can help you detect the threats on a local machine?** - HIDS

**[Task 3, Question 4] Which snort mode can help you stop the threats on a local network?** - NIPS

**[Task 3, Question 5] Which snort mode works similar to NIPS mode?** - NBA

The official description is provided in the room, or you can check the offical Snort website.

**[Task 3, Question 6] According to the official description of snort, what kind of NIPS is it?** - full-blown

**[Task 3, Question 7] NBA training period is also known as...** - baselining

## [Task 4] First Interaction with Snort

We can verify that snort is installed by running some simple commands, like `snort -V`. This outputs the instance version. You should take the time to ensure the configuration file is valid; this can be done with the command `sudo snort -c /etc/snort/snort.conf -T`. This tests the default configuration file, but it can be used with other configuration files too.

The configuration file is an all-in-one management file for snort, defining rules, plugins, detection mechanisms, default actions, and more. Multiple configuration files can be set up for different use cases, but one is typically used at runtime.

Every time you start Snort, it will show the default banner and initial information about the setup. This can be hidden if you use the `-q` flag to start Snort in quiet mode.

Running `snort -V` in the virtual machine provides the following output:

![image](https://github.com/user-attachments/assets/6831bf05-49e6-4746-8343-6b1ef54f40f6)


**[Task 4, Question 1] Run the Snort instance and check the build number.** - 149

Running the command `sudo snort -c /etc/snort/snort.conf -T` gives us a lot of output, but if we look through it we'll find that 4151 rules were loaded:

![image](https://github.com/user-attachments/assets/d07c17b9-a134-4e7c-b391-b4b6d29acf3d)


**[Task 4, Question 2] Test the current instance with "/etc/snort/snort.conf" file and check how many rules are loaded with the current build.** - 4151

Running the command `sudo snort -c /etc/snort/snortv2.conf -T` gives us the following output.

![image](https://github.com/user-attachments/assets/21636599-ba9e-418c-b0a7-266b7d6e1b69)


**[Task 4, Question 3] Test the current instance with "/etc/snort/snortv2.conf" file and check how many rules are loaded with the current build.** - 1

## [Task 5] Operation Mode 1: Sniffer Mode

Snort can display different information about the packet(s) that are being ingested. The parameters/flags for this mode are as follows:
- `-v` runs Snort in verbose mode, displaying TCP/IP output in the console.
- `-d` displays the packet data, or the payload.
- `-e` displays the link layer (TCP/IP/UDP/ICMP) headers.
- `-X` displays the packet details in hex and provides a full packet dump.
- `-i` defines a specific network interface to listen or sniff on.

The `traffic-generator` script is used in this task.

We can use `sudo snort -v -i eth0` to sniff packets on the "eth0" interface in verbose mode. If no interface is specified, eth0 is used by default. I generated some ICMP traffic and it appears to have been picked up by Snort:

![image](https://github.com/user-attachments/assets/22fcbda2-406e-4827-9aa5-c3fe8e4b6643)


Snort will continue sniffing packets until you press CTRL+C to stop it.

When run with `-d`, you can see the payload:

![image](https://github.com/user-attachments/assets/79cad0f1-a2ff-4ce8-b93f-aee9ac0d0f15)


And we can combine parameters to produce more information, e.g. if we run snort with `-de`, we can get link layer headers and payload information. The `-X` parameter will provide a full packet dump.

## [Task 6] Operation Mode 2: Packet Logger Mode

We can log sniffed packets with the packet logger mode. There are various parameters that can be used to help with this:
- `-l` runs Snort in logger mode, and will send logs to an output specified by the user. By default, the logs are dumped in tcpdump format in `/var/log/snort`.
- `-K ASCII` logs packets in ASCII format.
- `-r` is the reading option, which allows a user to read dumped logs in Snort.
- `-n` allows you to specify the number of packets to process or read.

Since Snort needs superuser/root rights to sniff traffic, requiring that you run it with sudo, the _root_ account will be the owner of any log files generated. You will have to use sudo to read the resulting logs or change the ownership of the files and directories by running `chown`.

To start Snort in packet logger mode, you can run `sudo snort -dev -l .`. Snort shows the packets and logs them in the target directory, which we set to `.` -- our current working directory. The resulting logs from this command will be in tcpdump binary format.

Running the command `sudo snort -dev -K ASCII` will show traffic in verbosity mode, and it will generate logs based on IP addresses and certain categories. These logs will all be text files, so you can read them without using Snort.

What we'll be doing in this room is reading generated logs. To read a log, you run the command `sudo snort -r (LOG NAME)`. Remember that Snort binary logs can also be read in tcpdump and Wireshark. You're also able to filter binary log files if you append any filters after (LOG NAME), i.e. `sudo snort -r (LOG NAME) (PACKET FILTERS)`. These filters are the same ones that you'd use for Wireshark and tcpdump.

For this first exercise, we'll need to run `sudo snort -dev -K ASCII -l .` and then generate traffic for this task (TASK-6 Exercise). Once we do this, we can stop Snort and navigate to the newly-created `145.254.160.237` directory. We're looking for the source port that connects to port 53; this is conveniently located in the `UDP:3009-53` text log. As the name would imply, source port 3009 communicated with port 53:

![image](https://github.com/user-attachments/assets/6bf6bce4-8f63-4cf5-b964-d1f820ab8a93)


**[Task 6, Question 1] What is the source port used to connect port 53?** - 3009

If we run `snort -r snort.log.1640048004 -n 10` and look at the last packet logged, we see that it has an ID of 49313.

![image](https://github.com/user-attachments/assets/e038df93-e877-4829-9693-a09fc714d334)


**[Task 6, Question 2] Read the snort.log file with Snort; what is the IP ID of the 10th packet?** - 49313

We'll need to rerun the command to read the log, but this time append `-X` to the end of it to get the full packet details. I'd recommend running `snort -r snort.log.1640048004 -n 4 -X` so that you don't have to sift through a bunch of packet data. The referer can be found at the very end of the packet data in the last packet.

![image](https://github.com/user-attachments/assets/cf70ea24-4589-4152-9c4d-a2c44c3fd85a)


**[Task 6, Question 3] Read the "snort.log.1640048004" file with Snort; what is the referer of the 4th packet?** - `http://www.ethereal.com/development.html`

Running a similar command as in Question 1 and looking at the eighth packet will give us the answer to the next question - we are looking at the "Ack" field:

![image](https://github.com/user-attachments/assets/31370b86-50e7-4ccc-9416-8919e7a38d35)


**[Task 6, Question 4] Read the "snort.log.1640048004" file with Snort; what is the Ack number of the 8th packet?** - 0x38AFFFF3

Here, we'd run the command `sudo snort -r snort.log.1640048004 'tcp and port 80'`. The "tcp and port 80" string is a BPF filter that can be used to filter for port 80/TCP packets. There are 41 packets total according to the output:

![image](https://github.com/user-attachments/assets/f1160b3f-4bc8-4397-8add-374a62390383)


**[Task 6, Question 5] Read the "snort.log.1640048004" file with Snort; what is the number of the "TCP port 80" packets?** - 41

### The Berkeley Packet Filter (BPF)

The specifics of the Berkeley Packet Filter (BPF), at least relevant to how we will use them in this learning path, will be explored in the Wireshark rooms. But why not learn about how it works now?

BPF is simply a way of filtering network packets at the operating systme level. A user process can supply filters, specifying the packets it wants to receive, and BPF will return only those packets that pass the filter. This can help improve performance. It's available on most Linux distributions, and there is an extended version of BFP (extended BPF, or eBPF) that works with Windows.

BPF syntax is simple: you provide expressions containing one or more primitives. Primitives have an ID (a name or a number) followed by a number of qualifiers. There are three types of qualifiers:
- A type qualifier tells us what kind of thing the ID name or number refers to. This may be a host, a net, a port, or a port range (portrange in BPF).
- A dir qualifier specifies a transfer direction to and/or from the ID. These qualifiers include src, dst, or any combination of these. "src or dst" is assumed if no direction is specified. Inbound and outbound can also specify the deisred direction.
- Proto qualifiers specify specific protocols, such as ether, wlan, ip, arp, and tcp. If there isn't a proto qualifier, all protocols consistent with the type are assumed.

Additional primitive keywords are provided. For the purposes of this room, we'll use simple BPF constructions. For example, "udp" in BPF will return only those packets which are associated with UDP. We can get more specific and say something like "udp and port 53", which will return only those packets that are associated with UDP over port 53 (i.e., DNS connections).

## [Task 7] Operation Mode 3: IDS/IPS

We will now cover the operating logic of Snort's IDS/IPS mode. The relevant parameters are as follows:
- `-c` defines the configuration file to use.
- `-T` tests the configuration file.
- `-N` disables logging.
- `-D` runs Snort in the background.
- `-A` can be used to define alert modes. `full` mode provides all possible information about the alert, which is the default mode. `fast` mode shows the alert message, timestamp, source/destination IPs and source/destination ports. `console` mode provides fast-mode alerts on the console screen. `cmg` mode provides basic header details with the payload in hex and text foramt. You can also set it to `none` to disable alerting entirely.

Snort will make use of rules in IDS and IPS mode. A rule looks something like this:

`alert icmp any any <> any any (msg: "ICMP Packet Found"; sid: 100001; rev:1;)`

We can find the rules for this instance of Snort in `/etc/snort/rules/local.rules`. If any of the rules are matched, then Snort will create an "alert" file that contains that information. In IDS/IPS mode, Snort will passively sniff packets, though we can use the parameters from earlier tasks as needed.

You may recall that we use `-c` and `-T` to load a configuration file and test it. If we run `sudo snort -c /etc/snort/snort.conf -N`, then Snort will run without generating a log file, however other functions will still be provided if specified in the command (e.g., you can still run in verbose mode with `-v` or dump full packets with `-X`).

If we run the command `sudo snort -c /etc/snort/snort.conf -D`, then Snort will run in the background and run with whatever parameters it is told to run. Since this causes Snort to become a background process, you can check the status of Snort by running `ps`, and if you want to stop the process, you can simply run `sudo kill -9 (PID OF SNORT PROCESS)`. This particular mode of running Snort is designed with automation in mind - you can have Snort do its thing in the background while you're working on other things.

Now we'll talk about the alerting modes, specified with `-A`. There are only two alert modes that output alerts to the console: `console` and `cmg`. When ran with `-A console`, Snort will generate alerts on the console as they come up, specifying the type of alert, the relevant IP addresses and directions, and so on. If ran with `-A cmg`, Snort will instead display the full packet details for any packet that generates an alert.

As for the other alert methods, `-A fast` produces a summary of the packets that were alerted on in the alert file. The alert file will simply contain the message, priority information, and IP addresses & directions. `-A full` produces more information about each alert in the alert file, such as the Time-to-Live and the Sequence Numbers. And `-A none` won't even create an alert file at all - Snort will log traffic as usual, but it will not alert on anything.

It's possible to use Snort without a configuration file - this allows you to test any rules that you create. We simply change the command to start Snort to `snort -c /etc/snort/rules/local.rules -A console`. This clearly provides a lot less visibility into the network.

Lastly, you can run Snort in IPS mode by using the `-Q --daq afpacket` parameters or by editing the `snort.conf` file. You can specify interfaces in this manner too. The command to run Snort as an IPS is `sudo snort -c /etc/snort/snort.conf -q -Q --daq afpacket -i eth0:eth1 -A console`.

If we run `sudo snort -c /etc/snort/snort.conf -A full -l .` and run the TASK 7 generator in the traffic generator script, we'll see that we only pick up two HTTP GET methods:

![image](https://github.com/user-attachments/assets/9316a393-a708-46e6-b426-37512055be03)

**[Task 7, Question 1] What is the number of the detected HTTP GET methods?** - 2

## [Task 8] Operation Mode 4: PCAP Investigation

Snort can also read PCAP files, allowing you to perform basic traffic statistics analysis and investigation of packets. Reading a pcap without any parameters will just give us a default overview of the packets and statistics about the PCAP file. More usefully, we can alert on the presence of certain packets according to our ruleset.

If we ran `sudo snort -c /etc/snort/snort.conf -q -r icmp-test.pcap -A console -n 10`, then we'll read in an ICMP test PCAP and spit out alerts to the console. This will only display the first ten packets alerted.

We can use `--pcap-list` in place of `-r` to specify a list of PCAPs to read. This can generate a lot of alerts, and it may be difficult to match the alerts with the provided PCAP files. If we want to correlate alerts with specific PCAP files, we'll need to append `--pcap-show` to the end of the command.

First we run the command `sudo snort -c /etc/snort/snort.conf -A full -l . -r mx-1.pcap` and begin investigating the output. If we scroll up in the console a bit, we can see the number of alerts made:

![image](https://github.com/user-attachments/assets/3018751f-d8f6-4198-b293-60e7df8de886)

**[Task 8, Question 1] What is the number of the generated alerts?** - 170

In this same output, we can get more granular details about the different protocols. For instance, we can see how many TCP segments were queued.

![image](https://github.com/user-attachments/assets/907d98c7-bb09-4bf2-b002-fa20c9cbced2)

**[Task 8, Question 2] Keep reading the output. How many TCP Segments are Queued?** - 18

And we can see how many HTTP response headers were found:

![image](https://github.com/user-attachments/assets/54ca7e76-1597-4a21-a4b4-fffb8fd41cf8)

**[Task 8, Question 3] Keep reading the output. How many "HTTP response headers" were extracted?** - 3

Now we must run this command, using the second ruleset: `sudo snort -c /etc/snort/snortv2.conf -A full -l . -r mx-1.pcap`. This exercise just tells us that we can use multiple rulesets for multiple purposes. Here, we have a different number of generated alerts:

![image](https://github.com/user-attachments/assets/58ee1927-a3fa-4a41-916c-c90119f63c9c)

**[Task 8, Question 4] What is the number of the generated alerts?** - 68

If we run `sudo snort -c /etc/snort/snort.conf -A full -l . -r mx-2.pcap` then we see that the number of generated alerts is 340:

![image](https://github.com/user-attachments/assets/d9aebb3c-3f20-4943-abbe-f92961cbb541)

**[Task 8, Question 5] What is the number of the generated alerts?** - 340

The number of TCP packets detected is 82:

![image](https://github.com/user-attachments/assets/e6a3fd56-360e-424b-be00-dbcab5715ac8)

**[Task 8, Question 6] Keep reading the output. What is the number of the detected TCP packets?** - 82

Running the command `sudo snort -c /etc/snort/snort.conf -A full -l . --pcap-list="mx-2.pcap mx-3.pcap"` allows us to read two PCAP files at a time. Looking through the output, we see...

![image](https://github.com/user-attachments/assets/ebb589db-3e73-4707-80fa-69904bd6b674)

**[Task 8, Question 7] What is the number of the generated alerts?** - 1020

## [Task 9] Snort Rule Structure

And now we are ready to go over Snort rules. Generally speaking, a snort rule looks like this:

`ACTION PROTOCOL SOURCE_IP SOURCE_PORT DIRECTION DESTINATION_IP DESTINATION_PORT OPTIONS`
- The `ACTION` is the action that you want Snort to take if it detects a packet that matches the rule. The most commonly used actions are `alert` (which generates an alert and logs the packet), `log` (which just logs the packet), `drop` (which blocks the packet and logs it), and `reject` (which does the same as `drop`, except that it will also try to terminate the packet session).
- The `PROTOCOL` is the protocol you want to alert for. Snort 2 only supports `IP`, `TCP`, `UDP`, and `ICMP`...
- ...but you can specify specific applications by supplying port numbers (`SOURCE_PORT` and `DESTINATION_PORT`). For instance, `...TCP (SOURCE_IP) 80...` would look for packets involving HTTP. To filter ports:
- - We can filter ports by simply supplying a single port number.
  - We can exclude ports by using the `!` operator before the port number, e.g. `!21`.
  - We can filter a port range by using `:`. The range `1:1024` indicates we're looking at ports 1-1024. The range `:1024` indicates we're looking at all ports up to 1024. The range `1025:` indicates we're looking at all ports above 1025. The range `[21,23]` indicates we're looking for ports 21-23.
- We can look for certain IPs by inputting information into the `SOURCE_IP` and `DESTINATION_IP` fields.
- - To filter for IPs, we'd simply input a single IP address into either field (e.g. `192.168.1.51`)
  - To filter for IP range, we would use CIDR notation, e.g. `192.168.1.0/24` would look for IPs on the whole subnet.
  - To filter many IP ranges, list them in brackets, e.g. `[192.168.1.0/24, 10.1.1.0/24]`.
  - To exclude IP addresses, use the `!` operator. It comes before the IP, e.g. `!192.168.1.0/24`.
- The direction operator indicates the traffic flow to be filtered by Snort. There are only two direction indicators: `->` for unidirectional (source to destination) and `<>` for bidirectional (between source and destination).
- There are there main rule options in Snort: general rule options, payload rule options, non-payload rule options. The general rule options are as follows:
- - `msg`, which provide basic prompts and quick identifiers of the rule. This is displayed in the console or the log, and is typically a one-liner summary of the event.
  - `sid`, which is just an identifier for the rule itself. SIDs < 100 are reserved, whereas SIDs 100-999,999 are rules that come with the build of snort. Thus, rules greater than or equal to 1,000,000 should be used, and each rule should have a unique SID.
  - `reference`, which just provides additional information or references for the purpose of the rule or threat pattern. This may include CVE numbers or external information.
  - `rev`, which can provide revision information about each rule. This indicates how many times a rule has been revised.
- The payload detection rule options are as follows:
- - The `content` option matches payload data, e.g. ASCII, HEX, or both. You can use this option multiple times in a single rule, though this makes packet investigation take longer. This rule option is case-sensitive.
  - The `nocase` option disables case sensitivity, making it easier to search for content.
  - The `fast_pattern` option prioritizes content searching to speed up payload search operations. This is required when using multiple content matchers; you can prioritize which content to match on first. By default, Snort prioritizes by the largest content. Ex: `... content:"GET"; fast_pattern; content:"www"; ...` would prioritize "GET" matches first.
- And the non-payload detection rules are:
- - `id`, which can filter the IP ID field.
  - `flags`, which can be used to filter for TCP flags. For instance, `F` filters by FIN, `S` by SYN, `R` by RST, `P` by PSH, `A` by ACK, and `U` by URG. Syntax is `flags:(FLAGS)`.
  - `dsize` allows you to filter the packet payload size. Syntax is `dsize:(RANGE)`The `<>` operator in this instance allows you to specify a range of sizes, e.g. `100<>300`. You can also just use `>100` or `<100`.
  - `sameip` allows you to filter for duplicate source and destination IP addresses.

Once you create rules, they are considered to be local rules and are located in, well, `local.rules`. By default you can find this in `/etc/snort/rules/local.rules`. You can edit this file, though you'll need to do so as the superuser.

If we want to alert on IP packets with ID 35369, we can create the rule `alert ip any any <> any any (msg: "ID Found"; id: 35369; sid: 1000000; rev:1;)` and slap it in the `local.rules` file in the Task-9 directory. After we run the command `snort -c local.rules -A full -l . -r task9.pcap` and check the `alert` file that's generated, we see:

![image](https://github.com/user-attachments/assets/a04ef2c4-e4e8-4f16-9171-ef6803222326)

**[Task 9, Question 1] Write a rule to filter IP ID "35369" and run it against the given pcap file. What is the request name of the detected packet?** - TIMESTAMP REQUEST

To alert on the SYN flag, we must create the rule `alert tcp any any <> any any (msg: "SYN found"; flags:S; sid: 1000001; rev:1;)`. We need to specify `tcp` in this instance because, well, that's where SYN flags are found! After checking the alert files, we see that there is only one SYN flag found in the pcap:

![image](https://github.com/user-attachments/assets/bc4319d4-1a36-41a5-8518-b704dc4e4064)

**[Task 9, Question 2] Create a rule to filter packets with Syn flag and run it against the given pcap file. What is the number of detected packets?** - 1

If we want to find the number of Push-Ack flags, we would simply change the flags parameter to be `P,A`. So in our case, we'd write `alert tcp any any <> any any (msg: "PSH-ACK found"; flags:P,A; sid: 1000002; rev:1;)`. If we investigate the output from Snort, we see that htere are 216 packets detected with this rule:

![image](https://github.com/user-attachments/assets/a57f7ec1-e60a-41cf-acdd-02da3d150b3c)

**[Task 9, Question 3] Write a rule to filter packets with Push-Ack flags and run it against the given pcap file. What is the number of detected packets?** - 216

If we want to look for packets with the same source and destination IP, we'd write the rule `alert ip any any <> any any (msg: "Same IP"; sameip; sid: 1000003; rev: 1;)`. Snort shows us that there are 13 alerts:

![image](https://github.com/user-attachments/assets/38518a1e-088c-463b-b452-1a0c78b79167)

...however, if we check the alert file, six of these alerts are for unassigned IPs, broadcast/multicast messages, and IP requests. These aren't relevant to us - we are only interested in packets with the same IPs. The rest of the packets that are alerted on actually involve IPs, starting with `192.168.0.1`... so there are 7 packets.

![image](https://github.com/user-attachments/assets/dac1319b-82f3-4977-b04a-de213183d3c5)


**[Task 9, Question 4] Create a rule to filter packets with the same source and destination IP and run it against the given pcap file. What is the number of packets that show the same source and destination address?** - 7

And now we wrap up with just a quick conceptual question. If you're ever modifying a rule in Snort, you're expected to update the revision number, or...

**[Task 9, Question 5] Case Example - An analyst modified an existing rule successfully. Which rule option must the analyst change after the implementation?** - rev

## [Task 10] Snort2 Operation Logic: Points to Remember

The main components of Snort are as follows:
- Packet collector: Snort can collect and prepare packets for pre-processing.
- Pre-processors: Snort can arrange and modify packets for the detection engine.
- Detection engine: Snort can process, dissect, and analyze packets by applying rules.
- Logging and alerting: Snort can generate logs and alerts.
- Outputs and plugins: Snort can integrate with different output sources (e.g. syslog, mysql) and other rule management detection plugins.

There are three types of rules available for Snort:
- Community rules: Free ruleset under GPLv2, no need to register
- Registered rules: Free ruleset, but requires registration. Provides subscriber rules with a 30-day delay.
- Subscriber rules: Paid ruleset, requiring a subscription. The main ruleset, update twice a week (Tues. & Thurs.).

Snort will automatically create the required directories and files once you download it, though you'll need to indicate the relevant rules you want to use in the `snort.conf` file. This file is a long configuration file, which can make it easy to misconfigure things. You should make use of Snort's rule-updating modules and integration tools.

Generally speaking, you should not replace your preconfigured Snort configuration files; rather, you should edit your own configuration files manually or update your rules with relevant tools and modules. Specifically, avoid replacing `snort.conf` and `local.rules`.

If you wanted to change your Snort configuration file, you can go through each section of the file. In `Step #1: Set the network variables.` you have the option to set the following:
- `HOME_NET` denotes the network/subnet you want to use Snort on, e.g. `any` or `192.168.1.1/24`.
- `EXTERNAL_NET` denotes the external network. It should be kept as `any` or `!$HOME_NET`.
- `RULE_PATH` denotes the hardcoded rule path, e.g. `/etc/snort/rules`.
- `SO_RULE_PATH` is used with registered and subscriber rules, e.g. `$RULE_PATH/so_rules`.
- `PREPROC_RULE_PATH` is used with registered and subscriber rules, e.g. `$RULE_PATH/plugin_rules`.

Then, the `Step #2: Configure the decoder.` section lets you manage Snort's IPS mode. The single-node installation works best with `afpacket` mode, which will allow you to run Snort as an IPS on your machine. You can set the following:
- `#configure daq:` allows you to select the IPS mode, e.g. `afpacket`
- `#configure daq_mode:` allows you to activate the inline mode, e.g. `inline`
- `#config logdir:` allows you to set the default log path, e.g. `/var/log/snort`

The Data Acquisition Modules (DAQ) are specific libraries used by Snort for packet I/O, bringing flexibility to packet processing. The modules are
- `pcap`: The default mode, i.e. sniffer mode. A popular choice.
- `afpacket`: The inline mode, i.e. IPS mode. Another popular choice.
- `ipq`: Inline mode on Linux using NetFilter. This replaces the snort_inline patch.
- `nfq`: Inline mode on Linux. An alternative option.
- `ipfw`: Inline on OpenBSD and FreeBSD, making use of divert sockets with the pf and ipfw firewalls.
- `dump`: Testing mode for inline and normalization

In `Step #6: Configure output plugins`, we can configure the outputs of the IDS and IPS actions, such as logging and alerting format details. By default, everything is prompted in console, but you can adjust this to use Snort more efficiently.

In `Step #7: Customize your ruleset`, you can set the following:
- `# site specific rules`: Hardcoded local and user-generated rules path, e.g. `include $RULE_PATH/local.rules`
- `#include $RULE_PATH/`: Hardcoded default/downloaded rules path, e.g. `include $RULE_PATH/rulename`
