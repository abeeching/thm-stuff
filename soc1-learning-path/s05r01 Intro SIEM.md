# Introduction to SIEM

## [Task 1] Introduction

We explored SIEMs a little bit in the previous section on endpoint security monitoring, but the purpose of that section was to develop techniques for investigating issues on an endpoint. Now we'll cover SIEMs - or Security Information and Event Management systems - in more detail. These allow us to get data from various endpoints and network devices, which helps us investigate issues throughout the entire network at once.

**[Task 1, Question 1] What does SIEM stand for?** - Security Information and Event Management system

## [Task 2] Network Visibility Through SIEM

Oftentimes, we like to have visibility of all activities in a network. Each network component can have one or more log sources generating a whole bunch of logs. Generally, in a network, you'll see two types of logs:
- Host-centric logs, which capture events occurring within (or related to) the host. Includes Windows event logs, Sysmon, Osquery, and can include events such as file access, authentication attempts, process execution, registry changes, and PowerShell execution.
- Network-centric logs, which capture events related to network communication between hosts (e.g. in a network, or perhaps over the Internet). This includes protocols and utilities such as SSH, VPN, HTTP(S), FTP, and so on, and can include events such as SSH connections, FTP file access, web traffic, VPN access, and file sharing.

These devices can generate many events (on the order of a hundred _per second_), so examining the logs on each device, one-by-one, is tedious. A SIEM solution helps out with this; it can take logs from many sources in real-time, and it also provides the ability to correlate events, search through logs, investigate incidents, and more. SIEMs often provide these features:
- Real-time log ingestion
- Alerting on abnormal activities
- 24/7 monitoring and visibility
- Early detection, protecting against the latest threats
- Data insights and visualization
- Ability to investigate past incidents

**[Task 2, Question 1] Is registry-related activity host-centric or network-centric?** - Host-centric

**[Task 2, Question 2] Is VPN-related activity host-centric or network-centric?** - Network-centric

## [Task 3] Log Sources and Log Ingestion

As mentioned, every device in the network generates logs when an activity is performed on it. Let's look at some common sources of logs. Windows will record events that take place on the computer, and they can be viewed with Event Viewer (and with other tools). We can forward all sorts of logs from Windows endpoints to the SIEM solution for better monitoring.

Linux, on the other hand, stores all related logs (events, errors, warnings, and so on) in various files in the operating system. Typically you'll find logs in `/var/log`:
- `/var/log/httpd`: Contains HTTP request-response and error logs
- `/var/log/cron`: Contains events related to cronjobs
- `/var/log/auth.log`, `/var/log/secure`: Contains authentication logs
- `/var/log/kern`: Contains kernel-related events.

Webservers will also make use of logs, which can help keep an eye on all requests and responses from the webserver as well as any potential attacks. Common locations for these logs in Linux are `/var/log/apache` and `/var/log/httpd`.

