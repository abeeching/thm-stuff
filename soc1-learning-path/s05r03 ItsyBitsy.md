# ItsyBitsy

## [Task 1] Introduction

So, our goal in this room is to investigate an IDS alert that signals a potential C2 communication. The communication seems to involve a user named `Browne` from the HR department. A week's worth of HTTP connection logs is available for us to investigate, and the connection logs only have been ingested into the `connection_logs` index. Fun, right?

Either way, we need to figure out what's going on with this user, so let's get to it.

## [Task 2] Scenario - Investigate a Potential C2 Communication Alert

We'll log into the ELK instance with the credentials given from Task 1. First we'll want to set the logs to the appropriate timeframe, March of 2022. This means we'll need to set our timeframe with absolute values, as we did in the introductory ELK room.

![image](https://github.com/user-attachments/assets/88b172e9-40b3-4a4b-96f1-31fa90c130f2)

Once we do so, we get the number of events:

![image](https://github.com/user-attachments/assets/f7351680-dc93-4b49-bf79-3ab33b83987f)

**[Task 2, Question 1] How many events were returned for the month of March 2022?** - 1482

Now let's try to figure out what's up with the suspect user `Browne`. These are HTTP logs so, as far as looking for IPs goes, we need to try and find any anomalies. Since we are suspecting that the host machine is communicating with a C2, we could start by looking at source IPs. If we check the most common values for the `source_ip` field, we see only two:

![image](https://github.com/user-attachments/assets/ef577a0f-32d8-4407-9370-4b416e9da79a)

It's safe to say that the first IP address is normal communication. Many of the hosts referenced appear to be relating to Google, which seems fairly reasonable:

![image](https://github.com/user-attachments/assets/2462e716-9136-4e3b-9637-eb588f914868)

So let's try checking this other IP here:

![image](https://github.com/user-attachments/assets/c5056707-8478-4704-ab7e-caa6420bd04a)

There's only two connections involving this IP, and they both seem to be communicating with Pastebin for some reason. It's certainly more suspicious than the other logs.

**[Task 2, Question 2] What is the IP associated with the suspected user in the logs?** - `192.166.65.54`

If we drill into the logs we found earlier, we see some pretty unusual stuff going on with the `user_agent`.

![image](https://github.com/user-attachments/assets/d6251aea-66e7-4c4e-94b5-e9c9150488a7)

Normally, we'd expect the user agent in HTTP logs to indicate, say, Mozilla Firefox being used. So, `bitsadmin` is a little strange. If we were to look up `bitsadmin`, then we see this.

![image](https://github.com/user-attachments/assets/2ade9227-35f5-482c-b255-64e6dc6d13d6)

Turns out `bitsadmin` is a tool that can be used to create download/upload jobs and monitor them. If we dig into the search results a little more, we see something more concerning - it's considered a Living Off The Land (LOLBAS) binary. This means that it has some extra functionality that would be useful for an attacker...say...downloading stuff from a C2 server?

![image](https://github.com/user-attachments/assets/a3660afe-5bd5-4b3b-ae6a-5b8b9ac76605)

**[Task 2, Question 3] The user's machine used a legit Windows binary to download a file from the C2 server. What is the name of the binary?** - `bitsadmin`

As mentioned earlier, Pastebin is pretty suspicious compared to the rest of the traffic. Knowing that (a) Pastebin _is_ a filesharing service, and (b) the attacker was using bitsadmin to download stuff off of it, we're can be confident in saying that Pastebin is the filesharing website being used as a C2 in this exercise.

**[Task 2, Question 4] The infected machine connected with a famous filesharing site in this period, which also acts as a C2 server used by the malware authors to communicate. What is the name of the filesharing site?** - `pastebin.com`

The full URL can be found by checking the `uri` field. We can simply append the conents of that field to `pastebin.com` to get the full URL.

![image](https://github.com/user-attachments/assets/c59773bd-eeeb-419f-b2e1-349f5bd43e23)

**[Task 2, Question 5] What is the full URL of the C2 to which the infected host is connected?** - `pastebin.com/yTg0Ah6a`

Going to the link above (which we should perhaps be more careful doing in a real situation) gives us the filename...

![image](https://github.com/user-attachments/assets/e378e139-37d8-4238-9344-08182e0cd7de)

**[Task 2, Question 6] A file was accessed on the filesharing site. What is the name of the file accessed?** - `secret.txt`

...and it gives us the flag.

![image](https://github.com/user-attachments/assets/a63a3e2e-0b78-4592-af68-2e8459304ca6)

**[Task 2, Question 7] The file contains a secret code with the format `THM{____}`.** - `THM{SECRET__CODE}`
