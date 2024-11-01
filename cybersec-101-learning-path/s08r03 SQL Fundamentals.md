# SQL Fundamentals

## [Task 1] Introduction

Databases are a key component when it comes to cybersecurity. In offensive security, you use vulnerabilities in how people implement databases to tamper with or retrieve data from a compromised service. In defensive security, you use databases to navigate through data and find suspicious or relevant activities. You can also work on protecting services associated with databases.

The goal of this room is to introduce some fundamental concepts regarding databases, particularly the language people use to communicate with them: Structured Query Language (SQL). No IT experience needed, though it would help to have a basic understanding of Linux before moving ahead.

## [Task 2] Databases 101

A database is an organized collection of structured information/data; ideally we are able to access, manipulate, and analyze this information easily. Databases are used in many places, e.g. for authentication (logging in, usernames, passwords) and user-generated content (e.g., user posts, comments, likes, and more). Databases are used extensively throughout organizations to handle data.

There are multiple types of databases, though the two major ones we'll focus on are _relational databases_ and _non-relational_ databases.
- Relational databases store _structured_ data. Every entry into the database follows the same structure, and can be put into a table with rows and columns. Relationships can be identified between tables.
- Nonrelational databases store data in a non-tabular format, so each entry may be handled differently in the database.

When choosing the type of database you want to work with, you make a choice based on the context in which the database will be used. Relational databases are used when the data being stored is going to be received in a consistent format and when accuracy is important (such as in e-commerce). Nonrelational databases are used when the data can vary but need to be stored in the same place, e.g. social media platforms.

All data in a relational database is stored in a table.
- The pieces of information that you need to define a record are the columns. This includes properties such as IDs, names, and so on. For the Book table example, this might be the book's ID, its name, when it was published, author information, etc.
  - Each column should have a specified data type, and if someone attempts to enter other types of data into the column, then it should be rejected.
  - Core data types include strings, integers, floats/decimals, and times/dates.
- Each record in a table is a _row_. In the Book example, each record will represent an actual book.
- Keys are used in relational database to help establish relationships.
  - A _primary key_ ensures that data collected in a certain column is unique, i.e., it's a unique way of identifying a record. A column is chosen in each table as a primary key. There can only be one primary key column for each table.
  - A _foreign key_ is a column (or columns) in a table that also exist in another table. These provide a link, a relationship, between the two tables. For instance, we may have an Author table separate from the Book table. The Author table may have its own primary key for the author IDs, and we can just put those IDs in the Book table as foreign keys.

**[Task 2, Question 1] What type of database should you consider using if the data you're going to be storing will vary greatly in its format?** - non-relational database

**[Task 2, Question 2] What type of database should you consider using if the data you're going to be storing will reliably be in the same structured format?** - relational database

**[Task 2, Question 3] In our example, once a record of a book is inserted into our "Books" table, it would be represented as a ___ in that table?** - row

**[Task 2, Question 4] Which type of key provides a link from one table to another?** - foreign key

**[Task 2, Question 5] Which type of key ensures a record is unique within a table?** - primary key

## [Task 3] SQL

Databases are controlled with a database management system (DBMS), which acts as an interface between the end-user and a database. DBMS software acn be used to let users retrieve, update, and manage data being stored. Examples include MySQL, MongoDB, Oracle Database, and Maria DB. The interaction between the end-user and database happens between SQL. SQL is a language that can be used to query, define, and manipulate data stored in a relational database.

SQL comes with many benefits:
- Relational databases can return a lot of data almost instantaneously. SQL does not use much storage space and helps process things quite quickly.
- SQL is easy to learn, given that it is written in plain English. It's pretty readable, so you can focus on learning functions and syntax.
- Relational databases can guarantee a level of accuracy when it comes to data by defining a strict structure into which data sets must fall in order to be inserted.
- SQL provides capabilities when it comes to querying a database. This lets users perform vast data analysis tasks efficiently.

The virtual machine attached in this room starts in Split Screen View, allowing you to easily practice some SQL. To get into the SQL database in this VM, run `mysql -u root -p` from a Terminal. When prompted for a password, enter `tryhackme`. From there, you can begin interacting with the MySQL database.

**[Task 3, Question 1] What serves as an interface between a database and an end user?** - DBMS

