# Gobuster: The Basics

## [Task 1] Introduction

This room focuses on Gobuster, a tool often used for reconnaissance. We'll use this tool to enumerate web directories, subdomains, and virtual hosts.

## [Task 2] Environment and Setup

The attached VM is an Ubuntu 20.04 VM and is acting as a web server. The web server has many subdomains and vhosts, and the web server has two content management systems (CMS) installed: Wordpress and Joomla.

We'll use the AttackBox, which has Gobuster already installed, to enumerate web server directories and domains. You can use your own machine; it just needs to be connected to the TryHackMe VPN and have Gobuster set up. You can grab Gobuster for your own machine via the Gobuster GitHub repository.

We'll be working in a local network with a DNS server on the web server. This means you'll need to do a few things so that you can actually resolve the domains used in this room.
- Open a terminal and run `sudo nano /etc/systemd/resolved.conf`
- Remove the `#` in front of `DNS=`. Add the web server's IP after it.
- Save the file by pressing CTRL + O, then press ENTER, and then exit the editor by pressing CTRL + X.
- Enter the command `sudo systemctl restart systemd-resolved`.

## [Task 3] Gobuster: Introduction

Gobuster is an open-source tool written in Golang that enumerates web directories, DNS subdomains, vhosts, Amazon S3 buckets, and Google Cloud Storage with brute-force, using wordlists and handling the incoming responses. This is often used for pentesting, bug bounty hunting, and cybersecurity assessments. It is often used during the reconnaissance and scanning phases of ethical hacking.

Enumeration is the act of listing all the available resources, whether they are accessible or not. Brute-force is the act of trying every possibility until a match is found.

Gobuster is included by default in distributiosn such as Kali Linux. To see what options are out there, run `gobuster --help`. There are multiple sections in this help page.
- "Usage" shows you how to use the command.
- "Available Commands" gives you the commands that we can use to enumerate various things. We'll be using the `dir`, `dns`, and `vhost` commands in this room. `dns` lets us do subdomain enumeration, and `vhost` lets us enumerate vhosts.
- "Flags" list off specific option that we can configure to customize our commands. Some options include:
  - `-t` or `--threads`: This configures the number of threads to use for the scan, with each thread sending requests in a slight delay. By default, this is 10; this may be slow with larger wordlists. You can increase/decrease this based on what resources you have available.
  - `-w` or `--wordlist` configures a wordlist to use for iterating. Each wordlist entry gets added to the URL you include in the command.
  - `--delay` lets you define an amount of time to wait before sending requests. This can be helpful since some web servers can detect enumeration by seeing how many requests are being sent in a period of time. We can increase the delay to make it look more normal.
  - `--debug` lets you troubleshoot when a command gives errors.
  - `-o` or `--output` writes the enumeration results to a file of our choosing.

An example command would be `gobuster dir -u "(URL)" -w /usr/share/wordlists/dirb/small.txt -t 64`.
- `gobuster dir` tells us that we're going to be using the directory/file enumeration mode.
- `-u "(URL)"` lets us specify the target URL.
- `-w /usr/share/wordlists/dirb/small.txt` specifies the wordlist to brute-force directories. Each entry is added to the URL to form a new URL, and a GET request is sent to the URL.
- `-t 64` specifies that we'll be using 64 threads, improving performance as long as we have the resources for it.

**[Task 3, Question 1] What flag would we use to specify the target URL?** - `-u`

**[Task 3, Question 2] What command do we use for the subdomain enumeration mode?** - `dns`

## [Task 4] Use Case: Directory and File Enumeration

Gobuster has a `dir` mode that lets us enumerate website directories and their files. This is useful for when we're performing a pentest and would like to see the directory structure of a website and what files it contains. Directory structures of websites and web apps typically follow a particular convention, making them susceptible to brute-force with wordlists. Gobuster lets you scan the website and return status codes, telling you if the directory can be requested or not.

