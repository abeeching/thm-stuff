# Firewall Fundamentals

## [Task 1] What is the Purpose of a Firewall?

Network traffic comes through our devices all the time, and there's usually a lot of it. We can stop attackers from sneaking through in the middle of all the traffic with firewalls. Firewalls inspect a network's or digital device's incoming and outgoing traffic, and they are used to preevnt unauthorized traffic from getting in and out the network. A firewall has rules that can be set to determine what's allowed through - the firewall uses these to determine if traffic should be allowed or denied. Some firewalls offer additional functionalities to help protect the device/network from the outside world.

**[Task 1, Question 1] Which security solution inspects the incoming and outgoing traffic of a device or network?** - firewall

## [Task 2] Types of Firewalls

Firewalls are typically deployed in networks to help filter out harmful traffic from systems and layers. Several types of firewalls exist, and they each serve different purposes.
- Stateless: Operates at Layers 3/4 of the OSI model, filtering data based on predetermine rules without taking note of previous connections. It matches each packet against the rules regardless of whether or not it is a part of a legitimate connection. Quick processing, but cannot apply complex policies to data.
- Stateful: Tracks previous connections and stores them in a state table, and inspects all packets in the context of their connections. Also operates at layers 3/4 of OSI. If a connection is legitimate, then the remaining packets will be allowed. If a connection is denied for a few packets, then any subsequent packets will probably be dropped.
- Proxy: Application-level gateways that act as intermediaries between the private network and the Internet. Operates on the OSI model layer 7. Inspects content of all packets as well. Requests made by users are forwarded by the proxy after inspection, and the IP addresses involved are masked to provide anonymity. Content filtering can also take place here.
- Next generation firewall (NGFW): Most advanced, operates from layers 3-7 of OSI model. Offers deep packet inspection and other functionalities to enhance network traffic security. Also provides intrusion prevention to block malicious activities in real time and heuristic analysis (analysis of patterns). Additionally provides SSL/TLS decryption capabilities.

In brief:
- Stateless firewalls provide basic filtering, but don't track previous connections. They're good for high-speed networks.
- Stateful firewalls recognize traffic by patterns and complex rules can be applied. Network connections are monitored.
- Proxy firewalls inspect data inside the packets as well and provide content filtering as well. They provide applicaiton control and can decrypt/inspect SSL/TLS data packets.
- Next-generation firewalls provide advanced threat protection, come with an intrusion prevention system, identify anomalies based on heuristic analysis, and decrypt/inspect SSL/TLS data packets.

**[Task 2, Question 1] Which type of firewall maintains the tsate of connections?** - stateful firewall

**[Task 2, Question 2] Which type of firewall offers heuristic analysis for the traffic?** - next-generation firewall

**[Task 2, Question 3] Which type of firewall inspects the traffic coming to an application?** - proxy firewall

## [Task 3] Rules in Firewalls

You can customize rules for various networks in a firewall. You may want rules that deny all SSH traffic coming into the network, though you may also want rules that accept SSH traffic from specific IP addresses. The basic components of a firewall rule include:
- Source IP address - where the traffic originates
- Destination IP address - where the traffic goes
- Port - the port number for the traffic
- Protocol - the protocol used for communication
- Action - the action taken upon identifying traffic of a particular nature
- Direction - defines the rule's applicability to incoming or outgoing traffic

The action defines what the firewall should do with the packet once it can apply a rule to it. There are typically three main actions that can be applied:
- Allow: This lets the traffic through the firewall.
- Deny: This prevents the traffic from coming through the firewall. This can be used to prevent traffic from malicious IP addresses, and you can create many Deny rules to reduce the network's threat surface.
- Forward: This redirects traffic to a different network segment using the forwarding rules created on the firewalls. This applies to the firewalls that provide routing functionality and act as gateways between different segments.

Directions can be used to specify what kind of traffic we want to allow/deny/forward:
- Inbound: They're meant to be applied to incoming traffic only.
- Outbound: Made for outgoing traffic only.
- Forward: Forward specific traffic inside the network

**[Task 3, Question 1] Which type of action should be defined in a rule to permit any traffic?** - allow

**[Task 3, Question 2] What is the direction of the rule that is created for the traffic leaving our network?** - outbound

## [Task 4] Windows Defender Firewall

Let's start looking at some firewalls found in an OS. We'll be exploring Windows Firewall in the attached VM for this task; it opens in Split View.

Windows Defender is a built-in firewall for the Windows operating system, containing all the basic functionality for creating, allowing, and denying specific programs and creating customized rules. We'll focus on some of the basic functionalities here. To open the firewall, we need to search for it in the Start Menu. The home page shows the network profiles, with the available options being in the left pane.

The firewall determines your current network profile based on _network location awareness_ (NLA) and will apply the appropriate firewall settings based on this. There are two network profiles:
- Private networks, which contain settings that apply to the home network.
- Guest/public networks, which contain settings that apply when connected to a public or untrusted network (such as coffee shops and restaurants).

