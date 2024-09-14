# Snort Challenge - Live Attacks

## [Task 1] Introduction

In previous rooms, we've discussed how we can create rules with Snort to detect certain things - thus treating Snort as an IDS. However, we've mentioned in the introductory room that Snort can act as an IPS, actively blocking traffic deemed to be suspicious. In this room, we will put the IPS functionality of Snort to the test against a couple of attack scenarios. The goal in both tasks is to create rules that will stop the malicious traffic for a full minute to receive a flag of some kind.

## [Task 2] Scenario 1 | Brute Force

The room told us that we are experiencing a brute-force attack, so I actually started by taking a look at packet logger mode by running the command `sudo snort -dev -K ASCII -l .`. This allows me to see what IP addresses were interacting with the machine and over what port, and one of the IP addresses was particularly interested in one of the ports:

![image](https://github.com/user-attachments/assets/c3bbaa68-13dc-44ec-a7f6-160d2e0d3787)

So let's create a rule that drops all traffic headed for this port. The rule would be `drop tcp any any -> any 22 (OPTIONS...;)`. I created a `local.rules` file as in the previous task, added the rule, and then ran `sudo snort -c local.rulues -q -Q --daq afpacket -i eth0:eth1 -A full`. You can test it with `-A console`. It works!

![image](https://github.com/user-attachments/assets/061017dd-d04b-4368-9fff-85d672d28fa8)

The flag shows up on the desktop once you stop the attack.

**[Task 2, Question 1] Stop the attack and get the flag (which will appear on your Desktop)** - THM{81b7fef657f8aaa6e4e200d616738254}

So now that we've taken care of the attack, we can make sense of what was happening. Port 22 (TCP) is the standard port for SSH communication, meaning the attacker was trying to brute-force SSH.

**[Task 2, Question 2] What is the name of the service under attack?** - SSH

**[Task 2, Question 3] What is the used protocol/port in the attack?** - TCP/22

## [Task 3] Scenario 2 | Reverse-Shell

We can use a similar methodology as in the previous task to get a "lay of the land" so to speak - our first step is to understand what is happening to the machine. Since we're being told that a reverse shell is potentially running on the system, we're likely going to be looking for nonstandard port usage. After logging packets, we see communication over ports 80, 443, and oddly enough:

![image](https://github.com/user-attachments/assets/d098faff-ca49-4e32-968f-4660c3ad873f)

This is likely the culprit, so let's create a rule to stop communication to this port: `drop tcp any any -> any 4444 (OPTIONS...;)`. As before, I made a `local.rules` file, added the rule, and then ran the command to use Snort in IPS mode. The flag shows up on the desktop after a minute or so.

**[Task 3, Question 1] Stop the attack and get the flag (which will appear on your Desktop)** - THM{0ead8c494861079b1b74ec2380d2cd24}

So, the suspect port is 4444. What could be communicating over this port?

**[Task 3, Question 2] What is the used protocol/port in the attack?** - tcp/4444

A little bit of research tells us that this is a default port setting for Metasploit!

![image](https://github.com/user-attachments/assets/90917a93-57ed-4696-90f6-24ad7f22f7db)

**[Task 3, Question 3] Which tool is highly associated with this specific port number?** - Metasploit