For a complete overview of what you can do with `gobuster dir` (or with any other sub-command), you can just add `--help`. There are many flags that can be used with this sub-command:
- `-c` and `--cookies`: This configures a cookie to pass along with each request, such as a session ID.
- `-x` and `--extensions`: This flag specifies which file extensions you want to scan for, e.g. `.php` and `.js`.
- `-H` and `--headers`: This configures a header to pass along with each request.
- `-k` and `--no-tls-validation`: This skips the process that checks the certificate when HTTPS is used. This is often used for CTF events or test rooms where a self-signed cert is used. Otherwise, you'd get an error.
- `-n` and `--no-status`: You can set this flag if you don't want to see status codes for each response received, helping keep the screen output clean.
- `-P` and `--password` can be used with the username flags if you want to execute authenticated requests, which is good if you have credentials from a user on standby.
- `-s` and `--status-codes` lets you configure which status codes of received responses you want to display. This could be a single status code or a range.
- `-b` and `--status-codes-blacklist` lets you configure which status codes of the received responses you don't want to display. Configuring this overrides `-s`.
- `-U` and `--username`, in conjunction with the password flags, lets you perform authenticated requests.
- `-r` and `--followredirect` lets Gobuster follow any redirects it may receive as a response (e.g. HTTP 301 or 302).

To run Gobuster in `dir` mode, use the command `gobuster dir -u "(URL)" -w (WORDLIST)`. The `-u` and `-w` flags are required for Gobuster to work in `dir` mode. Note that Gobuster uses the URL as the base path from which it'll start looking. The URL must also contain the protocol in use, e.g. `http://`. The hostname may be an IP or an actual hostname. When using the IP, you may target a different website than intended, though, so you should use the hostname if you want to be sure. Gobuster does not do enumeration recursively - if the results show a directory path you're interested in, you'll want to enumerate that specific directory.

**[Task 4, Question 1] Which flag do we have to add to our command to skip the TLS verification? Enter the long flag notation.** - `--no-tls-validation`

Here's a snippet of the output we get when we run `gobuster dir -u "http://www.offensivetools.thm/" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`:

