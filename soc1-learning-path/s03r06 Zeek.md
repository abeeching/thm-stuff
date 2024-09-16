# Zeek (introduction)

## [Task 1] Introduction

Zeek - formerly Bro - is a platform for network security monitoring; specifically, it is used as an open-source traffic analyzer.

## [Task 2] Network Security Monitoring and Zeek

Network monitoring is the process of watching and providing a continuous overview of the network traffic, optioanlly saving it for further investigation. Our goals in doing so are to find network problems, reduce them, increase performance, and perhaps increase productivity. This is a part of daily IT and SOC operations.

Generally network monitoring is focused on IT assets - uptime and availability, device health, connection quality and performance, and network traffic balance and management/configuration. At this stage, we would be monitoring and visualizing the network traffic for troubleshooting and root cause analysis. This is helpful for network administrators, but it is not necessartily used for detecting vulnerabilities and significant security concerns.

Network _security_ monitoring (NSM), on the other hand, is the process of finding network anomalies such as rogue hosts, encrypted traffic, suspicious services/ports, and malicious/suspicious traffic patterns in an intrusion detection and response approach. At this stage, we investigate suspicious events for SOC and related purposes.

Zeek is a tool that provides both capabilities, but is primarily used for NSM via its logs. This room uses the open-source version of Zeek, though Corelight - an enterprise utility - exists. Corelight provides for sensors, encrypted traffic collection, insight into C2, integrations with Suricata, smart packet capturing, and more.

While Zeek may look like an IDS/NIDS, it differs from other software like Snort in the following ways:
- Snort primarily uses signatures to detect vulnerabilities, whereas Zeek focuses on specific threats when alerts are triggered.
- Zeek is harder to use than Snort, and analysis is done right out of Zeek. It's harder to detect more complex threats with Snort.
- Zeek provides in-depth traffic visibility, is useful for threat hunting, is good for finding complex threats, provides scripting and event correlation, and its logs are easy to read. Snort has rules that are easy-to-write, including Cisco-supported rules and community support.
- Zeek is commonly used for network monitoring, in-depth traffic investigations, and chained event intrusion detection. Snort is commonly used for intrusion detection AND prevention, and it can help stop known attacks and threats.

Zeek's architecture consists of two primary layers - the "Event Engine" and the "Policy Script Interpreter."
- The Event Engine layer is where packets are processed - it is the event core and is responsible for describing the event without focusing on event details. This is where we get information about source/destination ddresses, protocol identification, session analysis, file extraction...
- The Policy Script Interpreter layer is where semantic analysis is conducted, i.e. where event correlations happen. This is considered to be the scripting layer.

There are various frameworks that can be used to provide extended functionality to the scripting layer, enhancing flexibility and compatibility wiht other network components. We'll be using the logging framework for all cases, but additional frameworks include Cluster, Signature, Notice, Broker Communication, Summary, Input, Supervisor, NetControl, Configuration, GeoLocation, Packet Analysis, Intelligence, File Analysis, and TLS Decryption.

Zeek provides many logs for differnet categories, e.g. traffic monitoring, intrusion detection, threat hunting, and more. We'll talk more about this in a moment, but for now all we need to know is that Zeek will automatically generate logs when you investigate traffic or a pcap file. Logs will be created in the working directory if a pcap is processed, or they will be put into a specified log path when Zeek is ran as a service (default `/opt/zeek/logs`).

You can check the version of Zeek with `zeek -v`. If you need to start Zeek as a service, you'll need to use the ZeekControl module, which requires superuser permissions to use. Like with Snort, you'd have to elevate your privileges to read generated log files. If you simply run `zeekctl`, you'll be able to issue further commands such as `status`, `start`, and `stop` to manage the Zeek service. You can also directly run `zeekctl (COMMAND)`. This is the only way to listen to live network traffic.

If you just want to read a pcap file, you run `zeek -C -r (PCAP)`. The parameters used in the command line are as follows:
- `-r` allows you to read something, like a pcap file.
- `-C` ignores checksum errors.
- `-v` provides version information
- `zeekctl` starts the ZeekControl module.

When it comes time to investigate the generated logs, you will need to make use of command-line tools such as `cat`, `cut`, `grep`, `sort`, and `uniq`, along with other tools specific to Zeek.

