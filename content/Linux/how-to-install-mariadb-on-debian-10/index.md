---
title: "How To Install MariaDB on Debian 10"
date: "2022-09-15"
---

![](images/How-To-Install-MariaDB-on-Debian-10_utho.jpg)

**Introduction**

Open-source database management system (DBMS) MariaDB is frequently used as a MySQL replacement in the well-known LAMP (Linux, Apache, MySQL, PHP/Python/Perl) stack. Designed to replace MySQL seamlessly, Debian now only includes MariaDB packages out of the box. The compatible MariaDB replacement versions will be installed in their place if you try to install packages related to the MySQL server.

The following three stages make up the abbreviated installation guide:

_Using apt, bring your package index up to date.  
_Using apt, install the mariadb-server package. Additionally, the package includes associated tools for interacting with MariaDB.  
\*To restrict access to the server, run the mysql secure installation security script that is included in the package.

```
#sudo apt update
```  
```
#sudo apt install mariadb-server
```  
```
#sudo mysql_secure_installation
```

This guide will show you how to install MariaDB version 10.3 on a Debian 10 server, then check to make sure it is up and running with a secure initial setup.

**Prerequisites**

You will require one Debian 10 server configured with a firewall and a non-root user with sudo rights in order to finish this tutorial. By following our basic server setup guide, you may set this up.

**Step 1 — Installing MariaDB**

MariaDB version 10.3 is by default available in the APT package repositories for Debian 10. The MySQL/MariaDB packaging team for Debian has designated it as the default MySQL variation.

Update your server's package index with apt to install it:

```
#sudo apt update
```

install the package:

```
#sudo apt install mariadb-server
```

These scripts will install MariaDB, but they won't ask you to modify any configuration settings or set a password. Because MariaDB's default settings make your installation insecure, you'll use a script provided by the mariadb-server package to limit server access and delete unused users.

**Step 2 — Configuring MariaDB**

Running the provided security script is the next step for fresh MariaDB installations. The default settings that are less secure are changed by this script. It will be put to use in order to eliminate unused database users and prevent remote root logins.

Activate the upcoming security script:

```
#sudo mysql_secure_installation
```

This prompts you to adjust MariaDB's security options. First, enter the database root password. Press ENTER to signify "none" since you haven't set one up.

Next, you're asked to set a database root password. Enter N. In Debian, the MariaDB root account is tied to automated system maintenance, therefore don't change its authentication methods. By removing access to the administrative user, a package update could disrupt the database system. If socket authentication isn't acceptable for your use case, we'll cover setting up a second administrative account for password access.

From there, press Y and then ENTER to accept all defaults. This removes anonymous users and the test database, disables remote root logins, and loads new rules so MariaDB respects your changes.

**Step-3 User Authentication and Privileges Changing**

In Debian 10.3 systems, the root MariaDB user authenticates using the unix socket plugin rather than a password. This increases security and usability in many circumstances, but it might complicate matters when granting administrative access to an external software (e.g., phpMyAdmin).

Because the server uses the root account for log rotation and starting and stopping, don't modify its authentication information. Changing credentials in /etc/mysql/debian.cnf may work initially, but package updates may overwrite them. Instead of altering root, package maintainers advocate creating a password-protected administrator account.

To demonstrate, we'll create an admin account with the same permissions as root, but password authentication. Open your terminal's MariaDB prompt:

```
#sudo mysql
```

Create a new user now with root access and password-based authentication. To suit your tastes, modify the username and password:

\[consiole\]maridb> #GRANT ALL ON _._ TO 'admin'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;\[/console\]

To make sure that the privileges are saved and accessible for the current session, flush them:

```
mariadb> #FLUSH PRIVILEGES;
```

exit the MariaDB shell:

```
mariadb> exit
```

```
Output  
Bye
```

Let's make sure the MariaDB installation is working properly.

**Step 4 — Testing MariaDB**

MariaDB ought should launch automatically after installation from the default repository. Check its status to see if this works:

```
#sudo systemctl status mariadb
```

```
Output  
● mariadb.service - MariaDB 10.3.31 database server  
Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor preset:  
Active: active (running) since Mon 2022-03-14 18:33:32 UTC; 2min 2s ago  
Docs: man:mysqld(8)  
https://mariadb.com/kb/en/library/systemd/  
Main PID: 3229 (mysqld)  
Status: "Taking your SQL requests now..."  
Tasks: 31 (limit: 4915)  
Memory: 74.4M  
CGroup: /system.slice/mariadb.service  
└─3229 /usr/sbin/mysqld

Mar 14 18:33:32 mariadb /etc/mysql/debian-start[3267]: performance_schema  
Mar 14 18:33:32 mariadb /etc/mysql/debian-start[3267]: Phase 6/7: Checking and u  
Mar 14 18:33:32 mariadb /etc/mysql/debian-start[3267]: Running 'mysqlcheck' with  
Mar 14 18:33:32 mariadb /etc/mysql/debian-start[3267]: # Connecting to localhost  
Mar 14 18:33:32 mariadb /etc/mysql/debian-start[3267]: # Disconnecting from loca  
Mar 14 18:33:32 mariadb /etc/mysql/debian-start[3267]: Processing databases  
Mar 14 18:33:32 mariadb /etc/mysql/debian-start[3267]: information_schema  
Mar 14 18:33:32 mariadb /etc/mysql/debian-start[3267]: performance_schema  
Mar 14 18:33:32 mariadb /etc/mysql/debian-start[3267]: Phase 7/7: Running 'FLUSH  
Mar 14 18:33:32 mariadb /etc/mysql/debian-start[3267]: OK
```

Start MariaDB with sudo systemctl start mariadb.

Try connecting to the database using the mysqladmin client to run administrative commands. This command connects as root to MariaDB and returns the Unix socket version:

```
#sudo mysqladmin version
```

It should look like this:

```
Output  
mysqladmin Ver 9.1 Distrib 10.3.31-MariaDB, for debian-linux-gnu on x86_64  
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Server version 10.3.31-MariaDB-0+deb10u1  
Protocol version 10  
Connection Localhost via UNIX socket  
UNIX socket /var/run/mysqld/mysqld.sock  
Uptime: 3 min 6 sec

Threads: 6 Questions: 473 Slow queries: 0 Opens: 175 Flush tables: 1 Open tables: 31 Queries per second avg: 2.543
```

You can carry out the same action if a different administrative user with password authentication was set up by executing the following command:

```
#mysqladmin -u admin -p version
```

```
Output  
Ver 9.1 Distrib 10.3.31-MariaDB, for debian-linux-gnu on x86_64  
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Server version 10.3.31-MariaDB-0+deb10u1  
Protocol version 10  
Connection Localhost via UNIX socket  
UNIX socket /var/run/mysqld/mysqld.sock  
Uptime: 7 min 11 sec

Threads: 6 Questions: 474 Slow queries: 0 Opens: 175 Flush tables: 1 Open tables: 31 Queries per second avg: 1.099
```

This output indicates that MariaDB is up and running, as well as that the person in question may successfully authenticate themselves.
