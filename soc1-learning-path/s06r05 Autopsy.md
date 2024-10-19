# Autopsy

## [Task 1] Introduction

Autopsy is an open-source digital forensics platform, with several features being developed by the DHS Science and Technology funding. It has a plug-in architecture, meaning it is easy for the community to develop new modules, making Autopsy extensible.

We've discussed Autopsy in the Windows Forensics 2 room, though we've only covered a few use cases. Here we'll focus on how to use Autopsy to analyze disk images. An understanding of the Windows OS and Windows forensic artifacts is assumed.

## [Task 2] Workflow Overview and Case Analysis

We need to follow a few steps before we can use Autopsy to analyze data. We need to tell Autopsy what the data source is and what to do with the data source. The basic workflow is as follows:
1. Create and open the case for the data source you will investigate
2. Select the data source you'd like to analyze
3. Configure the ingest modules to extract specific artifacts from the data source
4. Review the artifacts extracted by the ingest modules
5. Create the report

When we want to start a new case investigation, we need to create a case file from the data source. When Autopsy is started, we'll be presented with three options - we'll want to click on the New Case option. Once we do this, we'll see the Case Information menu, where we can provide information about the case name, the base directory where all the files specific to the case will be held, and whether the case will be local (single-user) or hosted on a server (multi-user).

NOTE: In this room, we already have a local case ready for analysis. We won't need to create a new case.

Once we complete the Case Information, we can fill out the Optional Information fields; in an actual forensic environment, you would likely fill out information here about case numbers and examiners, though we don't need to do so here. Once we click Finish, we'll have a new case in Autopsy.

Autopsy can also open prebuilt case files. Note that Autopsy case files have a `.aut` fiel extension. We'll be importing a case in this room. We will want to click the Open Case option, navigate to the case folder (on the Desktop in the VM), and select the `.aut` file to open. Then Autopsy will process the case files and open the case, and you can check the name of the case by looking at the top-left corner of the Autopsy window.

If Autopsy cannot locate the disk image, it will throw up a warning. You can point to the location of the disk image it's attempting to find, or you can click No to analyze the data that's already in the Autopsy case. Once the case is open, we can then start exploring the data.

**[Task 2, Question 1] What is the file extension of the Autopsy files?** - `.aut`

## [Task 3] Data Sources

Autopsy is capable of analyzing many disk image formats. Let's go over this real quick. We can add data sources by clicking the Add Data Source button in Autopsy proper. Data sources may include disk images, VM files, local disks, logical files, unallocated space image files, Autopsy logical imager results, and XRY test exports. Our primary focus will be on the Disk Image/VM File option.

Autopsy supports the following disk image formats:
- Raw single images: `*.img`, `*.dd`, `*.raw`, `*.bin`
- Raw split images: `*.001`, `*.002`, `*.aa`, `*.bb`, etc.
- EnCase files: `*.e01`, `*.e02`, etc.
- Virtual machines: `*.vmdk`, `*.vhd`

If there are multiple image files, then you only need to point Autopsy to the first image file. It will be able to figure out the rest.

### More on Autopsy's Data Sources

Some additional information from their documentation:
- The local disk option is helpful when you're analyzing a USB-attached device through a write blocker, and you can create a copy of the local disk as a VHD during analysis. Autopsy can even point to this VHD file, so you can analyze the drive even if it is physically removed. One ingest module must successfully run in order to generate the complete image copy.
- Files and folders may be added directly from the local computer/shared drive without putting them into a disk image first. Autopsy will ignore timestamps (they could be the timestamps that they were copied onto the examination device) and any unallocated space/deleted files on the surrounding drive.
- Limit support exists for logical evidence (`L01`) files.
- Logical imager results: Autopsy has a logical imager that you can use to collect files from a live Windows computer, and it is configured with rules that specify what files to collect. Useful if you do not have the time or authorization to perform a full-drive acquisition. It can also produce VHD images containing the filesystem data that was read, and these can be put into Autopsy or mounted on Windows. It'll also find user accounts and alert on encryption programs.

