# Traffic Analysis Essentials

## [Task 1] Introduction

In brief: Network security is focused on protecting data, applications, and devices connected to the network. It's a pretty important domain in cybersecurity given that most of our devices are connected via networks and the Internet, and it encompasses how systems are designed, operated, and managed in a networking context. Traffic analysis is the process of analyzing the data transmitted over a network to identify potential problems and anomalies.

This does require an understanding of networking in general. TryHackMe provides a module all about the fundamentals of networking to help bring you up to speed.

## [Task 2] Network Security and Network Data

There are two core concerns of network security: authentication (proving who you are) and authorization (proving that you are allowed to do a certain action). Many tools and technologies have been designed to help implement these two things.

Network security comes with three control levels to ensure maximum available security management.
- Physical security controls: Prevent unauthorized physical access to networking devices, cable boards, locks, linked components
- Technical security controls: Prevent unauthorized access to network data, e.g. installation of tunnels and implementation of security layers
- Administrative security controls: Provide consistency in security operations, e.g. creation of policies, access levels, authentication processes

Two main approaches to network security, generally, are as follows:
- Access control: The controls used to ensure authentication and authorization, so the starting point of network security
- Threat control: The controls used to detect and prevent anomalous/malicious activities on a network, internally and externally.

Access control is implemented with the following controls, among others:
- Firewalls: These control incoming and outgoing traffic with predefined rules. The focus is on blocking suspicious and malicious traffic and application-layer threats, while still allowing legitimate, expected traffic.
- Network access control (NAC): This process analyzes a device's security before letting it access the network. If device specifications and conditions are not compliant with a given security profile, then it is not let into the network.
- Identity and access management (IAM): This process controls the asset identities and user access to data, systems, and resources on the network.
- Load balancers: These distribute tasks, via metrics, over a set of resources to help improve overall data processing flow.
- Network segmentation: This process creates and controls network ranges, which can be used to separate and group access levels and assets with common functionalities. This is often used to protect sensitive devices and data on a network.
- Virtual private networks (VPNs): These create and control encrypted communications between devices over the network. This is typically used for secure, remote access to resources.
- Zero Trust: This model focuses on configuring and implementing access and permissions at minimum levels (thus adhering to the _principle of least privilege_, which says you should only have the access you need to fulfill your assigned role). In essence, Zero Trust is about assuming zero trust - users and devices are rarely (if not, never) assumed to be trustworthy.

Threat control is implemented with the following controls:
- Intrusion detection and prevention systems (IDS, IPS): These inspect traffic and create alerts (in the case of an IDS) or reset the connection (in the case of an IPS) when an anomaly or threat is detected.
- Data Loss Prevention (DLP): These systems inspect traffic (their content and the context in which the traffic is taking place) and blocks exfiltration or removal of sensitive data.
- Endpoint protection: This process focuses on protecting endpoints and appliances connected to the network, and often involves a multi-layered approach of encryption schemes, antiviruses, antimalware programs, DLP systems, and IDS/IPS systems.
- Cloud security: This focuses on protecting cloud and online-based system resources from threats and data leakage with countermeasures like VPNs and data encryption schemes.
- Security Information and Event Management (SIEM): This is a technology that helps with threat detection, compliance, and incident management by using available data from logs and traffic statistics. Vulnerabilities, threats, and anomalies are identified via event and contextual analysis.
- Security Orchestration, Automation, and Response (SOAR): This is a technology that helps with coordinating and automating tasks between various people, tools, and data within a single platform to identify anomalies, threats, and vulnerabilities. It is useful in vulnerability management, incident response, and security operations.
- Network traffic analysis, detection, and response: This process focuses on inspecting traffic and traffic captures to identify anomalies and threats. 

Typically, network security management operation processes are categorized as follows:
- Deployment: Device, software installation; initial configuration; automation
- Configuration: Feature configuration; initial network access configuration
- Management: Security policy implementation; NAT/VPN implementation; threat mitigation
- Monitoring: System, user activity, and threat monitoring; log/traffic capturing
- Maintenance: Upgrades; security updates; rule adjustments; license management; configuration updating

