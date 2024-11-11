# Velociraptor

## [Task 1] Introduction

Velociratpor is a tool developed by Rapid7 - the Metasploit folks - that offers an open-source endpoint monitoring, digital forensics, and cyber response platform. It was designed to help with artifact-hunting and endpoint-monitoring across many hosts. An understanding of the basics of Windows forensics and KAPE will be needed for this room.

## [Task 2] Deployment

Velociraptor can act as a server and a client and can run on Windows, Linux, and macOS. It also works with cloud file systems such as Amazon EFS and Google Filestore. It can be deployeda cross many client endpoints and runs very well. Our focus in this room will just be on how Velociraptor works; we won't be going over how to deploy it with a server-agent architecture in an environment. Instead, we'll briefly touch on how to start Velociraptor as a server and how to run it as an agent. We'll use the Windows Subsystem for Linux (WSL) to simulate Velociraptor running as a server in Linux, and then we'll run it as a client in Windows.

We'll start by launching the attached VM in this task. It'll open in Split Screen View, though we may want to run this in full-screen mode. Once all that is set up:
1. Open the Ubuntu terminal.
2. Run the command to "Start the Velociraptor Server (Ubuntu Terminal)" in `commands.txt`. In this case, it's `cd velociraptor` followed by `./velociraptor-v0.5.8-linux-amd64 --config server.config.yaml frontend -v`.
3. Follow any other instructions.

The version of Velociraptor running in this instance is 0.5.8. If we launch Google Chrome and go to the Velociraptor shortcut, we'll be warned about our connection not being private. This is OK; we're just connecting to `127.0.0.1`, and we can bypass this error by going to the advanced options. The credentials are `thmadmin:tryhackme`. This will bring us to the Welcome screen.

If we want to interact with and deploy Velociraptor directly to our lab, we may want to use Instant Velociraptor; this is a fully-functional Velociraptor system that is deployed only to the local machine. There is more information in the documentation on how to deploy Velociraptor, either in a server-client infrastructure or with Instant Velociraptor.

The documentation contains the following on Instant Velociraptor:

