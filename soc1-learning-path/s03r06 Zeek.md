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
- To cut the first field of a file, we can use `cat (FILE) | cut -f 1`. For the first column, we simply use `-c1`.
- To filter for specific keywords, we use `cat (FILE) | grep (KEYWORDS)`.
- To sort outputs alphabetically, use `cat (FILE) | sort`. To sort numerically, use `cat (FILE) | sort -n`.
- To get rid of duplicate lines, use `cat (FILE) | uniq`.
- To count line nubmers, use `cat (FILE) | wc -l`. To show line numbers, use `cat (FILE) | nl`.
- To print a certain line, use `cat (FILE) | sed -n '#p'`, where `#` is the line number you want to print. This can be a range, e.g. `10,15`.
- To print lines below a certain line number, use `cat (FILE) | awk 'NR < 11 {print $0}'`. You can replace the `<` with `==` to print out a specific line.
- Again, to filter specific fields of Zeek logs, use `cat (ZEEK LOG) | zeek-cut FIELDS ...`
- There are special combinations and other commands we can use for different purposes:
  - `sort | uniq` will remove duplicate values. If we want to count the number of occurrences of each value, use `sort | uniq -c`. To sort values numerically and in reverse, use `sort -nr`.
  - `rev` will reverse string characters.
  - To split strings on delimiters and keep a certain number of fields, use `cut -d '(DELIMITER)' -f (FIELDS)`, where `(DELIMITER)` may be something like a period, and `(FIELDS)` may be a single number or range.
  - To display lines that do not contain a certain string, use `grep -v (STRING)`. To avoid matching multiple strings, you can use `grep -v -e (STRING1) -e (STRING2) ...`
  - To display file information, simply use `file`.
  - To search for a string everywhere, organize the output into columns, and view the output with less, use `grep -rin (VALUE) * | column -t | less -S`.

## [Task 5] Zeek Signatures

Zeek signatures can be used to develop rules and event correlations, helping you find activities of interest on a network. Low-level pattern matching and conditions similar to Snort rules are used to this end, and Zeek even has a scripting language. With Zeek scripting, you can chain multiple events together to find some event of interest - we'll talk more about scripting later. Signatures are comprised of three things:
- Signature ID: The unique signature identifier
- Conditions: Conditions that allow us to filter packet headers for specific addresses, protocols, port numbers, as well as packet contents for specific values and patterns.
- Actions: By default, Zeek can create a `signatures.log` file when it has a signature match, but you can also trigger Zeek scripts as needed.

Some common conditions and filters include:
- For headers:
  - `src-ip` lets us filter by source IP, and `dst-ip` lets us filter by destination IP.
  - `src-port` lets us filter by source port, and `dst-port` lets us filter by destination port.
  - `ip-proto` allows us ot filter by the target protocol, including TCP, UDP, ICMP, ICMP6, IP, and IP6.
- For content:
  - `payload` lets us filter by packet payload.
  - `http-request` allows us to filter with client-side HTTP headers. We can even check request bodies with `http-request-body` and headers with `http-request-header`.
  - `http-reply-header` allows us to filter by server-side HTTP headers, and `http-reply-body` allows us to filter by server-side HTTP response bodies.
  - `ftp` allows us to filter by the command line input of FTP sessions.
- For context, we can use `same-ip` to filter out duplicate source and destination addresses.
- For actions, we may use `event` to describe a signature match message.
- Comparison operators include the usual `==`, `!=`, `<`, `<=`, `>`, `>=`
- Filters can generally accept string, numerical, and regex values.

When running Zeek with a signature file, you can issue the command `zeek -C -r (FILE) -s (SIGNATURE)`. Zeek signature files use the `.sig` extension. The `-C` flag ignores checksum errors, `-r` allows us to read pcap files, and `-s` allows us to use signature files.

The room provides an example of a signature used to detect HTTP cleartext passwords:

```
signature http-password {
  ip-proto == tcp
  dst-port == 80
  payload /.*password.*/ # This is regex -- when we use .*, we can match a character zero or more times. This will specifically match when a "password" phrase is detected in the payload.
  event "Cleartext password found!"
}
```

