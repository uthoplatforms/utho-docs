---
title: "How to install MySQL Relational Databases on Fedora 12"
date: "2022-09-21"
---

![](images/How-to-install-MySQL-Relational-Databases-on-Fedora-12_utho.jpg)

Many web and server programmes rely on MySQL as their database management system. Following this tutorial will make working with MySQL on Fedora 12 Instance much easier for newcomers. It is assumed that you have updated your system, followed the instructions in our Setting Up and Securing a Compute Instance guide, and logged into your Instance as root via SSH before proceeding with this tutorial.

## System Configuration

please ensure that your /etc/hosts file has complete entries, similar to the given shown below

```
File: /etc/hosts
```

```
1.127.0.0.1 localhost.yourdoamin localhost  
2.12.34.56.78 servername.yourdomain.com servername
```

## Installing MySQL

```
#yum update
```

```
#yum install mysql-server
```

```
#service mysqld start
```

## Configure mysql

Mysql secure installation is a software that may be executed after MySQL has been installed to assist strengthen security. In addition to changing the MySQL root password, removing anonymous users, disabling root logins from hosts other than localhost, and erasing test databases are also options provided while executing mysql secure installation. You should select "yes" for these questions. When asked, choose "yes" to reload the privilege tables. To begin using the application, use the following command:

```
#mysql_secure_installation
```

```
#service mysqld restart
```

## Resetting the MySQL Root Password

If you have forgotten your MySQL root password, you can retrieve it using the following commands:

```
#service mysqld stop
```

```
#mysqld_safe --skip-grant-tables
```

```
#mysql -u root
```

The MySQL client programme will now handle the next part of the password reset:

```
#USE mysql;
```

```
#UPDATE user SET PASSWORD=PASSWORD("CHANGEME") WHERE User='root';
```

```
#FLUSH PRIVILEGES;
```

```
#exit;
```

Final step, restart MySQL with this command:

```
#service mysqld restart
```

Thank you