**[Task 2, Question 1] What is the installed Zeek instance version number?** - 4.2.1

**[Task 2, Question 2] What is the version of the ZeekControl module?** - 2.4.0

**[Task 2, Question 3] Investigate the `sample.pcap` file. What is the number of generated alert files?** - 8

## [Task 3] Zeek Logs

Zeek will generate logs according to traffic data, including application-level protocols and fields. Zeek classifies logs into seven categories, and Zeek logs are well-structured and tab-separated ASCII files. It's easy to read and process these logs as a result, it just takes effort. A familiarity with networking and protocols certainly helps here.

Each log output consists of multiple fields, each field holding a different part of the traffic data. Correlation is done via the UID, which is the unique identifier assigned for each session.

The log types are as follows:
- Network: These are network protocol logs, including conn.log, dce_rpc.log, dhcp.log, dnp3.log, dns.log, ftp.log, http.log, irc.log, kerberos.log, modbus.log, modbus_register_Change.log, mysql.log, ntlm.log, ntp.log, radius.log, rdp.log, rfb.log, sip.log, smb_cmd.log, smb_files.log, smb_mapping.log, smtp.log, snmp.log, socks.log, ssh.log, ssl.log, syslog.log, tunnel.log.
- Files: File analysis result logs, including files.log, oscp.log, pe.log, x509.log.
- NetControl: Network control and flow logs, including netcontrol.log, netcontrol_drop.log, netcontrol_shunt.log, netcontrol_catch_release.log, and openflow.log.
- Detection: Detection and possible indicators, including intel.log, notice.log, notice_alarm.log, signatures.log, traceroute.log
- Network observations: Network flow logs, including known_certs.log, known_hosts.log, known_modbus.log, known_services.log, and software.log.
- Miscellaneous: Additional logs that cover external alerts, inputs, and failures, including barnyard2.log, dpd.log, unified2.log, unknown_protocols.log, weird.log, weird_states.log.
- Zeek diagnostics: Logs that cover system messages, actions, and some statistics, including broker.log, capture_loss.log, cluster.log, config.log, loaded_scripts.log, packet_filter.log, print.log, prof.log, reporter.log, stats.log, stderr.log, and stdout.log.

Some logs get updated daily or per session. Some of the most commonly-used logs that update with these frequencies are given as follows:
- known_hosts.log (Daily): List of hosts that completed TCP handshakes.
- known_services.log (Daily): List of services used by hosts.
- known_certs.log (Daily): List of SSL certificates.
- software.log (Daily): List of software used on the network.
- notice.log (per Session): Anomalies detected by Zeek.
- intel.log (per Session): Traffic containing malicious patterns and indicators.
- signatures.log (per Session): List of triggered signatures.

There's a lot to dissect, but the logs below are good places to start:
- If you're looking for overall info, check conn.log, files.log, intel.log, and loaded_scripts.log
- If you're looking for protocol-based info, check http.log, dns.log, ftp.log, ssh.log
- If you're looking for detection info, check notice.log, signatures.log, pe.log, and traceroute.log
- If you're looking for observations, check known_host.log, known_services.log, software.log, and weird.log

Generally though, you should investigate logs that are associated with the case type and working hypothesis.

Once you run Zeek, you can view logs by using your standard Linux command line tools and utilities, though this might not be enough to spot anomalies quickly -- logs are expansive, after all. You can use visualization and correlation tools like ELK and Splunk, though we can make use of other tools that Zeek itself provides.

The `zeek-cut` tool allows you to cut specific columns from Zeek logs. All logs have field names in the beginning that you can use with `zeek-cut`. If, for instance, we wanted to grab the UID, protocol, source/destination hosts, and source/destination ports from `conn.log`, we'd want to run the command `cat conn.log | zeek-cut uid proto id.orig_h id.orig_p id.resp_h id.resp_p`. This lets us extract information with minimal effort.

**[Task 3, Question 1] Investigate the `sample.pcap` file. Investigate the `dhcp.log` file. What is the available hostname?** - Microknoppix

**[Task 3, Question 2] Investigate the `dns.log` file. What is the number of unique DNS queries?** - 2

**[Task 3, Question 3] Investigate the `conn.log` file. What is the longest connection duration?** - 332.319364