These logs can be helpful in identifying security issues, so we'd like to _ingest_ them, or set them up in a SIEM system. Here are a few ways that logs are ingested with a SIEM:
- Agents and forwarders: A lightweight utility is set up on a host to collect logs. This is known as an agent (or, if you're using Splunk, a forwarder). These utilities capture all the important logs and send them to the SIEM server.
- Syslog: This is a widely-used protocol used to collect data from various systems. Real-time data is sent to the centralized system with this protocol.
- Manual upload: Some SIEM solutions let users ingest offline data for quick analysis. Once the data is ingested, it's normalized and made available for analysis.
- Port forwarding: SIEM solutions can be configured to listen on a certain port. The data is forwarded by the endpoints to the SIEM instance over this port.

**[Task 3, Question 1] In which location within a Linux environment are HTTP logs stored?** - `/var/log/httpd`

## [Task 4] Why SIEM?

SIEMs are used to provide correlation on collected data, which can be used to detect threats. Once a threat is detected, an alert is raised, allowing analysts to take action based on the results of investigations. SIEMs help with timely detection and prevention against the latest threats, and they're generally good for providing network visibility.

A SIEM is a major component of a security operations center (SOC) ecosystem. SIEMs collect logs and examine if an event or flow matches conditions for an alert to trigger (e.g. rules, thresholds). Common capabilities of a SIEM system were mentioned earlier, but in brief, it allows for event correlation, host and network visibility, investigation and response, and threat hunting.

A SOC analyst will typically use SIEMs to gain visibility into the network, which helps with meeting responsibilities, such as monitoring/investigating, identifying false positives, tuning rules, reporting, compliance, and identifying blind spots in network visibility.

## [Task 5] Analyzing Logs and Alerts

Once logs are ingested, a SIEM can be used to look for unwanted, abnormal, or suspicious behaviors and patterns within the logs. Most SIEMs will have a dashboard, which presents the data for analysis after being normalized and ingested. Multiple dashboards can be used to create a summary of analyses and to receive actionable insights. SIEM solutions may provide the option to create a custom dashboard, in case any of the defaults don't do the job. Dashboards may contain the following information:
- Alert highlights
- System notification
- System health alerts
- List of failed login attempts
- Count of ingested events
- Rules triggered
- Top domains visited

Correlation rules are used in SIEMs to dictate when an alert should be issued. Here's some examples:
- Say a user fails at logging in five times within ten seconds. We could raise an alert for this, because that may be suspect.
- If a user were to succeed at logging in after failing multiple times, then we may want to alert on this too. I suppose it would depend on how many times the user tried to log in before!
- If USB usage is restricted by the company, then you may want to set up a rule that alerts whenever a USB is inserted into a device.
- If outbound traffic is more than a certain amount, you may want to alert for potential exfiltration. This ultimately depends on company policy and baselines.

We could go further and develop more conditional statements for some more complex situations. Notice how these examples look at and correlate information between multiple fields.
- Adversaries like removing logs, because it helps them cover their tracks. When a user attempts to remove or clear log entries, Windows will log that as an event under the ID 104. So, we may want to set up a rule that issues an alert if a given log comes from the Windows Event Logs AND if that log contains event ID 104.
- Adversaries often use commands like `whoami` to figure out what user they have logged into once exploitation and privilege ecalation is complete. On Windows, we know that we can look for Windows Event ID 4688 if we're looking for process execution, and we're looking for the `whoami` process. So our rule might be "if the log comes from the Windows Event Logs AND the Event ID is 4688 AND the process name is `whoami`, trigger an alert."

Analysts will spend much of their time at a SIEM's dashboard to see key details about a network. If an alert is triggered, then the events and flows associated with the alert are examined, and the rule is reviewed to see what conditions are in place. After some investigation, the analyst can determine whether or not the alert is a true or false positive, and take action:
- If the alert is a false alarm (false positive), then the analyst may consider tuning the rules.
- If the alert is a true positive, the analyst might perform further investigations.
- The analyst may contact the asset owner to ask about the activity.
- If the analyst can confirm that suspicious (potentially malicious) activity is taking place, then they could isolate the infected host.
- The analyst may also block associated IPs as needed.

**[Task 5, Question 1] Which event ID is generated when event logs are removed?** - 104

**[Task 5, Question 2] What type of alert may require tuning?** - False Alarm

## [Task 6] Lab Work

Now let's look at a simulated SIEM example. We'll have a sample dashboard with some events, and if a suspicious activity occurs, an alert will go off.

If we click Start Suspicious Activity, one of the processes on the process list on the bottom-left will start flashing red. This suggests that this is our suspicious process.

![image](https://github.com/user-attachments/assets/940d983e-901f-4229-9e40-f1152efdb673)

**[Task 6, Question 1] Click on Start Suspicious Activity. Which process caused the alert?** - `cudominer.exe`

Now we are taken to a list of events. If we scroll to the right, we see the process being run, and we see the user that's running the process right next to it.

![image](https://github.com/user-attachments/assets/f71bc998-863a-407e-bd00-4cd1e15c7404)

**[Task 6, Question 2] Find the event that caused the alert. Which user was responsible for the process execution?** - `chris.fort`

We can get more information from this screen. The hostname is just to the left of the username:

![image](https://github.com/user-attachments/assets/b7135d2c-f07d-4c0b-a34f-eed8c70b6df4)

**[Task 6, Question 3] What is the hostname of the suspect user?** - `HR_02`

Now we are taken to the rule that caused the alert to trigger in the first place. Here's what it looks like:

![image](https://github.com/user-attachments/assets/0c4b8e64-50ed-4e60-9439-d23be7f1574e)

You'll notice at the end that we're looking for process names that have `*miner*` or `*crypt*` in them, i.e., we're looking for processes that have "miner" or "crypt". The process from earlier was `cudominer`, so I think we can figure out why this alert went off.

**[Task 6, Question 4] Examine the rule and the suspicious process. Which term matched the rule that caused the alert?** - `miner`

Well, we don't know for sure if this is an actual cryptominer program, but it makes sense that the SIEM would alert on this process. It satisfies the conditions of the rule pretty well. If we're only considering this information, that's grounds for saying this is a true positive. We should choose the option that allows us to isolate the host in the simulated SIEM.

**[Task 6, Question 5] What is the best option that represents the event? False-Positive or True-Positive?** - True-Positive

Once we select the correct action, we get our flag:

**[Task 6, Question 6] Selecting the right ACTION will display the FLAG. What is the FLAG?** - `THM{000_SIEM_INTRO}`.
