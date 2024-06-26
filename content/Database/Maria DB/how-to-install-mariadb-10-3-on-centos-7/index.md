---
title: "HOW TO INSTALL MARIADB 10.3 ON CENTOS 7"
date: "2020-06-08"
title_meta: "Guide to install  MARIADB on CentOS 7"
description: "his guide provides step-by-step instructions for installing MariaDB 10.3, a popular open-source relational database management system, on your CentOS 7 server. It covers adding the MariaDB repository, installing the MariaDB server package, initializing the database, and securing your installation."

keywords: ['MariaDB 10.3', 'CentOS 7', 'database server', 'MySQL', 'LAMP stack']

tags: ["MARIADB", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/how-to-install-mariadb-10-3-on-centos-7/']
tab: true
---

The MariaDB server is a MySQL server fork developed by the community. MariaDB is developed as a drop-in replacement for MySQL(r), with new features , new storage engines, fewer bugs and better performance by core members of the original MySQL team. For databases professionals who want a robust , scalable and reliable SQL server, MariaDB can be a better choice.

#### Add MariaDB Yum Repository

```
 [root@Microhost ~]# vi /etc/yum.repos.d/mariadb.repo 
```

![](images/mar1.png)

The above command will open a blank file . Please copy and paste the content given below:

```
[mariadb]
name=MariaDB
baseurl=http://yum.mariadb.org/10.3/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
enabled=1
```

Press the **Esc** button and exit with **:wq**

We need to import the public GPG key to verify the digital signatures of the packages in this repository before using the MariaDB andum repository.

However, the GPG public key does not need to be manually imported. When we first install a package from MariaDB yum repository, the public GPG key will be automatically installed by yum.

Here, for the sake of demonstration, we manually import the GPG public key.

```
 [root@Microhost ~]# rpm --import https://yum.mariadb.org/RPM-GPG-KEY-MariaDB 
```

We will move to the installation section. You can use the following command to install mariaDB.

```
 [root@Microhost ~]# yum install -y mariadb-server 
```

![](images/mar2.png)

Now, MariaDB has been installed successfully. We can start and enable the service of MariaDB.

```
 [root@Microhost ~]# systemctl start mariadb 
```

```
 [root@Microhost ~]# systemctl enable mariadb 
```

We will move to the secure installation of MariaDB. The command is given below to initiate the process.

```
 [root@Microhost ~]# mysql_secure_installation 
```

![](images/mar3-1.png)

Press "enter" to set new password for MariaDB root, as shown in the above screenshot.

Now, You will get the prompt of the removal of anonymous User. You need to press "y" as per the image given below:

![](images/mar4.png)

In the next prompt, you have to disable the root login of mariadb. You need to press "y" as per the image given below:

![](images/mar5.png)

Now, the prompt would be shown for the removal of test databases. You need to press "y" as per the image given below:

![](images/mar6.png)

In the last prompt, You will press "y" to reload the table privileges as shown in image below:

![](images/mar7.png)

We have done with the configuration and secure installation portion of MariaDB.

We will use the below command for login into database:

```
 [root@Microhost ~]# mysql -u root -p 
```

![](images/mar8.png)

We have completed the section for installation and configuration of MariaDB.

Thank You :)
