# Tshark - Challenge I: Teamwork

## [Task 1] Introduction

This is the first of two rooms that allow you to practice what you learned in the tshark rooms. This room covers a traffic capture that may contain a suspicious domain - a potential threat to an organization.

You should not interact with the links or IPs given in this room.

## [Task 2] Case: Teamwork!

First, we are tasked with looking for suspicious domain addresses. One place to start looking would be to check the HTTP hosts in the packet capture, so maybe we could run `tshark -r teamwork.pcap -Y http -T fields -e http.host | awk NF`. When we do this, we get something that looks pretty obviously suspicious:

![image](https://github.com/user-attachments/assets/fb8e0fe8-b40f-41e5-8477-0264503e5799)

We can pop this into, say, CyberChef, and apply the Defang URL recipe to get the defanged address:

![image](https://github.com/user-attachments/assets/8dff0c52-e3a6-4e0b-af56-1602c0f20921)

**[Task 2, Question 1] What is the full URL of the malicious/suspicious domain address?** - `hxxp[://]www[.]paypal[.]com4uswebappsresetaccountrecovery[.]timeseaways[.]com/`

Now that we have a suspicious addrss, it's time to investigate it in VirusTotal. We'll want to use the regular address - not the defanged one. When we search for the address, we can see when it was first submitted by going to the Details tab.

![image](https://github.com/user-attachments/assets/438848eb-953c-4b28-8aed-6e810d4eb191)

**[Task 2, Question 2] When was the URL of the malicious/suspicious domain address first submitted to VirusTotal?** - `2017-04-17 22:52:53 UTC`

Judging by the domain address itself, we can see that it's trying its best to impersonate PayPal.

**[Task 2, Question 3] Which known service was the domain trying to impersonate?** - PayPal

We can also find the domain's IP address if we check VirusTotal. We'll need to drop the `http://` from our search in VirusTotal, but once we do we can go to the Relations tab and check the Passive DNS Replication information at the top.

![image](https://github.com/user-attachments/assets/527fcfc9-caea-4274-ab3c-dffcbefccdc5)

**[Task 2, Question 4] What is the IP address of the malicious domain?** - `184[.]154[.]127[.]226`

The verbiage of the question, along with the fact that we're dealing with a potential phishing website, we may be interested in seeing if anyone tried to post any information to it. We can check by running `tshark -r teamwork.pcap -Y 'http.request.method==POST'`, and this yields two packets:

![image](https://github.com/user-attachments/assets/ea5f4e2a-b349-4f39-af48-028c525d63c6)

We'll investigate these packets by appending an `-x` to our command: `tshark -r teamwork.pcap -Y 'http.request.method==POST -x`. We can see the username/email submitted by checking the details in the second packet:

![image](https://github.com/user-attachments/assets/5c5ce743-aa58-49bb-be3c-16c927fe5c81)

**[Task 2, Question 5] What is the email address that was used?** - `johnny5alive[at]gmail[.]com`
