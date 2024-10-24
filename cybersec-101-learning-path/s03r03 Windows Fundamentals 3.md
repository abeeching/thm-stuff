# Windows Fundamentals 3

## [Task 1] Introduction

Now we have a pretty decent grasp of all the tools that Windows makes available to us. In this final room in the Windows Fundamentals sequence, we'll take a look at the features that are most relevant to security-minded folks... the security features! The VM in this room can be launched in Split Screen View.

## [Task 2] Windows Updates

One of the key components that can be used to protect Windows is Windows Update - this is a service provided by Microsoft to provide security and feature updates for Windows and other products, including Microsoft Defender and Office. Typically, updates are released on the second Tuesday of each month - hence the name "Patch Tuesday." Microsoft will go out of this cadence if the need arises, e.g. if there's a critical security vulnerability that needs to be patched ASAP, they'll put out a patch as soon as they can. The Microsoft Security Response Center has information on various vulnerabilities and updates that can address them.

To get to Windows Update:
- Go to Settings -> Update & Security
- Open the Run dialog box or Command Prompt -> Run `control /name Microsoft.WindowsUpdate`

The VM has a few things of note with regards to Windows Update:
1. Windows Update settings are "managed" by an organization - this is typically not the case for home users. This effectively means other folks are responsible for keeping the machine up-to-date.
2. There won't be any available updates in the virtual machine, because an Internet connection is required to get them.

