# Active Directory (AD) Basics

## [Task 1] Introduction

...There is still more we can learn, as far as Windows basics go. While we've covered some of the fundamentals, Windows products are often used for their enterprise capabilities. Active Directory is the basis for these features, and corporations love it because it can help simplify device and user management.

AD is a different kind of ballpark compared to the features present on consumer Windows offerings. That said, a good understanding of how Windows works will be helpful to have; this is covered in the Windows Fundamentals sequence.

## [Task 2] Windows Domains

In smaller networks, it's easy to set up, manage, and fix computers - you can probably just physically walk to the computer and do what you need to do. However, when a business suddenly grows and is working with hundreds of computers, this becomes infeasible very quickly. This is where the Windows domain comes in.

A Windows domain is a group of users and computers that are under the administration of a given business. The goal in using a domain is to centralize the administration of common components of a Windows computer network. Active Directory is teh repository that contains all of these components, and the server that manages the AD services is known as the Domain Controller (DC).

Windows domains come with a few advantages, some of which are going to be important for cybersecurity folks:
1. Centralized identity management: All users across the networks can be set up in AD easily.
2. Managing security policies: Security policies may be configured and applied across the network as needed.

There's a decent chance that you have already interacted with a Windows domain - they can be frequently found in schools, universities, and the workplace. Universities, in particular, use AD so you can log into any of the computers on campus with your credentials. The authentication process always happens through Active Directory, so you don't have to store them in any way on a Windows machine. Additionally, universities may configure domains such that certain features are unavailable to you. For instance, you're probably not going to be allowed access to the Control Panel, and you probably won't have administrative privileges.

The virtual machine in this room is a domain controller; we'll be doing some basic configuration on it. It should pop open in Split Screen View.

**[Task 2, Question 1] In a Windows domain, credentials are stored in a centralized repository called...** - Active Domain

**[Task 2, Question 2] The server in charge of running the Active Directory services is called...** - Domain Controller

## [Task 3] Active Directory

The Active Directory Domain Service (AD DS) is a catalogue that holds information about all objects that exxist on the network. An object can be any number of things on the network, including:
- Users: These are the most common object types, also known as "security principals." They can be authenticated by the domain and assigned privileges over resources (e.g. files,p irnters). Simply put, users can do stuff with resources.
  - People: Users that represent actual people in an organization, e.g. employees
  - Services: "Users" that represent services ran on machines; they should only have the permissions needed to run the service.
- Machines: For every computer that joins the AD domain, a machine object is created. They are considered security principals and get accounts like other users, albeit with limited rights.
  - Machine account privileges: These are local administrators on an assigned computer; if you know the password, you can log in. Folks aren't expected to access these accounts normally.
  - Machine account passwords are typically rotated and consist of 120 random characters.
  - Identifying machine accounts: They assume the name of the computer and are followed by a dollar sign.
- Security Groups: These are like user groups in Windows. Users may be added to them, and they will inherit the permissions given to the group at large. Security groups are security principals and have privileges over resources on the network.
  - Groups may contain users, machines, and if you want to get a little extra funky, groups themselves.

Some groups have been created by default in an Active Directory installation; this is an incomplete list:
- Domain Admins: Administrative permissions over the entire domain; can administer any computer on the domain, including DCs
- Server Operators: Administers of Domain Controllers; cannot change administrative group memberships
- Backup Operators: Can access any file, ignoring permissions; used to perform data backups
- Account Operators: Can create or modify other accounts in the domain
- Domain Users: Existing user accounts on the domain
- Domain Computers: Existing computers on the domain
- Domain Controllers: Existing DCs on the domain

If we want to configure users, groups, and machines in AD, we need to log in to the DC and run "Active Directory Users and Computers" from the Start Menu.

This gives you a hierarchy of users, computers, and groups that exist within the domain. These are organized in Organizational Units (OUs). OUs are container objects that we can use to classify users and machines, and they're generally used to define sets of users with similar policing requirements. A user can only be in one OU at a time.

The machine contains an OU called THM, with four child OUs - IT, Management, Marketing, and Sales. The OUs will typically mimic the business structure; this can help with deploying baseline policies that paply to entire departments. That said, you can always define OUs arbitrarily; you can right-click an OU to access options that let you create a child OU.

Opening OUs will let you see the users that are assigned to them, and you can create, delete, and modify users as needed. You can also reset passwords from here, which is helpful for helpdesk work.

Windows will automatically create some OUs:
- Builtin: Default groups available to any Windows host
- Computers: Any machine that joins the network is put here by default
- Domain Controllers: The default OU that contains the network's DCs
- Users: Default users and groups that apply to a domain-wide context
- Managed Service Accounts: Holds accounts used by services in the Domain

