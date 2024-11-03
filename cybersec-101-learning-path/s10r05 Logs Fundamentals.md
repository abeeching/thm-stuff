# Logs Fundamentals

## [Task 1] Introduction to Logs

Attackers try to avoid leaving traces on the victim machine to avoid detection, though the security team can still identify what has happened, how it happened, and in some cases they can even find out who was behind the attack. This is possible with the help of logs, which can still record traces of malicious activity and any "digital footprints," so to speak. Examining logs makes it easier to trace the activity and who was responsible for it.

Logs play a pivotal role in various cases:
- For security event monitoring, we use logs to detect anomalous behavior when real-time monitoring is used
- For incident investigation and forensics, we use logs to see what happened during an incident and perform root cause analysis
- For troubleshooting, we can look at what errors are observed in the system to identify and fix issues
- For performance monitoring, logs give us some insight into the performance of applications
- For auditing and compliance, we can use logs to establish a trail for different kinds of activities

Our goal is to learn how to use logs to investigate different attacks.

**[Task 1, Question 1] Where can we find the majority of attack traces in a digital system?** - logs

## [Task 2] Types of Logs

There's many events of different catgories to investigate. Fortunately, systems that do logging will categorize logs based on what information they provide. Our goal is to figure out which log we need to examine to investigate a specific issue.

Here are some logs that are used for investigating incidents:
- System logs: Troubleshooting running issues in the OS; provides info on OS activities - system startup/shutdown, driver loading, system errors, hardware events
- Security logs: Detect and investigate incidents; provide info on security-related activities - authentication, authorization, security policy changes, user account changes, abnormal activities
- Application logs: Contain specific events related to the application; interactive and non-interactive activities are logged here - user interaction events, application change/update events, policy enforcement events
- Audit logs: Provide detailed information on system changes and user events; helpful for compliance requirements and security monitoring - data access, system change, user activity, and policy enforcement events
- Network logs: Information on outgoing and incoming traffic; troubleshooting network issues and useful for incident investigations - incoming/outgoing network traffic events, network connection logs, network firewall logs
- Access logs: Provide detailed information about access to different resources - webserver access logs, database access logs, app access logs, API access logs
- There are other types of logs depending the applications present and what services they provide

Once we identify the log(s) of interest, we'll want to start analyzing them. Log analysis is a technique for extracting valuable data from logs and looking for signs of abnormal activities. Searching for specific activities and abnormalities in the logs with the naked eye is impossible, so we've developed some techniques for manually and automatically investigating logs. Manual techniques will be discussed in the next few tasks.

**[Task 2, Question 1] Which type of logs contain information regarding the incoming and outgoing traffic in the network?** - network logs

**[Task 2, Question 2] Which type of logs contain the authentication and authorization events?** - security logs

## [Task 3] Windows Event Logs Analysis

The Windows operating system logs many activities that take place, and these are put into separate log files for specific categories. Some of the most important logs include
- Application: Information related to apps installed on the operating system - errors, warnings, compatibility issues, etc.
- System: Information related to the operation of the OS itself, including driver and hardware issues, system startup/shutdown, service information, etc.
- Security: Information related to security-related activities, including authentication, user account changes, security policy changes, and so on

Several other log files exist in Windows for logging activities related to specific actions and applications. Windows OS has a built-in utility, incidentally, with which we can conduct log analysis: the Event Viewer. We can open this app by going to the Start menu and searching for Event Viewer.

The left pane has a collection of logs to examine. The logs described earlier in this task can be accessed by expanding the Windows Logs section. Clicking on the logs here will allow you to explore them in the middle pane. The right-most pane gievs you options for analyzing the logs. To view the contents of the logs, you can double-click a log in the middle pane. Windows event logs have several different fields:
- Description: A detailed description of the activity
- Log Name: The log file name
- Logged: The time the activity was logged
- Event ID: Unique identifier for a given activity type

Various event IDs exist in the Windows event logs, and we can use them to search for specific activities. Here's some important event IDs:
- 4624: A user account was successfully logged in
- 4625: A user account failed to log in
- 4634: A user account successfully logged off
- 4720: A user account was created
- 4724: An attempt was made to reset an account's password
- 4722: A user account was enabled
- 4725: A user account was disabled
- 4726: A user account was deleted

There's many more event IDs. To look for events with a given ID, we can click "Filter Current Log..." on the rightmost pane. We'll be prompted to enter the ID in there, and once click OK, you can see all logs with the given ID.

Let's look at a scenario. Say a critical organization reported being a victim of a cyberattack. We find out that critical data was exfiltrated from a file server in the organization's network. The team was successful in determining the username and IP address of the compromised system. It had access to a file server when the attack took place.

Our goal is to figure out what the attacker was doing before he got into the file server. The VM in which we will do this is attached to this task and will open in Split View automatically. All of the following questions can be answered using information in the logs.