You can click the "Allow an app or feature through Windows Defender Firewall"  option to see all apps and features installed and choose which ones are allowed through the firewall. You can specify this at the network profile level. Windows Firewall is turned on by default but can be turned off by clicking the "Turn Windows Defender Firewall on or off" button on the left. This is not recommended. You can also block all incoming connections from this setting. If you need to restore the default settings, click the "Restore defaults" option on the left.

You are able to create custom rules for the network to allow and disallow specific traffic as needed. To create a custom rule, click "Advanced Settings" on the left in Windows Defender Fir3ewal. This wil ltake you to the "Windows Defender Firewall with Advanced Security" app.

The left-hand pane of this app contains links to the inbound and outbound rules. To create a custom rule, click on one of these, then click "New Rule" in the right-hand pane to open the Rule Wizard. From here:
1. Click Custom, then click Next to set up a custom rule.
2. Select "All programs" from the next option, then click Next. If you'd like to specify specific programs, you can also do so here.
3. For the protocol, choose the appropriate settings. You can set it to TCP, UDP, or any other from a list. We can set the local and remote ports as needed. To allow HTTP/S traffic, you might set the remote ports to be `80,443`. Note the use of commas and the lack of spaces between ports.
4. In the Scope tab, you can adjust the local and remote IP addresses and click Next.
5. For the Action, you can choose whether to allow the connection, allow if secure (i.e., authenticated via IPsec), or block the connection. Then click Next.
6. Then you can choose the network profiles you want to apply the rule to. After clicking next, you'll get to give your rule a name and a description. When you're all done, click Finish. The new rule should appear in the appropriate list.

For our hands-on exercise, we'll look at some rules created by a security team on the attached VM. We'll want to find the rule that blocks all incoming connections on the SSH port (22), so we'll want to go to Inbound Rules in the advanced Windows Firewall and look any such rules. One rule, _Core Op_, has the following properties set:

![image](https://github.com/user-attachments/assets/49d0667c-6889-4700-9d93-a36a789b6cf9)

Here, port 22 is blocked. This is the rule that we're concerned with.

**[Task 4, Question 1] What is the name of the rule that was created to block all incoming traffic on the SSH port?** - Core Op

We're also looking for a rule that allows SSH communication from one IP. If we scroll over in the middle pane in Split Screen view, we see that TCP 22 is allowed for one remote address in one rule:

![image](https://github.com/user-attachments/assets/f0a955ca-2e0b-45ac-b1b9-4b23daa0880c)

This rule is called "Infra team."

**[Task 4, Question 2] A rule was created to allow SSH from one single IP address. What is the rule name?** - Infra team

The IP is given in the screenshot above.

**[Task 4, Question 3] Which IP address is allowed under this rule?** - `192.168.13.7`

## [Task 5] Linux iptables Firewall

Linux users also have a built-in firewall - in fact, they have multiple options for a firewall.

Netfilter is a framework in Linux that offers core firewall functionalities, including packet filtering, NAT, and connection tracking. This is fundamental to the firewall utilities in Linux that control network traffic, including:
- iptables: This is the most widely-used utility in Linux distributions, using the Netfilter framework to control traffic.
- nftables: This is a successor to the iptables utility, providing enhanced packet filtering and NAT.
- firewalld: This has predefined rulesets and comes with different pre-built network zone configurations compared to the rest.

The `ufw` utility is the Uncomplicated Firewall. It eliminates the complications of making rules with complex syntax (e.g., like the `iptables` and `nftables` syntax) by providing an easier interface. It's more beginner friendly and can be used to define firewall rules that you'd normally set for `iptables`.

To check the status of a firewall, run `sudo ufw status`. If it is inactive, enable it by running `sudo ufw enable`. This can be disabled when you don't need it anymore by running `sudo ufw disable`.

A rule that allows all outgoing connections on a Linux machine would be `sudo ufw default allow outgoing`. The `default` means that we're defining this policy as a default policy, allowing all outgoing traffic unless we have another rule that explicitly prohibits a certain type of traffic. If we wanted to control incoming traffic, we'd replace `outgoing` with `incoming`.

A rule that would deny all SSH traffic coming into the system would be `ufw deny 22/tcp`. This specifies the action - deny - as well as TCP port 22 for SSH.

To list all active rules in a numerical order, we run `sudo ufw status numbered`. To delete a rule, we run `sudo ufw delete (RULE_NUMBER)` afterwards.

These utilities can be used to manage Netfilter. Depending on your familiarity with Linux and your requirements, you may wish to use different utilities.

**[Task 5, Question 1] Which Linux firewall utility is considered to be the successor of "iptables"?** - `nftables`

**[Task 5, Question 2] What rule would you issue with `ufw` to deny all outgoing traffic from your machine as a default policy? (answer without `sudo`)** - `ufw default deny outgoing`
