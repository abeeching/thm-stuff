# Wireshark: Packet Operations

## [Task 1] Introduction

This is a continuation of the Wireshark Basics room, focusing on how to investigate events of interest at the pacet level. We cover advanced features regarding Wireshark's statistical capabilities, along with filters, operators, and functions.

## [Task 2] Statistics | Summary

The Statistics menu provides many statistics options that can help analysts investigate the traffic scope, available protocols, endpoints, and conversations, along with details specific to certain protocols. The summary information can be useful when trying to develop hypotheses for an investigation. Here's the provided information:
- Resolved Addresses: Identify IP addresses and DNS names available in the capture file, with hostname information being pulled from DNS answers. Can be helpful in identifying accessed resources. Access by going to Statistics -> Resolved Addresses.
- Protocol Hierarchy: Breaks down avaialble protocols and displays them in a tree view based on packet counters and percentages. Helps with understanding usage of ports and services. You can right-click and filter for events of interest. Access by going to Statistics -> Protocol Hierarchy.
- Converastions: Displays traffic between specific endpoints, listed in terms of ethernet (MAC) addresses, IPv4, IPv6, TCP, and UDP. Helpful for identifying all contact endpoints and conversations. Access by going to Statistics -> Conversations.
- Endpoints: Similar to Conversations. Provides unique information for a single information type (e.g. Ethernet/MAC Address, IPv4, IPv6, TCP, UDP). You can identify unique endpoints in the capture file with this. Access by going to Statistics -> Endpoints.
  - If you check the Name resolution button, you can convert the MAC addresses to manufacturer names. This works only if the manufacturers are well-known enough.
  - Wirshark also lets you do IP-geolocation mapping. This is not enabled by default and requires a GeoIP database. Wireshark supports MaxMind databases, and it has a MaxMind database resolver. You still need the database files, though, and you can point to them with Edit -> Preferences -> Name Resolution -> MaxMind database directories.

For the first question, we'll take a look at Statistics -> Resolved Addresses. Fortunately, we can type part of the hostname in, which helps us avoid having to scroll through a large list of addresses and hosts. Doing so gives us the information we need:

![image](https://github.com/user-attachments/assets/55986220-3f3d-4fa2-b071-e74e7744c251)

**[Task 2, Question 1] Investigate the resolved addresses. What is the IP address of the hostname that starts with `bbc`?** - `199.232.24.81`

For this one, we'll want to go to Statistics -> Conversations. We don't have to look far for the answer - the tabs tell us how many conversations there are for each protocol!

![image](https://github.com/user-attachments/assets/1ab2cda3-5955-44dc-93a0-b6f136576030)

**[Task 2, Question 2] What is the number of IPv4 conversations?** - 435

To answer the next question, we might be able to get away with using the Conversations option, but this lists out all unique conversations involving the MAC address, and we just want how many btes were transferred generally. This would require doing some more legwork to get the answer. So instead, we'll want to go to Statistics -> Endpoints. By default, Wireshark displays the MAC addresses in standard form, so we'll want to check the Name Resolution box to convert the addresses to actual names.

![image](https://github.com/user-attachments/assets/e564b8e0-3ade-483f-a954-06b1bc3a0f32)

**[Task 2, Question 3] How many bytes (k) were transferred from the "Micro-St" MAC address?** - 7474

The required information for using IP address geolocation is already set up in the VM. Thus, we can check Statistics -> Endpoints, and go to the IPv4 tab. It'll help if you sort by the City column, and then count the number of instances of "Kansas City."

![image](https://github.com/user-attachments/assets/45d5ce0d-c01c-4d8b-aad7-bf10c5b0ad27)

**[Task 2, Question 4] What is the number of IP addresses linked with "Kansas City"?** - 4

We can also get this information by checking Statistics -> Endpoints, and going to the IPv4 tab. This time, we can sort by AS Organization and find the entry with Blicnet:

![image](https://github.com/user-attachments/assets/aefba3a2-1e86-4b25-8c5d-136ac2614818)

**[Task 2, Question 5] Which IP address is linked with "Blicnet" AS Organization?** - `188.246.82.7`

## [Task 3] Statistics | Protocol Details

As mentioned earlier, you can get information about specific protocols.
- IPv4 and IPv6: You can narrow down the statistics on packets for specific IP versions. Access by going to Statistics -> IPv4 Statistics, or IPv6 Statistics.
- DNS: You can analyze DNS packets, how the packets were used, opcodes, rcodes, classes, query types, and more. This can be found in Statistics -> DNS.
- HTTP: You can get break down the HTTP packets based on requests, responses, and other related information. This can be found in Statistics -> HTTP.

For this question, we can go to Statistics -> IPv4 Statistics. I chose the Source and Destination Addresses option, looked only at the Destination IPv4 addresses and sorted by count:

![image](https://github.com/user-attachments/assets/2b54fb55-aeba-4522-a683-0913c958b808)

**[Task 3, Question 1] What is the most used IPv4 destination address?** - `10.100.1.33`

This information can be found by going into Statistics -> DNS and looking for the `max val` entry for `request-response time (secs)`:

![image](https://github.com/user-attachments/assets/c069d0b1-15fb-468b-8f1a-24b5543314e7)

**[Task 3, Question 2] What is the max service request-response time of the DNS packets?** - `0.467897`

This information can be found by going into Statistics -> HTTP -> Load Distribution (surprisingly not Requests -- this just lists out all the requests that were made and makes answering this question much harder than it has to be). You're looking for HTTP Requests by Server, and you can drill down deeper into the HTTP Requests by HTTP Host - just look for `rad[.]msn[.]com` -- the count of requests will be right next to it:

![image](https://github.com/user-attachments/assets/16b51695-b88f-4c0c-ae31-3130e0069bf6)

**[Task 3, Question 3] What is the number of HTTP requests accomplished by `rad[.]msn[.]com`?** - 39

## [Task 4] Packet Filtering | Principles

To review, there are two kinds of filters in Wireshark:
- Capture filters let you save only specific parts of incoming traffic, and is set BEFORE capturing traffic.
- Display filters let you display only certain packets in a packet capture, and can be changed during capture.
- These filters cannot be used interchangeably.

Typically you capture everything, and then filter out only the packets according to the event of interest. Experienced analysts are more likely to use capture filters, but these only support certain protocol types. Wireshark supports more protocol types in display filters. You should make sure you understand how to use capture filters before making use of them in a live environment.

Capture filters use byte offset hex values and masks with boolean operators. It's hard to understand how they work at a first glance. Some of the base syntax is as follows:
- Scope is defined with host, net, port, portrange
- Direction is defined as src, dst, src or dst, src and dst
- Protocol is defined as ether, wlan, ip, ip6, arp, rarp, tcp, udp
- Then you can use some basic constructs to capture certain traffic, e.g. `tcp port 80` to capture HTTP traffic.

Wireshark's documentation contains more information on capture filters, but you can use the Capture -> Capture Filters menu to get a quick reference.

Wireshark also supports many protocols for use with their display filters, and like with capture filters, there are display filter references both in the Wireshark documentation and in the program itself (Analyze -> Display Filters). The Display Filter Expression option keeps track of supported protocol structures to help with creating display filters.

The syntax for Wireshark display filters includes the following:
- Comparison operators:
  - Equal to: `eq`, `==`
  - Not equal: `ne`, `!=`
  - Greater than: `gt`, `>`
  - Less than: `lt`, `<`
  - Greater than or equal to: `ge`, `>=`
- Logical expressions:
  - Logical AND: `and`, `&&`
  - Logical OR: `or`, `||`
  - Logical NOT: `!`. You used to do `!=(value)`, but that has since been deprecated. Use `!(value)` instead.
- Wireshark supports both decimal and hex values for filters.

Note that packet filters are defined in lowercase.

The packet filter toolbar - located above the packet list - is where you can create and apply display filters. It helps show you if you have a valid filter or not. Packet filters may have additional protocol details, which you may access by appending a dot. For instance, `ip.src` allows us to filter for source IP addresses. If you put a dot in the filter toolbar, Wireshark will help autocomplete details. You can also add bookmarks and pull up recently-applied filters. The toolbar also changes in color depending on what you input:
- If it's green, then you have a valid filter.
- If red, then you have an invalid filter.
- If yellow, then the filter works, but it is unreliable or deprecated. It's a cue to change it to a valid filter.

## [Task 5] Packet Filtering | Protocol Filters

Adding filters based on IP traffic is commonly done in Wireshark. This can help filter packets so that you're left only with packets for certain hosts, for instance. Some common IP filters include:
- `ip`, which just shows all IP packets
- `ip.addr == (IP)`, which shows all IP packets containing the address given, regardless of the direction of communication (that is, it'll show packets containing the IP address, whether it is the source or destination address).
- `ip.addr == (CIDR)`, which shows all IP packets from a given subnet, written in CIDR notation
- `ip.src == (IP)`, which shows all IP packets originating from the given address
- `ip.dst == (IP)`, which shows all IP packets sent to a given address

We can also apply filters for TCP and UDP. We often use this to filter for ports, among other things.
- `tcp.port == (PORT)` shows all TCP packets with the given port, regardless of the direction of communication
- `tcp.srcport == (PORT)` shows all TCP packets originating from the given port
- `tcp.dstport == (PORT)` shows all TCP packets sent to the given port
- These filters can be applied to UDP traffic as well; simply replace `tcp` with `udp`.

We can also filter for specific application-level protocol information, which can help if we're looking for specific types of payloads or data.
- `http` shows all HTTP packets.
- `http.response.code == (CODE)` shows all packets with the given HTTP response code.
- `http.request.method == (METHOD)` shows all packets with the given HTTP request method, e.g. GET or POST
- `dns` shows all DNS packets.
- `dns.flags.response == 0` shows all DNS requests.
- `dns.flags.response == 1` shows all DNS responses.
- `dns.qry.type == 1` shows all DNS A records.

Again, we can use the Display Filter Expression option to help with creating filters. If you're not sure how to structure a certain filter, or if you don't remember what the valid values for a filter are, you can check this option out. It provides a display filter builder guide. This is useful because there are simply so many protocols and filters to work with, each with their own assignable values.

It is also worth noting that you can apply coloring rules to display filter results.

If we're just looking for IP packets, we can simply use `ip` as our display filter. Simply type it into the Filter Toolbar, press ENTER, and check the status bar:

![image](https://github.com/user-attachments/assets/da12a9ff-afac-427d-83ce-60e6d07817d4)

**[Task 5, Question 1] What is the number of IP packets?** - 814230

The query writes itself. We want IP packets (so we start with `ip`) with TTLs (`ip.ttl`) less than 10. Our final query will be `ip.ttl < 10`. Again, we check the status bar:

![image](https://github.com/user-attachments/assets/83be493a-2ed7-40b0-b224-a900e670fde5)

**[Task 5, Question 2] What is the number of packets with a TTL value less than 10?** - 66

If we're looking for the number of packets using TCP port 4444, we can simply use the filter `tcp.port == 4444`. This yields...

![image](https://github.com/user-attachments/assets/784d195c-58c8-4009-a46e-2bf13c851982)

**[Task 5, Question 3] What is the number of packets which uses TCP port 4444?** - 632

So there are two conditions we need to look out for: HTTP GET requests (which we can find with the filter `http.request.method == GET`) and packets sent to port 80 (since we're working with HTTP, we'd want to use `tcp.dstport == 80`). We can combine these with `and` to get the query `http.request.method == GET and tcp.dstport == 80`. The Status Bar gives us the following:

![image](https://github.com/user-attachments/assets/5a6437b6-c397-4081-ae28-7711403b6270)

**[Task 5, Question 4] What is the number of HTTP GET requests sent to port 80?** - 527

Here we'll want to filter for type A DNS queries with `dns.qry.type == 1`. We'll also want to filter for DNS responses - that is, the number of queries that actually give us type A records - so we'll actually have `dns.qry.type == 1 and dns.flags.response == 1`.

![image](https://github.com/user-attachments/assets/be42eca2-8e3d-48f8-8c84-247a0bb341c7)

**[Task 5, Question 5] What is the number of type A DNS queries?** - 51

## [Task 6] Advanced Filtering

There are some additional keywords and features that you can use to look for specific events of interest.
- `(x) contains (y)` is a comparison operator that lets you search for a value inside packets. Similar to using the Find option. For instance, `http.server contains "Apache"`
- `(x) matches (y)` is a comparison operator that lets you search with a regular expression. It's case-insensitive, and there's a little margin of error for complex queries. For instance, `http.host matches "\.(php|html)"`.
- `(x) in (y)` is a set membership operator that lets you search for a value or field inside of a specific scope or range. For instance, `tcp.port in {80 443 8080}` will look for TCP packets with port 80, 443, or 8080 (the standard HTTP/S ports).
- `upper(x)` is a function that lets you convert a string value to uppercase, e.g. `upper(http.server) contains "APACHE"`.
- `lower(x)` is a function that lets you convert a string value to lowercase. Note that, like the example above illustrates, these functions can be applied towards a filter -- all values in that filter will be made upper- or lowercase.
- `string(x)` converts a non-string value to a string, e.g. `string(frame.number) matches "[13579]$"`.
- Bookmarks allow you to save user-created filters as bookmarks and buttons for later use. You can reuse complex filters with a few clicks, or you can create buttons to apply a certain filter with a single click.
- Wireshark provides profiles, allowing you to set different configurations, coloring rules, filtering buttons, etc. at once. These can be accessed from Edit -> Configuration Profiles, or you can click in the lower-right corner of the status bar, and click on Profile.

**[Task 6, Question 1] Find all Microsoft IIS servers. What is the number of packets that did not originate from port 80?** - 21

We'll note that, in packets originating from Microsoft IIS servers, the string "IIS" will be present there somewhere. Hence, we'll want to start with `http.server contains "IIS"`. We'll also note that we're looking for packets NOT originating from port 80. We can assume they're talking about TCP packets (port 80 TCP is HTTP, after all), so we'll also want to add `tcp.srcport != 80`. Our final query is `http.server contains "IIS" and tcp.srcport != 80`, or if you want to avoid using deprecated filter structures, `http.server contains "IIS" and !(tcp.srcport == 80)`.

![image](https://github.com/user-attachments/assets/a0f3a27c-4192-4c46-a657-8fa2f9d49765)

For this one, we'll want to note that, in HTTP packets, the IIS Server will have the version listed after the name. For instance, a packet from an IIS server running version 6.0 has the string `IIS/6.0` somewhere inside of it. Thus, we'll want to look for `http.server contains "IIS/7.5"`.

![image](https://github.com/user-attachments/assets/42c39f86-c926-4576-a2c1-07c04447256e)

**[Task 6, Question 2] Find all Microsoft IIS servers. What is the number of packets that have version 7.5?** - 71

If we want the total number of packets using ports 3333, 4444, or 9999, we can use the filter `tcp.port in {3333, 4444, 9999}`:

![image](https://github.com/user-attachments/assets/c3dff0f0-e4c4-41a8-be2a-acd7a5d4d3db)

**[Task 6, Question 3] What is the total number of packets that use ports 3333, 4444, or 9999?** - 2235

One way to answer this next question is to make use of regex. The regex `[02468]$` will look for the numbers 0, 2, 4, 6, or 8 at the end of a line. If we can filter specifically for TTL values, this has the effect of giving us only the even TTL values. However, Wireshark treats TTLs as numerical values rather than strings, so we'll need to convert the TTL field to a string first. Thus, our full query should be `string(ip.ttl) matches "[02468]$"`.

![image](https://github.com/user-attachments/assets/1f7cbfde-90c8-4c65-82f3-b285e56ce633)

**[Task 6, Question 4] What is the number of packets with even TTL numbers?** - 77289

We can switch the profile either by using Edit -> Configuration Profiles and choosing the profile in question, or we can go to the bottom right, click on Profile: Default and change it to Checksum Control. You'll need to investigate the packet list to see how they've set things up -- they color packets with a bad TCP checksum with red text on a black background. I clicked into one of these packets, checked the TCP information, and checked the Checksum information. You can right-click the "[Bad checksum]" line and apply a filter to get `tcp.checksum_bad.expert`. This number of packets with a bad TCP checksum can be found in the status bar:

![image](https://github.com/user-attachments/assets/c044f4d7-7380-416c-86c4-64a113d26a21)

**[Task 6, Question 5] Change the profile to "Checksum Control." What is the number of Bad TCP Checksum packets?** - 34185

The button in question is next to the toolbar, and it reads "`gif/jpeg with http-200`" - if we click this, a filter gets automatically applied. From there, we can check the status bar to get the number of displayed packets:

![image](https://github.com/user-attachments/assets/b8fd72e7-155a-464f-82a8-5aa0946942e5)

**[Task 6, Question 6] Use the existing filtering button to filter the traffic. What is the number of displayed packets?** - 261

## [Task 7] Conclusion