Not all organizations have the resources to create dedicated teams for specific security domains. Budget constraints, employee skillset, and organizational size may inhibit the ability to handle security operations well. Thus, managed security services (MSS) may be used to help provide security needs. Essentially, an organization can outsource their security to a service provider, which are known as Managed Security Service Providers (MSSPs). Managed security services can be time and cost-effective and are easy to use. An MSS may provide the following services, among others:
- Network penetration testing: Assessing network security via simulation of attacker techniques
- Vulnerability assessment: Assessing network security via discovery and analysis of vulnerabilities in the environment
- Incident response: Addressing and managing security breaches via identification, containment, and elimination of incidents
- Behavioral analysis: Addressing system and user behaviors via baselines and traffic profiles for identifying anomalies

The following questions can be answered with the information given above.

**[Task 2, Question 1] Which Security Control Level covers creating security policies?** - Administrative

**[Task 2, Question 2] Which Access Control element works with data metrics to manage data flow?** - Load balancing

**[Task 2, Question 3] WHich technology helps correlate different tool outputs and data sources?** - SOAR

## [Task 3] Traffic Analysis

Traffic analysis involves intercepting, recording, monitoring, and analyzing network data and communications to detect and respond to various issues, including those of system health, anomalies, and therats. Being able to analyze the traffic over a network is useful for security and operational reasons. It's essential to network security, and it is the main focus of this section of rooms. Traffic analysis can be helpful in a variety of areas, and tools have been developed with these in mind:
- Need to just sniff a network and do some general traffic analysis? Use Wireshark!
- Need to monitor a network generally? Use Zeek!
- Need to implement an intrusion detection and prevention system? Use Snort!
- Need to perform network forensics? Use NetworkMiner!
- Need to hunt for threats on your network? Use Brim!

Traffic analysis generally falls into two categories:
- Flow analysis: The collection of data and evidence from networking devices, aiming to provide statistical results via a data summary instead of providing in-depth packet-level investigation. Easier to perform, but you don't get all the details.
- Packet analysis: The collection of all available networking data. In-depth packet-level investigation (or deep packet inspection/DPI) is used to detect and block anomalous and malicious packets. Gives you more details, but is harder to perform well.

Traffic analysis helps give full network visibility, provide a baseline for asset tracking, and it helps with detection and response to anomalies and threats. Network data is generally invaluable when it comes to investigations, since it really is a pure and rich data source. Even if an attacker encrypts or encodes data sent over the network, you can still gather a lot of information just by looking for weird or unexpected patterns and situations. Being able to analze traffic is important for security analysis.

The room provides a simulation of what you can do with traffic analysis, so let's go through it! The simulation is comprised of two levels. In the first level, we are given a series of IP addresses and we need to filter out those that appear to be malicious. We get a traffic analyzer and an IDS/IPS system to work with:

![image](https://github.com/user-attachments/assets/57f20ad3-72ec-40cc-b461-6a175a5bc510)

What wasn't pictured was the animation of traffic being sent to the system at `10.10.99.199`. It did show some examples of bad traffic (presumably in red). If you didn't catch where all the bad traffic was coming from, then we'll want to analyze the results in the tables. We do see two points of interest in the table above. One is the existence of Metasploit traffic; given that Metasploit can be used to exploit vulnerabilities in a system, this is not good. This same IP is responsible for multiple login attempts, which is also quite suspicious. We'll want to block `10.10.99.99` first. There is also a general warning for bad traffic coming from `10.10.99.62`, so we'll block that off too. If we block off these two bits of traffic, we get our first flag.

**[Task 3, Question 1] What is the flag?** - `THM{PACKET_MASTER}`

For this next level, we must filter traffic going to specific ports to help prevent the server from getting compromised. We know that traffic from `10.10.99.99` is from Metasploit, and Metasploit often makes use of nonstandard ports for attacks. In the Traffic Analyzer table, the right-hand column is presumably the destination traffic. A lot of these ports - 445 (HTTPS), 21 (FTP), 8080 (HTTP, alternate), 3689, and 3306 - seem normal enough. But 4444, 7777, and 2222 seem pretty weird - these are ports associated with `10.10.99.62` and `10.10.99.99`, the latter of which we know to be Metasploit traffic. Let's block these three ports. Upon doing so, we get our second flag.

**[Task 3, Question 2] What is the flag?** - `THM{DETECTION_MASTER}`
