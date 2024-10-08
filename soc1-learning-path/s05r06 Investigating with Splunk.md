# Investigating with Splunk

## [Task 1] Investigating with Splunk

An analyst spots some anomalous behavior in a few Windows logs. The adversary may have access to machines and may have been able to create a backdoor. Let's investigate this.

Navigating to the IP in the AttackBox (or over a VPN), we can start by taking a look at how many logs are available in the `main` index. We can find this simply by searching for `index="main"`.

![image](https://github.com/user-attachments/assets/1538b3b5-b5e3-4691-b901-95a354550f3e)

**[Task 1, Question 1] How many events were collected and ingested in the index `main`?** - 12256

Now let's investigate the situation proper. We want to look for backdoor users being created. When a user account gets created in Windows, you may see an event with ID 4720 logged. If we search `index="main" EventID="4720"`, we get one event, and the details of the account are provided:

![image](https://github.com/user-attachments/assets/30105c44-c487-46bb-aebc-e53da2ecb074)

**[Task 1, Question 2] On one of the infected hosts, the adversary was successful in creating a backdoor user. What is the new username?** - `A1berto`

We also would like to find any registry keys that were set in relation to this user. Now that we know the username of the backdoor user, we could just search `index="main" A1berto` and see what comes up. There are only fourteen results in this search, and one of the logs suggests a registry value was set:

![image](https://github.com/user-attachments/assets/1ff5325d-a858-4c0e-8adb-841f9d5aa743)

The value updated in the registry is `(Default)`, but we're looking for the path to that value. So in this case we'll just ignore the `(Default)` at the end. You can also find this key in other log entries, namely those where a key was created. These should show up in the list as well!

**[Task 1, Question 3] On the same host, a registry key was also updated regarding the new backdoor user. What is the full path of that registry key?** - `HKLM\SAM\SAM\Domains\Account\Users\Names\A1berto`

If we go back to `index="main"`, we can see the total number of users. There are only four entries in the `User` field, so let's investigate:

![image](https://github.com/user-attachments/assets/822c9933-5b43-4feb-914a-676c67cf9299)

It's pretty clear that the only user the attacker would be trying to impersonate is `Alberto`.

**[Task 1, Question 4] Examine the logs and identify the user that the adversary was trying to impersonate.** - Alberto

If we look back in `index="main" A1berto`, we _do_ see an event where a user was added from a remote computer. The `WMIC` executable is able to run on different computers, and a process is called to create the user with the `net` command. I am keeping the VM in split-screen mode for my convenience, so the entire command won't show up, but here's what a portion of the command should look like:

![image](https://github.com/user-attachments/assets/9a22ee8c-4466-4df6-a844-39538beebdb8)

This is a Process Create event, which correlates with Security Log Event ID 4688. You could use this to narrow down your search as well.

The full command is as follows.

**[Task 1, Question 5] What is the command used to add a backdoor user from a remote computer?** - `C:\Windows\System32\Wbem\WMIC.exe /node:WORKSTATION6 process call create "net user /add A1berto paw0rd1`

Now let's see how many times the backdoor user tried to log in. A login event can be traced to event ID `4624`, so we'll run the query `index="main" EventID="4624"`. From here, we'll need to see what accounts were logged into. We can get an idea of what accounts were logged into by checking the fields, i.e. `TargetUserName`:

![image](https://github.com/user-attachments/assets/52ea7b4f-62e9-4704-b9d8-24e95c5f2f3f)

Investigating the values and the logs in this query don't seem to indicate that the backdoor user was logged into at any time...so perhaps it was never logged into.

**[Task 1, Question 6] How many times was the login attempt from the backdoor user observed during the investigation?** - 0

Now we're looking for suspicious PowerShell execution. We would expect PowerShell to be executed, along with some other additional process, so we might want to narrow our search down to logs with Event ID 4688. These logs are generated whenever a new process is created. We could also look specifically for `Powershell` in the results. The query that gives us the information we need is `index="main" EventID="4688" Powershell`. This yields one event.

![image](https://github.com/user-attachments/assets/a8ec471a-fc68-4972-95d1-99f9ec13a598)

The command run, as seen in `CommandLine`, is certainly suspicious. So we can presume this is the host where suspicious PowerShell commands were run. The information we'd want is in `Hostname`.

**[Task 1, Question 7] What is the name of the infected host on which suspicious Powershell commands were executed?** - `James.browne`

It turns out that PowerShell logging is enabled, meaning we should have access to PowerShell logs from the Windows Event Logs. We can confirm this by going to `index="main"` and looking at the `Channel` field. As it would turn out, the PowerShell logs are available, and there's 79 of them. We're looking specifically at the `Microsoft-Windows-PowerShell/Operational` logs here, since that's going to contain information about how PowerShell was actually used.

![image](https://github.com/user-attachments/assets/13447db2-9e7a-4e0a-9af0-a7d02be362db)

**[Task 1, Question 8] Powershell logging is enabled on this device. How many events were logged for the malicious Powershell execution?** - 79

Starting from the previous question, we can click `Microsoft-Windows-PowerShell/Operational` to filter for those logs specifically. We're looking for a script that initiated a web request. We see many commands that have a lot of stuff encoded in base64. The best way we can handle this is going into CyberChef, using the "From Base64" recipe, and then applying the "Remove null bytes" recipe to clean up the output a bunch.

Once we start scrolling through the output, we see that it's trying to access some `/news.php` thing. There's an encoded string right by it, so let's figure out what that is.

![image](https://github.com/user-attachments/assets/50f375ae-9ddc-4a49-aebb-2136a1c7faf5)

When we decode the string in the output, we get...

![image](https://github.com/user-attachments/assets/567632a2-027b-4b8e-b4d6-79119bd5c3c4)

We can combine the output that we're seeing to get the full URL. We are expected to defang it though, so let's just pop it into CyberChef and apply the Defang URL recipe.

![image](https://github.com/user-attachments/assets/640ec412-d5b8-42bf-947e-a064d4a5edc6)

**[Task 1, Question 9] An encoded Powershell script from the infected host initiated a web request. What is the full URL?** - `hxxp[://]10[.]10[.]10[.]5/news[.]php`