![image](https://github.com/user-attachments/assets/f02ddf17-660d-4688-92e1-fb08da32de09)

One of these is a little interesting - the `secret` directory. While this leads to a redirect, it could have some neat stuff inside.

**[Task 4, Question 2] Enumerate the directories of `www.offensivetools.thm`. Which directory catches your attention?**

Now that we know the directory is there, let's run `gobuster dir -u "http://www.offensivetools.thm/secret" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .js` and see what we can find.

![image](https://github.com/user-attachments/assets/8fe947b8-d790-4c6c-9ddc-6ca1c6b4e6ee)

We see a `flag.js` file in there! Let's go to `http://www.offensivetools.thm/secret/flag.js` and see what's inside!

![image](https://github.com/user-attachments/assets/4046dd26-0ef3-49a3-8fda-5095b4185887)

**[Task 4, Question 3] Continue enumerating the directory found in question 2. You will find an interesting file there with a `.js` extension. What is the flag found in this file?** - `THM{ReconWasASuccess}`

## [Task 5] Use Case: Subdomain Enumeration

The next mode we'll focus on is `dns` mode, which lets Gobuster brute-force subdomains. In a pentest, this is an essential task. Something that is patched in the regular domain may not be patched in the subdomain. An opportunity to exploit a vulnerability in one of these subdomains may exist.

According to the `--help` flag, these are the switches we can use with `dns`:
- `-c` and `--show-cname` shows the CNAME records. This cannot be used with the `-i` flag.
- `-i` and `--show-ips` shows the IP addresses that the domains and subdomains resolve to.
- `-r` and `--resolver` can be used to configure a custom DNS server to use for resolving.
- `-d` and `--domain` can be used to configure the domain you want to enumerate.

There's not many options, but this should be enough to do the job.

The command syntax is `gobuster dns -d (DOMAIN) -w (WORDLIST)`. The `-d` and `-w` flags are required for subdomain enumeration.

**[Task 5, Question 1] Apart from the `dns` keyword and the `-w` flag, which shorthand flag is required for the command to work?** - `-d`

We'll run the command `gobuster dns -d offensivetools.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt`, with the wordlist being specifically called out by the hint. We'll ignore duplicate entries that get caught due to upper/lowercase variations.

![image](https://github.com/user-attachments/assets/721e0a96-608c-4e2f-82dc-d7d305c4be3a)

We see five entries, with two of them being `www` and `WWW`. This leaves us with four unique subdomains.

**[Task 5, Question 2] Use the commands learned in the task. How many subdomains are configured for the `offensivetools.thm` domain?** - 4

## [Task 6] Use Case: Vhost Enumeration

Lastly, we'll use Gobuster to brute-force virtual hosts (vhosts). These are different websites that are on the same machine. Sometimes they look like subdomains, but they're IP-based and they run on the same server, whereas subdomains are set up in DNS.

There's a key difference in how `vhost` and `dns` works in Gobuster: `vhost` navigates to the URL created by combining the configured hostname (`-u` flag) with an entry of a wordlist, whereas `dns` does a DNS lookup to the FQDN created by combining the configured domain name (`-d` flag) with an entry of a wordlist.

Here's some flags that can be used with `vhost`, as given in `gobuster vhost --help`:
- `-u` and `--url`: Specifies the base URL/target domain for brute-forcing virtual hostnames
- `--append-domain`: Appends the base domain to each word in the wordlist, e.g. `(WORD).(DOMAIN)`.
- `-m` and `--method`: Specifies the HTTP method to use for the requests.
- `-d` and `--domain`: Appends a domain to each wordlist entry to form a valid hostname, useful if not provided explicitly.
- `--exclude-length`: Excludes results based on the length of the response body, which is helpful for filtering out unwanted responses. False positive results will typically have similar response sizes; we expect a 200 OK response to have a true positive, so we can use this flag to focus only on those kinds of responses.
- `-r` and `--follow-redirect`: Follows HTTP redirects, which is useful for cases where subdomains may redirect.

You need to use `-u` and `-w` with the `vhost` command. The syntax is `gobuster vhost -u "(URL)" -w (WORDLIST)`. In more realistic tests, you might use more flags depending on the domain's infrastructure; if it's not fully set up, then you might need more flags like `--domain` and `--append-domain`.

Gobuster will take what's written in the `Host:` portion of the request and change it. With a website like `www.example.thm`, there are three components here:
- `www` is the subdomain, and is the part that Gobuster fills in with each entry of the wordlist.
- `example` is the second-level domain, which can be configured with the `--domain` flag.
- `thm` is the top-level domain and can be configured with `--domain`. This and the second-level domain must be configured together.

For this task, the example command will do nicely. We just need to alter it for the question: `gobuster vhost -u "http://10.10.124.178" --domain offensivetools.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain --exclude-length 250-320`. This results in the following:

![image](https://github.com/user-attachments/assets/bc8e7146-5d54-4558-a7d9-29421c788d0f)

There are 5 domains that get a 200 status code, and removing duplicates we'll only have 4 leftover.

**[Task 6, Question 1] Use the commands learned in this task to answer the following question: How many vhosts on the `offensivetools.thm` domain reply with a status code 200?** - 4

## [Task 7] Conclusion

Gobuster is a tool used for enumerating things, such as directories, files, DNS subdomains, and virtual hosts. The `dns` mode enuemrates DNS subdomains, the `dir` mode enumerates directories, and the `vhost` mode enumerates virtual hosts. Each mode has its own set of flags that need to be configured, as well as optional flags that can be used to fine-tune the results. In `dns` mode, Gobuster uses DNS services to scan for subdomains; in `vhost` mode, Gobuster sends web requests with the configured URL and wordlist.
