# Tshark: CLI Wireshark Features

## [Task 1] Introduction

Now that we know how to use tshark, we will now focus on how the advanced features of Wireshark are implemented in tshark. As the premise suggests, this room will make no sense if you're not familiar with Wireshark.

## [Task 2] Command-Line Wireshark Features I | Statistics I

We've pointed out that tshark is a command-line version of Wireshark. Turns out this statement holds even for Wireshark's various advanced features. A few things to note before we dive into the different commands:
- The options are applied to all packets in scope unless a display filter is provided.
- Most of the commands shown are CLI equivalents to the features discussed in the Wireshark rooms.
- Tshark will explain the parameters used at the beginning of the output - for instance, if you use the protocol hierarchy option in tshark, you'll see `Packet Hierarchy Statistics` in the header.

First, we'll look at colorization and some basic statistics.
- To apply Wireshark-like colored output for the packets, use `--color`.
- To display statistics, use `-z`. This has a lot of other options, and you can view them with `-z help`. One such option is `-z filter`. Each time you filter the packets, statistics are shown first, and then the packets are provided.
- Note that you can focus on just the statistics by using `-q`.

We'll dive into these a bit more. Colorized output, as discussed in the Wireshark rooms, can be helpful for spotting certain protocols in use and potential anomalies quickly, so it may be helpful to run `tshark -r (PCAP) --color`.

Now we'll talk about the statistics options. The first option is the protocol hierarchy, which helps show you the protocols used, frames involved, and packet sizes. This acts as more of a summary of the capture. You can access this by using `-z io,phs -q`. Once you view the packet tree, you can add on additional protocols, e.g. `-z io,phs,udp -q`. This will display additional packets, such as DNS packets (which are UDP traffic).

You can also see a packet lengths table/tree. This helps you get a general idea of how the packets are distributed by size, and it may be helpful if you're on the lookout for anomalously large or small packets. This can be accessed via `-z plen,tree -q`.

You're also able to view endpoint information as you could in Wireshark. In tshark, the `-z endpoints -q` option lets you view unique endpoints, as well as the number of packets associated with each endpoint. You're also able to apply filters, so you could use `-z endpoints,ip -q` to view endpoints in IP traffic. There are other filters you can apply:
- `eth` for Ethernet addresses,
- `ipv6` for IPv6,
- `tcp` for TCP,
- `udp` for UDP, and
- `wlan` for IEEE 802.11.

There is also the ability to view conversations, which can help you analyze the traffic between two connection points. You can also view these in different formats using the same parameters as the `endpoints` option. You can use `-z conv,(PARAMETERS) -q` to access this information.

And lastly, you can access expert information as you could in Wireshark by using `-z expert -q`.

For this first question, we'll want to gather some basic protocol hierarchy statistics. The command for doing so is `tshark -r write-demo.pcap -z io,phs -q`, and it yields the following information.