**[Task 3, Question 1] What is the disk image name of the `e01` format?** - EnCase

## [Task 4] Ingest Modules

Ingest Modules are Autopsy's plug-ins. These modules are used to analyze and retrieve specific data about the drive, and Autopsy can be configured to run specific modules during the source-addition stage or later by choosing the target data source available on the dashboard. The Ingest Modules are set to run on all files, directories, and unallocated space by default. This can be changed when you're selecting modules.

Note that ingest modules take time to implement, and also note that Autopsy will add metadata about files to the local database and not the actual file contents.

If you'd like to use ingest modules after adding data sources:
1. Open the "Run Ingest Modules" menu by right-clicking the data source.
2. Choose modules to implement and click Finish.
3. You can track the progress by checking the progress bar.

Results of the ingest module will populate the Results node in the left pane of Autopsy's UI. Results will depend on the dataset. If we try to retrieve specific data that is unavailable in the drive, then results won't show up.

In the Configure Ingest Modules window, we can set per-run settings for certain modules, such as the Interesting Files Finder module. The yellow triangle next to the module name indicates that there are per-run settings for that given module.

Alerts may populate the ingest inbox, which can be used to alert to anything interesting from the ingest module results.

## [Task 5] The User Interface I

The Autopsy interface has five main areas. The first is the left pane, known as the Tree Viewer. There are five top-level nodes in the Tree Viewer:
- Data Sources: All data is organized as you would see it in a regular filesystem explorer, e.g. File Explorer for Windows.
- Views: All data is organized based on file types, MIME types, file size, and so on.
- Results: All ingest module results appear here.
- Tags: Displays files or results that have been tagged.
- Reports: Displays reports generated by modules or the analyst.

The Result Viewer is the right pane of Autopsy. Whenever you choose a volume, file, folder, or anything from the Tree Viewer, additional information is displayed here. If you select a data source in the Tree Viewer, you will get more information about the case's data source. If you select a volume, you will instead get information from the local database about the selected volume.

In the Result Viewer, you can view the results as a table, as thumbnails (helpful if working with images and video), or as summaries. Volume nodes may be expanded to navigate through their contents as in Windows. You should also examine results from the Views tree node - files may be categorized by extension, MIME type, deleted files, or by file size. Adversaries like to rename files with misleading file extensions, but Autopsy will correctly categorize them by their MIME type.

The Contents Viewer allows you to see the contents of a folder or a file. There are three columns in the Contents Viewer table that may need some explanation:
- The S column is the Score. It shows a red exclamation point if the folder or file is marked/tagged as notable. It shows a yellow triangle pointing downward if a folder or file is suspicious. These items may be marked or tagged by an ingest module or an analyst.
- The C column is the Comment column. If there is a yellow page in the Comment column, it means a comment has been made on the folder or file.
- The O column is the Occurrence column. This indicates how many times the file/folder has been seen in past cases, though this requires the Central Repository.

At the top-right, you can perform Keyword Search. You can see lists of keywords, or you can perform ad-hoc searches of keywords in the case.

The Status Area at the bottom right is where you see ingest module progress - a progress bar with the percentage completed will be displayed here. More detail regarding the currently-running Ingest Module is provided if you click on the bar. If you click the X button next to the progress bar, you will be prompted to confirm ending the Ingest Module.

First we will go ahead and import the case in question. We're going to open the Sample Case that's located on the Desktop:

