# Osquery: The Basics

## [Task 1] Introduction and [Task 2] Connect with the Lab

In short, Osquery is an open-source agent created by Facebook (of all things), which treats the OS as a relational database. As with other relational databases, we can ask questions about the status of the system via SQL queries, specifically asking about running processes, user accounts created on the host, communications with suspicious domains... it can be used for other things too, such as incident response and threat hunting. It is available on Windows, Linux, macOS, and FreeBSD.

The VM provided in the room allows you to access Osquery - just open PowerShell and run `osqueryi` to enter Osquery's interactive mode. As an aside, it helps to be familiar with SQL queries somewhat - the room goes into some detail about how to construct SQL queries, but having some background in the language certainly doesn't hurt.

## [Task 3] Osquery: Interactive Mode

When you start Osquery in interactive mode, you can run `.help` to see a list of commands that Osquery specifically accepts. Meta-commands -- those that affect the operation of Osquery itself -- are all prefaced with the period, `.`

To list all tables, run `.table`. We can use this command to search for specific tables, too... If we wanted to see the tables associated with processes, we may run `.table process`. If we wanted to see the tables associated with users, we can urn `.table user`.

Listing the tables is a start -- this doesn't tell us what's inside each table. So, we should be familiar with the columns and associated data types for each table. In the language of relational databases, this is known as a _schema_. To list a table's schema, run `.schema (TABLE NAME)`. This will give us an idea of what kind of information we can obtain from a given table. From there, we may extract certain information from a table with the SQL statement `select (COLUMN) [, (COLUMN)...] from (TABLE)`.

Osquery also has display modes that you can select from, and you can change the viewing mode with `.mode (MODE)`. According to `.help`, this can be one of five options -- `csv` for comma-separated values, `column` for left-aligned columns, `line` for one-value-per-line output, and `pretty` for pretty-printed SQL results. `pretty` is the default.

As we'll see momentarily, the Osquery schema API documentation allows us to see all the available tables, columns, and associated types.

For now, we'll dive into Osquery proper -- in the VM, we run `osqueryi` in PowerShell. Let's start by looking for process-related tables. We can do this by running `.table process` and looking at the results.