![image](https://github.com/user-attachments/assets/f6a8d617-383a-41c9-9913-3619910daec5)

**[Task 2, Question 1] What is the byte value of the TCP protocol?** - 62

We'll now want to get packet length information, which we can acquire by running `tshark -r write-demo.pcap -z plen,tree -q`. Investigating the table, we see only one packet, and our answer will be the range it shows up in, `40-79`:

![image](https://github.com/user-attachments/assets/ac78d238-dae0-49e5-80c6-cc60319deecc)

**[Task 2, Question 2] In which packet lengths row is our packet listed?** - 40-79

To grab the expert information for this file, run `tshark -r write-demo.pcap -z expert -q`. We are looking at the information provided under Summary.

![image](https://github.com/user-attachments/assets/211bbf64-9819-4ee9-b7f7-6a25c584c86c)

**[Task 2, Question 3] What is the summary of the expert info?** - Connection establish request (SYN): server port 80

Now we are working with `demo.pcapng`. We want to find the IP address involved in all IPv4 conversations, so we run the command `tshark -r demo.pcapng -z conv,ip -q`. Examining the output, we really do only see one IP show up in all three conversations:

![image](https://github.com/user-attachments/assets/2eb7d610-11f0-4f36-a99b-06c93dfaa662)

**[Task 2, Question 4] List the communication. What is the IP address that exists in all IPv4 conversations?** - `145[.]254[.]160[.]237`

## [Task 3] Command-Line Wireshark Features II | Statistics II

As we saw in Wireshark, we can apply specific filters and obtain specific statistics for specific protocols. For instance, we can get statistics on IPv4/IPv6 usage.
- With `-z ptype,tree -q`, we can get information on available protocol types and details.
- If we want to view the hosts, we can use `-z ip_hosts,tree -q` or `-z ipv6_hosts,tree -q`, whichever is needed.
- We may need to correlate information regarding source and destination IPs; in this case, use `-z ip_srcdst,tree -q` or `-z ipv6_srcdst,tree -q`.
- If you just need to see the outgoing traffic (and thus see used ports and services), use `-z dests,tree -q` or `-z ipv6_dests,tree -q`.

We can get DNS statistics by using `-z dns,tree -q`.

We may want to get information on HTTP packets - their distributions, requests, packets, status information, you name it. Here are some options that may help:
- For viewing HTTP packet and status information: `-z http,tree -q`
- For viewing HTTP2 packet and status information: `-z http2,tree -q`
- For viewing load distribution: `-z http_srv,tree -q`
- For viewing requests: `-z http_req,tree -q`
- For viewing requests and responses: `-z http_seq,tree -q`.

Still working in `demo.pcapng`, we can start looking at occurrences of various IP addresses. Let's start by running `tshark -r demo.pcpang -z ip_hosts,tree -q`.

![image](https://github.com/user-attachments/assets/badea03f-ea73-4a47-a19f-f0e5f2bac00a)

**[Task 3, Question 1] Which IP address has 7 appearances?** - `216[.]239[.]59[.]99`

Keeping the above IP address in mind, we'll now run `tshark -r demo.pcapng -z ip_srcdst,tree -q`. This gives a lot of output, but fortunately what we're looking for is at the bottom of said output (if it wasn't, we'd be better off using a tool like `grep` to search for the IP address manually). The values toward the bottom of the output are for destination addresses, whereas the outputs closer to the top are for source addresses.

![image](https://github.com/user-attachments/assets/4ed2afa7-6ba2-4106-b283-79a7753f15f2)

**[Task 3, Question 2] What is the "destination address percentage" of the previous IP address?** - 6.98%

The answer to the next question is, incidentally, at the very bottom of the output from the previous questioN:

![image](https://github.com/user-attachments/assets/215ab64d-02de-4776-b89f-146879846cf5)

**[Task 3, Question 3] Which IP address consititutes "2.33% of the destination addresses"?** - `145[.]253[.]2[.]203`

If we want to find "Qname Len" values, our best bet would be to check DNS -- this looks like it means "query name lengths," anyway. So, if we run `tshark -r demo.pcapng -z dns,tree -q`, we get a lot of information. There's a row for "Qname Len" and the second number represents the average value.

![image](https://github.com/user-attachments/assets/9cc0b7f2-3722-4f06-9e20-906bdbbaf121)

**[Task 3, Question 4] What is the average "Qname Len" value?** - 29.00

## [Task 4] Command-Line Wireshark Features III | Streams, Objects, and Credentials

There are additional filters for the different things we could do in Wireshark, like following streams or extracting objects and credentials.

When it comes to following streams, there's a standard query structure you can follow: `-z follow (TCP|UDP|HTTP|HTTP2),(HEX|ASCII),(0|1|2|3|...) -q`. Basically, we need to figure out what kind of stream we'd like to follow, whether to display it in hexadecimal or ASCII form, and then the actual stream to follow. Streams are numbered starting with 0. So, if we wanted to follow HTTP stream #3, we'd use `-z follow,http,ascii,3 -q`.

If we want to export objects (that is, extract files), there's also a standard query we can use for this: `--export-objects (DICOM|HTTP|IMF|SMB|TFTP),(TARGET FOLDER TO SAVE FILES) -q`. Thus, if we wanted to save HTTP files to our Desktop, we might run `--export-objects http,/home/ubuntu/Desktop -q`.

Lastly, if we want to detect and get cleartext credentials from the cleartext protocols (FTP, HTTP, IMAP, POP, and SMTP), we can run `-z credentials -q`. Tshark will display the usernames and the packets associated with them.

Again, we'll work in `demo.pcapng`. First we follow UDP stream 0 by running `tshark -r demo.pcapng -z follow,udp,ascii,0 -q`. The Node 0 information is located near the top of the output:

![image](https://github.com/user-attachments/assets/4ad026e6-363e-4e02-86b1-cf555f48dde2)

**[Task 4, Question 1] Follow the "UDP stream 0." What is the "Node 0" value?** - `145[.]254[.]160[.]237:3009`

Now we want to look at an HTTP stream, so we'll run `tshark -r demo.pcapng -z follow,http,ascii,1`. Looking through the output, we'll find the Referer at the bottom of the topmost entry:

![image](https://github.com/user-attachments/assets/6b444d6f-bfa4-48b3-8070-495ad244ad00)

**[Task 4, Question 2] Follow the "HTTP stream 1." What is the "Referer" value?** - `hxxp[://]www[.]ethereal[.]com/download[.]html`

Now we are going to look for credentials in the `credentials.pcap` file. To do so, we'll want to run `tshark -r credentials.pcap -z credentials -q | nl`. When we want to count how many detected credentials there are, we want to exclude the header and footer of the output (i.e. the equals signs, the column names, and so on). There are four of these lines, so we should have 75 lines left over of just credentials:

![image](https://github.com/user-attachments/assets/f0321879-9b6e-4dfa-82d7-8190ca32329a)

**[Task 4, Question 3] What is the total number of detected credentials?** - 75

## [Task 5] Advanced Filtering Options | Contains, Matches, and Fields

Sometimes we'd like to filter for specific information, but the default filters given by tshark/Wireshark don't cut it. In this case, we can use the `contains` and `matches` operators -- these work exactly as they do in Wireshark. Generally speaking:
- The `contains` operator looks for values inside packets and is case-sensitive. It's similar to a "find" option.
- The `matches` operator looks for patterns (including regex) inside packets and is also case-insensitive. This is useful for complex queries, though there is a margin of error.
- Note that you cannot use these operators with integer-valued fields, and you can use ASCII values for a match...though hex and regex generally work better.

We may need to extract fields - specific parts of data - from our packets to collect and correlate information. If nothing else, it can help with managing the amount of output that shows up on the terminal. To extract fields, use the query structure: `-T fields -e (FIELD NAME) [-e (FIELD NAME) ...] -E header=y`. Note that if you want to extract multiple fields, you will need to use `-e (FIELD NAME)` multiple times. The `-E header=y` displays the field names as headers.

The `contains` operator is a comparison operator.
- It looks for values inside packets. It's case-sensitive and works like the "find" option for a specific field.
- As an example, you may use `http.server contains "Apache"` to find all packets whose `server` field contains "Apache".
- Note that when you use these operators, you will need to still use `-Y`, so the above command would be `-Y 'http.server contains "Apache"'`. This is also why we suggested enclosing your queries in single-quotes -- you'll typically want to enclose whatever it is you're looking for in double-quotes.

The `matches` operator is also a comparison operator.
- This time, though, it looks for patterns and regular expressions. It's also case-insensitive, though there is a margin of error for more complex queries.
- Say you want to find all HTTP packets whose request method was a GET or POST. In this case, our filter might be `http.request.method matches "(GET|POST)"`.

We will go back to `demo.pcapng`. To find the packet that contains "CAFE", we should use the `contains` operator as follows: `tshark -r demo.pcpang -Y 'http contains "CAFE"'`. Running this command, we should get one packet back, and the packet number will be the very first number we see in the output.

![image](https://github.com/user-attachments/assets/8b3d77c3-db07-4096-ab8e-655066343066)

**[Task 5, Question 1] What is the HTTP packet number that contains the keyword "CAFE"?** - 27

We'll want to combine a couple of techniques. For one, since we're looking for GET and POST requests, we will need to use `http.request.method matches "(GET|POST)"`. It'll also help if we can extract the exact packet's timeframe, so we will also want to add `-T fields -e frame.time` to our final command. Thus, our final command should be `tshark -r demo.pcapng -Y 'http.request.method matches "(GET|POST)"' -T fields -e frame.time`. We get two lines of output, and we only care about the first one:

![image](https://github.com/user-attachments/assets/2ce6086e-2bc3-49f8-9e36-5467532d2127)

**[Task 5, Question 2] Filter the packets with "GET" and "POST" requests and extract the packet frame time. What is the first time value found?** - `May 13, 2004 10:17:08.222534000 UTC`

## [Task 6] Use Cases | Extract Information

There are many common use-cases for tshark filters. You should be familiar with some of the techniques used to acquire low-hanging fruit (though more use-cases are studied in the Wireshark rooms, namely the Traffic Analysis one):
- To get hostnames, use `tshark -r (PCAP) -T fields -e dhcp.option.hostname`.
  - You may want to organize the output and deduplicate it via pipes, e.g. `tshark -r (PCAP) -T fields -e dhcp.option.hostname | awk NF | sort -r | uniq -c | sort -r`. This removes empty lines, sorts, shows unique values with the number of occurrences of each, and then sorts by the number of occurrences.
- To extract DNS queries, use `tshark -r (PCAP) -T fields -e dns.qry.name | awk NF | sort -r | uniq -c | sort -r`.
- To extract user agents, use `tshark -r (PCAP) -T fields -e http.user_agent | awk NF | sort -r | uniq -c | sort -r`.

Let's put some of these use-cases to work. First, we'll look at `hostnames.pcapng`. If we want to find the total number of unique hostnames, we should run `tshark -r hostnames.pcapng -T fields -e dhcp.option.hostname | awk NF | sort -r | uniq | nl`. This will give us all the unique hostnames, as well as a count of how many there are:

![image](https://github.com/user-attachments/assets/f8dacee8-41d7-453c-9d21-b15f0c0171b6)

**[Task 6, Question 1] What is the total number of unique hostnames?** - 30

We'll modify our previous command to figure out how many times `prus-pc` shows up. Since we want to find that hostname and how often it occurs, we could run `tshark -r hostnames.pcapng -T fields -e dhcp.option.hostname | awk NF | sort -r | uniq -c | sort -r | grep "prus-pc"`. Running this yields...

![image](https://github.com/user-attachments/assets/cbecdf59-cac2-4d95-a181-24682b4222ff)

(Realistically, you could just run this without the last `sort -r` command -- you'll end up with one line of output anyway...)

**[Task 6, Question 2] What is the total appearance count of the `prus-pc` hostname?** - 12

Now we move to `dns-queries.pcap`. We'd like to find the most common DNS query, and then figure out how many times the query appears. So we'll run `tshark -r dns-queries.pcap -T fields -e dns.qry.name | awk NF | sort -r | uniq -c | sort -r`. Looking at the output, the answer jumps out at us pretty easily:

![image](https://github.com/user-attachments/assets/8b8eb0a7-c1db-4564-9ba3-30be01f1ef5f)

**[Task 6, Question 3] What is the total number of DNS queries of the most common DNS query?** - 472

For our last two questions, we'll look at `user-agents.pcap`. We can run the command `tshark -r user-agents.pcap -T fields -e http.user_agent | awk NF | sort -r | uniq -c | sort -r`, and then just add up how many times we see `Wfuzz`:

![image](https://github.com/user-attachments/assets/88639662-abfa-4928-877b-59644418ed7b)

**[Task 6, Question 4] What is the total number of detected "Wfuzz user agents"?** - 12

For this last one, we'll want to add an extra field to our filter: `-e http.host`. This should give us the HTTP hostname for the packets that have the Nmap Scripting Engine as their user-agent. Thus our command could be something like `tshark -r user-agents.pcpa -T fields -e http.user_agent -e http.host | awk NF | sort -r | uniq -c | sort -r`. We may want to consider using `grep` to look only for the Nmap Scripting Engine instances, but the answer will show up in our output regardless:

![image](https://github.com/user-attachments/assets/7b36b9ce-cd62-425a-b027-f67b7cc2338c)

**[Task 6, Question 5] What is the "HTTP hostname" of the nmap scans?** - `172[.]16[.]172[.]129`
