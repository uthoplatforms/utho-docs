---
title: "How To Install MariaDB on Debian 11"
date: "2022-09-16"
title_meta: "GUIDE How to install MariaDB on Debian 11"

description: "A comprehensive guide on how to install MariaDB, a popular open-source relational database system, on Debian 11."

keywords: ['Debian 11', 'MariaDB', 'installation', 'database management', 'SQL', 'Linux']


tags: ["MariaDB"]
icon: "Debian"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Debian/how-to-install-mariadb-on-debian-11/']
tab: true
---

![](images/How-To-Install-MariaDB-on-Debian-11_utho.jpg)

How To Install MariaDB on Debian 11

**Introduction**

The popular LAMP (Linux, Apache, MySQL, PHP/Python, Perl) stack, which consists of MariaDB as the database component, is an open-source relational database management system. It is designed to replace MySQL seamlessly.

The three steps below make up the condensed version of this installation guide:

_Using apt, bring your package index up to date.  
_Using apt, install the mariadb-server package. Additionally, the package includes associated tools for interacting with MariaDB.  
\*Run the included mysql\_secure\_installation security script to restrict access to the server

```
#sudo apt update
```  
```
#sudo apt install mariadb-server
```  
```
#sudo mysql_secure_installation
```

This tutorial will show you how to install MariaDB on a Debian 11 server and ensure that it is up and running with a secure initial configuration.

**Prerequisites**

\*You will need a server running Debian 11 to complete this tutorial. This server should have a non-root administrative user and a UFW-enabled firewall. Set this up by following our Debian 11 initial server setup guide.

**Step 1 — Installing MariaDB**

As of this writing, MariaDB version 10.5.15 is available in Debian 11's default software repositories. The Debian MySQL/MariaDB packaging team has designated it as the default MySQL variant.

To install it, use apt to update your server's package index:

```
#sudo apt update
```

Then proceed to install the package:

```
#sudo apt install mariadb-server
```

These commands will install MariaDB but will not prompt you to set a password or change any other settings. Because the default configuration makes your MariaDB installation insecure, you will use a script provided by the mariadb-server package to restrict server access and remove unused accounts.

**Step 2 — Configuring MariaDB**

The following step for new MariaDB installations is to run the included security script. This script modifies some of the less secure default options for things like remote root logins and sample users.

Run the security script:

```
#sudo mysql_secure_installation
```

This will take you through a series of prompts where you can modify the security settings for your MariaDB installation. The first prompt will request your current database root password. Because you haven't yet created one, press ENTER to indicate "none."

```
Output  
NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB  
SERVERS IN PRODUCTION USE! PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, you'll need the current  
password for the root user. If you've just installed MariaDB, and  
you haven't set the root password yet, the password will be blank,  
so you should just press enter here.

Enter current password for root (enter for none):
```

You'll be asked if you want to use unix socket authentication. You can skip this step if you already have a protected root account. Enter n and then press ENTER.

```
Output  
. . .  
Setting the root password or using the unix_socket ensures that nobody  
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] n
```

The next prompt asks if you want to change the root password. Because the root account for MariaDB is closely linked to automated system maintenance in Debian 11, you should not change the configured authentication methods for that account.

By doing so, a package update could break the database system by removing access to the administrative account. Enter n followed by ENTER.

```
Output  
Change the root password? [Y/n]
```

If socket authentication isn't appropriate for your use case, you'll learn how to create an additional administrative account for password access later.

From there, you can accept the defaults for all subsequent questions by pressing Y and then ENTER. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MariaDB can immediately apply your changes.

You've now completed MariaDB's initial security configuration. The next step is optional, but you should do it if you prefer to use a password to connect to your MariaDB server.

Step.3 Establishing a New Administrative User Who Will Require a Password for Authentication

On Debian systems running MariaDB 10.5, the root MariaDB user authenticates via the unix socket plugin by default. This increases security and usability in many circumstances, but it might complicate matters when granting administrative access to an external software (e.g., phpMyAdmin).

Because the server uses the root account for log rotation and starting and stopping, don't modify its authentication information. Changing credentials in /etc/mysql/debian.cnf may work initially, but package updates may overwrite them. Instead of altering root, package maintainers advocate creating a password-protected administrator account.

To do this, we'll create an admin account with the same permissions as root but password authentication. Terminal MariaDB prompt:

```
#sudo mariadb
```

Then, make a new user with root privileges and password access. Change the username and password to reflect your preferences:

```
mariadb > #GRANT ALL ON *.* TO 'admin'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
```

To ensure that the privileges are saved and available in the current session, flush them:

```
mariadb > #FLUSH PRIVILEGES;
```

exit the MariaDB shell:

```
mariadb > #exit
```

Now that everything is in place, let's check out MariaDB.

**Step 4 — Testing MariaDB**

MariaDB will start automatically when installed from the default repositories. Check its status to see if it works.

```
#sudo systemctl status mariadb
```

You'll get output that looks somewhat like this:

```
Output  
● mariadb.service - MariaDB 10.5.15 database server  
Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor preset: enabled)  
Active: active (running) since Fri 2022-03-11 22:01:33 UTC; 14min ago  
Docs: man:mariadbd(8)  
https://mariadb.com/kb/en/library/systemd/  
. . .
```

If MariaDB isn't already running, use the command sudo systemctl start mariadb to get it going.

You can also connect to the database using the mysqladmin tool, which is a client that allows you to run administrative commands. For example, this command instructs the server to connect to MariaDB as root via a Unix socket and return the version:

```
#sudo mysqladmin version
```

You will see output that looks somewhat like this:

```
Output  
mysqladmin Ver 9.1 Distrib 10.5.15-MariaDB, for debian-linux-gnu on x86_64  
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Server version 10.5.15-MariaDB-0+deb11u1  
Protocol version 10  
Connection Localhost via UNIX socket  
UNIX socket /run/mysqld/mysqld.sock  
Uptime: 4 min 20 sec

Threads: 1 Questions: 72 Slow queries: 0 Opens: 32 Open tables: 25 Queries per second avg: 0.276
```
