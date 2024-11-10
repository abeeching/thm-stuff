# KAPE

## [Task 1] Learning Objectives

Back in the Windows Forensics sequence of rooms, we learned about different artifacts that are used to store information about a user's activity, where these artifacts are located, and how they can be accessed and interpreted. Back in those rooms, we did all this manually; we may not always have the time and luxury to manually analyze a machine for forensic artifacts, though. Here we'll be using KAPE to help automate the process.

## [Task 2] Introduction to KAPE

KAPE - the Kroll Artifact Parser and Extractor - can parse and extract Windows forensic artifacts, which can help reduce the time needed to respond to an incident by providing forensic artifacts earlier than the imaging proecss can complete. It has two main purposes:
1. Collecting files
2. Processing files according to provided options

KAPE accomplishes these two goals by using targets and modules; the targets are the artifacts that are to be collected, and modules are programs that process the artifacts and extract information from them.

KAPE collects targets, adds them to a queue, and then performs two passes: it first attempts to copy the files that it can (i.e., those that the OS has not locked), and then the rest of the files are passed to a secondary queue. In this queue, the files are processed with raw disk reads, which effectively bypasses OS locks and copies the files. The copied files get their original timestamps and metadata and are stored in a similar directory structure.

Once data is collected, KAPE processes it with modules. These may be independent binaries that run on the collected data and process them to extract information. KAPE may, for instance, collect a copy of the Prefetch file to the target destination during the target collection. If we were to run the Prefetch Parser (PECmd) module, we would get the Prefetch information saved to a CSV file.

The broad process for using KAPE is as follows:
1. Choose source: This may be a live system, a mounted image, or something from F-Response, a remote forensics software utility.
2. Set target options in KAPE.
3. Choose destination: Select where to copy files from the source.
4. Set module options in KAPE.
5. Examine module output: Programs are run against destination files, and data is extracted.

KAPE does not need to be installed to work; it can be used from network locations and USB drives.

The VM attached in this task will start in Split Screen View; this will be used throughout the other tasks. On the Desktop, there is a KAPE folder with two binaries:
- `kape.exe`, which is the command-line version of the tool, and
- `gkape.exe`, which is the GUI version.

Other files include `Get-KAPEUpdate.ps1`, which is a PowerShell script that checks for and downloads updates, and `ChangeLog.txt` and `Documentation` which contain what you think they would. The `Targets` and `Modules` folders will be discussed later.

**[Task 2, Question 1] From amongst `kape.exe` and `gkape.exe`, which binary is used to run GUI version of KAPE?** - `gkape.exe`

## [Task 3] Target Options

Targets are the artifacts that need to be collected from a system or image and copied to our provided destination. Windows Prefetch would be an example of such an artifact - this was used to locate evidence of execution. Another example would be Registry hives. In KAPE, we copy targets from one place to another.

The `Targets` directory in KAPE includes sub-directories like Antivirus, Apsp, Browsers, Logs, Windows, and more. The Compound Targets in this folder will be discussed later. Target guides and templates are included to help with creating custom targets.

We can check these sub-directories for information on the various targets; target definition files have an extension of `.tkape`. These files contain information about the artifact we want to collect, including its path, category, and file masks (e.g., `*.pf` for Prefetch flies, both in `C:\Windows\prefetch` and `C:\Windows.old\prefetch`. The latter contains files from an older Windows installation).

Compound Targets are targets comprised of multiple other targets. Since KAPE is often used for triage collection and analysis, we'd like to avoid having to collect every single artifact individually. Thus, we use Compound Targets to specify everything to collect in a single command. Compound Targets include !BasicCollection, !SANS_triage, and KAPEtriage. All compound targets are stored in `KAPE\Targets\Compound`, and they all have their own `.tkape` files.

The `!Disabled` directory contains Targets that you want to keep in KAPE, but don't want to actually appear in the active Targets list. The `!Local` directory contains Targets that you don't want to sync with the KAPE GitHub repository; these may be things that are specific to your environment. Anything not present in the GitHub repository when we update KAPE will be moved to `!Local`.

**[Task 3, Question 1] What is the file extension for KAPE `Targets`?** - `.tkape`

**[Task 3, Question 2] WHat type of Target will we use if we want to collect multiple artifacts with a single command?** - Compound Targets

## [Task 4] Module Options

Modules run specific tools against the provided set of files. They are used to run some command on the relevant file(s) and store the resulting output somewhere. The output is generally in the form of CSV or TXT files. The `Modules` directory is structured similarly to the `Targets` directory, including the `!Disabled` and `!Local` sub-directories. Most modules are grouped in different directories. Opening these sub-directories will lead you to files with a `.mkape` extension. These files are used to specify information about a module, primarily what command needs to be run, what the parameters are, the output format, and the filename to export to.

