# IDS Fundamentals

## [Task 1] What is an IDS?

Previously, we looked at the role of a firewall, which is a security solution deployed on the boundary of a network to protect incoming and ougoing traffic. There, we set up rules to determine if a connection should be allowed to take place. We might also want something in place to detect the activities of the connection that passed through the firewall and have already taken place. If an attacker gets past the firewall, how do we know if they perform malicious activities? That's where the intrusion detection system (IDS) comes into play.

An IDS is more like a security camera. It monitors traffic based on signatures and baselines, and detects any abnormalities in the network and generates alerts as needed. An IDS will NOT act on these detections; it just tells security administrators about suspicious or malicious activity.

Our goal here is to learn about the basics of IDS and explore one popular IDS solution.

**[Task 1, Question 1] Can an intrusion detection system (IDS) prevent the threat after it detects it?** - Nay

## [Task 2] Types of IDS

There are many types of IDS, depending on what deployment and detection modes there are. An IDS may be deployed in a few ways:
- Host IDS: Host-based solutions are installed individually on the hosts and are responsible only for detecting potential threats on a particular host. This can be good for one host, but it may be challenging to manage in large networks since they're resource intensive and require management on each host.
- Network IDS: Network-based solutions focus on detecting potentially malicious activities in the entire network, regardless of the host. They monitor the traffic of all hosts involved to detect suspicious activities, and it provides a centralized view of all detections inside the whole network.

An IDS will detect things in a few ways:
- Signature-based IDS: Attacks have unique patterns known as signatures, and an IDS will be on the lookout for known malicious signatures. If such a signature is found on the network, it sends out an alert. This depends on the strength of the signature database and how new the attack is. A signature-based IDS can't detect attacks that have no prior signatures - _zero-day_ attacks. It can only handle signatures it has seen previously. Snort is an example of such an IDS.
- Anomaly-based IDS: This IDS will learn the normal behavior of the network/system first (creating a _baseline_), and then if there are any deviations from this baseline it will send out an alert. This can be used to detect zero-day attacks, since they pay attention to abnormalities in the network or system. This can generate a lot of false positives, however, since some legitimate behavior may match malicious behavior. You'd have to fine-tune the IDS and manually define legit behavior.
- Hybrid IDS: This IDS combines the signature- and anomaly-based approaches to leverage both of their strengths. Some known threats may have signatures in the IDS database, but if a given threat doesn't, it might get picked up by the anomaly-based detection.

Signature-based IDS are good at detecting things quickly, but they can't detect novel threats. Anomaly-based IDS are good at detecting these novel threats, among others, but they require high processing overhead.

**[Task 2, Question 1] Which type of IDS is deployed to detect threats throughout the network?** - Network Intrusion Detection System

**[Task 2, Question 2] Which IDS leverages both signature-based and anomaly-based detection techniques?** - Hybrid IDS

## [Task 3] IDS Example: Snort

Snort uses signature- and anomaly-based detections to identify known threats. Rules can be defined in the rule files for SNort, and several rules come pre-packaged with the tool. These files contain known attack patterns and can detect a lot of the malicious traffic for you. However, Snort can be set up to detect traffic not covered by the default rule files. You can create custom rules based on your requirements to detect specific traffic. You can also disable built-in detection rules if they don't point to harmful traffic for your system or network.

Snort has three major modes of operation:
- Packet sniffer mode: Reads, displays packets without performing analysis. Can be helpful in monitoring and troubleshooting. Lets you display traffic on the console or output it to a file for analysis later.
- Packet logging mode: Log network traffic as a PCAP file, which is a standard format for packet captures. This includes all traffic and any detections from it. Forensic investigators might use these to perform root cause analysis.
- Network IDS (NIDS) mode: Primary mode that monitors traffic in real-time, applies rule files, and identifes matches to known attack patterns. In the event of a match, Snort will generate an alert.

**[Task 3, Question 1] Which mode of Snort helps us to log the network traffic in a PCAP file?** - Packet Logging Mode

**[Task 3, Question 2] What is the primary mode of Snort called?** - Network Intrusion Detection System Mode

## [Task 4] Snort Usage