![image](https://github.com/user-attachments/assets/50b41c48-2e4a-40b2-a503-99535b5ac6e3)

We will not need to find missing disk images for this room (even if we had to, they're not on this VM!), so we can click No at this dialog prompt. We won't need to use ingest modules either, so we don't have to worry about being unable to run the ingest process.

![image](https://github.com/user-attachments/assets/fe0abc3d-baa3-4dce-a84e-da32312240c1)

From here, we can begin investigating. If we check the Data Sources option in the Tree Viewer, we see that there are four sources (each volume counts as a source for this question):

![image](https://github.com/user-attachments/assets/c80c01f9-f0d5-44ae-b7bc-08ed8c8da6c6)

**[Task 5, Question 1] Expand the Data Sources option; what is the number of available sources?** - 4

Now let's look for the number of Removed files. By "Removed files", they mean that we want to check the Recycle Bin entries. We can see this in the Tree View by expanding the Results node, then the Extracted Content node. The number next to Recylce Bin tells us how many results there are.

![image](https://github.com/user-attachments/assets/9d711b92-3fc6-416e-8957-e889b43041e0)

**[Task 5, Question 2] What is the number of the detected Removed files?** - 10

For this next question, we'll want to investigate the Interesting Items node under the Results node. Expanding the Interesting Items node, then expanding Cloud Storage, we can click the Interesting Files node. We see two instances of the same file name in the Results Viewer:

![image](https://github.com/user-attachments/assets/e4eb3253-339b-43eb-b6f7-7a834ae0f2d5)

**[Task 5, Question 3] What is the filename found under the Interesting Files section?** - `googledrivesync.exe`

## [Task 6] The User Interface II

Now let's go over how to find summarized info. This can help analysts decide where to focus by evaluating the available artifacts. Viewing a summary of data sources can be helpful before starting an investigation to get an idea of the system and artifacts.

The Data Sources Summary gives us summarized information in nine categories, though this is only an overview of the total findings. If we wanted to find specific artifacts, we would have to turn to the Result Viewer.

We can generate a report of findings in many formats, allowing us to create data sheets for our investigation and perhaps reinvestigate findings after a live investigation is finished. The report gives us all the information listed under the Result Viewer pane, however reports do not have additional search options, so you have to find artifacts manually.

An aside: Autopsy can be pretty heavy on systems with low resources, so it may be difficult to complete an investigation when you're working on low-end computers. Browsing a large amount of results can result in the system freezing. Reports are helpful in this case, since you can just parse the data and genreate a report, and then analyze the report itself instead of Autopsy. That said, it's always easier to conduct an investigation with the Autopsy GUI.

If you want to generate a report, you can click on the Generate Report button. You can output the rpeort in different formats, provide headers and footers, include different data sources, and identify what kind of data you want to report on. Once you choose the format and scope, Autopsy generates the report, and you can go to the HTML Report link to view the report in your browser.

To view a summary of our data sources, we can right-click the disk image in Autopsy, then click View Summary Information:

![image](https://github.com/user-attachments/assets/e67dc21a-4024-4c86-8b41-9d3b2b489d0b)

Doing so gives us the name of the OS -- this is what you should see upon clicking View Summary Information:

![image](https://github.com/user-attachments/assets/ac19e956-7832-4203-bc66-2b55039ed9b4)

**[Task 6, Question 1] What is the full name of the operating system version?** - Windows 7 Ultimate Service Pack 1

Also in the Data Sources Summary, we can see a pie chart representing the different types of files on the image. We see that 40.8% of the image is just documents.

![image](https://github.com/user-attachments/assets/6b8a4ba7-1d03-40c3-a71f-1ec77ac2c3fd)

**[Task 6, Question 2] What percentage of the drive are documents? Include the % in your answer.** - 40.8%

Now let's generate a report. We can do so by clicking on Generate Report in the toolbar, or (if you're unable to get to it with the split-screen Attack Box) clicking Tools -> Generate Report:

![image](https://github.com/user-attachments/assets/010de652-b1e2-40a6-9132-51e1d38b39d9)

From here, we just want to make sure that we generate an HTML report. We'll leave all the options default, so click Next a few times and then click Finish. Once it is complete, we can access the HTML report by clicking the link provided in teh Report Generation Process screen:

![image](https://github.com/user-attachments/assets/b984a8de-95e4-402f-a44a-a944d78c5d2d)

When you open the HTML report, you'll be greeted with a page giving information about the image and software. If you scroll down on this page, you'll see the ingest history, which numbers each job/ingest module used in the case. We find that the Interesting Files Identifier is the tenth job:

![image](https://github.com/user-attachments/assets/2198bbb2-f191-4b9f-bfb6-1dd1e3e28448)

**[Task 6, Question 3] Generate an HTML report as shown in the task and view the Case Summary section. What is the job number of the Interesting Files Identifier module?** - 10

## [Task 7] Data Analysis

Now that we understand how Autopsy works, we will use it to investigate a mini-scenario: an employee leaking company data. A disk image was retrieved from the machine and we must do some initial analysis. When we view the case in Autopsy, we won't need to add the disk image (since it's not in the VM proper), and we won't need to run ingest modules.

Let's dive in. We're still working with the same case. We're looking for a program with the specific verison number 6.2.0.2962. We can see what programs were installed by going to the Results -> Extracted Content -> Installed Programs node in the Tree Viewer. There are quite a few programs to sift through, though:

![image](https://github.com/user-attachments/assets/3e3f9572-7540-4c9f-91dc-f1a18be16324)

We can make our life a little easier if we search for the version number using the Keyword Search:

![image](https://github.com/user-attachments/assets/3d10e2da-c337-4b39-8770-1140646be500)

Doing so gives us the answer at the top of the results:

![image](https://github.com/user-attachments/assets/bc30a719-634f-44cb-865f-94285b59225e)

Note that this shows up in a different "tab" in the Results Viewer. We can close the search results tab when we're done.

**[Task 7, Question 1] What is the name of an installed program with the version number 6.2.0.2962?** - Eraser

Now we're looking for a user with a password hint. We're probably going to want to look in the Results -> Extracted Content -> Operating System User Account node. We should see this:

![image](https://github.com/user-attachments/assets/34d32a9d-1aba-4ecf-89e5-73e70ebf5259)

If we scroll the table to the right, we see a column for password hints, and as we are told, there's only one user with a password hint. This is what we're looking for!

![image](https://github.com/user-attachments/assets/134a060b-672c-460c-bfc2-669741715df6)

**[Task 7, Question 2] A user has a password hint. What is the value?** - IAMAN

The next question asks us to see where the SECRET files came from. The verbiage (and the fact that "secret" is in all caps) would suggest that we should search for "secret" with the keyword search. Doing so gives us the following results:

![image](https://github.com/user-attachments/assets/7bf4b7ba-9978-4939-b996-c790a38b8017)

If we investigate the files pertaining to the `secret_project`, then we can see the IP of the shared network drive by checking the Text tab of the Contents Viewer:

![image](https://github.com/user-attachments/assets/2e766221-2b67-4974-a310-0889c3c6a68a)

**[Task 7, Question 3] Numerous SECRET files were accessed from a network drive. What was the IP address?** - `10.11.11.128`

Now let's see what else we can find on this image. We are asked to find the web search term with the most entries, so we can start by investigating the Results -> Extracted Content -> Web Search module. Scrolling through the results, it's pretty clear what got searched up the most:

![image](https://github.com/user-attachments/assets/1333a905-1aef-4b4c-8ff4-65ca98a5604b)

**[Task 7, Question 4] What web search term has the most entries?** - information leakage cases

While we're still here, we're being asked to find a search conducted on a given date. If we scroll the Web Search table to the right, we see the date and time the search was conducted. We can scroll through and look for the entry `2015-03-25 21:46:44 GMT`.

![image](https://github.com/user-attachments/assets/7c3cd572-2ade-4b0e-9123-3278a8132532)

**[Task 7, Question 5] What was the web search conducted on `3/25/2015 21:46:44`?** - anti-forensic tools

Now we're being asked to find the MD5 hash of the interesting file we found a few tasks ago. Going back to the Interesting Files by going to the Results -> Interesting Items -> Cloud Storage -> Interesting Files node, we can click the first `googledrivesync.exe` file and check the File Metadata in the Content Viewer pane. Scrolling through, we can see its MD5 hash:

![image](https://github.com/user-attachments/assets/1b3c44ba-bbde-413f-8cb9-8710e1c43c59)

**[Task 7, Question 6] What MD5 hash value of the binary is listed as an interesting file?** - fe18b02e890f7a789c576be8abccdc99

This last question may be a little tricky if you're not familiar with the actual Sticky Notes program on Windows. We saw earlier that this image is of a Windows 7 machine. Windows 7 machines came with the Sticky Notes program which allowed you to put virtual sticky notes on your desktop. The contents of the sticky notes are typically saved as `StickyNotes.snt` in Windows 7, so we can do a Keyword Search for this specific file.

Once we do, we can click it and view the Text in the Contents Viewer. The message is at the very bottom of the file:

![image](https://github.com/user-attachments/assets/9cf63ee4-11c7-44d3-bdb5-9b1e62c7742f)

**[Task 7, Question 7] What self-assuring message did the Informant write for himself on a Sticky Note (no spaces)?** - Tomorrow...Everything will be OK...

## [Task 8] Visualization Tools

There are other elements of the GUI that are worth looking into. Note that we will only be able to interact with the Timeline, and not the other visualization tools.

The Timeline is composed of three areas:
- The filters (which you can use to narrow events based on filter criteria - it's the top-left pane of the Timeline screen)
- The events (the top-right pane; this will be a bar graph if you're in the Counts View Mode)
- The files/contents (the bottom panes, which show further information about events).

There are three view modes that you can use with the Timeline:
- Counts (which displays the number events as a bar graph)
- Details (which provides information about events, clustering and collapsing them)
- List (which displays the events in a tabular view)

In Details mode, the numbers indicate the number of clustered and collapsed events for a specific timeframe. You can click on the green plus sign icon to expand a cluster, and you can click the red minus sign icon to collapse the events. You can pin events by clicking the map marker icon with a plus sign. This moves the events to an isolated section of the Events view, making them easier to get to. To unpin events, click the map marker icon with a minus sign.

The eye icons in Details Mode allow you to hide a group of events from the Events view. You can click the eye with a minus sign to hide events. This places the events under Hidden Descriptions in the Filters area/pane. If you want to unhide the events, right-click the events in the Hidden Descriptions section, then click Unhide and Remove From List.

### More on Autopsy's Other Visualization Tools

The visualization tools that are specifically called out in the task are the Images/Videos and Communications tools. We'll go over them briefly.
- The Images/Videos tool allows you to see the Image Gallery, containing all images and videos in the disk image. This can be helpful in investigations involving images and videos, and Autopsy will even mark images/videos that have known bad hashes if you use the hash lookup ingest module. Images can be grouped by folders and other attributes to help break a large set of images into smaller groups.
- The Communications tool allows you to see all communication events in the case, the most commonly-used accounts, and when communications happened. You can choose the devices to display, what types of data to display, and timeframes. You can select accounts to see how many times the account appears in different data types, files, and other cases. You can see any messages or call logs associated with thea ccount. You can see contacts and media files sent, among other things. There's even a tool that can create a graph showing all accounts associated with a given account.

Now let's check out the timeline for our sample case. We can click the Timeline button at the top of the Autopsy GUI to access it. We'd like to see how many events happened on January 12, 2015. We could start by setting the time at the bottom of the Events pane. We can type `Jan 12, 2015 12:00:00 AM`, and then click the first bar that appears on the bar graph.

![image](https://github.com/user-attachments/assets/ab80aed6-9608-4373-8257-4ec058e1ba8e)

Once we do, the number of events is displayed in the files/contents pane at the bottom.

**[Task 8, Question 1] Using the timeline, how many results were there on 2015-01-12?** - 46

We'll still want to use the Counts view for this last question. If we set the filters up right (i.e. set them up so the Time Units are months - this will be between the Years and Days marks on the slider), we can see that the majority of events happened in March.

![image](https://github.com/user-attachments/assets/c1f2ab43-bbe1-4931-a3d4-5aeea71c5b7c)

We can then set the Time Units to Days. We can set the start time on the graph to `Mar 1, 2015 12:00:00 AM` and then look at the bar graph. Note that we'll want to be in Linear mode (which you can see at the top-right of the screenshot below), otherwise our results will be off.

![image](https://github.com/user-attachments/assets/af4d04fd-13f0-4e14-9633-110c2b9d3f52)

**[Task 8, Question 2] The majority of file events ocurred on what date?** - March 25, 2015