When this script is run, any matches will cause an alert to be generated, in addition to new log files - `signatures.log` and `notice.log`. These logs provide basic details about what was found and the signature message, and you can even extract application banners by using `zeek-cut sub_msg` or `zeek-cut sub` as needed. This will help you figure out where the signature match occurs.

The room provides another signature example that can be used to investigate admin login attempts over FTP:

```
signature ftp-admin {
  ip-proto == tcp
  ftp /.*USER.*dmin.*/
  event "FTP admin login attempt!"
}
```

While this is helpful in the case of finding login attempts to the admin account, we may want to consider looking for login attempts into other accounts that may be anomalous. We'll want to generalize this rule a little bit, and we can do so by making use of how FTP reports things. A login failure generates an FTP 530 response, so we can create a signature to look for these instances:

```
signature ftp-brute {
  ip-proto == tcp
  payload /.*530.*Login.*incorrect.*/
  event "FTP brute force attempt!"
}
```

Zeek signature files may consist of multiple signatures, allowing us to have one file with signatures for each event type. Here's a global rule for detecting username input and incorrect password attempts:

```
signature ftp-username {
  ip-proto == tcp
  ftp /.*USER.*/
  event "FTP username input found!"
}

signature ftp-brute {
  ip-proto == tcp
  payload /.*530.*Login.*incorrect.*/
  event "FTP brute force attempt!"
}
```

By combining these rules together, we can alert on and investigate potential brute-force attempts against different accounts more generally.

It was possible to convert Snort rules to Zeek (Bro) signatures, however workflows between the platforms have since changed since Bro became Zeek.

**[Task 5, Question 1] Investigate the `http.pcap` file. Create the HTTP signature shown in the task and investigate the pcap. What is the source IP of the first event?** - `10.10.57.178`

**[Task 5, Question 2] What is the source port of the second event?** - 38712

**[Task 5, Question 3] Investigate the `conn.log`. What is the total number of the sent and received packets from source port 38706?** - 20

**[Task 5, Question 4] Investigate the `notice.log`. What is the number of unique events?** - 1413

**[Task 5, Question 5] What is the number of `ftp-brute` signature matches?** - 1410

## [Task 6] Zeek Scripts | Fundamentals

As mentioned earlier, Zeek has its own scripting language, giving us a greater ability to investigate and correlate events. Scripts can be used to apply policies as well, in which case they are called policy scripts. Zeek has several different types of scripts and files:
- Base scripts are those that are installed by default, and are not supposed to be modified. See `/opt/zeek/share/zeek/base`
- User-generated or modified scripts are scripts that we make, and can be put in `/opt/zeek/share/zeek/site`
- Policy scripts have their own path, `/opt/zeek/share/zeek/policy`
- To automatically load or use scripts in live sniffing mode, you must identify the script in the Zeek configuration file, found in `/opt/zeek/share/zeek/site/local.zeek`. You can also just load a script for a single usage of Zeek, as with signatures.

Some key points:
- Zeek scripts use the `.zeek` extension.
- User-generated and modified scripts should ONLY be placed in `zeek/base` -- do not modify the base scripts.
- You can call scripts in live-monitoring mode with the commands `load @/script/path` or `load @script-name` in the `local.zeek` file.
- Zeek is event-oriented, so when we write scripts, we'll want to write them to handle events of interest.

Zeek scripting can be used to automate tasks that you'd normally have to manually perform in Wir3eshark, tshark, or tcpdump. If we wanted to, say, extract all available DHCP hostnames from a pcap file, we can do so in a few ways. Wireshark allows us to get this information directly, but since Wireshark is a GUI, it's not exactly easy to transfer the data to another tool for processing. On the other hand, tcpdump and tshark are command line tools, but formatting will require piping the output through a lot of different commands which can get unwieldy. Here's a Zeek script that can handle the job in four lines:

```
event dhcp_message (c: connection, is_orig: bool, msg: DHCP::Msg, options: DHCP::Options)
{
  print options$host_name;
}
```

