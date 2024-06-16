---
title: "How To Migrate a MySQL Database Between Two Servers"
date: "2022-09-15"
---

![](images/How-To-Migrate-a-MySQL-Database-Between-Two-Servers_utho.jpg)

SCP (Secure Copy), a method of transferring files derived from the SSH Shell, can be used to transfer databases across virtual private servers. Remember that both of the virtual servers' passwords must be known.

There are two steps in the database migration process:

## **Step-1 The first thing you need to do is run a MySQL dump.**

We need to use the mysqldump command to create a backup copy of the database file on the primary virtual server before we can move it to the new virtual private server (VPS).

```
#mysqldump -u root -p --opt [database name] > [database name].sql
```

You can now transfer the database after running the dump.

## **Step-2 The second step is to copy the database.**

The database can be copied with the assistance of SCP. If you used the command that came before it, you exported your database to the folder in your home directory.

The following syntax is utilised by the SCP command:

```
#scp [database name].sql [username]@[servername]:path/to/database/
```

A sample transfer might look like this:

```
scp newdatabase.sql user@example.com:~/
```

The database will be moved to the new virtual private server after you login.

## **Step.3 Import the Database, which is the third step.**

After the information has been moved to the new server, you will be able to import the database into MySQL as follows:

```
#mysql -u root -p newdatabase < /path/to/newdatabase.sql
```

After this, the transfer that you were working on using SCP will be finished.
