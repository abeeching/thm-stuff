# Networking: Core Protocols

## [Task 1] Introduction

In this room, we start to dive into some of the protocols used on the Internet, including some major ones that are used in the Application Layer. This requires an understanding of the models/layers, as well as the Ethernet, IP, and TCP protocols. The attached VM as well as the AttackBox will be needed for Tasks 4 and onward.

## [Task 2] DNS: Remembering Addresses

Typically, you don't need to remember the IP addresses associated with websites; you're only really concerned about them when you're making some changes on an internal network. This is thanks to the Domain Name System, or DNS, which focuses on mapping a domain name to an IP address.

DNS is an Application-Layer Protocol (Layer 7). DNS traffic is sent over UDP port 53 by default, though it may use TCP port 53 as a fallback option. There are many DNS records; these are some of the important ones:
- The A (Address) Record maps a hostname to an IPv4 address, e.g. `example.com -> 172.17.2.172`
- The AAAA Record mmaps a hostname to an IPv6 address. Note the four As compared to the one A above.
- The CNAME (Canonical Name) Record maps a domain name to another domain name, e.g. `www.example.com -> example.com`
- The MX (Mail Exchange) Record maps a mail server to a domain.

If you were to navigate to `example.com` in a browser, your browser would try to resolve the domain name by querying the DNS server and asking for its A record. If you were to try and send an email to `test@example.com`, then the DNS server would instead ask for an MX record.

To look up the IP address of a domain from a command line, use `nslookup`. It gives A and AAAA records, among others.

**[Task 2, Question 1] Which DNS record type refers to IPv6?** - AAAA

**[Task 2, Question 2] Which DNS record type refers to the mail server?** - MX

## [Task 3] WHOIS

Great! We know how to figure out what IP address a domain name is related to. So how do these mappings appear in the first place? When someone registers a domain name, they are given the power to set DNS records for their given domain. You're allowed to register an available domain for one or more years, though you have to pay a fee. More critically for DNS, you are required to supply accurate contact information as the registrant. This information is made available in WHOIS records publicly, among other things. There are privacy services out there that you can use to hide information from the WHOIS records, though accurate information is expected to maintain domain ownership.

You can look up WHOIS records for any domain name with an online service or with a command-line tool, `whois`. This tool is available on Linux systems, among others, and as you would expect it provides information about the entity that registered a domain name. To use the command, run `whois (DOMAIN)`.

We may as well make use of the AttackBox's `whois` command for these questions - we'll need it for the next task anyway. Let's run `whois` on everyone's favorite formerly-known-as-Twitter website: `x.com`.

The output looks like this:

