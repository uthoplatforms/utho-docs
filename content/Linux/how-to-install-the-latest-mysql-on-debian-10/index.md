---
title: "How To Install the Latest MySQL on Debian 10"
date: "2022-09-15"
---

![](images/How-To-Install-the-Latest-MySQL-on-Debian-10_utho.jpg)

**Introduction**

MySQL is a popular open-source database management system used for many applications. MySQL is part of the LAMP stack, which comprises Linux, Apache, and PHP.

MariaDB is the default MySQL variant in Debian 10. If you need functionality only available in Oracle's MySQL, you can install packages from a MySQL developer repository.

In this lesson, you'll add the MySQL repository, install MySQL, secure the install, and test that MySQL is running and responding to commands.

**Prerequisites**

You will need one Debian 10 server with a non-root user with sudo privileges and a firewall configured before beginning this tutorial.

## **Step-1 Including the MySQL Software Repository in the compilation  
**

MySQL developers provide a.deb package for configuring and installing software repositories. Once the repositories are set up, you can install software using apt.

Install GnuPG, an open-source OpenPGP implementation, first.

Update the local package index with upstream modifications.

```
#sudo apt update
```

Installing the gnupg package comes next:

```
#sudo apt install gnupg
```

APT will install gnupg and its dependencies after you confirm the installation.

The MySQL.deb package will then be downloaded with wget and installed with the dpkg command.

Open your web browser and navigate to the MySQL download page. Locate the Download button in the lower-right corner and proceed to the next page. This page will ask you to log in or create an Oracle web account. You can skip that and go straight to the No thanks, just start my download link. Copy the link address by right-clicking it (this option may be worded differently, depending on your browser).

You will now download the file. On your server, navigate to a writeable directory, such as the temporary /tmp directory used in this example:

```
#cd /tmp
```

Then, use the wget programme to download the file, being sure to replace the highlighted area with the location you copied:

```
#wget https://dev.mysql.com/get/mysql-apt-config_0.8.22-1_all.deb
```

The file should now be in your current directory. List the files you want to confirm:

```
#ls
```

The file you just downloaded will be included in the output list of files, as seen in the example below, which is highlighted:

```
Output  
. . .  
mysql-apt-config_0.8.22-1_all.deb  
. . .
```

You are now ready to begin installation. Run the dpkg command to install, uninstall, and inspect.deb software packages. The -i option specifies that you want to install from the specified file:

```
#sudo dpkg -i mysql-apt-config*
```

You'll be given a configuration screen during the installation where you can choose the version of MySQL you'd like to use and choose to install repositories for additional MySQL-related utilities. The defaults will only add the repository details for the most recent stable version of MySQL. Use the down arrow to get to the Ok menu choice and press ENTER to select this for our purposes.

The repository will now be fully added by the package. To make the new software packages available, you must update your apt package cache:

```
#sudo apt update
```

You're ready to install the MySQL server software now that you've added the MySQL repositories. If you need to update the configuration of these repositories, run sudo dpkg-reconfigure mysql-apt-config, then sudo apt-get update to refresh your package cache.

## **Step 2 — Installing MySQL**

You may now use apt to install the most recent MySQL server package after adding the repository and after your package cache has been recently updated:

```
#sudo apt install mysql-server
```

apt will scan all available mysql-server packages and determine that the package provided by MySQL is the most recent and best candidate. It will then calculate package dependencies and request your approval before proceeding with the installation. Enter y followed by ENTER. The software will be downloaded.

During the configuration phase of the installation, you will be prompted to enter a root password. To proceed, enter and confirm a secure password. Following that, you will be prompted to select a default authentication plugin. To understand the options, read the display. If you are unsure, select Use Strong Password Encryption.

MySQL should now be installed and operational. Check with systemctl:

```
#sudo systemctl status mysql
```

```
Output  
● mysql.service - MySQL Community Server  
Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: en  
Active: active (running) since Thu 2022-02-24 18:59:22 UTC; 23min ago  
Docs: man:mysqld(8)  
http://dev.mysql.com/doc/refman/en/using-systemd.html  
Main PID: 3722 (mysqld)  
Status: "Server is operational"  
Tasks: 38 (limit: 4915)  
Memory: 371.7M  
CGroup: /system.slice/mysql.service  
└─3722 /usr/sbin/mysqld

Feb 24 18:59:21 sql-debian systemd[1]: Starting MySQL Community Server...  
Feb 24 18:59:22 sql-debian systemd[1]: Started MySQL Community Server.
```

MySQL is installed and operating as indicated by the Active: active (running) line. You'll add a little additional security to the installation in the following step.

## **Step 3 — Securing MySQL**

For some security-related updates on your fresh install, MySQL includes a command. Run it right away:

```
#mysql_secure_installation
```

This will ask for your MySQL root password. Enter it. Now answer yes/no questions. Recap:

First, you're asked about the validate password plugin, which enforces password restrictions for MySQL users. Enable this based on your security needs. Enter y to enable or skip it. If enabled, you'll be asked to choose a password validation level from 0–2. Press ENTER to continue.

You'll be asked to change the root password. Since you just installed MySQL, you can skip this. Continue without changing the password.

The rest are true. You must remove anonymous MySQL users, disable remote root logins, delete the test database, and reload privilege tables to verify the changes take effect. Good concepts. Enter each y.

All prompts will end the script. Now MySQL is secure. Next, launch a client that connects to the server and returns data.

## **Step 4 – Testing MySQL**

The MySQL administrative client for the command line is called mysqladmin. It will allow you to connect to the server and output details about the version and status. Mysqladmin is told to log in as the MySQL root user by the -u root, -p informs the client to request a password, and version is the actual command you want to execute:

```
#mysqladmin -u root -p version
```

The output will show the MySQL server's version, uptime, and other status information as in the examples below:

```
Output  
mysqladmin Ver 8.0.28 for Linux on x86_64 (MySQL Community Server - GPL)  
Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its  
affiliates. Other names may be trademarks of their respective  
owners.

Server version 8.0.28  
Protocol version 10  
Connection Localhost via UNIX socket  
UNIX socket /var/run/mysqld/mysqld.sock  
Uptime: 25 min 31 sec

Threads: 2 Questions: 20 Slow queries: 0 Opens: 143 Flush tables: 3 Open tables: 62 Queries per second avg: 0.013
```

This output confirms that you installed and secured the most recent MySQL server successfully.
