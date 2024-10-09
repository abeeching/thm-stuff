# Benign

## [Task 1] Introduction

In this room, we'll investigate host-centric logs to trace suspicious process execution. The network in this scenario is as follows:
- The IT department, made up of James, Moin, and Katrina
- The HR department, made up of Haroon, Chris, and Diana
- The Marketing department, made up of Bell, Amelia, and Deepak

A client IDS noted a suspicious process being executed on one of the HR department hosts, suggesting compromise. Upon further investigation, it seems that information gathering tools and scheduled tasks were ran, confirming suspicions. Only logs with event ID `4688` were pulled and ingested into Splunk; they can be found under the `win_eventlogs` index.

## [Task 2] Scenario: Identify and Investigate an Infected Host

So, let's see what's going on! We'll start by checking the logs produced during the month of March 2022. We can set the date/time range to start at the beginning of March 1st, 2022 and end on March 31st, 2022. This yields the number of events shown to the left:

![image](https://github.com/user-attachments/assets/2fd5626c-ed49-4e05-960b-a14427c28b5f)

**[Task 2, Question 1] How many logs are ingested from the month of March 2022?** - 13959

It appears that someone tried to impersonate another user, and it showed up in the logs. If we check the `UserName` field, we don't see anything out of the ordinary.

![image](https://github.com/user-attachments/assets/0752d3b5-6d91-4d01-9d3b-c5c1ce69bb7a)

There's 11 users, and only the top ten are shown. Let's try checking out the Rare Values and see what names don't commonly show up. Doing so gievs us this table:

![image](https://github.com/user-attachments/assets/6569e7f1-413d-490a-83fb-69923b9f46cb)

It's a little hard to tell, but it seems like Amelia's being impersonated here!

**[Task 2, Question 2] Imposter alert: There seems to be an imposter account observed in the logs. What is the name of that user?** - `Amel1a`

Now we're looking for an HR user who's setting up scheduled tasks in the logs. Since it's an HR user, we can narrow our focus down to the HR hosts, and if we're looking for scheduled tasks, searching for logs that involve `schtasks` or `schtasks.exe` would be a good place to start. So, one query we could use is `index="win_eventlogs" HostName="HR*" schtasks`. This yields eighteen events.

Scrolling through the events, we see a bunch of changes and deletions that seem relatively normal. What is interesting is the one that features this as the `CommandLine` value: `/create /tn OfficUpdater /tr "C:\Users\Chris.fort\AppData\Local\Temp\update.exe" /sc onstart`. It's a little unusual for an Office update executable to be stored in some user's Temp folder, so it's likely that `Chris.fort` here seems to be running some weird scheduled tasks.

![image](https://github.com/user-attachments/assets/c7f36e59-0655-4b1f-822b-0df9fe554db2)

**[Task 2, Question 3] Which user from the HR department was observed to be running scheduled tasks?** - `Chris.fort`

NOTE: A "LOLBIN" is a system process that has unintended functionality that may be useful for an attacker or someone on the red team. You can check out a list of them [here](https://lolbas-project.github.io/#).

We'll go a little out of order and try to identify any suspicious executables _first_. We're specifically looking for programs that let you _download_ files. A bunch of the files on this list are `.exe` files, so we could start by looking at `index="win_eventlogs" HostName="HR*" *.exe`. We may want to check the `ProcessName` field to see what's getting executed. There's nothing _that_ suspicious in the list of top ten (`msaccess` _can_ be used to download files, but they need to have very specific extensions, and it seems like the executable was used generally legitimately). If we again investigate the rarest values of the field, we see `certutil.exe` show up:

![image](https://github.com/user-attachments/assets/bef6a25b-a8db-4f16-8117-2558b16ded21)

This is significant because Certutil is, indeed, a LOLBIN. And it can, indeed, be used to download files:

![image](https://github.com/user-attachments/assets/ba04327b-7095-49b6-8cbb-97ad2da13113)

If we check how `certutil` is used by running the query `index="win_eventlogs" HostName="HR*" *.exe`, we see that this is exactly how the executable is used. The `UserName` is what we want for this question.

![image](https://github.com/user-attachments/assets/fc22d54e-9a94-4cc6-ae18-d6b260920ba6)

(As an aside, it's also possible to search for instances of `http*` in the logs, instead of looking for specific executables. We're told that a payload was downloaded from a filesharing host, so a good first step would also have been to check for the existence of URLs in commands.)

**[Task 2, Question 4] Which user from the HR department executed a system process (a LOLBIN) to download a payload from a filesharing host?** - `haroon`

Based on our discussion above, the answer to the next question should be pretty straightforward.

**[Task 2, Question 5] To bypass the security controls, which system process (LOLBIN) was used to download a payload from the Internet?** - `certutil.exe`

We know that the executable was `benign.exe`. If we continue examining this event log, then we can see it was downloaded on March 4th, 2022. We don't have any other instances of this filename in the logs, so we can assume that it was run on that day.

**[Task 2, Question 6] What was the date that this binary was executed by the infected host?** - 2022-03-04

We can get the site used to download the payload if we simply look at the `CommandLine` entry for the log. This tells us how `certutil` was used as a LOLBIN, and how it was used to transfer an executable to the computer.

**[Task 2, Question 7] Which third-party site was accessed to download the malicious payload?** - `controlc.com`

We went over what the name of the file was in previous questions, and it can be seen in the `CommandLine` entry of the log.

**[Task 2, Question 8] What is the name of the file that was saved on the host machine from the C2 server during the post-exploitation phase?** - `benign.exe`

As it turns out, `controlc.com` is a website like Pastebin, so it might be worth checking out. If we navigate to the URL shown in the `CommandLine` entry, we see the following:

![image](https://github.com/user-attachments/assets/7e2a49b1-a64e-41ca-a6a8-7ca160336b3f)

It appears that this is our flag. Of course, we may want to exercise more caution in navigating to suspicious files and websites in the logs.

**[Task 2, Question 9] The suspicious file downloaded from the C2 server contained malicious content with the pattern `THM{...........}`. What is that pattern?** - `THM{KJ&*H^B0}`

The full URL is given in the `CommandLine` entry of the log.

**[Task 2, Question 10] What is the url that the infected host connected to?** - `https://controlc.com/e4d11035`
