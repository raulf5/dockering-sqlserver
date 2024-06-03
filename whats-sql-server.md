# What's SQL Server

### Intro

Microsoft SQL Server is a relational [database management system](https://es.wikipedia.org/wiki/Sistema\_de\_gesti%C3%B3n\_de\_bases\_de\_datos) (RDBMS). Applications and tools connect to a SQL Server _instance_ or _database_, and communicate using [Transact-SQL](https://learn.microsoft.com/en-us/sql/t-sql/language-reference?view=sql-server-ver16) (T-SQL).

### Versions

Last Version is [SQL Server 2022](https://learn.microsoft.com/en-us/sql/sql-server/editions-and-components-of-sql-server-2022?view=sql-server-ver16\&preserve-view=true).

### Editions

There are 5 edition levels and each of the levels are geared towards different sized companies/applications.  Below is a short description of each edition.

* **Enterprise** - contains all features with high end datacenter capabilities needed in large enterprises
* **Standard** - has basic data management which is geared towards departments or small companies
* **Web** - is a low cost option that web hosting companies can offer to their customers
* <mark style="color:orange;">**Developer**</mark> - contains all of the functionality in enterprise edition, but is only licensed for development and test systems.  This is an ideal version for developers who build applications (this is the default).
* **Express** - free entry level database ideal for learning how to build a data driven application or for very small databases

### SQL Server Architecture

The engine of the database instance can be broken down into two parts. The Relational Engine and the Storage Engine.&#x20;

* The **Relational Engine** handles the execution of queries as they are received from client applications.  This engine has the following components: Query Parser, Optimizer/Planner, and Executor.&#x20;
* The **Storage Engine** handles the actual accessing of the data as requested by the query executor.  Within this engine there is a transaction manager and buffer manager which interact with the data and log files to select/insert/update/delete data as required.

**Databases**

Each SQL Server instance contains 4 or more databases which are, at a high level, simply a logical collection of objects.  These objects can include tables, indexes, views, users, etc.  The initial databases that exist are the system databases: master, model, msdb and tempdb. &#x20;

* The **master** database contains all the server configuration settings,
* the **model** database serves as a template that is used when user databases are created,
* the **msdb** database contains information related to database backups, replication, SSIS packages and all the configuration information for any SQL Server jobs that exist on the server
* and finally the **tempdb** database stores any temporary objects that are required by the system.&#x20;

**Database Files**

Each of these databases is made up of at least 2 files. &#x20;

* The first is the primary datafile which stores the objects that make up the database.&#x20;
* &#x20;The second is the transaction log file which holds all the information related to any changes made to the database. &#x20;

There can be one or more of each of these types of files although having multiple transaction logs won't really help performance as this file is written to sequentially. &#x20;

Data files on the other hand can benefit from having multiple files.  For example, by separating table and index data into different data files you can speed up performance as these files can then be accessed in parallel when executing a query.  Both of these files are stored on server's filesystem and can be placed where required by specifying the file path when the database is created.&#x20;

<img src="https://www.mssqltips.com/tutorialimages/9214_sql_server_101_tutorial.002.png" alt="sql server database files" data-size="original">

**Database Objects**

As mentioned above, each database is a collection of different objects.  Below is a brief description of the most common objects that you will find in a SQL Server database.

* **User/Login** - Sometimes used interchangeably in SQL Server they are in fact two different things.  A login is used for authentication to the database instance whereas a user is used for database/object access.  Each login will have an associated database user (most often with the same name).
* **Role** - An object that contains a set of permissions for objects within the database.  Can be granted to users to ease administration by not having to grant individual permissions to each user.
* **Schema** - Schema can have many different meanings in a database context but here it can be described as something that holds a collection of objects, kind of like a container or namespace.  Other objects like tables, indexes, function, etc. can be grouped logically and assigned to a schema on creation.
* **Table** - Object that holds the relational data.
* **Constraint** - These are rules for the data columns in a table.  Types of constraints are primary key, foreign key, default, etc.
* **Index** - This is a structure that can be used to speed up accessing data in a table.  Specified columns make up the index and, when specified in a query, can help to speed up performance as the indexed column will contain a pointer to where the rest of the data is located that makes up that row.
* **Statistics** - Statistics are a set of data that describes the data distribution and cardinality of the columns in a table.  These statistics are used by the planner in order to determine the best/fastest method of accessing the table in order to execute a query.
* **View** - A view is basically a stored query.  Sometimes a query can be so complex it is created a view so that other connections can just write "SELECT \* FROM view".  Note that the actually result set from the query is not stored/cached (this is done for materialized views), only the query text, so each call to the view results in the entire query being executed.
* **Stored Procedure/Function** - Both of these objects, as you would assume, are objects that store a collection of SQL statements and other than this one similarity they do not have much in common.  Some of the key differences are: Functions can only run SELECT and a stored procedure can also run DELETE/INSERT/UPDATE, stored procedures can have both input and output parameters whereas functions can only have input, functions can be used in one or many parts of a SELECT query and stored procedures cannot be used, stored procedures can optionally return a value whereas function must return a value, etc.

![sql server database objects](https://www.mssqltips.com/tutorialimages/9214\_sql\_server\_101\_tutorial.003.png)\