![image](https://github.com/user-attachments/assets/af5b6680-6171-44ef-a25f-45c8d88768c1)

The record was created on the date specified in Creation Date.

**[Task 3, Question 1] When was the `x.com` record created? Provide the answer in YYYY-MM-DD format.** - `1993-04-02`

We'll need to run `whois twitter.com` for this information. The output's quite long, so you may want to consider using some commands to look for the Creation Date specifically - perhaps you could pipe to grep! You could also scroll through the output if you wanted to.

![image](https://github.com/user-attachments/assets/5327a52e-7bbd-4ebd-81f6-fa059bdacf99)

**[Task 3, Question 2] When was the `twitter.com` record created? Provide the answer in YYYY-MM-DD format.** - 2000-01-21

## [Task 4] HTTP(s): Accessing the Web

When going on your Web browser, you are using HTTP or HTTPS to get to web pages. HTTP/S, or Hypertext Transfer Protocol/Secure, uses TCP to define how the browser communicates with web servers. Some commands or methods that the browser issues to the web server include
- GET, which retrieves data from a server (e.g. HTML files and images)
- POST, which is used when submitting new data to the server (e.g. forms and files)
- PUT, which is used to place new resources on a server and update/overwrite information
- DELETE, which deletes specified files and resources.

HTTP uses TCP port 80, while HTTPS uses port 443. Less common ports include 8080 for HTTP and 8443 for HTTPS. When we browse to a webpage, the browser gets the webpage and displays it perfectly well. What's going on behind the scenes?

The Wireshark tool - which is a tool that can be used to see packets sent over a network - allows us to examine the exchange between the web browser and the web server. Text sent by the browser will be in red, whereas text sent by the server is in blue. A lot of information is exchanged here, and there's a lot that we don't see as the user. This incloudes the server version and when the page was modified.

We did use Telnet in the Networking Concepts room to connect to a web server, though we had to issue the `GET / HTTP/1.1` and `HOST: (hostname)` lines before doing so. In some cases, you only have to issue the GET command. Either way, you can request other resources on the server by changing the `GET` line. For instance, if we wanted to retrieve the `file.html` file, we would run `GET /file.html HTTP/1.1`.

Let's try it! We'll run `telnet 10.10.36.197 80` to get to the web server at that IP, and then we'll run `GET /flag.html HTTP/1.1`. Note that once you press Enter, you will want to type `Host: anything` and then press Enter. Doing so gives us this HTTP code:

![image](https://github.com/user-attachments/assets/13b7fb85-34b6-49fe-9ce5-eafbe2d1b36f)

**[Task 4, Question 1] Use `telnet` to access the file `flag.html` on `(YOUR_MACHINE_IP)`. What is the hidden flag?** - `THM{TELNET-HTTP}`

## [Task 5] FTP: Transferring Files

Now let's go over how to transfer files. The File Transfer Protocol (FTP) is used to do just that, and if conditions are just right, it can be an incredibly efficient way to do so. Some commands defined in the FTP protocol include
- `USER`, which is used to input the username.
- `PASS`, which is used to enter the password.
- `RETR`, which is used to RETRieve or download files from the FTP server.
- `STOR`, which is used to STORe or upload files to the FTP server.

On default, FTP listens on port 21 TCP, though data transfer will be conducted via another connection from the client to the server. To make use of FTP, you can use the `FTP (TARGET_IP_ADDRESS)` command to connect to the remote server. You can do the following to retrieve a file:
1. Provide the username, which in this case is `anonymous`. Note that not all FTP servers will allow you to log in with that user.
2. Provide the password. In this case, since we're doing what's called an anonymous login, we can press Enter to skip typing a password.
3. Run `ls` to list the files available for download.
4. Run `type ascii` to tell FTP that we want to work with text files.
5. Run `get (TEXT_FILE)` to grab the text file off the FTP server.

In Wireshark, you can see the actual commands and responses that are being exchanged between the client and the server. For instance, `ls` is interpreted by the FTP client as a `LIST` command, and the FTP server responds approporiately. Note that separate connections are used to establish a connection/list files and download files.

Let's try this ourselves! We run through Steps 1-5 above, though now we run `get flag.txt` at the end. Then we exit the FTP client by running `exit`. Then we run `cat flag.txt` to see the flag.

![image](https://github.com/user-attachments/assets/0bbb25e2-f1d3-4c73-943b-0824a43d4adb)

![image](https://github.com/user-attachments/assets/e5cb527a-e659-45b9-b186-1a0ffc161f7a)

**[Task 5, Question 1] Using the FTP client `ftp` on the AttackBox, access the FTP server at `(YOUR_MACHINE_IP)` and retrieve `flag.txt`. What is the flag found?** - `THM{FAST-FTP}`

## [Task 6] SMTP: Sending Email

This and the next two tasks are focused on email communication. The Simple Mail Transfer Protocol, SMTP, defines how a mail client talks with a mail server and how a mail server talks with another. It works like a post office, in which you tell the folks there where you want to send a package and hand it to them. You may be asked to show identification before you can make use of their service; SMTP is much the same way. Here are some important commands that SMTP uses:
- `HELO (MAIL_SERVER)` or `EHLO (MAIL_SERVER)`, which initiates SMTP communication.
- `MAIL FROM`, to specify the sender's email address.
- `RCPT TO`, to specify the recipient's email address.
- `DATA`, which tells SMTP that you are beginning to send the content of an email message. You can then type out the message, breaking it across many lines as needed.
- `.`, which when put on a line of its own, tells SMTP that you are done writing the message content and are ready to send it.

When done, you can run `QUIT` to exit an SMTP connection. Using Telnet to send email over SMTP seems a little tedious - and it is - but it can be helpful for seeing how email clients work when they send off email. You can monitor the connection with Wireshark if desired.

**[Task 6, Question 1] Which SMTP command indicates that the client wil lstart the contents of the email message?** - `DATA`

**[Task 6, Question 2] What does the email client send to indicate that the email message has been fully entered?** - `.`

## [Task 7] POP3: Receiving Email

Now say you've gotten an email and you want to view it in your email client. That's where the Post Office Protocol version 3 (POP3) comes in: this lets a client communicate with an email server to retrieve messages. It's like checking your local mailbox for letters and packages. Some important POP3 commands include
- `AUTH`, which initiates the authentication process
- `USER (USERNAME)`, which is how you identify yourself as a user
- `PASS (PASSWORD)`, which is how you enter your password
- `STAT`, which tsates the number of messages and the total size
- `LIST`, which lists all messages and their sizes
- `RETR (MESSAGE_NUMBER)`, which retrieves a specified message
- `DELE (MESSAGE_NUMBER)`, which marks the specified message for deletion
- `QUIT`, to end the POP3 session and apply any changes (e.g., deletion)

We can communicate over POP3 via Telnet. POP3 listens on TCP port 110 by default, so you should run `telnet (IP_ADDRESS) 110` to use POP3. It's possible to intercept exchanged traffic (as you would see if you ran Wirshark after communicating over POP3), and this is not good...our next room will focus on more secure methods of communication.

The answer to this first question is at the top of the Wireshark screenshot.

**[Task 7, Question 1] Looking at the traffic exchange, what is the name of the POP3 server running on the remote server?** - Dovecot

For now, though, we'll practice using POP3. We'll want to access the POP3 server over `telnet`, and then we'll want to run `AUTH`, then `USER linda` and `PASS Pa$$123` to log in to that user. From there, we run `LIST` to see what messages are there, and then run `RETR 4` to retrieve the contents of the fourth email.

![image](https://github.com/user-attachments/assets/1150c0eb-2b15-4ddb-bbd1-e79a116d2ebc)

![image](https://github.com/user-attachments/assets/3f671fab-baf2-4a77-b23c-b1b9ff1d349f)

The flag is shown in the second screenshot above.

**[Task 7, Question 2] Use `telnet` to connect to `10.10.36.197`'s POP3 server. What is the flag contained in the fourth message?** - `THM{TELNET_RETR_EMAIL}`

## [Task 8] IMAP: Synchronizing Email

POP3's usually good enough if you want to work from one device - say, your email client on your desktop. But what if you want to check your email from your office desktop and your laptop/smartphone? POP3 doesn't really synchronize email across clients, in the interest of minimizing stored data. That's where the Internet Message Access Protocol, IMAP, comes in. It synchronizes read, moved, and deleted messages, and is good when you're accessing email from many clients. IMAP tends to use more storage since email is kept on the server longer and it is snyced.

Some important commands include:
- `LOGIN (USERNAME) (PASSWORD)` for authentication
- `SELECT (MAILBOX)` to select the mailbox folder to work from
- `FETCH (MAIL_NUMBER) (DATA_ITEM_NAME)` to retrieve contents of a specified message. For instance, `FETCH 3 body[]` will retrieve the header and body of the third message.
- `MOVE (SEQUENCE_SET) (MAILBOX)` moves specified messages to the specified mailbox
- `COPY (SEQUENCE_SET) (DATA_ITEM_NAME)` to copy the specified messages to another mailbox
- `LOGOUT` to log out.

IMAP communicates over TCP port 143 by default. You can use `telnet` to practice with these commands, and it's good practice to do so, since they're more complicated than POP3's commands. The mail available in IMAP will be the same as that found in the POP3 server from the previous task.

**[Task 8, Question 1] What IMAP command retrieves the fourth email message?** - `FETCH 4 body[]`

## [Task 9] Conclusion

In previous rooms, we've focused on `telnet` as a protocol itself. Now we've used it to tinker with fundamental protocols like HTTP, FTP, SMTP, POP3, and more. This gives us a better understanding of how things work on the Internet, including how hostnames are resolved and how web/email traffic is transferred at a more detailed level.

You should be familiar with the default port numbers - this is useful in a cybersecurity context because it allows you to spot abnormal port usage.
- TELNET runs on TCP 23
- DNS runs on UDP/TCP 53
- HTTP runs on TCP 80 (TCP 8080 as a secondary option)
- HTTPS runs on TCP 443 (TCP 8443 as a secondary option)
- FTP runs on TCP 21
- SMTP runs on TCP 25
- POP3 runs on TCP 110
- IMAP runs on TCP 143