So when would we use an OU over a group, and vice versa?
- OUs: Useful for applying policies to computers and users, including specific configurations that pertain to sets of users depending on their role.
- Security Groups: Grant permissions over resources, e.g. you'd set these up if you want users to access a certain folder or network printers. Unlike OUs, a user can be in many security groups.

**[Task 3, Question 1] Which group normally administrates all computers and resources in a domain?** - Domain Admins

**[Task 3, Question 2] What would be the name of the machine account associated with a machine named TOM-PC?** - `TOM-PC$`

**[Task 3, Question 3] Suppose our company creates a new department for Quality Assurance. What type of containers should we use to group all Quality Assurance users so that policies can be applied consistently to them?** - Organizational Units

## [Task 4] Managing Users in AD

As a domain administrator, you may be tasked with checking existing AD OUs and Users. You may be given an organizational chart to this end and asked to make adjustments so that the AD matches it.

You might need to delete extra OUs and users to match the chart. Trying to right-click the OU and deleting it from there may give you a permissions error. This is because OUs are protected against accidental deletion. To properly delete the OU, you will need to go and enable View -> Advanced Features. This shows aditional containers and you will be allowed to disable the accidental deletion protection feature. Simply right-click the OU and go to Properties. In the Options tab, there will be a checkbox where you can disable the protection.

If you delete the OU now, you will be asked if you really want to delete the OU, and then you can delete it. Note that this deletes users, groups, and OUs that are under it.

You can also perform delegation - that is, you can give specific users certain control over some OUs. They can be given permissions to perform advanced tasks on certain OUs without needing an administrator's help. This can be used in many ways; for instance, you may need to grant IT Support permissions to reset users' passwords. To delegate control over an OU, right-click it and choose Delegate Control. Then you'll be prompted to add users/groups. Click the Add button.

A nifty feature in AD is that, whenever you're asked to provide a user name (or, more generally, an object name), you can enter a portion of the name and then click the Check Names button. Windows will autocomplete the user's name for you in the correct format.

Once you've added the users, you can click OK, and then select tasks to delegate. Windows provides a list of common tasks to delegate (e.g., resetting user paswords, managing groups, managing accounts, etc.), or you can provide your own custom task to delegate to the users/groups.

After clicking Next in a few dialog prompts, you'll have successfully delegated the task. In this room, we'll want to delegate the "Reset user passwords and force password change at next logon" task to Phillip for the Sales OU. From there, we can reset Sophie's password, which is required for the next question.

Phillip won't be able to change anything via the Active Directory Users and Groups utility, so he can instaead run a command in PowerShell to do this: `Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose`. This command will allow us to set a new password for Sophie's account.

In practice, you should force Sophie to change her password whenever she logs on again. The command `Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose` will do just fine for this purpose.

Let's give all this a shot. We'll open the Active Directory Users and Computers tool in the provided VM. We've gone ahead and made adjustments to the provided OUs under THM as per the organizational chart (see the room for more info), and now we can right-click Sales and begin the delegation process.

