---
title: "How To Install MariaDB On Ubuntu 18.04"
date: "2022-09-21"
title_meta: "MariaDB installtion guide on Ubuntu 18.04"
description: "Learn how to install MariaDB on Ubuntu 18.04 with this comprehensive guide. Follow these step-by-step instructions to set up MariaDB, a popular MySQL alternative, on your Ubuntu 18.04 system for efficient database management and development.
"
keywords: ["install MariaDB Ubuntu 18.04", "MariaDB setup Ubuntu 18.04", "Ubuntu 18.04 MariaDB installation guide", "MySQL alternative Ubuntu", "Ubuntu MariaDB tutorial", "MariaDB installation steps Ubuntu 18.04", "database management Ubuntu", "MariaDB Ubuntu 18.04 instructions"]


tags: ["MariaDB", "ubuntu"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Ubuntu/how-to-install-mariadb-on-ubuntu-18-04/']
tab: true
---

![](images/How-To-Install-MariaDB-On-Ubuntu-18.04_utho.jpg)

**Description**

A wide variety of applications, including data warehousing, e-commerce, enterprise-level functionality, and logging programmes, make use of the MariaDB database. MariaDB will let you to fulfil all of your burden in an effective manner; it can function in any cloud database and can function at any scale, whether it be little or huge. What exactly is a database, then?

**Note**  
This walkthrough is intended for users who do not have root privileges. The prefix sudo is added to commands that need to be run with elevated privileges. Check out our Users and Groups guide if you aren't familiar with the sudo command and want to learn more about it.

## Set up and install MariaDB

```
#sudo apt install mariadb-server 
```

**Root Login**

```
#sudo mysql -u root -p
```

then provide the password for mariadb

```
MariaDB [(none)]>
```

Providing Protection for the Setup

After you have logged into MariaDB as the root user of your database, activate the mysql native password plugin to enable root password authentication.

```
#USE mysql;
```

```
#UPDATE user SET plugin='mysql_native_password' WHERE user='root';
```

```
#FLUSH PRIVILEGES;
```

```
#exit;
```

Execute the mysql secure installation script in order to address a number of security concerns that are present in a default MariaDB installation:

```
#sudo mysql_secure_installation
```

You can change MariaDB's root password, remove anonymous user accounts, disable root logins outside localhost, and delete test databases. Yes is recommended. MariaDB's Knowledge Base has more on the script.

## Create a New User and Database in MariaDB

log in in again to the database. If you set a password above, type it in when asked.

```
#sudo mysql -u root -p
```

Below is an example in which testdb is the name of the database, testuser is the name of the user, and password is the password for the user. You are strongly encouraged to switch to a more secure password:

```
#CREATE DATABASE testdb;
```

```
#CREATE user 'testuser'@localhost IDENTIFIED BY 'password';
```

```
#GRANT ALL ON testdb.* TO 'testuser' IDENTIFIED BY 'password';
```

This procedure can be streamlined by creating the user and assigning database permissions simultaneously:

```
#CREATE DATABASE testdb;
```

```
#GRANT ALL ON testdb.* TO 'testuser' IDENTIFIED BY 'password';
```

```
exit;
```

## Reset the MariaDB Root Password

If you forget your root password for MariaDB, you can change it.

Stop the MariaDB server instance currently running.

```
#sudo systemctl stop mariadb
```

Then run the following command, which will let the database start up without loading the grant tables or networking.

```
#sudo systemctl set-environment MYSQLD_OPTS="--skip-grant-tables --skip-networking"
```

## Restart MariaDB:

```
#sudo systemctl start mariadb
```

Sign in to the MariaDB server with the root account, but don't give a password this time:

```
#sudo mysql -u root
```

To change root's password, enter the following commands. Swap out your weak password for a secure one:

```
#FLUSH PRIVILEGES;
```

```
#UPDATE mysql.user SET password = PASSWORD('password') WHERE user = 'root';
```

Make necessary adjustments to the authentication procedures for the root password:

```
#UPDATE mysql.user SET authentication_string = '' WHERE user = 'root';
```

```
#UPDATE mysql.user SET plugin = '' WHERE user = 'root';
```

```
#exit;
```

Change the environment settings back to the way they were so that the database can start with grant tables and networking:

```
#sudo systemctl unset-environment MYSQLD_OPTS
```

Then restart MariaDB:

```
#sudo systemctl restart mariadb
```

You should now be able to use your new root password to get into the database:

```
#sudo mysql -u root -ps
```

We hope these are helpful,

Thank you
