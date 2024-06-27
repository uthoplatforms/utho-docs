---
title: "How to Allow Remote Connections to MySQL in centos"
date: "2022-09-05"
title_meta: "Guide for Allow Remote Connections to MySQLin Centos 7"
description: "Discover how to enable and configure remote connections to MySQL on CentOS. This tutorial provides step-by-step instructions to modify MySQL configuration files, adjust firewall settings, and allow external connections to MySQL databases from remote clients on CentOS, facilitating efficient database management.
"
keywords: ["CentOS MySQL remote connection", "allow MySQL remote access CentOS", "CentOS MySQL remote connection setup", "configure MySQL remote access CentOS", "CentOS MySQL enable remote access", "MySQL remote access CentOS firewall", "CentOS MySQL remote access tutorial", "CentOS MySQL bind-address"]

tags: ["MySQL", "CentOS"]
icon: "mysql"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Database/MY SQL/how-to-allow-remote-connections-to-mysql-in-centos/']
tab: true
---

![Allow Remote Connections to MySQL in centos](images/How-to-Allow-Remote-Connections-to-MySQL-in-centos_utho.jpg)

**Introduction**

On the same local system, databases and web servers are frequently hosted. But a lot of businesses are now switching to a more distributed environment.

You may grow resources fast and improve hardware performance and security by using a separate database server. Learning how to manage remote resources efficiently is a priority in such use situations.

This guide demonstrates how to make a [MySQL](https://en.wikipedia.org/wiki/MySQL) database accessible from a distance.

To Allow Remote Connections to MySQL in centos so follow the below steps..

**Step.1** You must begin by editing the file that is used to configure MySQL. Launch your text editor of choice, which in our case is vi, and open the file:

```
#vi /etc/my.cnf
```

![](images/Screenshot_24-2.png)

Add the following contents at the end of the ‘\[mysqld\]‘ section:  
bind-address = \*  
require\_secure\_transport = ON  
When you are done, save and close the file.

![](images/Screenshot_25-3.png)

**Step.2**  
Then, restart MySQL service on Centos to apply the changes:

```
#systemctl restart mysqld  

```

**Step.3**  
If you are useing a firewall then add the 3306 in firewall rules

To allow MySQL to connect from remote server on CentOS 7 server, you need to enable port 3306 in firewall.

```
#sudo firewall-cmd --zone=public --add-port=3306/tcp  

```

```
#sudo firewall-cmd –reload
```

**Step.4** Now you can access the mysql database.using below command.give the password and enter.

```
#mysql -u root -u
```

![](images/Screenshot_26-3.png)

Hosting for databases and web servers frequently takes place on the same physical machine. However, many companies are making the transition to a more decentralised setting as of late.

I am hoping that you are able to follow the steps. The Step-by-Step Guide to Enabling Remote MySQL Connections on CentOS

Must read:- [https://utho.com/docs/tutorial/change-ssh-default-port-22-to-custom-port/](https://utho.com/docs/tutorial/change-ssh-default-port-22-to-custom-port/)

**Thankyou**