Users have historically put off updating Windows for various reasons, including the fact that a reboot is typically required. (I've also had some friends of mine run into issues with Windows updates, as in, issues that bricked their computers entirely. I'd wager they're a little skeptical of updating their machines nowadays.) However, with Windows 10 and later, you are no longer allowed to put off updates; Windows will automatically update after postponing it enough times. This is to ensure that the device is kept safe and secure against the latest threats.

Windows has the decency to prompt you when a restart will be needed - you'll be given options on when to restart. You can choose to restart immediately, or you can schedule the restart for later.

You can see installed updates by going to Settings -> Update & Security -> Windows Update and clicking "View update history." It displays updates in different categories; the VM has driver and definition updates installed. You can expand the categories to see what was actually installed and when:

![image](https://github.com/user-attachments/assets/f7c1d5ae-fa46-4360-ab5e-d048fdb0264f)

**[Task 2, Question 1] There were two definition updates installed in the attached VM. On what date were these updates installed?** - 5/3/2021

## [Task 3] Windows Security

Windows Security is a hub where you can access various options relating to, you guessed it, security. It can be accessed from Settings -> Update & Security -> Windows Security. From here, you can click on Open Windows Security. It's also a standalone app in Windows that you can look for and run. There are various areas of protection that Microsoft calls out:
- Virus & threat protection
- Firewall & network protection
- App & browser control
- Device security

These sections will be the focus of the next few tasks. It is worth noting the status icons provided by Windows for these various security areas; different colors are used to indicate if there are any actions that need to be taken:
- Green check marks indicate that the device is protected in a particular area, and that no further action needs to be taken.
- Yellow exclamation marks indicate that there is a recommendation that you can implement to improve system security.
- Red X marks indicate that something needs your immediate attention.

Note that Windows Security will look a tad different in the VM, since it is running on Windows Server 2019. Windows 10 provides additional areas of security:
- Device performance & health
- Family options (e.g., parental controls)

The questions in this and the next task refer to screenshots posted in the room - you should have a look at them. Incidentally, the same exact issue is present in the VM:

![image](https://github.com/user-attachments/assets/7b84ed44-aa23-4dfe-9461-2b6b85b89cbd)

**[Task 3, Question 1] In the above image, which area needs immediate attention?** - Virus & threat protection

## [Task 4] Virus & Threat Protection

There are two sections in this area of Windows Security: current threats and virus/threat protection settings. Here are the options you can choose in the Current Threats area.
- Current Threats:
  - Quick Scan: This option will check folders in the system where threats are commonly found.
  - Full Scan: This checks all files and running programs on the hard disk, and could take longer than an hour to complete.
  - Custom Scan: You can choose what files and locations you want to check.
 - Threat History: This is a link available from the Current Threats page.
   - Last Scan: This area gives statistics on the previously-run scan, including any threats found, how long the scan took, and how many files were scanned.
   - Quarantined Threats: This area lists threats that have been isolated and prevented from running on the device. They are periodically removed.
   - Allowed Threats: This lists items that have been identified as threats, but were allowed to run on the device anyway. You should only do this if you're sure of what you're doing.

In the Virus and Threat Protection Settings, the following options are available:
- Manage Settings:
  - Real-Time Protection: This feature locates and stops malware from installing or running in real time.
  - Cloud-Delivered Protection: This feature provides more protection quickly by using data from the cloud.
  - Automatic Sample Submission: This feature allows you to send sample files to Microsoft to help with improving threat detection/prevention.
  - Controlled Folder Access: This feature allows you to protect files, folders, and memory areas on the device from unauthorized changes.
  - Exclusions: Here you can list items that you don't want Windows Defender to scan. These could contain threats that can make your system vulnerable, so only configure this if you know what you're doing.
  - Notifications: Windows Defender can be configured to send notifications regarding the health and security of a device.
- Virus and Threat Protection Updates -> Check for Updates: This feature allows you to manually check for updates to Windows Defender definitions.
- Ransomware Protection -> Controlled Folder Access: This feature is required for ransomware protection to kick in. Incidentally, Controlled Folder Access requires Real-Time Protection to be enabled.

Note that Real-Time Protection is disabled in the VM due to potential performance issues - this is OK because the VM has no connection out to the Internet, and there are no threats in the VM proper. However, you should not do this on machines that you actually own and can reach out to the Internet. Either enable real-time protection on the machine, or get third-party antiviruses that will do the job for you. If you take the latter route, ensure it's up-to-date.

You can scan any file and folder on demand by right-clicking the item and clicking "Scan with Microsoft Defender."

The question below still refers to the screenshots and information provided about the attached VM. See the above screenshot here too - Windows Security tells you specifically what needs to be done.

**[Task 4, Question 1] Specifically, what is turned off that Windows is notifying you to turn on?** - Real-time protection

## [Task 5] Firewall & Network Protection

A firewall can be used to control how traffic flows through the network. If you go to Firewall & Network Protection, you'll see configurations for the different kinds of networks:
- Domain: If you're connecting into a domain (with a Windows domain controller, specifically), you'll be in this network.
- Private: If you're in private or home networks, chances are this will be enabled.
- Public: If you're in a public network (e.g., Wi-Fi hotspots, coffee shops, airports...), you'll have this enabled.

Clicking on the firewall profile will give you options to turn on/off the firewall and to block all incoming connections. Obviously, you should leave the firewall enabled unless you have a very good reason to shut it off.

From here, you can also choose what apps you would like to allow through the firewall - with some apps, you can choose to allow them through the firewall in private networks or in public networks (or both). Some of the applications in the list may provide further information if you click the Details button.

Advanced Settings can be used to set up more granular control over what apps can communicate over the firewall, and is geared towards advanced users (as one would expect).

**[Task 5, Question 1] If you were connected to airport Wi-Fi, what most likely will be the active firewall profile?** - Public network

## [Task 6] App & Browser Control

In the App & Browser Control section, you can change settings for Microsoft Defender SmartScreen, which is a utility used to protect against phishing sites, malware sites/apps, and downloading of potentially malicious files.
- The Check Apps and Files options allow you to configure SmartScreen to either block suspicious/malicious things entirely or to simply warn about them (or you could turn it off, which is not recommended, as you'd guess).
- Exploit Protection: These features are built into Windows 10/Server 2019 to help protect the device against attacks. You should leave these settings as the default.

## [Task 7] Device Security

This final section includes settings that pertain more to the security of the device's hardware components.
- Core Isolation -> Memory Integrity: This prevents attacks from inserting malicious code into high-security processes.
- Security Processor: This section provides information on the security features in your computer's hardware.

The Security Processor, in particular, will contain information about the Trusted Platform Module (TPM). TPM is technology that can provide hardware-based security functions, including carrying out cryptographic operations. It is meant to be tamper-resistant.

You will likely not need to change any of the settings here.

**[Task 7, Question 1] What is the TPM?** - Trusted Platform Module

## [Task 8] BitLocker

BitLocker Drive Encryption is a data protection system available to users who have the Pro version of Windows. It provides full-drive encryption, which can help mitigate data theft/exposure threats, particularly when lost or decommissioned computers are involved.

Note that BitLocker works best with TPM version 1.2 (or later) enabled. TPM is set up on newer computers by manufacturers, and helps ensure that the system hasn't been tampered with to begin with. Without TPM 1.2 enabled, you would need to supply a startup key file on a removable drive (e.g., USB) to make use of BitLocker. You _could_ use it with just a password, but this isn't as secure as the other options.

**[Task 8, Question 1] What must a user insert on computers that DO NOT have a TPM version 1.2 or later?** - USB startup key

## [Task 9] Volume Shadow Copy Service

The Volume Shadow Copy Service (VSS) coordinates the creation of shadow copies, which are snapshots/point-in-time copies of the data that is to be backed up. These are stored in the System Volume Information folder on each drive with protection enabled.

If VSS is enabled (that is, if System Protection is turned on), then the following tasks can be done from Advanced System Settings:
- Creating a restore point
- Performing system restore (i.e., restoring from a restore point)
- Configure restore settings
- Delete restore points

The folks who write malware are well aware of this functionality, and so oftentimes malware will be coded to seek these files out and delete them. This would stop you from, say, recovering from a ransomware attack. This is why folks will recommend keeping backups off-site - it's unfortunately quite easy to lose access to these shadow copies.

**[Task 9, Question 1] What is VSS?** - Volume Shadow Copy Service

## [Task 10] Conclusion

And that'll be it for the Windows Fundamentals sequence. We covered the features that can be used to maintain the security of a Windows 10 system, though there's plenty more to learn about that alone. The THM room specifically calls out the following features that are worth investigating:
- Antimalware Scan Interface (AMSI)
- Credential Guard
- Windows 10 Hello

Also worth investigating is the notion of "Living off the Land" - how adversaries use built-in Windows binaries to go undetected while performing malicious activities.