## [Task 4] CLI Kung-Fu Recall: Processing Zeek Logs

Generally, CLIs can help provide functionality that may not be present in GUIs. You should be familiar with how to use CLI tools, Berkeley Packet Filters (or the BPF), and perhaps regular expressions. Some commands to know:
- Command history tools: To view, use `history`. To run the nth command in history, run `!n`. To run the previously issued command, run `!!`.
- Read files: To read a file, use `cat`. To read the first 10 lines of a file, use `head`. For the last 10 lines, use `tail`. For both head and tail, use `-n` to specify the number of lines to read.
- To cut the firt 

## [Task 5] Zeek Signatures

**[Task 5, Question 1] Investigate the `http.pcap` file. Create the HTTP signature shown in the task and investigate the pcap. What is the source IP of the first event?** - `10.10.57.178`

**[Task 5, Question 2] What is the source port of the second event?** - 38712

**[Task 5, Question 3] Investigate the `conn.log`. What is the total number of the sent and received packets from source port 38706?** - 20

**[Task 5, Question 4] Investigate the `notice.log`. What is the number of unique events?** - 1413

**[Task 5, Question 5] What is the number of `ftp-brute` signature matches?** - 1410

## [Task 6] Zeek Scripts | Fundamentals

**[Task 6, Question 1] Investigate the `smallFlows.pcap` file. Investigate the `dhcp.log` file. What is the domain value of the `vinlap01` host?** - `astaro_vineyard`

**[Task 6, Question 2] Investigate the `bigFlows.pcap` file. Investigate the `dhcp.log` file. What is the number of identified unique hostnames?** - 17

**[Task 6, Question 3] Investigate the `dhcp.log` file. What is the identified domain value?**- `jaalam.net`

## [Task 7] Zeek Scripts | Scripts & Signatures

**[Task 7, Question 1] Investigate the `sample.pcap` file with `103.zeek` script. Investigate the terminal output. What is the number of detected new connections?** - 87

**[Task 7, Question 2] Investigate the `ftp.pcap` file with `ftp-admin.sig` signature and `201.zeek` script. Investigate the `signatures.log` file. What is the number of signature hits?** - 1401

**[Task 7, Question 3] Investigate the `signatures.log` file. What is the total number of `administrator` username detections?** - 731

**[Task 7, Question 4] Investigate the `ftp.pcap` file with all local scripts, and investigate the `loaded_scripts.log` file. What is the total nubmer of loaded scripts?** - 498

**[Task 7, Question 5] Investigate the `ftp-brute.pcap` file with `/opt/zeek/share/zeek/policy/protocols/ftp/detect-bruteforcing.zeek` script. Investigate the `notice.log` file. What is the total number of brute-force detections?** - 2

## [Task 8] Zeek Scripts | Frameworks

**[Task 8, Question 1] Investigate the `case1.pcap`file with `intelligence-demo.zeek` script. Investigate the `intel.log` file. Look at the second finding, where was the intel info found?** - `IN_HOST_HEADER`

**[Task 8, Question 2] Investigate the `http.log` file. What is the name of the downloaded .exe file?** - `knr.exe`

**[Task 8, Question 3] Investigate the `case1.pcap` file with `hash-demo.zeek` script. Investigate the `files.log` file. What is the MD5 hash of the downloaded `.exe` file?** - `cc28e40b46237ab6d5282199ef78c464`

**[Task 8, Question 4] Investigate the `case1.pcap` file with `file-extract-demo.zeek` script. Investigate the `extract-files` folder. Review the contents of the text file. What is written in the file?** - Microsoft NCSI

## [Task 9] Zeek Scripts | Packages

**[Task 9, Question 1] Investigate the `http.pcap` file with the `zeek-sniffpass` module. Investigate the `notice.log` file. Which username has more module hits?** - BroZeek

**[Task 9, Question 2] Investigate the `case2.pcap` file with `geoip-conn` module. Investigate the `conn.log` file. What is the name of the identified City?** - Chicago

**[Task 9, Question 3] Which IP address is associated with the identified City?** - `23.77.86.54`

**[Task 9, Question 4] Investigate the `case2.pcap` file with `sumstate-counttable.zeek` script. How many types of status codes are there in the given traffic capture?** - 4
