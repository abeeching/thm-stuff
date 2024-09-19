# Zeek - Exercises

## [Task 2] Anomalous DNS

First, we'll run Zeek on the given pcap in `Exercise-Files/anomalous-dns`: `zeek -C -r dns-tunneling.pcap`. Once we do so, we can investigate the contents of `dns.log`. DNS will store IPv6 information in AAAA records, and the record type is given by the field `qtype_name`. So, if we want to find how many IPv6 records there are, then we may run a command like `cat dns.log | zeek-cut qtype_name | grep "AAAA" | wc -l`.

![image](https://github.com/user-attachments/assets/bf98f311-d1a9-40f8-8be3-db5a0a58569d)

**[Task 2, Question 1] Investigate the `dns-tunneling` pcap file. Investigate the `dns.log` file. What is the number of DNS records linked to the IPv6 address?** - 320

We saw a question of this sort in the Zeek introductory room, and a similar solution works here: we `cat` the `conn.log` file, `zeek-cut` on the `duration` field, and then use `sort -n` to figure out what the longest duration was. We can use `uniq` too if we want to remove duplicates. The longest duration will be at the bottom of the output:

![image](https://github.com/user-attachments/assets/8c3781ba-efd3-4e04-a698-50b5bcaa146d)

**[Task 2, Question 2] Investigate the `conn.log` file. What is the longest connection duration?** - `9.420791`

This is actually quite the tricky question. The `query` field is what we're interested in, so we might initially start with a command like `cat dns.log | zeek-cut query | sort | uniq`, but there's a problem. The `cisco-update.com` domain has many, many subdomains, and we just need to figure out how many unique domans there are. So all of these subdomains would count as 1 single domain for the purposes of this question.

We'll need to add a little more to our command so we can get the correct number of queries. One way to do it is given in the hint, and it involves cutting off the subdomain from the output -- we use `cat` and `zeek-cut` first as per usual, and then we use `rev` to temporarily reverse the lines -- that way, we have `moc.etadpu-ocsic...` 

Then we can use `cut -d '.' -f 1-2` to recover only the top-level domain (TLD, e.g. `.com`) and the second-level domain (e.g., `cisco-update`), and then we use `rev` again to print the lines forwards. After all of that, then we can use the `sort`, `uniq` and `wc` commands to get the number of unique domains.

We do this because every query will at least have a top-level domain (e.g. `.com`) and a second-level domain name (e.g. `ubuntu` or `cisco-update`). Not all queries will have a subdomain, however, and so if we just used `cut`, we'd lose the actual domains and be left with the TLDs in most cases. This causes our number to be off, since two different domains can share the same TLD.

Our full command is `cat dns.log | zeek-cut query | rev | cut -d '.' -f 1-2 | rev | sort | uniq`, though we can add `wc -l` too to get the exact number.

![image](https://github.com/user-attachments/assets/03e7599f-c31a-4195-a2b1-db23cc4d0216)

**[Task 2, Question 3] Investigate the `dns.log` file. Filter all unique DNS queries. What is the number of unique domain queries?** - 6

Now we need to investigate `conn.log` again. We're looking for the source IP address of a host that is sending a massive amount of DNS queries out. That means we're going to be interested in the `id.orig_h` and `service` fields, and so we can run `cat conn.log | zeek-cut id.orig_h service | sort | uniq -c` to see who's using DNS the most.

![image](https://github.com/user-attachments/assets/b338bdd2-71a3-491a-8d83-89336caa43ed)

**[Task 2, Question 4] There are a massive amount of DNS queries sent to the same domain. This is abnormal. Let's find out which hosts are involved in this activity. Investigate the `conn.log` file. What is the IP address of the source host?** - `10.20.57.3`

## [Task 3] Phishing

We are now working in `Exercise-Files/phishing`. We are given scripts for getting hashes and files, so I decided to run Zeek with both of these: `zeek -C -r phishing.pcap file-extract-demo.zeek hash-demo.zeek`, and then I decided to check the `http.log` file to see if there's anything suspicious there. We see that one source IP is involved with a Word and an .exe file, which is suspicious in the context of a phishing attempt.

![image](https://github.com/user-attachments/assets/54395e8e-54db-4ec9-955b-58b69581dc55)

The source IP is the first IP in each entry of the log.

**[Task 3, Question 1] Investigate the logs. What is the suspicious source address? Enter your answer in defanged format.** - `10[.]6[.]27[.]102`

We also see where the file comes from in `http.log` - the suspicious files both come from `smart-fax`.

**[Task 3, Question 2] Investigate the `http.log` file. Which domain address were the malicious files downloaded from? Enter your answer in defanged format.** - `smart-fax[.]com`

Now to investigate the suspicious files. The files in question are in `extract_files`, assuming that the file extraction script was used. We can investigate the files in VirusTotal by grabbing their hashes. If the hash script was used, hashes should be available in the logs proper, but you can also hash the relevant file in `extract_files` using commands like `sha256sum` or `md5sum`. We're particularly interested in the Word document for this question, so I used `file *` to identify the Word document, and then I grabbed the hash:

![image](https://github.com/user-attachments/assets/e30b0c90-fac1-4d56-b16d-c1dc07349a53)

Once I got the hash, I put it into VirusTotal, and judging by some of these detections, we can say that this document has some VBA in it.

![image](https://github.com/user-attachments/assets/8d5a9a6a-04c3-4b39-9b5e-88df58d43f1f)

**[Task 3, Question 3] Investigate the malicious document in VirusTotal. What kind of file is associated with the malicious attachment?** - VBA

As before, we can grab the hash for the executable file and pop it into VirusTotal. It tells us what the filename is:

![image](https://github.com/user-attachments/assets/2b43fa3f-f417-4249-b7b3-26e6f4bc8910)

**[Task 3, Question 4] Investigate the extracted malicious `.exe` file. What is the given file name in VirusTotal?** - `PleaseWaitWindow.exe`

We can investigate the contacted domains if we go to the "Relations" tab on VirusTotal. It contacts a handful of domains, but the most suspicious one is the `hopto` domain.

![image](https://github.com/user-attachments/assets/9dedbcdc-3fe2-4589-9905-83621d07d8f1)

**[Task 3, Question 5] Investigate the malicious `.exe` file in VirusTotal. What is the contacted domain name? Enter your answer in defanged format.** - `hopto[.]org`

And now we head back into `http.log`. We can see the files requested by examining the `uri` field. Fortunately, there's not many requests here, so a simple `cat http.log | zeek-cut uri` will do:

![image](https://github.com/user-attachments/assets/e3ba8e93-fb15-4034-a396-9b8d8ca8e715)

**[Task 3, Question 6] Investigate the `http.log` file. What is the request name of the downloaded malicious `.exe` file?** - `knr.exe`

## [Task 4] Log4J

Now we move to `Exercise-Files/log4j` and run `zeek -C -r log4shell.pcapng detection-log4j.zeek`. This will generate a `signatures.log` file. If we just want to count how many unique hits there are, we can run a command like `cat signatures.log | zeek-cut uid | wc -l`, though `wc` isn't needed because there aren't that many to begin with:

![image](https://github.com/user-attachments/assets/3ecb3ba2-d40c-47fc-ba47-0a7f34ea2c66)

**[Task 4, Question 1] Investigate the `log4shell.pcapng` file with `detection-log4j.zeek` script. Investigate the `signature.log` file. What is the number of signature hits?** - 3

Scanning tools can be indicated by their user-agent (this is typically default behavior). So, we can run the command `cat http.log | zeek-cut user-agent | sort | uniq`, and we get the following:

![image](https://github.com/user-attachments/assets/d047f5e8-3dd3-4e74-973f-2d894e6b995f)

**[Task 4, Question 2] Investigate the `http.log` file. Which tool is used for scaning?** - Nmap

We can reuse our old command for this one, just replacing `user-agent` with `uri` to get the files involved here. We can see a few exploit files by doing this:

![image](https://github.com/user-attachments/assets/1fce18d6-f6d6-4154-87f2-1678335a87f1)

**[Task 4, Question 3] Investigate the `http.log` file. What is the extension of the exploit file?** - `.class`

Now let's try to make sense of how log4j was exploited. The `log4j.log` file displays all commands issued via the `log4j` exploit, specifically in the `value` field. Fortunately, the interesting commands are located at the top of the log file; they're just in base64.

![image](https://github.com/user-attachments/assets/c428bec1-7fd2-453d-a467-e4a11ed9ef20)

So, we can take the base64 strings and decode them. There's Linux commands and online converters that can do this; I decided to make use of the latter. Files can be created with the `touch` command, and all we need is the filename:

![image](https://github.com/user-attachments/assets/c1647a52-92a9-4dea-95c7-163d4b41f3c3)

**[Task 4, Question 4] Investigate the `log4j.log` file. Decode the base64 commands. What is the name of the created file?** - pwned
