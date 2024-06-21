---
title: "How to Install MariaDB 10.3 on Ubuntu 20.04"
date: "2022-10-11"
title_meta: "MariaDB 10.3 installtion guide on Ubuntu 22.04"
description: "Learn how to install MariaDB 10.3 on Ubuntu 20.04 with this comprehensive guide. Follow these step-by-step instructions to set up MariaDB 10.3, a popular fork of MySQL, on your Ubuntu 20.04 system for efficient database management and development.
"
keywords: ["install MariaDB 10.3 Ubuntu 20.04", "MariaDB 10.3 setup Ubuntu 20.04", "Ubuntu 20.04 MariaDB 10.3 installation guide", "MySQL fork Ubuntu", "Ubuntu MariaDB tutorial", "MariaDB installation steps Ubuntu 20.04", "database management Ubuntu", "MariaDB 10.3 Ubuntu 20.04 instructions"]

tags: ["MariaDB 10.3", "ubuntu"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Ubuntu/how-to-install-mariadb-10-3-on-ubuntu-20-04/']
tab: true
---

![](images/final-aditya-linux-1024x576.png)

## Introduction

In this article, you will learn how to install MariaDB 10.3 on ubuntu 20.04.

This is to Install MariaDB 10.3 on [Ubuntu 20.04](https://utho.com/docs/tutorial/how-to-install-multicraft-on-ubuntu-20-04/). MariaDB is a database management system that is a fork of [MySQL](https://www.mysql.com/). It is extremely similar to MySQL, which is a database management system. Several different applications, including data warehousing, e-commerce, enterprise-level features, and logging programmes, all make use of the MariaDB database.

MariaDB will let you to fulfil all of your burden in an effective manner; it can function in any cloud database and can function at any scale, whether it be little or huge.

A database is a repository for information that can be easily retrieved and applied in the context in which it is required. When compared to recording information on a piece of paper or in a Word document, storing all of your information in a database allows it to be organized into tables, making it simple to retrieve each individual entry in a manner that is both systematic and accurate.

## Install MariaDB APT repos on your Ubuntu 20.04 server by following the instructions below and executing the commands.

```
# apt update
```

```
# apt install software-properties-common dirmngr apt-transport-https ca-certificates -y
```

```
# wget -qO- https://mariadb.org/mariadb_release_signing_key.asc | gpg --dearmor > /etc/apt/trusted.gpg.d/mariadb.gpg
```

```
# echo  'deb [arch=amd64,arm64,ppc64el,s390x] https://atl.mirrors.knownhost.com/mariadb/repo/10.7/ubuntu focal main' >  /etc/apt/sources.list.d/mariadb.list
```

## Install MariaDB 10.3 on Ubuntu 20.04

```
# apt install mariadb-server mariadb-client -y
```

You can use the command to check the version of MariaDB that is currently installed;

```
# mysql -V
```

![command output](images/image-357.png)

Now, using the following command, check the current status of mariadb.

```
# systemctl status mariadb
```

![command output](images/image-359.png)

## Run the mariadb-secure-installation script, which assists you in protecting your MariaDB database server.

```
# sudo mariadb-secure-installation
```

Enter current password for root (enter for none): Press Enter

![command output](images/image-349.png)

Switch to unix\_socket authentication \[Y/n\] : Press y

![command output](images/image-340.png)

Change the root password? \[Y/n\] : Press y

![command output](images/image-341.png)

Remove anonymous users? \[Y/n\] : Press y

![command output](images/image-342.png)

If you choose you wish to let root login remotely, then press the y key; otherwise, press the n key.

![command output](images/image-343.png)

Reload privilege tables now? \[Y/n\] : Press y

![command output](images/image-345.png)

## A username and password combination should be required in order to access the MariaDB shell.

```
# mysql -u root -p
```

![command output](images/image-346.png)

```
# CREATE DATABASE microhost;
```

```
# SHOW DATABASES;
```

![command output](images/image-347.png)

Executed the following command To make changes take effect without reloading or restarting mysql service, you can use the flush privileges command to reload the grant tables in the database.

```
# FLUSH PRIVILEGES;
```

```
# exit
```

![command output](images/image-348.png)

## Conclusion

Hopefully now you have Installed MariaDB 10.3

Thank You 🙂
