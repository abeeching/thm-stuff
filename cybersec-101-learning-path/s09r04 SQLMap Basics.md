# SQLMap: The Basics

## [Task 1] Introduction

SQL injection is a prevalent vulnerability and, thus, a hot topic in the world of cybersecurity. To understand it, we must first understand what a database is and how websites interact with one. Databases are collections of data that can be stored, modified, and retrieved. A website will record user data in a database and verify it in authentication. This same website may store information that you can search for later, e.g. available books.

Interactions with databases take place using database management systems (DBMS) and Structured Query Language (SQL). Our focus is on learning about SQL injection and how to exploit it.

**[Task 1, Question 1] Which language builds the interaction between a website and its database?** - SQL

## [Task 2] SQL Injection Vulnerability

Let's take a look at how interaciton happens between an app and a database via SQL. Note that you should obtain permission before trying to perform SQL injection from an owner against their application.

Say there's a login page, and we try to log in with the username `John` and the password `Un@detectable444`. A simple way of performing authentication with the database is to run this query: `SELECT * FROM users WHERE username = 'John' AND password = 'Un@detectable444';`. If this query returns something, then the credentials do exist for that particular user, and so we can authenticate them into the app.

Sometimes input isn't properly sanitized or properly validated; this can allow an attacker to manipulate the input and write SQL queries that would get executed in the database and perform the attacker's desired actions. This is how SQL injection (SQLi) arises, and it can be incredibly dangerous, since databases hold critical user data.

Say the attacker didn't know `John`'s password and instead wrote `abc' OR 1=1;-- -` as his password. Then we get this query: `SELECT * FROM users WHERE username = 'John' AND password = 'abc' OR 1=1;-- -';`.

This looks like the previous query, but now we have an `OR` operator. This will check if John has the password `abc`...OR if 1 = 1. If either of these conditions hold, then the query is true and will return results. We know 1 = 1 is always true, so it will ignore the password attempt we made and just assume the statement is true, executing the query successfully. The `-- -` at the end of the query comments out anything after 1 = 1, so the query should work without any issues.

Note the use of a single quote, `'`, after `abc`. Without this quote, the string `'abc OR 1=1;-- -'` would be considered the password, which isn't intended. The single quote is what allows us to introduce the `OR` condition.

**[Task 2, Question 1] Which boolean operator checks if at least one side of the operator is true for the condition to be true?** - `OR`

**[Task 2, Question 2] Is 1 = 1 in an SQL query always true? (Yea/Nay) - Yea

## [Task 3] Automated SQL Injection Tool

Carrying out SQLi involves discovering the vulnerability in the application and manipulating the database, which can take time if done manually. That's why we have SQLMap - this is an automated tool that detects and exploits SQLi vulnerabilities in web apps. It's built into some Linux distributions, but you can install it easily if it's not already there.

It's a command-line utility, so you open your terminal to make use of it. To see all the available flags, use `--help`. If you don't want to manually add flags, you could run it with `--wizard` - this guides you through each step and asks questions to complete the scan. This utility is good for beginners.

The `--dbs` flag lets you extract all the database names. Once you get to know the names, you can get information about the tables of that database with `-D (database_name) --tables`. If you want to enumerate the records in this table, you'd run `-D (database_name) -T (table_name) --dump`. These flags are useful for extractingi nformation.

Here's how you would use SQLMap to exploit an SQLi vulnerability:
1. Look for potentially vulnerable URLs and requests. This might be a URL that uses GET parameters to retrieve data. This can be tested in SQLMap with `-u`. This is HTTP GET-based testing. The command would include the URL with the vulnerable parameter, e.g. `sqlmap -u http://sqlmaptesting.thm/search/cat=1`.
2. The SQLMap results will display different types of SQLi vulnerabilities that were tested and identified on the target. The query itself is included with the results for reference. Queries may be modified such that they cause errors in the database; error messages can often be a good way to gather information about a target.
3. Once you can find an SQLi vulnerability, run the same command with `--dbs` to see the databases, and then run it again with `-D (table_name) --tables` to see the available tables in a given database. Then run it once more with `-D (database_name) -T (table_name) --dump`.

POST-based testing can be used for testing instead of GET-based testing. In this case, the web app sends data in the request's body instead of the URL. This may include login forms, registration forms, and so on. You'll need to intercept a POST request on the login/registration page, save it as a text file, and then input the request into SQLMap with `sqlmap -r intercepted_request.txt`. POST-based testing is out of the scope of this room.

**[Task 3, Question 1] Which flag in the SQLMap tool is used to extract all the databases available?** - `--dbs`

**[Task 3, Question 2] What would be the full command of SQLMap for extracting all tables from the "members" database? The vulnerable URL is `http://sqlmaptesting.thm/search/cat=1`. - `sqlmap -u http://sqlmaptesting.thm/search/cat=1 -D members --tables`

## [Task 4] Practical Exercise

This task has a VM attached to it that lets you test SQLi vulnerabilities. Start it, and start the AttackBox so you can use SQLMap. The login page is hosted at `http://MACHINE_IP/ai/login` - this page is vulnerable to SQLi.

If we see GET parameters in the URL, they may be vulnerable to SQLi... in this particular case, if we try to log in, we won't see any GET parameters in the URL. We'll need to supply the URL and the GET parameters. Here's how to do it:
1. Right-click the login page. Click the Inspect option. The specifics here dpeend on your browser.
2. Select the Network tab.
3. Enter test credentials in the username and password fields and click the login button.
4. The GET request will be displayed in the Network tab. Click on it to see the full request with the correct parameters. Copy this into SQLMap.

The full URL, in case you can't get into it, is `http://MACHINE_IP/ai/includes/user_login?email=test&password=test`. Remember to include single quotes when pasting in the URL, so that the Terminal handles the special characters correctly.

If you don't get results from a simple scan, add `--level=5` at the end of the commands to perform in-depth scans. You may also be asked questions by SQLMap; you should respond to them as follows:
- "It looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n]" - Y
- "For the remaining tests, do you want to include all tests for 'MySQL' extending provided risk (1) value? [Y/n]" - Y
- "Injection not exploitable with NULL values. Do you want to try a random integer value for option '--union-char'? [Y/n]" - Y
- "GET parameter 'email' is vulnerable. Do you want to keep tesitng others (if any)? [y/N]" - N

For this first question, we'll run `sqlmap -u 'http://MACHINE_IP/ai/includes/user_login?email=test&password=test' --dbs --level=5`. The level flag is required. After going through all the prompts, we'll see a list of the available databases at the end of the output.

![image](https://github.com/user-attachments/assets/b85fafc4-59c3-48f5-aa6d-640eb530c909)

**[Task 4, Question 1] How many databaseas are available in this web application?** - 6

To investigate the tables in the AI database, we'll need to run `sqlmap -u 'http://MACHINE_IP/ai/includes/user_login?email=test&password=test' -D ai --tables --level=5`. This yields the following:

![image](https://github.com/user-attachments/assets/1b6e2766-5629-4b94-b625-9371247fdf35)

**[Task 4, Question 2] What is the name of the table available in the `ai` database?** - `user`

Now let's investigate what's in this table. To do so, we run `sqlmap -u 'http://MACHINE_IP/ai/includes/user_login?email=test&password=test' -D ai -T user --dump --level=5`.

![image](https://github.com/user-attachments/assets/44b93d32-d472-4c0f-bd4e-ab7d6030abb8)

**[Task 4, Question 3] What is the password of the email `test@chatai.com`?** - `12345678`
