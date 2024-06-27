---
title: "How To Import and Export Databases in MySQL or MariaDB"
date: "2022-09-15"
title_meta: "Guide to import Export Database"
description: "Database import and export is a common task in software development. Data dumps can be used to backup and restore your information. They are also useful for migrating data to a new server or development environment."

keywords: ['MySQL 5.7', 'CentOS 7', 'EOL', 'security risk', 'database server', 'MariaDB']

tags: ["SQL", "CentOS", "Export"]
icon: "mysql"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Database/MY SQL/how-to-import-and-export-databases-in-mysql-or-mariadb/']
tab: true
---

![](images/How-To-Import-and-Export-Databases-in-MySQL-or-MariaDB_utho.jpg)

**Introduction**

Database import and export is a common task in software development. Data dumps can be used to backup and restore your information. They are also useful for migrating data to a new server or development environment.

In this tutorial, you will work with MySQL or MariaDB database dumps (the commands are interchangeable). You will specifically export a database and then import that database from the dump file.

**Prerequisites**

You will need the following tools to import or export a MySQL or MariaDB database:

\*A virtual machine with a sudo user that is not root. If you require a server, visit this page to create a microhost Droplet running your preferred Linux distribution. Following creation, select your distribution from this list and proceed with our Initial Server Setup Guide.

\*MySQL or MariaDB must be installed. Follow our How To Install MySQL tutorial to install MySQL. Follow our How To Install MariaDB tutorial to get started.

\*In your database server, create a sample database. To make one, go to "Creating a Sample Database" in our "An Introduction to Queries in MySQL" tutorial.

**Note**: Instead of manually installing MySQL, you can use the DigitalOcean Marketplace's MySQL One-Click Application.

**Step-1 The First Step — Exporting a MySQL or MariaDB Database**

The console utility mysqldump exports databases to SQL text files. This facilitates the transfer and relocation of databases. You will need the name of your database as well as credentials for an account with at least full read-only access to the database.

To export your database, use mysqldump:

```
#mysqldump -u username -p database_name > data-dump.sql
```

_username is the username with which you can access the database.  
_The database to export is called database name.  
\*The output is stored in the data-dump.sql file located in the current directory.

Although the command won't output anything visually, you can examine the contents of data-dump.sql to determine whether it's a genuine SQL dump file.

Run the command line:

```
#head -n 5 data-dump.sql
```

The top of the file should look like this, with a MySQL dump for the database database name.

```
SQL dump fragment  
-- MySQL dump 10.13 Distrib 5.7.16, for Linux (x86_64)  
-- 
-- Host: localhost Database: database_name  
-- ------------------------------------------------------ 
-- Server version 5.7.16-0ubuntu0.16.04.1
```

Mysqldump will print any errors to the screen if they arise during the export procedure.

**Step 2 — Importing a MySQL or MariaDB Database**

You must build a new database if you want to import an existing dump file into MySQL or MariaDB. The imported data will be stored in this database.

In order to create new databases, you must first log into MySQL as root or another user who has the necessary rights:

```
#mysql -u root -p
```

This command will launch the MySQL shell prompt. Then, run the following command to create a new database. The new database in this example is called new database:

```
mysql> #CREATE DATABASE new_database;
```

This result verifies the construction of the database.

```
Output  
Query OK, 1 row affected (0.00 sec)
```

Then, using CTRL+D, exit the MySQL shell. You can import the dump file from the command line using the following command:

```
#mysql -u username -p new_database < data-dump.sql
```

_username is the username with which you can access the database.  
_The newly established database is known by the name newdatabase.  
\*The data dump file to be imported is called data-dump.sql, and it can be found in the current directory.

If the command is executed successfully, no output is produced. If any errors arise during the process, mysql will instead print them to the terminal. Log in to the MySQL shell and inspect the data to see if the import was successful. Selecting the new database with USE new database and then viewing some of the data with SHOW TABLES; or a similar command.
