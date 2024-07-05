---
title: "How to install Zabbix 4.4 in CentOS 7"
date: "2022-01-09"
title_meta: "Guide to install Zabbix 4.4 on CentOS 7"
description: 'This guide walks you through installing Zabbix 4.4, an open-source enterprise-class monitoring solution, on your CentOS 7 server. Zabbix allows you to monitor various system metrics, network devices, and applications for performance and availability.'

keywords:  ['Zabbix', 'CentOS 7', 'server monitoring', 'database', 'web interface']
tags: ["Zabbix 4.4", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/how-to-install-zabbix-4-4-in-centos-7/']
tab: true
---

![](images/How-to-install-Zabbix-4.4-in-CentOS-7_utho.jpg)

**1. Login to the server via Putty (SSH port 22)**

![](images/login-4.png)

**2. Set the SELinux in disabled mode, use the following command and reboot your server**.

```
# # sed -i 's/^SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config 
```

**3\. Restart the server by running**

 ```
 # reboot 
```

4**. Install apache and mariadb**

```
# yum update 
```

```
# yum install httpd mariadb-server -y 
```

**5\. Start Apache and mariaDB Services.**

```
# systemctl enable httpd && systemctl start httpd 
```

```
 # systemctl enable mariadb && systemctl start mariadb 
```

**6. Set Mariadb root Password:**

a.      Run:  # [mysql\_secure\_installation](https://manastri.blogspot.com/2019/09/securing-mysql-mariadb-with.html)

b.      It'll ask for setting the root password. Press Y to do so.

![](images/Screenshot_7-9.png)

c.      disallow remote root login

d.      Remove anonymous user

e.      it'll drop test databases

![](images/Screenshot_8-11.png)

**7.  Install Zabbix Server with MySQL**

```
# # rpm --import [http://repo.zabbix.com/RPM-GPG-KEY-ZABBIX](http://repo.zabbix.com/RPM-GPG-KEY-ZABBIX) 
```

```
# rpm -Uvh [https://repo.zabbix.com/zabbix/4.4/rhel/7/x86_64/zabbix-release-4.4-1.el7.noarch.rpm](https://repo.zabbix.com/zabbix/4.4/rhel/7/x86_64/zabbix-release-4.4-1.el7.noarch.rpm) 
```

8**. Now use the below command to install Zabbix and necessary packages**

```
# yum install zabbix-server-mysql zabbix-web-mysql zabbix-agent zabbix-get zabbix-sender zabbix-java-gateway -y
```

**9\. Edit PHP timezon**e

```
# vi /etc/httpd/conf.d/zabbix.conf 
```

**9\. Edit PHP timezone**

```
# vi /etc/httpd/conf.d/zabbix.conf 
```

<figure>

![](images/Screenshot_12-1-1.png)

<figcaption>

Save the file and exit.

</figcaption>

</figure>

1**0.  Restart httpd service using the below command**:

```
# systemctl restart httpd
```

**11. Edit create and import initial zabbix database and user:**

```
# mysql -u root -p
```

![](images/Screenshot_14-1-1.png)

\[filecode file\]

Enter password:Welcome to the MariaDB monitor.  Commands end with ; or \\g.Your MariaDB connection id is 10Server version: 5.5.60-MariaDB MariaDB ServerCopyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.  
Type 'help;' or '\\h' for help. Type '\\c' to clear the current input statement.  
**MariaDB \[(none)\]>CREATE DATABASE zabbixdb CHARACTER SET utf8 COLLATE utf8\_bin;**  
Query OK, 1 row affected (0.00 sec)  
**MariaDB \[(none)\]>GRANT ALL PRIVILEGES ON zabbixdb.\* TO zabbixuser@localhost IDENTIFIED BY "**a\_strong\_password**";**  
Query OK, 0 rows affected (0.00 sec)  
**MariaDB \[(none)\]>FLUSH PRIVILEGES;**  
Query OK, 0 rows affected (0.00 sec)  
**MariaDB \[(none)\]>exit**  
Bye

\[/filecode\]

**12.**  **After creating the Zabbix database and user we need to import the zabbix initial database using the below commands:**

```
# zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -u zabbixuser -p zabbixdb 
```

**13.**  **Now we need to edit database configuration in the Zabbix server configuration file zabbix\_server.conf:**

```
# vi /etc/zabbix/zabbix_server.conf 
```

Specify the database name for zabbix , database user name and the password  
DBHost=localhost

DBUser=zabbixuser

DBUser=zabbixuser

DBPassword=YOURPASSWORD

**14.**  **Now enable and start zabbix service:**

```
# systemctl enable zabbix-server
```

 ```
# systemctl start zabbix-server&&systemctl enable zabbix-agent 
```

```
 # systemctl start zabbix-agent 
```

**15.**  **Setup Zabbix Web Frontend**

Navigate to [http://ip\_address/zabbix](http://ip_address/zabbix) or [http://host\_name/zabbix](http://host_name/zabbix)

<figure>

![](images/Screenshot_22-1-1024x549.png)

<figcaption>

click Next

</figcaption>

</figure>

![](images/Screenshot_23-2-1024x549.png)

**16.  Please enter DataBase details:**

![](images/Screenshot_25-2-1024x547.png)

**18.  Summary:**

![](images/Screenshot_26-2-1024x550.png)

**19.  Finish the installation:**

![](images/Screenshot_27-2-1024x547.png)

**20.**  **Login Prompt:**

**Default username and password is "Admin" & "zabbix"**

![](images/Screenshot_28-2.png)

![](images/Screenshot_29-2-1024x526.png)

Thank You :)