To use a script, run the command `zeek -C -r (FILE) (SCRIPT)`.

Our goal at this point is to cover the logic behind Zeek scripting and how to use Zeek scripts. There are online resources - including those from the Zeek devs themselves - that are designed to get you up to speed with Zeek scripting.

Zeek has various trigger conditions and built-in functions (BIFs) that can be used to extract information from traffic data. Customized scripts can be found in the following locations:
- `/opt/zeek/share/zeek/base/bif`
- `/opt/zeek/share/zeek/base/bif/plugins`
- `/opt/zeek/share/zeek/base/protocols`

**[Task 6, Question 1] Investigate the `smallFlows.pcap` file. Investigate the `dhcp.log` file. What is the domain value of the `vinlap01` host?** - `astaro_vineyard`

**[Task 6, Question 2] Investigate the `bigFlows.pcap` file. Investigate the `dhcp.log` file. What is the number of identified unique hostnames?** - 17

**[Task 6, Question 3] Investigate the `dhcp.log` file. What is the identified domain value?**- `jaalam.net`

## [Task 7] Zeek Scripts | Scripts & Signatures

Zeek scripts can contain operators, types, attributes, declarations, statements, and directives. There are two events that can be called once Zeek starts and once ZZeek stops. These events do not have parameters:

```
event zeek_init() # runs when Zeek process starts
{
  print ("Started Zeek"); # displays a message on the terminal
}

event zeek_done() # runs when Zeek process stops
{
  print ("Stopped Zeek");
}
```

Zeek will create logs in the working directory separately from any scripting tasks.

We can create a script to print packet data to the terminal, thus allowing us to see raw data. Since we want to request the details of a connection, we can use the `new_connection` event, which is automatically generated for each new connection. This data won't be very helpful to us yet - we'd need to process it first - but it's good to see what the bulk data looks like before we start processing it.

```
event new_connection(c: connection)
{
  print c;
}
```

We can then print specific events of interest by referring to specific tags, such as the id value and the field name. The fields will be the same as the fields in the log files.

```
event new_connection(c: connection)
{
  print ("###################################");
  print ("");
  print ("New connection found!");
  print ("");
  print fmt ("Source Host: %s # %s --->", c$id$orig_h, c$id$orig_p); # We use %s to indicate where we'd like to have the string output.
  print fmt ("Destination Host: %s # %s <---", c$id$resp_h, c$id$resp_p); # The value c$id is the reference field that we're trying to use.
  print ("");
}
```

It's possible to use scripts in conjunction with other scripts and signatures for further event correlation. Let's revisit the `ftp-admin` script from earlier -- this time, we'll try to detect if that rule got any hits:

```
event signature_match (state: signature_state, msg: string, data: string)
{
  if (state$sig_id == "ftp-admin")
  {
    print ("Signature hit! --> #FTP-Admin ");
  }
}
```

This simply checks if there is a signature hit, and if so, tells us via the terminal. The `signature_match` event helps us with this. There are, of course, other events -- you can check the Zeek documentation for more events and required parameters.

If we awnt to load all local scripts from the `local.zeek` file, we can run the `local` command with Zeek: `zeek -C -r (FILE) local`. Zeek will provide additional log files, such as `loaded_scripts.log`, `capture_loss.log` and more. Zeek does not provide log files for scripts that do not have any hits or results. We may also identify specific script paths if we need to load a specific script or framework. In this case, we'd run the command `zeek -C -r (FILE) (SCRIPT PATH)`. Zeek's documentation contains some prebuilt scripts and frameworks that we can use as needed.

**[Task 7, Question 1] Investigate the `sample.pcap` file with `103.zeek` script. Investigate the terminal output. What is the number of detected new connections?** - 87

**[Task 7, Question 2] Investigate the `ftp.pcap` file with `ftp-admin.sig` signature and `201.zeek` script. Investigate the `signatures.log` file. What is the number of signature hits?** - 1401

**[Task 7, Question 3] Investigate the `signatures.log` file. What is the total number of `administrator` username detections?** - 731