![image](https://github.com/user-attachments/assets/9b13f914-5379-4710-93cf-39e598471a21)

**[Task 2, Question 1] Using the documentation, how would you launch an Instant Velociraptor on Windows?** - `velociraptor.exe gui`

## [Task 3] Interacting with Client Machines

The left-hand pane contains a bunch of icons, some of which are greyed out. This is because these options are for client endpoints; they will only become active when an analyst interacts with these endpoints within the Velociraptor UI. Let's go ahead and add the Windows system as a client. In `commands.txt` we'll be given the command to "Add Windows as a client (CMD)" - open a Command Prompt/PowerShell window and run it. The command is `velociraptor-v0.5.8-windows-amd64.exe --config velociraptor.config.yaml client -v`, in this case from `C:\Program Files\Velociraptor`.

Once all is set up, go back to the Velociraptor interface. You can either click the magnifying glass next to the search bar (i.e., search for nothing) or you can click on Show All to display the list of client machines running Velociraptor as an agent. The information retrieved is as follows:
- Online State: A green dot means the endpoint is online and communicating with the Velociraptor server. A yellow dot means the server hasn't received any communication from the endpoint within 24 hours. A red dot means the server hasn't received any communication from the endpoint for more than 24 hours.
- Client ID: A unique ID assigned to the client by the Velociraptor server used to identify the endpoint. These always start with the letter C.
- Hostname: The hostname the client identifies itself to the Velociraptor server. Since these can change, hostnames are NOT used to uniquely identify an endpoint; the client ID is used instead.
- Operating System Version: Details regarding the client operating system, typically Windows, Linux, or macOS.
- Labels: May be used to identify multiple clients as a group. Client machines can have many labels attached to them.

If you click the agent, you can see a semi-detailed view - the Overview. This contains additional information about the client, such as
- Client ID
- Agent Version
- Agent Name
- Last Seen At
- Last Seen IP
- Operating System
- Hostname
- Release
- Architecture
- Client Metadata

The VQL Drilldown view gives additional information about the client, like memory/CPU usage over a 24-hour timespan, the Active Directory domain (if the client is domain-joined), and the active local accounts for the client. The data is represented in two colors in the memory and CPU usage:
- Orange is used for memory
- Blue is used for CPU

Velociraptor also allows you to interact with a remote machine using the Shell. Commands may be run in PowerShell, CMD, Bash, or VQL. The choice will depend on the target operating system; CMD won't work too well if the target is on Linux, for instance. You simply choose one of the options to run the command in, and click Launch. The results of the command may not be immediately visible; you'll have to click the eyeball icon to see the command results.

The Collected Vide shows the results from commands executed previously in the Shell. Other actions, such as interacting with the Virtual File System (VFS) will appear here. More on VFS later.

There are also details of the collected artifacts. Clicking on any FlowId will populate the bottom pane with additional details regarding the information collected for that artifact or collection. The output is from Artifact Collection.

Lastly, there's the Interrogate option. Interrogation normally occurs when the client first enrolls, though you can interrogate any client with the Interrogate button. If you click Interrogate, then navigate back to Collected, you'll see that the Artifact Collection now has Generic.Client.Info. This is an additional collection on the list. This is the same information displayed under VQL Drilldown.

Once we run the appropriate command to set up our Windows machine as the client, we can click Show All in the Velociraptor UI to see the clients. This includes the hostname:

![image](https://github.com/user-attachments/assets/f05022da-2fc8-4c55-b203-27581e46ad1c)

**[Task 3, Question 1] What is the hostname for the client?** - `THM-VELOCIRAPTOR.eu-west-1.compute.internal`

The agent version is provided when you click the client ID:

![image](https://github.com/user-attachments/assets/07d5134b-7bb1-4e23-a8e9-927f80bd2f1a)

**[Task 3, Question 2] What is listed as the agent version?** - `2021-04-11T22:11:10Z`

We can use click the Collected tab in the client information to see the commands used to query the client user accounts. We'll have to go to the Requests tab from here, and then look at the VQL statements that were issued. The `Windows.Sys.Users()` artifact contains information about client user accounts, so we're concerned with the hihglighted VQL statement.

![image](https://github.com/user-attachments/assets/90135c1d-93ce-4325-a64d-8c70f9751e58)

**[Task 3, Question 3] In the Collected tab, what was the VQL command to query the client user accounts?** - `LET Generic_Client_Info_Users_0_0=SELECT Name, Description, Mtime AS LastLogin FROM Artifact.Windows.Sys.Users()`

For this next question, we'll have to run `whoami` in PowerShell under the Shell tab.

![image](https://github.com/user-attachments/assets/509308db-4972-4f27-bad9-824d4f49303e)

Once this command executes we can switch to the Collected tab and check the Results tab therein. The results of the command are shown in `Stdout`:

![image](https://github.com/user-attachments/assets/3ee53e6f-a1d4-46d8-8c77-dfa710565a9a)

**[Task 3, Question 4] In the Collected tab, check the results for the PowerShell `whoami` command you executed previously. What is the column header that shows the output of the command?** - Stdout

Let's go back to the Shell. This time we'll run the command `Get-Date`:

![image](https://github.com/user-attachments/assets/603d070e-0d28-475a-a467-069efd1f73a1)

And then, to see the VQL statements taht were executed, we can go to the Collected -> Log tab. This contains all commands and statements issued in the execution of the PowerShell command:

![image](https://github.com/user-attachments/assets/13fa9465-c0ee-441e-bbc9-290a52fe0499)

**[Task 3, Question 5] In the Shell, run the following PowerShell command: `Get-Date`. What was the PowerShell command executed with VQL to retrieve the result?** - `powershell -ExecutionPolicy Unrestricted -encodedCommand ZwBlAHQALQBkAGEAdABlAA==`

## [Task 4] Creating a New Collection

To create a new collection, click the Plus icon in the top-left in the Collection view. Do the following:
1. Select Artifacts: Search for the artifacts to collect. In this task we'll use `Windows.KapeFiles.Targets`, which consist of the community-created targets and modules for use with KAPE. You'll see a description of the collector on the right.
2. Configure Parameters: You can set different settings for the collectors. In this case, we'll check the Ubuntu option.
3. Specify Resources: This specifies different resource constraints pertaining to the collection process (e.g., how long to execute for maximum, etc.). We can leave this untouched.
4. Review: You can see, in JSON format, the configuration you have done in the previous steps. From here, you can launch the collection process.
5. Launch: When you click this, you will be sent to the Collected view. There will be a new entry with the newly-created collection. The State icon shows you what's going on with the collector. If there's an hourglass, artifacts are actively being gathered. If there's a checkmark, the collection is complete. You can search for specific collections from this view as needed.

We'll need to go through the steps above for the questions in this task. Here is the information on the Ubuntu setting in the Configure Parameters screen.

![image](https://github.com/user-attachments/assets/8d505e9c-0d60-41d0-9038-f7c9dd696e28)

**[Task 4, Question 1] Earlier you created a new artifact collection for Windows.KapeFiles.Targets. You configured the parameters to include Ubuntu artifacts. Review the parameter description for this setting. What is this parameter specifically looking for?** - Ubuntu on Windows Subsystem for Linux

Once this process completes, we can check out the collected information by selecting the new FlowId:

![image](https://github.com/user-attachments/assets/ff8c8e16-5764-4cb9-bc83-94c4371e5b64)

The information in Artifact Collection gives us the number of uploaded files.

![image](https://github.com/user-attachments/assets/d644d0bb-3d46-4c4c-9d6f-80997d0715b0)

For some reason I'm only getting 19 after following the steps above, though the answer should be 20.

**[Task 4, Question 2] Review the output. How many files were uploaded?** - 20
 
## [Task 5] VFS (Virtual File System)

VFS is a server-side cache of the files on the endpoint, allowing you to inspect the filesystem from within Velociraptor. This can prove useful in incident response when you need to inspect artifacts in a client. A complete overview of VFS is provided in the official documentation.

There are four folders in the middle and left panes. These are "accessors", or filesystem access drivers:
- file: Things that were retrieved via operating system APIs
- ntfs: Things retrieved with raw NTFS parsing, low-level files
- registry: Windows registry objects accessed via operating system APIs
- artifacts: Previously-run collections

The buttons in the top-left of the middle pane allow you to refresh the current directory (syncing its listing from the client), recursively refresh the current directory, and recursively download the directory from the client.

When any folder is clicked in the left pane, additional details are displayed in the middle pane. If the file folder is clicked, you'll see the `C:` folder, and the details in the middle pane will change to reflect this.

The next two questions can be answered with information present in the tasks.

**[Task 5, Question 1] Which accessor can access hidden NTFS files and alternate data streams? (format: xyz accessor)** - ntfs accessor

**[Task 5, Question 2] Which accessor provides file-like access to the registry?** - registry accessor

We'll need to go to the File section in the left pane, and then check the `file` accessor. Note that we may need to sync files before we can start answering the next few questions - this can take some time:

![image](https://github.com/user-attachments/assets/f802c8d9-7bfc-40b8-82f0-8075dab8aa64)

(Note that you can click the first folder icon to just refresh the contents of the current directory, which will likely be faster.)

We can navigate to `file -> C: -> $Recycle.Bin -> S-1-5-21-1966530601-3185510712-10604624-500` in the left-hand pane. We are left with the following:

![image](https://github.com/user-attachments/assets/c976c298-b4a3-4128-a751-febbc0d8f44c)

**[Task 5, Question 3] What is the name of the file in `$Recycle.Bin`?** - `desktop.ini`

If we need to find hidden things in the filesystem, it'll be best to use the `ntfs` accessor. Going to `ntfs -> \\.\C: -> Users -> Administrator -> Documents`, we'll see the following:

![image](https://github.com/user-attachments/assets/b9590992-c56b-450a-943a-51cdf406bc47)

For this to work you must collect the file from the client. You are prompted to do this when you click on the file for the first time in the bottom pane.

**[Task 5, Question 4] There is a hidden text in a file located in the Admin's Documents folder. What is the flag?** - `THM{VkVMT0NJUkFQVE9S}`

## [Task 6] VQL (Velociraptor Query Language)

A lot of tools in SOC have their own query languages. Splunk has the Search Processing Language (SPL), Elastic has the Kibana Query Language (KQL), and Microsoft Sentinel has Kusto Query Language (also KQL).

VQL is a framework that can be used to create highly-customized artifacts, which let you collect, query, and monitor almost any aspect of an endpoint, groups of endpoints, or an entire network. You can also use it to create continuous monitoring rules on the endpoint and automate tasks on the server. VQL has been the centerpiece of everything we've done so far in Velociraptor; we saw such an example back in Task 3.

To execute a VQL statement, we can start by creating a notebook. Notebooks are containers that can be used to execute queries and commands. They consist of Markdown and VQL, similar to Jupyter Notebooks. To create a notebook:
1. Click the Plus icon on the Notebook view
2. Set the name, description, and collaborators and then click Submit.
3. Click the NotebookId that you just created. You can edit the contents of the notebook by clicking its text in the bottom pane.

The top-right of the bottom pane allows you to specify whether Markdown is in use or if VQL is in use. We can set it to be this, and then enter a VQL query such as `SELECT * FROM info()` to run a query.

You can also run VQL statements from the command line by using `velociraptor.exe -v query "(VQL QUERY)"`.

VQL queries can be packaged inside mini-programs called Artifacts. Artifacts are structured YAML files containing a query, with a name attached to it. This lets Velociraptor users search for the query by name or description, and then run the query on the endpoint without needing to understand or type the query into the UI.

More information on VQL can be found in the documentation, as well as the "VQL Reference" and "Extending VQL" pages. In fact, the documentation can be used to answer the questions that follow:

**[Task 6, Question 1] What is followed after the SELECT keyword in a standard VQL query?** - Column Selectors

**[Task 6, Question 2] What goes after the FROM keyword?** - VQL Plugin

**[Task 6, Question 3] What is followed by the WHERE keyword?** - Filter Expression

**[Task 6, Question 4] What can you type in the Notepad interface to view a list of possible completions for a keyword?** - `?`

This particular question can be answered by reading the "Extending VQL" page:

![image](https://github.com/user-attachments/assets/f9f4524f-369e-4740-b20c-06c4badbccfd)

**[Task 6, Question 5] What plugin would you use to run PowerShell code from Velociraptor?** - `execve()`

## [Task 7] Forensic Analysis VQL Plugins

VQL has a wide array of plugins and functions geared towards digital forensics and incident response. Here are the categories for forensic analysis:
- Searching Filenames
- Searching Content
- NTFS Analysis
- Binary Parsing
- Evidence of Execution
- Event Logs
- Volatile Machine State

Let's take a look at the NTFS Analysis techniques first. We'll need to make use of it in the next task; mainly the `parse_mft` function. Below are the VQL statements that are used for this function:

![image](https://github.com/user-attachments/assets/1e85c7c7-b8ab-4258-be92-60e287510765)

We're concerned with just the `parse_mft()` function and its arguments. This particular function allows you to parse the MFT directly on Velociraptor without fetching the file. It provides a high-level summary of each MFT entry, including timestamps and IDs.

**[Task 7, Question 1] What are the arguments for `parse_mft()`?** - `parse_mft(filename="C:/$MFT",accessor="ntfs")`

Next we'll look at how to search for filenames. There's some information on how the `glob()` plugin works and returns results. It specifically contains the following information about a matched file:

![image](https://github.com/user-attachments/assets/4c9b959f-109b-4722-b052-8054b51ea19b)

It seems like if we're concerned about directories in our results, we need to filter out `IsDir`.

**[Task 7, Question 2] What filter expression will ensure that no directories are returned in the results?** - `IsDir`

## [Task 8] Hunt for a Nightmare

We'll close the first VM from Task 2 and open the attached VM here. Our exercise now is to create an artifact to detect the PrintNightmare vulnerability. There's an artifact entry in the Artifact Exchange, but let's create a simple VQL query.
1. The SELECT clause: Column accessors should be `fullpath` - concatenate `C:/` to the `fullpath` column accessor - and `filename`.
2. Make sure the column headers for each column accessor are renamed. `fullpath` should be `Full_Path`, and for `filename` it should be `File_Name`.
3. Use `parse_pe()` to ensure only PE files are returned.
4. Make sure the column header for this plugin is renamed to PE.
5. The `From` clause should use `parse_mft()`.
6. The `Where` clause should not return any directories, only return binaries (PE files) and search the directory where this malicious DLL will most likely be found.

The query will look something like this:
- `SELECT "C:/" + FullPath AS ____, FileName AS ____,parse_pe(file="C:/" + FullPath) AS __`
- `FROM parse_mft(filename="C:/$___", accessor="____")`
- `WHERE ___ IsDir`
- `AND FullPath =~ "Windows/System32/spool/drivers"`
- `AND __`

We'll need to start Velociraptor in Instant Velociraptor mode. The documentation provides instructions on how to do this. The VM attached to this task is running Velociraptor 0.6.2.

PrintNightmare is a vulnerability that existed in the Windows Print Spooler service that would allow attackers to achieve RCE on the system. If we check the Artifact Exchange for Velociraptor, we see that there is an entry for PrintNightmare:

![image](https://github.com/user-attachments/assets/9b51087d-bdce-4faa-bbe3-fb761c03cc6a)

**[Task 8, Question 1] What is the name in the Artifact Exchange to detect PrintNightmare?** - Windows.Detection.PrintNightmare

Let's start filling out the VQL statement. To fill in the Select statement, all we need to do is change some column names around as per the instructions.

**[Task 8, Question 2] Per the above instructions, what is your Select clause? No spaces after commas** - `SELECT "C:/" + FullPath AS Full_Path,FileName AS File_Name,parse_pe(file="C:/" + FullPath) AS PE`

Now let's handle the rest of this. We know from the previous task that the FROM clause will be `FROM parse_mft(filename="C:/$MFT",accessor="ntfs")`. We don't want to include any folders in our results, so our `WHERE` clause should be `WHERE NOT IsDir`. Lastly, we'll want to return only PE files (binaries), so our final part of the VQL statement is `AND PE`.

Now that we have our complete statement, let's go ahead and set it up in Velociraptor. Remember that to open Instant Velociraptor we need to run `Velociraptor.exe gui`. We run the command in PowerShell from the Desktop, navigate to `127.0.0.1:8889` and login with the default `admin:password` combo, then navigate to the Notebook.

From there, we create a new notebook with the Plus icon, set the name and description, and then add a VQL block by clicking in the text of the notebook:

![image](https://github.com/user-attachments/assets/5cd3dece-a5df-42c8-af0e-70c4dafec502)

When we save the output, it'll attempt to run the VQL query, and after some time passes it'll finish. There should be around 29 results (it'll generate results over time, so you may see less for a little bit). At the end of the result list, at the very last page, we see...

![image](https://github.com/user-attachments/assets/36ad6de9-a7ee-4d40-8116-9cdac3ea411d)

That seems a bit suspect, doesn't it?

**[Task 8, Question 3] What is the name of the DLL that was placed by the attacker?** - `nightmare.dll`

The PDB is given in the right-most column.

**[Task 8, Question 4] What is the PDB entry?** - `C:\Users\caleb\source\repos\nightmare\x64\Release\nightmare.pdb`
