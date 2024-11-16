# Phishing Prevention

## [Task 1] Introduction

We've spent a great deal of time exploring phishing emails. Now let's learn how to protect against them. How do we stop users from falling victim to these emails? Some strategies include
- Employing email security mechanisms such as SPF, DKIM, and DMARC
- Applying spam filters- flag and block incoming emails based on reputation
- Apply email labels- alert users that email is coming from an outside source
- Email address, domain, and URL blocking- based on reputation and explicit deny-lists
- Attachment blocking- based on the attachment extension
- Attachment sandboxing- detonating email attachments in a sandbox to detect malicious activity
- Security awareness training- use internal phishing campaigns to help train employees and defend against actual phsihing attempts

The MITRE ATT&CK framework describes _phishing for information_ as an attempt to trick targets into divulging information, and can include spearphishing attempts via third party services, attachments, links, and voice communication. Mitigation methods include using software configuration (SPF, DKIM, DMARC, etc.) and instituting security awareness training.

The relevant mitigation that we are studying is shown below:

![image](https://github.com/user-attachments/assets/19a68384-8381-413f-b4d3-3fea88d15ebd)

**[Task 1, Question 1] After visiting the [link](https://attack.mitre.org/techniques/T1598#mitigations) in the task, what is the MITRE ID for the "Software Configuration" mitigation technique?** - M1054

## [Task 2] SPF (Sender Policy Framework)

The Sender Policy Framework, or SPF, is used to authenticate the sender of an email. This lets an internet service provider verify that a mail server is authorized to send email for a specific domain. SPF records come in the form of DNS TXT records listing the IP addresses that are allowed to send email on behalf of the domain.
1. The sender sends an email and sends it out through their email server.
2. It is transferred to the recipient's email server, as usual.
3. Before it arrives in the recipient's inbox, however, a check is done against the sending organization's DNS server. This is the Sender ID Framework (SIDF) SPF Record Lookup.
4. If there is something amiss with the SPF information, i.e., the mail server wasn't authorized to send email for the domain it intended to, then it is rejected and marked as spam.
5. Otherwise, it proceeds into the recipient's inbox after passing through a reputation database.

A basic SPF record might look like this: `v=spf1 ip4:127.0.0.1 include:_spf.google.com -all`.
- The `v=spf1` indicates the beginning of an SPF record.
- `ip4:127.0.0.1` indicates which IP can send mail, and what IP protocol we're referring to.
- `include:_spf.google.com` indicates which domain can send email.
- `-all` tells us that non-authorized emails will be rejected.

There's some more to SPF syntax, but this should be enough to get a basic understanding of what's going on. It's worth checking out the dmarcian page on SPF. We'll specifically need to know the following for this task:

![image](https://github.com/user-attachments/assets/4317c75f-9643-4698-b51b-73656c7cd7c2)

You can use dmarcian's SPF Surevyor tool to investigate SPF revords. dmarcian also happens to have some information available on how to create your own SPF records. Note that Mesasgeheader also tells us the results of an SPF record check.

**[Task 2, Question 1] Referencing the dmarcian SPF syntax table, what prefix character can be added to the "all" mechanism to ensure a "softfail" result?** - `~`

**[Task 2, Question 2] What is the meaning of the `-all` tag?** - fail

## [Task 3] DKIM (DomainKeys Identified Mail)

DomainKeys Identified Mail, or DKIM, is used to authenticate the email being sent. DKIM is an open standard that helps with DMARC alignment - more on that later. DKIM records exist in DNS, and while they tend to be more complicated than SPF records, it can survive forwarding. This makes it very useful for ensuring email security. A DKIM record looks like this: `v=DKIM1; k=rsa; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAxTQIC7vZAHHZ7WVv/5x/qH1RAgMQI+y6Xtsn73rWOgeBQjHKbmIEIlgrebyWWFCXjmzIP0NYJrGehenmPWK5bF/TRDstbM8uVQCUWpoRAHzuhIxPSYW6k/w2+HdCECF2gnGmmw1cT6nHjfCyKGsM0On0HD`.
- The `v=DKIM1` tells us this is using version 1 of DKIM; you do not need to include a version in the record.
- `k=rsa` tells us the key type used in the DKIM record, which in this case is RSA. RSA happens to be the default.
- `p=...` is the public key that will be matched to the private key, which was created during DKIM setup.

The DKIM process works like this:
1. An email is sent, and the originating server generates a unique digital signature for the message using a private key known only to the server's domain.
2. The signature is added to the email as a header, also known as the DKIM-Signature header, including the version (optionally), the domain claiming responsibility for the email, and the actual signature.
3. The email gets to the recipient's email server. Intermediary servers may have the option to inspect DKIM signatures if they are set up to do so.
4. When the email arrives at the recipient's server, the server extracts the DKIM-Signature header and runs a DNS query. It looks up the public key published in the sender's DNS records under the specific DKIM selector, which then verifies the digital signature.
5. If the signature matches the content of the email, its authenticity and integrityy are verified _as long as the signing domain matches the "From" domain_.

Here's a snippet from a potentially malicious email's source code, which includes the results of a DKIM check. Interestingly enough, it passed:

![image](https://github.com/user-attachments/assets/0f1c0499-54ca-49ce-b3f3-1ca7c63a820b)

**[Task 3, Question 1] Which email header shows the status of whether DKIM passed or failed?** - Authentication-Results

## [Task 4] DMARC (Domain-based Message Authentication, Reporting, and Conformance)

Domain-based Message Authentication, Reporting, and Conformance (DMARC) is an open-source standard with uses a concept called _alignment_ to tie SPF and DKIM to the contents of an email. Having a DMARC record can be particularly useful for troubleshooting these two standards as applied to your email server. A basic record looks like this: `v=DMARC1; p=quarantine; rua=mailto:postmaster@website.com`.
- `v=DMARC1` specifies the version of DMARC. This is not optional and must be in all caps.
- `p=quarantine` specifies what to do if a check fails, i.e., the DMARC policy. In this case, the email is quarantined and sent to the Spam folder.
- `rua=mailto:postmaster@website.com` indicates that aggregate reports will be sent to this email address.

Interestingly enough you can actually include _RUF_ as a tag; this will send forensic copies of the individual emails, albeit redacted, to a given address. This will, by nature, include more sensitive details than the RUA reporting scheme.

DMARC's process involves simply running through the aforementioned tests:
- An email gets sent off with the DKIM header attached.
- As it arrives to the receiver's server, standard validation tests are conducted - IP blocklists, reputation checks, rate-limiting, and so on.
- Then the sender DMARC policy is validated and applied. Verified DKIM domains are retrieved, the "Envelope From" SPF value is obtained, and then the appropriate DMARC policy is applied.
- If the email passes DMARC, then it arrives at the recipient's inbox after standard processing is done. Otherwise it is quarantined or rejected entirely, and a failure report gets sent back to the sender.

The key mechanism underlying DMARC is alignment; it requires that the domain that yields a passing SPF/DKIM result must match the domain of the From header in the message body. Neither SPF nor DKIM on their own have anything to do with the From address.

The dmarcian Domain Health Checker allows you to check the DMARC status of a given domain. Here's the DMARC results for Microsoft's domain, which dmarcian reports is all good:

![image](https://github.com/user-attachments/assets/95068fb6-cfd3-4988-984c-362246cbf35e)

All emails that fail the DMARC check for Microsoft get rejected.

**[Task 4, Question 1] Which DMARC policy would you use not to accept an email if the message fails the DMARC check?** - `p=reject`

## [Task 5] S/MIME (Secure/Multipurpose Internet Mail Extensions)

Secure/Multipurpose Internet Mail Extensions (S/MIME) is a protocol for sending digitally signed and encrypted messages. S/MIME guarantees data integrity and nonrepudiation with public key cryptography:
1. Bob wants to use S/MIME when sending an email. He'll need a digital certificate with his public key on it.
2. Thanks to the digital certificate, Bob signs the email message with his private key.
3. Mary, the recipient, decrypts Bob's message with his public key.
4. If Mary wants to reply to Bob's email, she can do the same - send her certificate to Bob. Bob will complete the same process on his end.
5. And now they both have each other's certificates for future correspondence.

The question for this task requires us to lift verbiage from Microsoft's documentation on S/MIME:

![image](https://github.com/user-attachments/assets/774301ab-2160-432c-b9b9-faee7cb06cc7)

**[Task 5, Question 1] What is nonrepudiation? (The answer is a full sentence, including the ".")** - The uniqueness of a signature prevents the owner of the signature from disowning the signature.

## [Task 6] SMTP Status Codes

We'll spend a good chunk of the rest of the room investigating a PCAP file with SMTP traffic. This does require some knowledge of investigating packet captures in Wireshark. The attached virtual machine contains the PCAP file and a copy of Wireshark; it will start in Split Screen View.

According to the Wireshark documentation, we have this for the SMTP response code:

![image](https://github.com/user-attachments/assets/a55ed8e9-1257-4ec2-a007-28a7f1225547)

**[Task 6, Question 1] What Wirshark filter can you use to narrow down the packet output using SMTP status codes?** - `smtp.response.code`

If we check one of the packets with the SMTP status code 220, we see this:

![image](https://github.com/user-attachments/assets/8199cb59-b784-43ac-b9aa-0a058723a003)

The message immediately follows the status code in the packet data.

**[Task 6, Question 2] Per the network traffic, what was the message for status code 220? (Do not include the sttatus code (220) in the answer)** - [domain] Service ready

Wireshark tells us that one packet, in particular, has been blocked by `spamhaus.org`. Here's the packet data for packet #156:

![image](https://github.com/user-attachments/assets/04909aca-04f9-4413-bb74-c89241b19b39)

We're particularly interested in the Response Code line.

**[Task 6, Question 3] One packet shows a response that an email was blocked using `spamhaus.org`. What were the packet number and status code? (no spaces in your answer)** - 156,553

Immediately following the response code line above, we see the message regarding the mailbox.

**[Task 6, Question 4] Based on the packet from the previous question, what was the message regarding the mailbox?** - mailbox name not allowed

Typically, SMTP will let you knwo when it's ready to receive data, and at that point you can supply SMTP DATA commands as needed. Thus, it would make the most sense for status code 354 to be what precedes a DATA command:

![image](https://github.com/user-attachments/assets/c3508927-52a2-482a-aabf-71c70b8aa520)

It tells us to "Go ahead" after all.

**[Task 6, Question 5] WHat is the status code that will typically precede a SMTP DATA command?** - 354

## [Task 7] SMTP Traffic Analysis

Now we'll focus on some trivial SMTP traffic. The port that SMTP is using in this scenario is the default port typically used, though you can always check the ports used in Wireshark:

![image](https://github.com/user-attachments/assets/a64bacc9-d51c-41a0-8a57-06c5cb617551)

**[Task 7, Question 1] What port is the SMTP traffic using?** - 25

Note that, if you want to list only the SMTP packets, you can just apply the query `smtp`:

![image](https://github.com/user-attachments/assets/ff8bc4aa-de5a-4a67-961f-ca16e664f50a)

**[Task 7, Question 2] How many packets are specifically SMTP?** - 512

Scrolling through the SMTP output in Wireshark, you'll see a certain IP address show up time and time again:

![image](https://github.com/user-attachments/assets/f60ffdba-cc5f-48e3-8e74-4fab4537fe2b)

**[Task 7, Question 3] What is the source IP address for all the SMTP traffic?** - `10.12.19.101`

The link provided in the task gives information about the Internet Message Format filter options in Wireshark - so it seems like we'll want to filter for `imf` packets in Wireshark. These are the actual mail messages that will contain extensions. The third packet, if we look under "MIME Multipart Media Encapsulation" and then "Encapsulated multipart part (application/octet-stream)", has information on what was attached:

![image](https://github.com/user-attachments/assets/0cc02df9-9036-4713-a7a6-447ebc80eaa6)

**[Task 7, Question 4] What is the filename of the third file attachment?** - `attachment.scr`

And if we do the same for the last packet, we see this:

![image](https://github.com/user-attachments/assets/e56cd23f-62ea-4d4a-bed5-b48bda2c0e62)

**[Task 7, Question 5] How about the last file attachment?** - `.zip`

## [Task 8] SMTP and C&C Communication

It is worth noting that the standard protocols used for email - as discussed at the beginning of this section of rooms - can be leveraged by adversaries. SMTP, in particular, can be used for command-and-control (C2) communications. MITRE ATT&CK discusses this, noting that adversaries will do this to blend in with existing traffic. Commands for the remote system, as well as their results, will be embedded in the SMTP traffic between the client and the server. Several APTs have done this. MITRE recommends that you use network intrusion and prevention systems that use network signatures to identify traffic for specific adversary malware. To detect this activity, you can look for application layer protocols that don't follow the expected standards regarding syntax, structure, and so on.

Here's some examples from MITRE on how email protocols are used by adversaries for C2 communication:

![image](https://github.com/user-attachments/assets/79a187b9-7ec8-4c36-a43e-dee155309651)

**[Task 8, Question 1] Per MITRE ATT&CK, which software is associated with using SMTP and POP3 for C2 communications?** - Zebrocy