When setting up Snort, you must provide the network interface and range on which you want to use it. You can run it normally and capture traffic intended only for your host, or you can use it to find intrusions in your entire network. Doing the latter requires that you set up your network interface to be in _promiscuous mode_, where it can read all traffic.

The VM attached in this task will start in Split Screen view and will be used in the next task.

Snort has some built-in rule files, a configuration file, and some other files, all stored in the `/etc/snort` directory. The Snort configuration file is `snort.conf`, and it allows you to specify which rule files to enable and which network range to monitor, among other things. The rule files are stored in the `rules` folder.

Snort has a specific syntax for writing rules. Here's a rule that would detect ICMP packets (e.g., for pings) coming into the host network from any IP address over any port: `alert icmp any any -> $HOME_NET any (msg:"Ping Detected"; sid:10001; rev:1;)`.

The components of any rule in Snort are as follows:
- Action: This tells Snort what to do in the event that the rule is triggered. Typically for an IDS you'd set this to `alert` so Snort can send off an alert.
- Protocol: This tells Snort what protocol you're looking for, which in this case would be ICMP.
- Source IP: This tells Snort where the traffic is originating from. If this is set to "any" (or any of the port/IP options for that matter), then Snort will not care about the particular IP (or port).
- Source port: This tells Snort the port from which the traffic originates.
- Destination IP: This tells Snort where the traffic is heading towards. $HOME_NET is a variable that can be used to specify our entire network range in the Snort configuration file.
- Destination port: This tells Snort the port the traffic would reach.
- Metadata: Each rule has some metadata in parentheses.
  - The message, msg, describes the message to be displayed when the subject rule triggers.
  - The signature ID, SID, is a unique identifier that differentiates a rule from other rules.
  - Rule revision, rev, is the revision number of the rule. Every time the rule is modified the number is incremented.

To create a rule, you can take a rule written in the proper format and paste it into `/etc/snort/rules/local.rules` with `nano` or any other text editor.

To test a rule, you can run Snort. For the ICMP rule in particular, you can tell it to run `sudo snort -q -l /var/log/snort -i lo -A console -c /etc/snort/snort.conf` and then run `ping 127.0.0.1`. If the rule was implemented correctly, Snort should display some alerts in the console.

If you'd like to run Snort on a pcap file, you can run the command `sudo snort -q -l /var/log/snort -r (PCAP_FILE) -A console -c /etc/snort/snort.conf`.

**[Task 4, Question 1] Where is the main directory of Snort that stores its files?** - `/etc/snort`

**[Task 4, Question 2] Which field in the Snort rule indicates the revision number of the rule?** - `rev`

**[Task 4, Question 3] Which protocol is defined in the sample rule created in the task?** - ICMP

**[Task 4, Question 4] What is the file name that contains custom rules for Snort?** - `local.rules`

## [Task 5] Practical Lab

Now let's do some investigation. We want to check a PCAP file called `Intro_to_IDS.pcap`, which contain the network traffic captured during a recent attack on a network. Our goal is to extract some information from the PCAP file. We'll be using the information provided in the attached VM located in `/etc/snort.

We'll want to read the PCAP file by running `sudo snort -q -l /var/log/snort -r Intro_to_IDS.pcap -A console -c /etc/snort/snort.conf`. Running this command will already give us several attempted SSH connections:

![image](https://github.com/user-attachments/assets/414c29d1-229c-4613-8f92-2aa359aefaaa)

The first IP address listed in any Snort alert will be the source IP, thus identifying who tried to connect via SSH.

**[Task 5, Question 1] WHat is the IP address of the machine that tried to connect to the subject machine using SSH?** - `10.11.90.211`

Also in this PCAP file, we get some different alerts:

![image](https://github.com/user-attachments/assets/3bf9efbb-3c82-4fae-951c-fb98b60351c7)

**[Task 5, Question 2] What other rule message besides the SSH message is detected in the PCAP file?** - Ping Detected

If we want to figure out how the rules are set up, we'll want to run `cat rules/local.rules` from `/etc/snort`. This will display the two rules that resulted in the detections we saw in the previous questions:

![image](https://github.com/user-attachments/assets/1c740d46-7820-4496-b960-c52fe2925ffa)

The SID for the SSH rule is 1000002.

**[Task 5, Question 3] What is the sid of the rule that detects SSH?** - 1000002
