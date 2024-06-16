---
title: "How To Install MariaDB on Ubuntu 22.04"
date: "2022-09-16"
---

![](images/How-To-Install-MariaDB-on-Ubuntu-22.04_utho.jpg)

How To Install MariaDB on Ubuntu 22.04

**Introduction**

MariaDB is a relational database management system that is available as open source software. It is frequently utilised as a substitute for MySQL as the database component of the widely used LAMP (Linux, Apache, MySQL, PHP/Python/Perl) stack of software. It is designed to be a direct replacement for MySQL in all respects.

The abridged version of this tutorial to the installation process is comprised of the following three steps:

Using apt, bring your package index up to date.  
The mariadb-server package should be installed using apt. Additionally, associated tools that interface with MariaDB are fetched and included by the package.  
Run the included mysql\_secure\_installation security script to restrict access to the server

```
#sudo apt update
```  
```
#sudo apt install mariadb-server
```  
```
#sudo mysql_secure_installation
```

This guide will show you how to install MariaDB on an Ubuntu 22.04 server, check that it is up and running, and set it up securely.

**Prerequisites**

For this tutorial to work, a server running Ubuntu 22.04 is required. This server needs a firewall set up with UFW and a non-root administrative user. Follow our basic server setup guide for Ubuntu 22.04 to set this up.

## Step 1 — Installing MariaDB

Ubuntu 22.04's APT repositories feature MariaDB 10.5.12.

To install, update your server's apt package index:

```
#sudo apt update
```

Then install the package:

```
#sudo apt install mariadb-server
```

The MariaDB server will be installed after you execute these commands; however, you will not be prompted to choose a password or make any other configuration modifications. You will use a script that is provided by the mariadb-server package in order to restrict access to the server and remove unnecessary accounts. This is necessary since your installation of MariaDB would be insecure if it is left with the default settings.

## Step 2 — Configuring MariaDB

Running the security script that is included is the next step for fresh MariaDB instals. This script modifies a few of the less secure default settings for things like sample users and remote root logins.

Activate the security script:

```
#sudo mysql_secure_installation
```

This will lead you through a series of prompts where you will have the opportunity to make some modifications to the security parameters that are associated with your MariaDB installation. At the first step, you will be required to enter the root password for the database that is currently being used. Because you have not established one of these yet, use the ENTER key to indicate that there is none.

```
output  
NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB

Output  
NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB  
SERVERS IN PRODUCTION USE! PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, you'll need the current  
password for the root user. If you've just installed MariaDB, and  
you haven't set the root password yet, the password will be blank,  
so you should just press enter here.

Enter current password for root (enter for none):
```

If you want to switch to unix socket authentication, you will be prompted. You can skip this step since you already have a protected root account. After entering n, hit ENTER.

```
output  
. . .  
Setting the root password or using the unix_socket ensures that nobody  
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] n
```

You are prompted to create a database root password in the following window. You should avoid changing the configured authentication methods for the root account for MariaDB on Ubuntu because it is tightly linked to automated system upkeep.

The removal of access to the administrative account would enable a package update to destroy the database system. Press ENTER after you type "n".

```
output  
. . .  
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB  
root user without the proper authorisation.

Set root password? [Y/n] n
```

If socket authentication isn't acceptable for your use case, you'll set up a second administrative account for password access.

From there, press Y and then ENTER to accept all defaults. This removes anonymous users and the test database, disables remote root logins, and loads new rules so MariaDB applies your modifications.

You've finished configuring MariaDB's security. The next step is optional, but you should do it if you want to password-protect MariaDB.

## Step 3 — (Optional) Creating an Administrative User that Employs Password Authentication

The root MariaDB user is configured by default to authenticate using the unix socket plugin rather than a password on Ubuntu systems running MariaDB 10.5. In many cases, this improves security and usability, but it can also make things more difficult when you need to grant administrative rights to an outside programme, like phpMyAdmin.

It is preferable to avoid changing the root account's login information because the server uses it for operations like log rotation and starting and terminating the server. Changing the password in the /etc/mysql/debian.cnf configuration file might initially succeed, but future package updates might overwrite those modifications. The package maintainers advise making a different administrative account with password-based access in instead of changing the root account.

To do this, a new account called admin will be created and given the same permissions as the root account but with password authentication set up. Open the MariaDB command window from your

```
#sudo mariadb
```

Create a new user with root capabilities and restrict access to that user with a password. Ensure that the username and password are updated to reflect your preferences by doing the following:

```
#GRANT ALL ON *.* TO 'admin'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;  

```

flush the privileges so that they can be saved and used for the current session:

```
#FLUSH PRIVILEGES;
```

After that, close the MariaDB shell:

```
#exit
```

## Step 4 — Testing MariaDB

MariaDB will launch automatically after installation from the default repositories. Check the status of that to test this.

```
#sudo systemctl status mariadb
```

```
output  
● mariadb.service - MariaDB 10.5.12 database server  
Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor preset: enabled)  
Active: active (running) since Fri 2022-03-11 22:01:33 UTC; 14min ago  
Docs: man:mariadbd(8)  
https://mariadb.com/kb/en/library/systemd/  
. . .
```

With the command sudo systemctl start mariadb, you can start MariaDB if it isn't already running.

The mysqladmin tool, a client that enables you to issue administrative commands, can be used to establish a connection to the database as an additional check. As an illustration, the following command instructs you to connect to MariaDB as root using a Unix socket and return the version:

```
#sudo mysqladmin version
```

```
output  
mysqladmin Ver 9.1 Distrib 10.5.12-MariaDB, for debian-linux-gnu on x86_64  
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Server version 10.5.12-MariaDB-1build1  
Protocol version 10  
Connection Localhost via UNIX socket  
UNIX socket /run/mysqld/mysqld.sock  
Uptime: 15 min 53 sec

Threads: 1 Questions: 482 Slow queries: 0 Opens: 171 Open tables: 28 Queries per second avg: 0.505
```