Our first question is to identify the last user account created on the system. User account creation is likely to be in the Security log under event ID 4720. Let's filter for that:

![image](https://github.com/user-attachments/assets/43cdda3f-24e2-497f-9a0b-73b2585bb1d8)

Looking through the logs provided (there should be only three), the most recent account created was named `hacked`.

![image](https://github.com/user-attachments/assets/e284572f-ccc5-4743-8873-57f576a394db)

**[Task 3, Question 1] What is the name of the last user account created on the system?** - `hacked`

Looking in this same log, we can see who created the account if we scroll back up a bit:

![image](https://github.com/user-attachments/assets/81959cc8-e855-4f21-803d-53ddab1b6195)

The Subject specifies who/what generated the event. In this case, the event was generated under the Administrator account, so we can say that the Administrator user created it.

**[Task 3, Question 2] Which user created the above account?** - Administrator

The date can be found by looking at the "Logged" field in the event log window.

![image](https://github.com/user-attachments/assets/b6fa9ebf-5191-49b2-b240-50012b6cbe8e)

**[Task 3, Question 3] On what date was this user account created? Format: M/D/YYYY** - 6/7/2024

Now let's see if the account had its password reset. To check for this we'd want to look for event ID 4724. We'll want to click Clear Filter on the right-hand pane first so we get the entire list of logs again. Then we filter for the event.

Upon doing this, we see two events. One event suggests that the user's password was, indeed, reset.

![image](https://github.com/user-attachments/assets/285c8c3f-7454-4f1b-8334-26bb7a361466)

**[Task 3, Question 4] Did this account undergo a password reset as well? Format: Yes/No** - Yes

## [Task 4] Web Server Access Logs Analysis

Let's take a look at another common log source. We interact with websites daily, whether it be to view content, post content, log in, whatever... all of these requests will get logged by the web server that runs the website. This log file will also contain information on the timeframe, the IP requested, the request type, and the URL of the request. Here's what you might find in an Apache web server access log file (typically located at `/var/log/apache2/access.log`):
- IP Address: The IP address of the user making the request.
- Timestamp: The time when the request was made.
- Request: Details of the request, including the HTTP method used (what action was to be performed) and the requested resource.
- Status Code: The response from the server, with different numbers indicating different response results.
- User-Agent: Information about the user's operating system, browser, and so on when making the request.

On Linux systems we'll have to make use of some command line utilties if we want to perform manual log analysis. Here are some useful commands for this purpose:
- `cat` is used to dipslay the contents of a log file, since they're typically in textual form. Most systems rotate logs regularly, meaning that log files are created for specific timeframes. If we want to combine multiple log files into one for further analysis, we can do so with this utility: `cat (LOG1) (LOG2) > (COMBINED_LOG)`
- `grep` is used to search for strings and pattern in a log file. The command syntax is `grep (SEARCH_TERM) (FILE)`. This will display all lines in the log file with the search term in it.
- `less` is useful for working with many log files. This lets you analyze specific chunks of a file one by one. You can use spacebar to move the next page and `b` to move the previous page. If you want to look for something in the logs, press `/` and then type the pattern in. Then press enter. To navigate to the next occurrence of your search, press `n`; press `N` to navigate to the previous occurrence.

We'll be looking at a sample web server log file in this task, though you can access it in the AttackBox by going to the `/root/Rooms/logs` directory. Note that the attached file may not download on your browser, since it contains IPs, URLs, and user-agent strings that may be considered malicious. You may be able to keep the file anyway in your browser - the specific option will vary on the browser.

Let's sift through the logs, then! For the first question we need to figure out which IP made the last GET request to `/contact`. To do this we'd run `grep "GET /contact" access.log | head`. Note that I used the `head` utility to display the first few lines, since the most recent log entries will be at the top.

![image](https://github.com/user-attachments/assets/f0cdd7f1-70b5-466f-8443-bdc798c85905)

The most recent IP that made this request is `10.0.0.1`.

**[Task 4, Question 1] What is the IP which made the last GET request to URL `/contact`?** - `10.0.0.1`

And now we need to see what the most recent POST request was made by `172.16.0.1`. To do this, we look up `grep "172.16.0.1" access.log | head`:

![image](https://github.com/user-attachments/assets/9623c71f-fc49-4390-bd28-02f4a707be85)

The most recent POST request was made on June 6th 2024 at `13:55:44`.

**[Task 4, Question 2] When was the last POST request made by IP `172.16.0.1`?** - `06/Jun/2024:13:55:44`

This particular log entry tells us that the request was made to `/contact`.

**[Task 4, Question 3] Based on the answer from question number 2, to which URL was the POST request made?** - `/contact`

## [Task 5] Conclusion

We've learned how logs are an important source of information during an investigation, the different types of logs, how they're used, and how to analyze them manually and automatically.