**[Task 7, Question 4] Investigate the `ftp.pcap` file with all local scripts, and investigate the `loaded_scripts.log` file. What is the total nubmer of loaded scripts?** - 498

**[Task 7, Question 5] Investigate the `ftp-brute.pcap` file with `/opt/zeek/share/zeek/policy/protocols/ftp/detect-bruteforcing.zeek` script. Investigate the `notice.log` file. What is the total number of brute-force detections?** - 2

## [Task 8] Zeek Scripts | Frameworks

The frameworks that Zeek provides can be used to discover different events of interest. We're interested in a few common frameworks and functions - specifically, the functionalities provided by the File Framework:
- The `hash-all-files` script gives us `md5`, `sha1`, and `sha256` fields in the `files.log` log, so we can get hashes for any detected files easily.
- The `extrat-all-files.zeek` script will (as the name suggests) extract all files from the pcap file. The new files will appear in an `extract_files` folder, and you can use `ls` and `file` to get an idea of what Zeek found. This includes timestamp information, source protocols, and connection IDs. You can correlate this information with other logs as needed.

There is also a functionality provided by the Notice Framework that we'd like to cover - the Intelligence function. This requires a feed to match and create alerts from network traffic. The feed (source file) must be tab-delimited, and you need to manually update the source. Adding new lines is simple enough, but deleting lines requires that Zeek be redeployed. The intelligence source information can be found in `/opt/zeek/intel/zeek_intel.txt`.

**[Task 8, Question 1] Investigate the `case1.pcap`file with `intelligence-demo.zeek` script. Investigate the `intel.log` file. Look at the second finding, where was the intel info found?** - `IN_HOST_HEADER`

**[Task 8, Question 2] Investigate the `http.log` file. What is the name of the downloaded .exe file?** - `knr.exe`

**[Task 8, Question 3] Investigate the `case1.pcap` file with `hash-demo.zeek` script. Investigate the `files.log` file. What is the MD5 hash of the downloaded `.exe` file?** - `cc28e40b46237ab6d5282199ef78c464`

**[Task 8, Question 4] Investigate the `case1.pcap` file with `file-extract-demo.zeek` script. Investigate the `extract-files` folder. Review the contents of the text file. What is written in the file?** - Microsoft NCSI

## [Task 9] Zeek Scripts | Packages

Lastly, the Zeek Package Manager allows users to install and setup third-party scripts and plugins to extend Zeek functionalities. This is installed with Zeek and can be accessed with `zkg`. Using this tool requires root privileges. Commands include
- `zkg install (PACKAGE PATH)` - allows you to install a package.
- `zkg install (GIT URL)` - allows you to install a package from a Git link.
- `zkg list` - lists installed packages
- `zkg remove` - removes installed packages
- `zkg refresh` - checks for version updates for installed packages
- `zkg upgrade` - updates installed packages.

There are multiple ways of using packages. You can use them as frameworks and call specific packages each time you use Zeek, you can use the `@load` method to load them via a script (which tends to be common), and you can also call package names directly if the packages were installed by `zkg`:
- If you want to call it with a script, use `zeek -Cr (FILE) (ZEEK SCRIPT)`. This assumes the Zeek script has `@load (SCRIPT PATH)` in there somewhere.
- You can call it from a path: `zeek -Cr (FILE) (PATH)`.
- You can call it with a package name: `zeek -Cr (FILE) (NAME)`.

There are many packages - the ones given in the room as examples include packages to sniff out cleartext credentials as well as packages to sniff out geolocation information for IP addresses.

**[Task 9, Question 1] Investigate the `http.pcap` file with the `zeek-sniffpass` module. Investigate the `notice.log` file. Which username has more module hits?** - BroZeek

**[Task 9, Question 2] Investigate the `case2.pcap` file with `geoip-conn` module. Investigate the `conn.log` file. What is the name of the identified City?** - Chicago

**[Task 9, Question 3] Which IP address is associated with the identified City?** - `23.77.86.54`

**[Task 9, Question 4] Investigate the `case2.pcap` file with `sumstate-counttable.zeek` script. How many types of status codes are there in the given traffic capture?** - 4