The `bin` directory in KAPE contains executables that we'd need to run on the system but are not natively present on most systems. KAPE will run executables either from the `bin` directory or the complete path. The EZTools suite is likely to be found here, since those tools aren't normally present on Windows.

**[Task 4, Question 1] What is the file extension of the `Modules` files?** - `.mkape`

**[Task 4, Question 2] What is the name of the directory where binary files are stored, which may not be present on a typical system, but are required for a particular KAPE module?** - `bin`

## [Task 5] KAPE GUI

Opening KAPE, you'll be presented with a lot of different options. In fact, you may need to maximize the Split Screen View in order for everything to display correctly.

Most options are disabled at the moment. To collect targets, we need to check the "Use Target Options" checkbox at the top-left. This enables the options in the left half of the GUI. If we want to perform forensics on the same machine on which KAPE is running, we can provide `C:\` as the Target Source. We can choose anything to be the Target Destination; all triage files are copied to this destination.

The `Flush` checkbox deletes all contents of the Target Destination, so be careful with this option. The `Add %d` option will append date information to the directory name where the collected data is saved, and `Add %m` appends machine information to the Target Destination directory as well. We can select our desired Target from a list, and the search bar helps us search for the names of desired targets quickly.

We can also choose to process Volume Shadow Copies by checking "Process VSCs". We can check the "Transfer" checkbox if we want to transfer the collected artifacts via an SFTP server or an S3 bucket. For transfer, the files must be enclosed in a container (Zip, VHD, VHDX). We can provide exclusions based on SHA-1 hashes, and KAPE will not copy the excluded files. When enclosing in a container, we need to give a base name that will be used for all created files. If we're not transferring files, this is not required.

The Current Command Line tab shows you the command line options being added or removed while configuring the UI. This changes as you select and deselect options. For instance, if we check "Flush", then we'll see the `--tflush` flag added to our command.

If we check the Use Module Options checkbox, the right half of the KAPE window is enabled. When using both options, the Module Source is not required. The selected modules will use the target destination as the source. The remaining options here are similar to those for the targets.

At the bottom-right of the screen, you can check the "Disable flush warnings" checkbox to skip any warnings about the flush tags. When you're all set up, you can click the Execute! button to start using KAPE. A command prompt opens and shows us the logs as KAPE does its thing, which can take a few minutes. Once complete, it'll show the total execution time, and we can press any key to close the command prompt.

Notice that this is just `kape.exe` running - we basically use the GUI version to set up the command line argument for `kape.exe`. Once KAPE finishes processing, you can check out the files created by KAPE. The files get processed into different categories (different sub-folders in the module destination).

The first two questions of this task refer to a screenshot from the room.

**[Task 5, Question 1] In the second-to-last screenshot above, what target have we selected for collection?** - KapeTriage

**[Task 5, Question 2] In the second-to-last screenshot above, what module have we selected for processing?** - !EZParser

**[Task 5, Question 3] What option has to be checked to append date and time information to triage folder name?** - `%d`

**[Task 5, Question 4] What option has to be checked to add machine information to the triage folder name?** - `%m`

## [Task 6] KAPE CLI

It's good to know how to use KAPE on the command line, should the need arise. To use it, you should open an elevated PowerShell (i.e., run PowerShell as an administrator), go to where the KAPE binary is located, and run `kape.exe`. On its own, this command produces a help page, giving you a list of arguments that you can provide to run KAPE.

When collecting targets, the switches `--tsource`, `--target`, and `--tdest` are all required. When processing with modules, you need `--module` and `--mdest`. Other switches may be needed to accomplish different tasks in the collection process.

Say we want to collect triage data from the machine with `KapeTriage` and process it with `!EZParser`. Then our command would be:
- `kape.exe`, to get us started
- `--tsource C:`, so that KAPE knows what to collect from
- `--target KapeTriage --tdest C:\Users\thm-4n6\Desktop\target`, so KAPE knows what it's collecting and where to put it.
- If we'd like to, we can add `--tflush` to flush out the target destination.
- If we wanted to use a module source, we'd have to specify it with `--msource`. However, if we have a target destination set, this isn't required.
- Thus, all we need now is a module destination, e.g. `--mdest C:\Users\thm-4n6\Desktop\module`
- And to specify the actual module we'd like to use, we need to add `--module !EZparser`.

Our full command will be `kape.exe --tsource C: --target KapeTriage --tdest C:\Users\thm-4n6\Desktop\Target --mdest C:\Users\thm-4n6\Desktop\module --module !EZParser`. We'll need to run this in an elevated shell for KAPE to do its thing. We can modify switches as needed.

KAPE may also be run in Batch Mode, whih requires a list of commands in a file named `_kape.cli`. This is a file that we place in the directory containing the KAPE binary. When `kape.exe` is executed as an administrator, it will execute whatever commands are in this file if it is in the directory. This can be useful if you need someone to run KAPE for you; all the person needs is the `_kape.cli` file in the KAPE directory, and they can run all the commands inside it by right-clicking `kape.exe` and selecting Run As Administrator.

You _can_ run `kape.exe` in a command line to answer these questions, though the help page is reproduced at the beginning of this task for convenience. All of the answers can be found there.

**[Task 6, Question 1] Run the command `kape.exe` in an elevated shell. Take a look at the different switches and variables. What variable adds the collection timestamp to the target destination?** - `%d`

**[Task 6, Question 2] What variable adds the machine information to the target destination?** - `%m`

**[Task 6, Question 3] Which switch can be used to show debug information during processing?** - `debug`

**[Task 6, Question 4] Which switch is used to list all targets available?** - `tlist`

**[Task 6, Quetsion 5] Which flag, when used with batch mode, will delete the `_kape.cli`, targets, and modules files after the execution is complete?** - `cu`

## [Task 7] Hands-On Challenge

Now let's make use of it. Say we have an organization X with an Acceptable Use Policy for their portable devices (e.g., laptops). This forbids users from connecting removable or network devices, installing software from unknown locations, and connecting to unknown networks. One of the users may have violated this policy. Let's figure out if the user violated the AUP on their device. The user's machine is attached to this room as a VM; all we need to do is navigate to the KAPE directory and run it with the desired target and moduel options.

If we need to read CSV files, we can use EZViewer, placed in the EZTools folder on the Desktop.

For running KAPE, we'll try the KapeTriage target and the !EZParser module and see where that gets us:

![image](https://github.com/user-attachments/assets/8dd0ee12-acd6-4b6d-87bd-d1eada62b594)

When done, we'll have a lot of output in whatever we have specified as our module destination. If we check the Registry files, particularly the USB file, we see this:

![image](https://github.com/user-attachments/assets/ff642cc5-732b-4d34-b491-f68dc8c958a9)

There are two USB Mass Storage devices in this CSV file.

**[Task 7, Question 1] Two USB mass storage devices were attached to this virtual machine. One had a serial number 0123456789ABCDE. What is the serial number of the other USB device?** - `1C6F654E59A3B0C179D366AE`

We have a few places to look to answer this next question. One place to look would be `NTUSER.DAT`, and specifically the UserAssist entries. In it, we see several entries for programs mentioned in the question:

![image](https://github.com/user-attachments/assets/8c1555d8-0e2d-4a6b-9dab-4e334f227d8a)

So, the network drive location must be `Z:\Setups`.

**[Task 7, Question 2] 7zip, Google Chrome, and Mozilla Firefox were installed from a network drive location on the virtual machine. What was the drive letter and path of the directory from where these software were installed?** - `Z:\Setups`

Incidentally, the UserAssist entries contain the date and time in which the program was last executed:

![image](https://github.com/user-attachments/assets/845112e3-c439-4420-b1cd-f21cd486348d)

**[Task 7, Question 3] What is the execution date and time of `CHROMESETUP.EXE` in MM/DD/YYYY HH:MM?** - 11/25/2021 03:33

The WordWheelQuery contains information on search queries performed on the system. In this case, there is only one search entry:

![image](https://github.com/user-attachments/assets/3a89b63c-69a5-441f-92b6-785f65d5df7a)

**[Task 7, Question 4] What search query was run on the system?** - `RunWallpaperSetup.cmd`

The `KnownNetworks` output file contains all of the networks that the user connected to, including when they first connected to the network. We just so happen to have an entry for Network 3 right here.

![image](https://github.com/user-attachments/assets/40292cd1-217f-4d19-bc16-5e073e4c8d20)

**[Task 7, Question 5] When was the network named Network 3 first connected to?** - 11/30/2021 15:44

Moving away from the Registry output for a minute, we can take a look at where the KAPE executable came from. Apparently it was copied from another location. It's possible that it will show up as an Automatic Destination (i.e., jump lists). We can check FileFolderAccess for the AutomaticDestinations output, which happens to contain exactly what we're looking for:

![image](https://github.com/user-attachments/assets/93f3a63d-d132-4780-aa45-5f00d7817396)

**[Task 7, Question 6] KAPE was copied from a removable device. Can you find out what was the drive letter of the drive where KAPE was copied from?** - `E:`
