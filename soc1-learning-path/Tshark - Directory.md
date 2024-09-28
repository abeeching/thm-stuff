# Tshark - Challenge II: Directory

## [Task 1] Introduction

This is the second of two challenges that tests how well you can use tshark to investigate traffic data. As before, going through the tshark rooms should help, and avoid interacting with the addresses and associated artifacts outside of the VM.

This case concerns someone coming across a file index and (presumably) bad things happening as a result. Let's dive in!

## [Task 2] Case: Directory Curiosity!

We can start by following a similar strategy to the previous room -- that is, we'll first want to investigate the HTTP hosts to determine if any of them are suspicious. The command to do so would be something like `tshark -r directory-curiosity.pcap -T fields -e http.host | awk NF | sort -r | uniq`. We see the following entries:

![image](https://github.com/user-attachments/assets/b7caeb82-97d1-45d9-b433-752b9274df01)

There's no reason to suspect anything weird with Bing or Digicert. That said, the IP address and the other domain are worth investigating. The answer fields in the room suggest that it'll be the actual domain and not the IP address, but let's actually confirm that before moving fowrward. We can use a command like `tshark -r directory-curiosity.pcap -Y 'http.host contains "bavuong"' -x` and see what we can find.

...and as it would turn out, there's some suspicious stuff in these packets.

![image](https://github.com/user-attachments/assets/a56d48e3-7bbb-44ec-9050-6646ff465b3d)

Hence, the bavuong domain is going to be our answer.

**[Task 2, Question 1] What is the name of the malicious/suspicious domain?** - `jx2-bavuong[.]com`

We can modify the previous query to obtain the number of requests sent out to this domain -- we just pipe it to `wc -l`. Thus our command should be `tshark -r directory-curiosity.pcap -Y 'http.host contains "bavuong"' | wc -l`.

![image](https://github.com/user-attachments/assets/c18a203c-4ccf-49dd-a2f0-ff9aad7081ee)

**[Task 2, Question 2] What is the total number of HTTP requests sent to the malicious domain?** - 14

Now we'd like to get the IP address associated with this domain. For this, we can just drop the `wc -l` portion of our previous query -- that is, run `tshark -r directory-curiosity.pcap -Y 'http.host contains "bavuong"'` -- and investigate the results. It'll be the second IP address in each entry:

![image](https://github.com/user-attachments/assets/0d111430-2bb6-45aa-9fb5-227ab4931e9d)

**[Task 2, Question 3] What is the IP address associated with the malicious domain?** - `141[.]164[.]41[.]174`

There are a few ways to do this. One way would be to make a note of the packets provided in the previous question and then to investigate some of the subsequent packets. For instance, we know that packet 250 is a communication with the suspicious host, so we should expect that within the next few packets there should be a response from the suspicious domain. The command `tshark -r directory-curiosity.pcap -c 251`. From there, you can use `-x` or `-T fields -e http.server` to extract server information.

![image](https://github.com/user-attachments/assets/f658f915-7a45-4d3a-8e6b-f3366e8ae17b)

You could also try running `tshark -r directory-curiosity.pcap -T fields -e http.server | awk NF | sort -r | uniq -c`. That gives us this:

![image](https://github.com/user-attachments/assets/ce1b95c2-fe90-4227-9c94-f5d70787a0fd)

The bottom server is found in 14 packets, and we know that the host communicated with the malicious domain 14 times... so it's safe to say that's our answer. I can't imagine this strategy would work perfectly in a real environment with many more packets captured, but it'll do for our case.

**[Task 2, Question 4] What is the server info of the suspicious domain?** - Apache/2.2.11 (Win32) DAV/2 mod_ssl/2.2.11 OpenSSL/0.9.8i PHP/5.2.9

Now we are asked to follow the first TCP stream in ASCII. To do so, we run `tshark -r directory-curiosity.pcap -z follow,tcp,ascii,0 -q`. I'll admit I was a little stuck on the wording for a moment, but we need to look at the files made available in the HTTP page that was sent back to the user. It seems that there are three files that can be downloaded - two PHP files and one EXE file.

![image](https://github.com/user-attachments/assets/d65096d9-dc4e-495d-af2c-baec5af594f6)

**[Task 2, Question 5] What is the number of listed files?** - 3

And the first file's name can be seen in the screenshot above -- it'll be the first PHP file made available.

**[Task 2, Question 6] What is the filename of the first file?** - `123[.]php`

If we want to grab the files from HTTP traffic, we'll want to run the command `tshark -r directory-curiosity.pcap --extract-files http,. -q`. Note that the `.` in the command just means I'm going to extract everything into the current working directory - ideally you'd put all of this in a directory of its own, but this'll do for now. Either way, we see some EXE files pop up:

![image](https://github.com/user-attachments/assets/adfe084b-050d-40e8-92a4-f655f45f9f54)

**[Task 2, Question 7] What is the name of the downloaded executable file?** - `vlauto[.]exe`

To get the SHA256 value, we can run `sha256sum vlauto.exe`.

![image](https://github.com/user-attachments/assets/ea0b4635-abc2-49e8-a447-a1b9572ce542)

**[Task 2, Question 8] What is the SHA256 value of the malicious file?** - `b4851333efaf399889456f78eac0fd532e9d8791b23a86a19402c1164aed20de`

Now we'll check out the results for this in VirusTotal by searching the hash. Once we find the file, we can check the Details tab to see all sorts of information about it. The PEiD packer information can be found in the Basic Properties section of this page, at the very bottom:

![image](https://github.com/user-attachments/assets/8e414335-bad6-4369-83c8-ca91485ef010)

**[Task 2, Question 9] What is the PEiD packer value?** - .NET executable

And to see what the Lastline Sandbox has to say about this file, we check the Behavior tab and look at the Dynamic Analysis Sandbox Detections.

![image](https://github.com/user-attachments/assets/1827f6a5-bfbf-4e1d-bae8-fd8300caf6fe)

**[Task 2, Question 10] What does the "Lastline Sandbox" flag this as?** - MALWARE TROJAN