![image](https://github.com/user-attachments/assets/2d1de616-13bf-473c-a4ac-1a64e9e67384)

At the Users or Groups screen, we may add Phillip to the list of users that we want to delegate control to. When we click Add, we see the "Select Users, Computers, or Groups" dialog prompt. We can type in "Phillip" into the textbox, and then click Check Names to get the full name as recognized by Windows:

![image](https://github.com/user-attachments/assets/f81fe53c-2496-42ef-8f30-7b9d8b03c088)

Then we can hit OK, Next, and then check the "Reset user passwords..." task to delegate to Phillip.

![image](https://github.com/user-attachments/assets/8250b67d-3974-4aea-8ec1-28a8e1b24eeb)

The last screen summarizes the results of the delegation process. From here, you can click Finish, and we can begin resetting Sophie's password. To log in to Phillip's account, we'll need to use the username `phillip` and the password `Claire2008`. To do so, I will connect to the account over RDP from the AttackBox.

To do so, we open the AttackBox and we open a terminal. The Remote Desktop application available in the AttackBox is `xfreerdp`, and the syntax is generally as follows: `xfreerdp /u:(USERNAME) /p:(PASSWORD) /v:(MACHINE_IP)`.

Since we're connecting to a Windows domain, we must specify the domain that we are accessing. So, in this case, our command will be `xfreerdp /u:THM\\phillip /p:Claire2008 /v:(MACHINE IP)`, as shown below:

![image](https://github.com/user-attachments/assets/ebc74461-e45a-4ef3-8b4f-837962436bef)

(Note that we had to write it as `THM\\phillip` - this is needed whenever you're connecting to a machine like this over the Terminal.)

Now that we're inside the machine, we can run one of the PowerShell commands. We'll run the first PowerShell command for this exercise, if only because it'll be easier for us to remember a password that we set. Go to the Start Menu and run the Windows PowerShell app, then input the command:

![image](https://github.com/user-attachments/assets/f03b1945-2b8e-4bd9-a001-35662b8aafd8)

Note that when you input the new password, you must adhere to the password complexity requirements. A password with uppercase and lowercase characters, as well as numerals and punctuation, will do just fine. In this case, I used `Test123!`.

Once the password is set, we can exit the FreeRDP session. We may either close the window directly, or we may go to the Start Menu, click the Power button, and then click Disconnect. Then we can start a new xfreerdp session under Sophie's account: `xfreerdp /u:THM\\sophie /p:(WHATEVER_PASSWORD_YOU_SET) /v:(MACHINE_IP)`. Upon doing so, we can finally see the flag on Sophie's desktop.

![image](https://github.com/user-attachments/assets/b56b18b8-e05a-443b-88e7-601ed5187d29)

**[Task 4, Question 1] What was the flag found on Sophie's desktop?** - `THM{thanks_for_contacting_support}`

We'll need to remotely connect to the machine again, so you should keep the AttackBox open if you can!

**[Task 4, Question 2] The process of granting privileges to a user over some OU or other AD object is called...** - Delegation

## [Task 5] Managing Computers in AD

All machines that join a domain, except for DCs, are put in the "Computers" container. In the attached VM, we see that we already have some computers present. Some servers, laptops, and PCs belonging to users in the network will be listed. We will probably want to put these into different containers, so we can apply different policies to different groups of machines. Servers may need different policies set when compared to regular employee-owned computers, for instance.

Devices will generally be split into at least three categories:
1. Workstations: The most common devices in an AD domain, with each user likely needing to log into one to do work. You should never have a privileged user signed into them.
2. Servers: The second most common device type. Generally used to provide services to users and other servers.
3. Domain Controllers: Will be the least common type of these three by necessity. These allow you to manage the AD domain, and are considered the most sensitive devices in the entire network, as they contain hashed passwords for all user accounts.

We may as well tidy up the Computers in the attached AD. We can go ahead and create two separate OUs for Workstations and Servers - Domain Controllers is already an OU created by Windows. Recall that to create a new OU, you can right-click where you want to place them (in this case, we'll simply create them under `thm.local`), and then click New -> Organizational Unit:

![image](https://github.com/user-attachments/assets/47959810-a283-4f34-9d83-2ac50fe41cc2)

Now we can move all the computers and servers to their respective OUs. This is a simple drag-and-drop operation - the PCs and LPTs can be dragged directly into the Workstations OU on the left, and the SRVs can be dragged into the Servers OU on the left. Note that AD will give you a prompt alerting you that this can affect how your overall domain works:

![image](https://github.com/user-attachments/assets/f29ea17e-143d-4cff-ae3a-9ee4a4686840)

For our purposes, this is fine - we can just click Yes. However, in a real environment, you should make sure that you absolutely want to do this before actually moving things around.

Here's what our Workstations OU looks like right now:

![image](https://github.com/user-attachments/assets/0f74873f-d631-47da-88dd-ab9e59734d13)

**[Task 5, Question 1] After organizing the available computers, how many ended up in the Workstations OU?** - 7

**[Task 5, Question 2] Is it recommendable to create separate OUs for Servers and Workstations? (yay/nay)** - yay

## [Task 6] Group Policies

The whole point in organizing things into OUs is to apply different policies to different units of users and machines. This allows us to tailor configurations and security baselines to users depending on their department. Policies in an AD environment are managed via Group Policy Objects, or GPOs. These are a collection of settings that can be applied to OUs. To configure GPOs, you will need to go to the Group Policy Management tool.

The Group Policy Management tool, at first, shows you the complete OU hierarchy as defined before. If you want to configure Group Policies, you will need to create a GPO under Group Policy Objects, and then link it to the OU where you want the policies to apply.

In the VM, three GPOs have been created: the Default Domain Policy and the RDP Policy GPOs are applied to the `thm.local` domain as a whole, whereas the Default Domain Controllers Policy is applied to the Domain Controllers OU only. Any GPO will apply to the linked OU as well as any sub-OUs underneath it. This means that, currently, all of the OUs we've discussed will have the Default Domain and RDP Policies applied to them.

Clicking a GPO will give you various information, including:
- Scope: This is the first tab you'll see, and it shows you where the GPO is linked in the AD - e.g., whether it's linked to the domain as a whole, or to specific sites or OUs, etc.
- Security Filtering: You can apply GPOs only to specific users and computers under an OU. By default, the GPOs will apply to the Authenticated Users group, which encompasses all PCs and users.
- Settings: This tab gives you the contents of the GPO and tels you what specific configurations are applied. GPOs can contain configurations that apply to computers only or to users only. The Default Domain Policy GPO only contains Computer Confiugration. You can see more information about configurations by clicking the Show button at the right.

In this VM, the Default Domain Policy GPO contains basic configurations that will probably apply to most domains. This includes password and account lockout policies. Changes to this GPO would affect all computers.

To edit a policy, simply right-click the GPO in the left pane, and then click Edit. You can navigate to and edit all sorts of configurations here. Some policies are pretty straightforward, though if you're not sure of what a policy does, you can double-click it and go to the Explain tab in the dialog box that opens up for further information.

GPOs get distributed to the network via the `SYSVOL` network share. This guy is stored in the DC. All users typically have access to this share over the network; otherwise they wouldn't be able to sync up their GPOs. On a DC, the `SYSVOL` directory can be specifically found in `C:\Windows\SYSVOL\sysvol`.

If a change is made to a GPO, it'll take some time for the computers to catch on - usually up to two hours. If you want to force a computer to update its GPOs immediately, you can run the command `gpupdate /force` from a computer's PowerShell.

In this room, we can configure a few policies for our domain:
1. Restrict Access to Control Panel - we would rather not want regular users to be able to mess with the Control Panel; the only folks who should be able to do so would ideally be IT Support staff. This can be found in User Configuration -> Policies -> Administrative Templates... -> Control Panel -> Prohibit access to Control Panel and PC settings.
2. Auto Lock Screen: We'd like the screens to lock on all computers after five minutes of inactivity. This setting will be in Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Local Policies -> Security Options -> Interactive logon: Machine inactivity limit.

For both, we can create a new GPO by right-clicking the Group Policy Objects folder on the left and clicking "New." We simply fill out the name for it and we're set. Again, to modify a GPO, right-click it and click "Edit."

For #1, we can double-click the "Prohibit access to Control Panel and PC settings" and click the "Enabled" button in the top-left and then Apply it. Once we do this, we can close the Group Policy Management Editor, and then we can drag the "Restrict Access..." GPO out to the THM -> Management, Marketing, and Sales OUs. This applies the GPO to these guys only.

For #2, we double click the "Interactive logon: Machine inactivity limit" policy, check the "Define this policy setting" checkbox, and then input 300 seconds (for five minutes). Then, since we want it to apply to all computers, we can simply drag it out to the `thm.local` domain.

We can verify that these were set up correctly by RDPing into one of the accounts in the affected OUs. For instance, we can log in via the username `THM\\Mark` and the password `M4rk3t1ng.21`. As a heads up, if you don't want to retype the entire `xfreerdp` command, you can press the Up arrow key on your keyboard. If you've kept the Terminal open, it'll remember that you typed such a command before, and you can go back and edit it with the correct information.

Either way, attempting to open Control Panel on Mark's machine gives us this:

![image](https://github.com/user-attachments/assets/ca7acad2-b181-4f87-84a6-5c164622958c)

This means we've done our job!

**[Task 6, Question 1] What is the name of the network share used to distribute GPOs to domain machines?** - SYSVOL

**[Task 6, Question 2] Can a GPO be used to apply settings to users and computers?** - yay

## [Task 7] Authentication Methods

We have done all we needed to do on the VM, so we may close it and the AttackBox now. We'll wrap up this room by focusing on a few additional basic concepts regarding Active Directory, the first of which being authentication.

All credentials in a Windows Domain are stored in the DC. If you're trying to authenticate to a service with domain credentials, the service is going to ask the DC to verify if they're correct. There are two protocols that are used for this authentication process:
- Kerberos, which is used by recent versions of Windows and is the default.
- NetNTLM, which is a legacy protocol used by default in versions of Windows up to Windows 2000. It is obsolete and weaker compared to Kerberos.

Both protocols will often be enabled in a Windows domain, with NTLM enabled as a backwards compatibility measure.

Under Kerberos, users who log into a service are assigned tickets - proof of being authenticated. Users present tickets to a service to demonstrate they have been authenticated into the network and are allowed to use it. Kerberos authentication takes place in several steps:
1. Initial Sending and Response:
   1. The user sends their username and a timestamp, all encrypted with a key derived from their password, to the Key Distribution Center (KDC). The KDC is set up on the DC and is in charge of creating Kerberos tickets.
   2. The KDC creates and sends back a Ticket-Granting Ticket (TGT). This lets the user request additional tickets for specific services without having to pass their credentials around every time. The user also receives a Session Key to generate requests.
   3. The TGT is encrypted with the `krbtgt` account's password hash; the user should not normally be able to access its contents. The encrypted TGT contains a copy of the Session Key as part of its contents, so the KDC can simply recover the Session Key by decrypting the TGT as needed.
2. Requesting Access to a Service:
   1. The user uses their TGT to ask the KDC for a Ticket-Granting Service (TGS). The TGS is a ticket that allows connection only to the specific service they were created for. To this end, the user sends their username and a timestamp encrypted with their Session Key.
   2. In additon, the user sends the TGT and a Service Principal Name (SPN), which indicates the service/server name they want to acecss.
   3. The KDC sends back a TGS along with a Service Session Key, which is used to authenticate to the service that the user wants access to. The TGS is encrypted with a key derived from the Service Owner Hash, which is the user/machine account that the service runs under. The TGS contains a copy of the Service Session Key so the Owner may access it as needed.
3. Once all of this is done, the TGS is sent to the desired service to authenticate and establish a connection. The configured service account's password hash is used to decrypt the TGS and validate the Service Session Key.

Under NetNTLM, a challenge-response mechanism is employed for authentication:
1. The client sends over an authentication request to the server they want to get into.
2. The server generates a random number and sends it as a challenge to the client.
3. The client combines their NTLM password hash with the challenge and some other data to genreate a response to the challenge. This response is sent back to the server for verification.
4. The server forwards the challenge and response to the DC for verification.
5. The DC recalculates the response and compares it to the client's response. If they match, the client is authenticated. If not, the client is denied access. The result is sent back to the server...
6. ...and the server sends the result back to the client.

You'll notice that the password or hash is NEVER sent through the network - this is just generally good security practice.

The NTLM process applies when you're using a domain account. If a local account is used, the server can verify the response to the challenge itself - the password hash is stored locally in this case.

**[Task 7, Question 1] Will a current version of Windows use NetNTLM as the preferred authentication protocol by default? (yay/nay)** - nay

**[Task 7, Question 2] When referring to Kerberos, what type of ticket allows us to request further tickets known as TGS?** - Ticket granting ticket

**[Task 7, Question 3] When using NetNTLM, is a user's password transmitted over the network at any point? (yay/nay)** - nay

## [Task 8] Trees, Forests, Trusts

One last thing: What if we need to have multiple domains? This might be the case if the company balloons in size, so much so that one domain won't cut it. There's a few ways to pull this off:
- If you want to integrate multiple domains under the same namespace (e.g., `thm.local`), you can use trees. This would be used for subdomains (e.g., domains in different countries), and each domain in the tree will get its own DCs and policies. They can configure these without interfering with the other trees. With a tree, you will be able to make use of the Enterprise Admins default group, which grants a user administrative permissions over all domains.
- If you want to integrate different namespaces altogether in the same network, then you would want to use a forest. Forests might come into play if you've got multiple companies that you'd like to manage. Each company might have its own tree, with their own management scheme.

Using forests and trees can be helpful in various circumstances, but you may run into an issue where one user in one tree needs to do something in another tree. In this case, you'll want to join domains in forests/trees together with trust relationships. This allows you to authorize a user from one domain to access resourecs in another domain. There are multiple kinds of relationships:
- One-way trust: If domain A trusts domain B, then a user in domain B may access stuff in domain A. The "trust direction" (e.g., A trusts B) is opposite the "access direction" (e.g., B can access stuff in A)
- Two-way trust: Both domains mutually trust each other, and so users can be authorized to do stuff across domains. Joining domains under a tree or forest will form a two-way trust by default.l

You can configure trusts further such that, while users can be authorized to access different domains, you can choose what they're authorized to do in different domains. Creating a trust relationship does not necessarily mean you have to grant access to all resources in the involved domains.

**[Task 8, Question 1] What is a group of Windows domains that share the same namespace called?** - tree

**[Task 8, Question 2] What should be configured between two domains for a user in Domain A to access a resource in Domain B?** - A trust relationship

## [Task 9] Conclusion

This room has served as a very basic introduction to the Active Directory environment and Windows domains. There's much more to explore when it comes to actually using AD in practice, especially in the context of security. TryHackMe has rooms pertaining to hardening Active Directory (if you're doing defensive security) and even attacking Active Directory (if you're doing offensive security).