**[Task 3, Question 2] What query language can be used to interact with a relational database?** - SQL

## [Task 4] Database and Table Statements

Now let's go through some statements that can be used to get started with databases and tables.

Some important database statements include:
- `CREATE DATABASE (database_name);` - creates a database.
- `SHOW DATABASES;` - returns list of present databases.
- `USE (database_name);` - tells MySQL what database you'd like to interact with
- `DROP DATABASE (database_name);` - remove database once it is no longer needed

Some important table statements include:
- `CREATE TABLE (table_name)` - This lets you create a table. You do need to specify the columns and their data types:

```sql
CREATE TABLE (table_name) (
  (col_1) INT AUTO_INCREMENT PRIMARY KEY, -- specifies a primary key number that goes up with each record. This is needed.
  (col_2) (data_type) NOT NULL, -- you may wish to specify that certain columns cannot be left empty
  (col_3) VARCHAR(255) -- this tells you that the data type is a string of up to 255 characters
);
```

- `SHOW TABLES;` - displays all tables in the currently active statement
- `DESCRIBE (table)` or `DESC (table)` - gives a detailed view of the table's columns and data types
- `ALTER TABLE (table)` - can be used to change columns in the table - adding, renaming, changing data types, removing columns

```sql
ALTER TABLE book_inventory
ADD page_count INT; -- creates a new column for the page count
```

- `DROP TABLE (table_name);` - removes the table

When we run `SHOW DATABASES;` in the VM, we see these:

![image](https://github.com/user-attachments/assets/fc5a8b1e-d78b-4f4e-8ab2-05376d11d227)

**[Task 4, Question 1] Using the statement you've learned to list all databases, it should reveal a database with a flag for a name; what is it?** - `THM{575a947132312f97b30ee5aeebba629b723d30f9}`

From here, we may run `use task_4_db;` to switch to the database for Task 4, and then run `SHOW TABLES;` to see a list of all the tables that we have in there.

![image](https://github.com/user-attachments/assets/57bcec4e-c60c-475c-8332-b09a99b9e059)

**[Task 4, Question 2] In the list of available adtabases, you should also see the `task_4_db` database. Set this as your active database and list all tables in this database; what is the flag present here?** - `THM{692aa7eaec2a2a827f4d1a8bed1f90e5e49d2410}`

## [Task 5] CRUD Operations

CRUD is an acronym that stands for Create, Read, Update, and Delete. These are the basic operations in a system that manages data. As you'd expect, then, MySQL allows us to do all of these things.
- Create: `INSERT INTO table_name (column1, column2, column3, ...) VALUES (value1, value2, value3, ...)`
  - Example: `INSERT INTO books (id, name, published_date, description) VALUES (1, ...)`
  - This inserts a new record containing the information in each column.
- Read: `SELECT` allows you to retrieve data from columns.
  - The simplest form would be `SELECT * FROM table`, e.g. `SELECT * FROM books;` - this retrieves all data
  - You can specify columns: `SELECT name, description FROM books;` will return only data from these two columns
- Update: `UPDATE table_name SET column = value_we_want_to_set WHERE condition;`
  - Example: `UPDATE books SET description = "..." WHERE id = 1;` will change the description of the book with ID 1.
  - Modifies an existing record in a table
- Delete: Removes records from a table. `DELETE FROM table WHERE condition;`
  - Example: `DELETE FROM books WHERE id = 1;` will delete the book with ID 1.
  - Do not run this command in the attached VM as this will affect later tasks.

In brief:
- The INSERT statement is used to create/add new records.
- The SELECT statement is used to read/retrieve records.
- The UPDATE statement is used to update/modify records.
- The DELETE statement is used to delete/remove records.

Now let's `use tools_db;`. Once we run this command, we'll want to check the data in `hacking_tools`. We can start by figuring out what's in this table - just run `DESCRIBE hacking_tools;`.

![image](https://github.com/user-attachments/assets/e9bdcaa3-49aa-4d7c-9e0b-237085193da9)

So now we know what we'd like to find. We could start by running `SELECT name, description FROM hacking_tools;` and seeing what comes up.

![image](https://github.com/user-attachments/assets/318fa34b-1539-469c-87b8-abf1ac633c81)

The tool we're looking for is Wi-Fi Pineapple.

**[Task 5, Question 1] Using the `tools_db` database, what is the name of the tool in the `hacking_tools` table that can be used to perform man-in-the-middle attacks on wireless networks?** - Wi-Fi Pineapple

To get the categories, we run `SELECT name, category FROM hacking_tools;`.

![image](https://github.com/user-attachments/assets/35d90324-1394-4abe-8763-add28d126253)

It appears the USB Rubber Ducky and Bash Bunny are both under the USB Attacks category.

**[Task 5, Question 2] Using the `tools_db` database, what is the shared category for both USB Rubber Ducky and Bash Bunny?** - USB attacks

## [Task 6] Clauses

Clauses help specify the criteria of the data being manipulated, usually by some initial statement. These define the type of data and how it should be retrieved and sorted. We use clauses like `FROM` to specify the table we are accessing, and `WHERE` to specify the records to use.

Here are some other clauses:
- `DISTINCT` is used to avoid duplicate records when doing a query, returning only unique values. Example: `SELECT DISTINCT name FROM books;` will only return the distinct, unique names in the book column.
- `GROUP BY` is used to aggregate data from many records and group query results in columns. These are primarily used with aggregating functions. Example: `SELECT name, COUNT(*) FROM books GROUP BY name;` will group records by the count function.
- `ORDER BY` is used to sort records returned by a query into ascending (ASC) or descending (DESC) order. Example: `SELECT * FROM books ORDER BY published_date ASC;` returns all records in ascending order.
- `HAVING` is used with other clauses to filter groups or results of records based on conditions. In the case of `GROUP BY`, it evaluates the condition to TRUE or FALSE. Example: `SELECT name, COUNT(*) FROM books GROUP BY name HAVING name LIKE '%Hack%';` returns books with names that contain "Hack" and the proper count.

Now let's get the distinct categories in `hacking_tools`. To do this we run `SELECT DISTINCT category FROM hacking_tools;`.

![image](https://github.com/user-attachments/assets/133ae524-005c-46c3-9737-a4998ec7d415)

We see there are six rows, so that's how many distinct categories there are.

**[Task 6, Question 1] Using the `tools_db` database, what is the total number of distinct categories in the `hacking_tools` table?** - 6

We can run `SELECT * FROM hacking_tools ORDER BY name ASC;` to get the names in ascending order. We could also specify `name` in the SELECT part of the statement so it only has the information we care about:

![image](https://github.com/user-attachments/assets/0d3d059d-759d-40fd-9c7e-cf8b85e22a3b)

**[Task 6, Question 2] Using the `tools_db` database, what is the first tool (by name) in ascending order from the `hacking_tools` table?** - Bash Bunny

And we may as well show off the `DESC` keyword. You could've answered this by looking at the _last_ name in the list above, though!

![image](https://github.com/user-attachments/assets/e55e5fd5-09fa-4e79-8e4a-73e286361661)

**[Task 6, Question 3] Using the `tools_db` database, what is the first tool (by name) in descending order from the `hacking_tools` table?** - Wi-Fi Pineapple

## [Task 7] Operators

We use operators in SQL to deal with logic, comparisons, and effective filtering/manipulation of data. These help us create more precise and powerful queries.
- Logical operators test the truth of a condition and return TRUE or FALSE.
  - `LIKE`: Used in conjunction with clauses such as `WHERE` to filter for specific patterns, e.g., `WHERE description LIKE "%guide%"`
  - `AND`: Combines multiple conditions in a query and returns TRUE if all of them are true.
  - `OR`: Combines multiple conditions and returns TRUE if at least one of these conditions is true.
  - `NOT`: Reverses the value of a Boolean operator, letting us exclude specific conditions
  - `BETWEEN`: Lets us test if a value exists within some defined range, e.g. `BETWEEN 2 AND 4`
- Comparison operators compare values and check if they meet certain criteria.
  - `=` (equals): Checks two expressions and determines if they are equal, or if a value matches another one.
  - `!=` (not equals): Checks expressions and determines if they are not equal, i.e., if they are different.
  - `<` (less than): Checks if one expression has a value less than another one. Can even work with dates.
  - `>` (greater than): Checks if one expression has a value greater than another one.
  - `<=` (less than or equal to) and `>=` (greater than or equal to) combines the comparison and equality operators. When used with dates, it can be used to return values on a specific date, as well as those before/after.

Now let's make use of these operators. For this question, we know the _category_ will be `Multi-tool`, so we can include `WHERE category = 'Multi-tool'` in our statement. We also know that the description mentions it'll be useful for `pentesters` and `geeks`, so we can also write `AND description LIKE '%geek%'`. One query might be `SELECT name, description FROM hacking_tools WHERE category = 'Multi-tool' AND description LIKE '%geek%';`.

![image](https://github.com/user-attachments/assets/c85b9411-6d59-4b54-8b00-411169be933e)
 
**[Task 7, Question 1] Using the `tools_db` database, which tool falls under the `Multi-tool` category and is useful for `pentesters` and `geeks`?** - Flipper Zero

For this question, we're looking for categories where the amount >= 300. So one query might be `SELECT category FROM hacking_tools WHERE amount >= 300;`.

![image](https://github.com/user-attachments/assets/2976a589-0712-4cc0-aabc-a4bc8b7a2aab)

**[Task 7, Question 2] Using the `tools_db` database, what is the category of tools with an amount greater than or equal to `300`?** - RFID cloning

And for this last one, we're looking for a Network Intelligence tool where the amount is less than 100. In this case, we'll run `SELECT name FROM hacking_tools WHERE category = 'Network intelligence' AND amount < 100;`.

![image](https://github.com/user-attachments/assets/98233da8-c015-4184-abb9-5f7b577016ad)

**[Task 7, Question 3] Using the `tools_db` database, which tool falls under the `Network intelligence` category with an amount less than `100`?** - Lan Turtle

## [Task 8] Functions

Lastly, we have functions in SQL that allow us to streamline queries and manipulate data.
- String functions let us do operations on a string and return values associated with it.
  - `CONCAT(str1, str2, ...)` lets us add two or more strings together, e.g. combining text from different columns.
  - `GROUP_CONCAT(name SEPARATOR ", ") AS field` lets us concatenate data from many rows into one field.
  - `SUBSTRING(column, number_of_starting_char, length)` lets us get a substring within a string from a certain position and with a specified length.
  - `LENGTH()` returns the number of characters in a string, including spaces and punctuation.
- Aggregate functions aggregate the value of many rows within one specified criteria in the query. It combines many results into one.
  - `COUNT()` returns the number of records within an expression.
  - `SUM(column)` sums all non-NULL values of a determined column.
  - `MAX(column)` calculates the maximum value in a given column.
  - `MIN(column)` calculates the minimum value in a given column.

For this question, we'll get the names by using the `LENGTH()` function. In this case, we might run the query `SELECT name, LENGTH(name) FROM hacking_tools ORDER BY LENGTH(name) DESC;`.

![image](https://github.com/user-attachments/assets/92cf0824-20ec-4454-95b1-81d804b865b7)
 
**[Task 8, Question 1] Using the `tools_db` database, what is the tool with the longest name based on character length?** - USB Rubber Ducky

I assume this refers to the amount of tools. In this case, we'd run `SELECT SUM(amount) FROM hacking_tools;` to get the total sum of tools.

![image](https://github.com/user-attachments/assets/b7df593e-69bb-42a5-a6fa-589cf2dc0819)

**[Task 8, Question 2] Using the `tools_db` database, what is the total sum of all tools?** - 1444

This question is a little specific, but here goes! We want the amounts that don't end in 0, so we'll need to include `WHERE amount NOT LIKE '%0'` - this excludes any entries where the amount _does_ end in 0. The question suggests we should use GROUP_CONCAT() with the & operator as a separator, so let's set up the query: `SELECT GROUP_CONCAT(name SEPARATOR " & ") AS tools FROM hacking_tools WHERE amount NOT LIKE '%0';`.

![image](https://github.com/user-attachments/assets/bc194cee-be32-4426-acb3-b193fb70bd06)

**[Task 8, Question 3] Using the `tools_db` database, what are the tool names where the amount does not end in `0`, and group the tool names concatenated by `&`.** - Flipper Zero & iCopy-XS

## [Task 9] Conclusion

This room helped illustrate the importance of databases in computing - we interact with them whenever we're browsing the web, and so learning the fundamentals is incredibly important. We learned about how databases work, what kinds of databases are there, and all sorts of SQL statements that can be used to manage databases and manipulate data.