![image](https://github.com/user-attachments/assets/8abb5f73-72b7-4750-9b48-6f97579d0032)

We see there are three results.

**[Task 3, Question 1] How many tables are returned when we query `table process` in the interactive mode of Osquery?** - 3

Now let's look at the `processes` table itself with `.schema processes`. We can figure out which displays the process ID from here!

![image](https://github.com/user-attachments/assets/0becd20e-8656-4ad0-ad86-ddbdeeaecb66)

We often abbreviate Process ID as PID, so it seems fair to say that the `pid` column gives us the process ID.

**[Task 3, Question 2] Looking at the schema of the `processes` table, which column displays the process ID for the particular process?** - `pid`

We did cover this next question in the information above but it doesn't hurt to check for ourselves. Running `.help` and reading through the output gives us this:

![image](https://github.com/user-attachments/assets/b7b12f4b-857f-4172-9808-74dc7d4e1b4f)

As we can count in the task...there are five different modes.

**[Task 3, Question 3] Examine the `.help` command. How many output display modes are available for the `.mode` command?** - 5

## [Task 4] Schema Documentation

If you go to the Osquery schema documentation, you'll be able to set all sorts of settings to help you understand what you're working with in a given version of Osquery.
- The top-right allows you to choose from different versions of Osquery.
- The number of tables will be shown in the top-left.
- The tables will be listed on the left, in alphabetical order. This is what we'd see if we ran `.table` in Osquery.
- When you click on a table, you'll see the name of the table along with a brief description...
- ...a chart showing the table's columns, types, and descriptions of each column...
- ...information on which OS the table applies to...
- ...and a drop-down menu (above the table name and description) that can be used to narrow down our focus to certain operating systems.

Now let's dive into the documentation proper. If we want to figure out how many tables we get when we select both Linux and Windows, we must set that option -- it'll be above the table name in the documentation website. The version is already set for us, so we don't have to change anything there. Check out the top-left of the documentation!

![image](https://github.com/user-attachments/assets/0b2e4e29-e5b1-4b36-9705-6905f4dc543d)

**[Task 4, Question 1] In Osquery version 5.5.1, how many common tables are returned, when we select both Linux and Windows Operating Systems?** - 56

Similarly, we change the tables to just those available for macOS and check the top-left for the final count. Notice how the tables are different from the ones we've seen in the previous question!

![image](https://github.com/user-attachments/assets/9c99bd57-518f-4dd6-8dd7-d23162b5598a)

**[Task 4, Question 2] In Osquery version 5.5.1, how many tables for macOS are available?** - 180

Now let's look for specific tables. We're looking specifically for tables used to display installed programs. There really is no easier way to find it than to just search through the webpage yourself, though a little CTRL+F can help expedite the process. When you search for `program` on the page, this table eventually shows itself:

![image](https://github.com/user-attachments/assets/b416b64c-5990-4106-a4a1-81a55e76f751)

**[Task 4, Question 3] In the Windows operating system, which table is used to display the installed programs?** - `programs`

Now we need to find a specific value from a given table -- `registry`. We can find this table by scrolling through the list of tables on the left (or using CTRL+F a few times), and when we find it we see this:

![image](https://github.com/user-attachments/assets/df812d18-0260-4fdf-99e9-d76a046ab477)

There's a lot of information provided here, but the "data content of registry value" is what we're looking for here.

**[Task 4, Question 4] In the Windows operating system, which column contains the registry value within the `registry` table?** - `data`

## [Task 5] Creating SQL Queries

SQL comes in many different flavors, and the SQL variant used in Osquery is no exception - it is a _superset_ of the SQLite variant. Typically speaking all your queries in Osquery will be SELECT statements (that is, statements that help retrieve information from a database). You are not likely to alter information in this database, though UPDATE and DELETE commands are supported (if only for runtime tables/views and certain extensions). Select statements will also contain a FROM clause and end with a semicolon, in standard SQL fashion.

To explore installed programs on an endpoint:
- Start by understanding the schema with `.schema programs`. Or check the documentation.
- Then, run `SELECT * FROM programs;`.
- You can adjust different portions of this query for your purposes; for instance, `SELECT * FROM programs LIMIT 1;` will limit the number of results to display.
- You can also adjust the columns displayed - maybe you don't need all of them! The command `SELECT name, version, install_information, install_date FROM programs LIMIT 1;` will display only those columns listed.

If you want to count the number of programs or entries returned in a table, use the `count()` function. For instance, `SELECT count(*) FROM programs;` should return the number of installed programs on the endpoint.

The WHERE clause can be added to any query to narrow down the list of results based on a specific criteria. This example would only give us information from the `users` table pertaining to the user James: `SELECT * FROM users WHERE username='James';`. Of course, we can use the WHERE clause in different ways.
- We know `=` means "equals" but there is also `<>` for "not equal".
- There is `>` and `>=` for "greater than" and "greater than or equal to".
- Likewise there is `<` and `<=` for "less than" and "less than or equal to".
- The `BETWEEN` operator allows us to specify ranges.
- The `LIKE` operator allows us to perform pattern and wildcard searches.
- The `%` wildcard allows us to match for multiple characters. In folder structures, `%` matches files and folders for one level, while `%%` matches files and folders recursively. For instance, `%abc` will look for all files and folders in the same level ending in `abc`. Conversely, `abc%` looks for all files and folders in the same level beginning in `abc`.
- The `_` wildcard allows us to match one character.
- Note that the WHERE clause may be required for certain tables, such as the `file` table. Not including it will throw an error.

Some examples of using these operators are as follows:
- `/Users/%/Library` -> matches users' Library folders, ignoring the contents within.
- `/Users/%/Library/` -> matches users' Library folders, including the contents within but NOT anything in subdirectories.
- `/Users/%/Library/%` -> matches users' Library folders, including contents in the folder AND in subdirectories.
- `/Users/%/Library/%%` -> matches users' Library folders recursively.
- `/bin/%sh` -> matches the bin directory against anything ending with `sh`. Helpful for finding scripts!

The JOIN function can be used to join two tables based on a column shared by both tables. For instance, say we want to join the `users` and `processes` tables. Both of these have a `uid` column which can be used to identify user records and the user executing a given process. So, our JOIN command might be `select p.pid, p.name, p.path, u.username from processes p JOIN users u on u.uid=p.pid LIMIT 10;`
- We use `p` and `u` as aliases. So `p.pid` refers to the `pid` column in the processes table ONLY, whereas `u.pid` refers to the `pid` column in the users table ONLY. We must define these aliases if we want to use them, which we do so later in the command (`process p JOIN users u`...)
- To use the JOIN syntax, you must indicate the column that you are joining on, e.g. `u.uid=p.pid`.

There is more information in the Osquery documentation on creating SQL queries (namely those for Osquery itself).

Now let's hop back into Osquery proper. First we'll start by figuring out how many progrmas there are on this host. We know the `programs` table tells us what programs are installed on the host, so if we can apply the count function to it, we can get our answer. We can just run `SELECT count(*) FROM programs;` to get the result.

![image](https://github.com/user-attachments/assets/87e7da7d-f83d-4081-9bba-0e27a2dcfba2)

**[Task 5, Question 1] Using Osquery, how many programs are installed on this host?** - 19

If we want to figure out what the description is for James, we'll want to run `SELECT * FROM users WHERE username='James';` and investigate the output. The `users` table gives us general information about each user on this host, including any descriptions applied.

![image](https://github.com/user-attachments/assets/73471300-6b37-4191-9209-820881c07df9)

**[Task 5, Question 2] Using Osquery, what is the description for the user James?** - Creative Artist

Now we get some queries of our own to run. Running the query provided in the question below, we see the following results. We want the SID information (starting with  `S-1-5...`) for the user with RID `1009`:

![image](https://github.com/user-attachments/assets/72b8c3ab-9731-4d44-a6ce-fd74f76ad17f)

The RID is going to be located at the _end_ of the HKEY_USERS paths and names, so we'll want to grab the SID that ends with `1009`.

**[Task 5, Question 3] When we run the query `select path, key, name from registry where key = 'HKEY_USERS';`, what is the full SID of the user with RID `1009`?** - `S-1-5-21-1966530601-3185510712-10604624-1009`

Now let's run the command given in the next question. This should give us information about the browser extensions installed for Internet Explorer on this host. The wording might not make it so obvious, but we actually want the _path_ of the extension and not the name.

![image](https://github.com/user-attachments/assets/1bb3c71f-418e-4104-926e-5534767e2aa6)

A few things:
- Mind the weird layout of the tables. I'm going to switch it to line-based view momentarily, I swear. It doesn't look so nice when you're viewing a VM in the standard split-screen view!
- If you forget to end a statement with a semicolon, or something like that... Osquery will give you a line with `...>`. This is pretty standard in a lot of shells - just insert whatever it is you need to add and you'll be set.

**[Task 5, Question 4] When we run the query `select * from ie_extensions;`, what is the Internet Explorer browser extension installed on this machine?** - `C:\Windows\System32\ieframe.dll`

And lastly, when we run the command given in the next question, we get the answer pretty quickly. I did change it to line-based output:

![image](https://github.com/user-attachments/assets/7e3d1ad7-f6f9-460d-9b63-79cea6693e06)

**[Task 5, Question 5] When we run the query `select name,install_location from programs where name LIKE '%wireshark%';`, what is the full name of the program returned?** - Wireshark 3.6.8 64-bit

## [Task 6] Challenge and Conclusion

Okay! Now let's use Osquery to really investigate this host. First, we'll want to figure out what table gives us process execution information, so we'll want to investigate the Osquery documentation (or just a little knowledge of the Windows Registry itself). In the documentation, we see a table that contains UserAssist information, which is a registry key that tracks when a user executes an application from Windows Explorer.

![image](https://github.com/user-attachments/assets/00b80f26-1685-4673-a05d-7a5bc0847a29)

**[Task 6, Question 1] Which table stores evidence of process execution in Windows OS?** - `userassist`

Let's try to get the names of the processes executed on this host. We can do this by running the SQL query `SELECT path FROM userassist`. This yields a lot of information, but we can see something pretty suspect in the output -- `DiskWipe` sounds like it can be used to, well, wipe disks clean. This is likely something we're looking for!

![image](https://github.com/user-attachments/assets/b1d3b463-adf0-48f7-aa87-ab966db5a79b)

**[Task 6, Question 2] One of the users seems to have executed a program to remove traces from the disk; what is the name of that program?** - `DiskWipe.exe`

This same output happens to give the answer to the next question. Though, if we wanted to actually construct a new query for this, then we'd follow the same process as in Questions 1-2. The documentation provided by Osquery suggests that `interface_details` would help -- it gives us information about the available network interfaces, which would include VPNs. Our query may be `SELECT * FROM interface_details`, or perhaps we can provide more detail and say `SELECT * FROM interface_details WHERE description LIKE '%vpn%';`. This gives us the same answer just fine:

![image](https://github.com/user-attachments/assets/0d3571a2-59ef-40ed-a72e-6b3723b074d8)

**[Task 6, Question 3] Create a search query to identify the VPN installed on this host. What is the name of the software?** - ProtonVPN

Conveniently enough, Osquery comes with a table literally called `services` which seems well-suited for our next task: counting the number of services. Thus, we'll run `SELECT count(*) FROM services;` and see what we get:

![image](https://github.com/user-attachments/assets/734ad64b-cd4a-4fd5-a548-22b04a8b0db9)

**[Task 6, Question 4] How many services are running on this host?** - 214

Now we'll investigate the `autoexec` table -- we're specifically hunting for a file that ends with `.bat`. We expect the path to end with `.bat`, so let's try `SELECT * FROM autoexec WHERE path LIKE '%bat';`. This yields the following information:

![image](https://github.com/user-attachments/assets/5becf141-9371-473e-b93a-47a6b26fb7ee)

We could have also easily searched with the `name` column instead of `path`.

**[Task 6, Question 5] A table `autoexec` contains the list of executables that are automatically executed on the target machine. There seems to be a batch file that runs automatically. What is the name of that batch file (with the extension `.bat`)?** - `batstartup.bat`

This output just so happens to give us what we need for the final question. We're grabbing the LAST file from this list, by the way!

**[Task 6, Question 6] What is the full path of the batch file found in the above question? (Last in the list)** - `C:/Users/James/AppData/Roaming/Microsoft/Windows/Start%20Menu/Programs/Startup/batstartup.bat`
