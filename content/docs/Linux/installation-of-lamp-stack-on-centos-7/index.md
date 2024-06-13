---
title: "How to Install (Linux, Apache, MariaDB, PHP) LAMP Stack on CentOS 7"
date: "2020-04-22"
---

LAMP, which operates Linux as the operating system, is an open-source web-developing platform using Apache as the web-based server and PHP as an object-oriented scripting language. (Instead of PHP, Perl or Python are sometimes used.)

## Prerequisites

- Before you begin with this guide, you should have root user account privilege set up on your server.
- Log in to the server with (SSH).
- Update the server packages using the command **yum update -y** .

## Installation of Apache Server

The Apache web server is the most popular web server in the world, making hosting websites a great default option.

We can easily install Apache with the package manager of CentOS, **yum.** Use the below command to install Apache server .

```
 [root@Microhost ~]# yum install httpd
```

Afterward, your web server has been installed.

Once it gets installed, you can start Apache on your server by the following command .

```
 [root@Microhost ~]# systemctl start httpd.service
```

Also, enable the https service by following command so, it will start at boot time whenever the system gets rebooted.

```
 [root@Microhost ~]# systemctl enable httpd.service
```

\[ht\_message mstyle="alert" title="NOTE" " show\_icon="true" id="" class=""style="" \]If firewalld is inactive then skip all the firewall related commands.\[/ht\_message\]

We need to check the firewall status. whether it is active or inactive. If firewall is in active mode then we have to add the port 80 or add the http service in the firewall .

```
 [root@Microhost ~]# systemctl status firewalld
```

The output will be shown as below if the firewall is active.

![](images/firewall.png)

Now we have to add the https service in the firewall by following command.

```
 [root@Microhost ~]# firewall-cmd --permanent --add-service=http 
```

Reload the firewall rules by the following command.

```
 [root@Microhost ~]# systemctl restart firewalld 
```

```
 [root@Microhost ~]# systemctl reload firewalld 
```

Now check the webserver while accessing the server IP address in the browser:

```
http://server_ip
```

The output will be shown as given below:

![](images/apache-1024x377.png)

## Installation of Mysql server

Download the MySQL packages from official website by the following command:

```
 [root@Microhost ~]# wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm 
```

Now that we have checked the file has not been corrupted or modified, we are going to install the package:

```
 [root@Microhost ~]# rpm -ivh mysql57-community-release-el7-9.noarch.rpm 
```

It introduces two additional MySQL yum repositories and we can use them in MySQL server installation:

```
 [root@Microhost ~]# yum install mysql-server 
```

To indicate you want to continue, click 'y'

With the following command we will start the daemon:

```
 [root@Microhost ~]# systemctl start mysqld 
```

Check the mysql service status by the following command:

```
 [root@Microhost ~]# systemctl status mysqld 
```

A temporary password for the MySQL root user is created during the installation process. Find it with the following command in mysqld.log:

```
 [root@Microhost ~]# grep 'temporary password' /var/log/mysqld.log 
```

The output will be shown as below:

```
 2020-04-22T09:53:31.440319Z 1 [Note] A temporary password is generated for root@localhost: Lktg)3NtgIlh 
```

Note the password that is required to protect your installation in the next step and where you are forced to change it. The default authentication policy requires 12 letters, with at least one letter from an upper case and one letter from a lowercase also a special character.

## Configuration of Mysql server

To run a security script, use this command:

```
 [root@Microhost ~]# mysql_secure_installation 
```

The output will be shown as below:

![](images/pass1.png)

Create a new 12-character password with at least one uppercase letter, one lowercase letter, and one special character. When asked, type it again.

Your new password is good and you're asked to change it right away. You should receive feedback. You can easily say no because you have just done it:

```
Output:
Estimated strength of the password: 100
Change the password for root ? (Press y|Y for Yes, any other key for No) :
```

If we reject the request to rework the password, we must press Y and then Enter all of the following questions so that the anonymous users can be removed, the remote key can be disabled also test database will be removed.

After completion of the installation we can enable the service of Mysql:

```
 [root@Microhost ~]# systemctl enable mysqld 
```

We can check the MySQL while login using below command:

```
 [root@Microhost ~]# mysql -u root -p 
```

## Installation of Php

PHP is the part of our setup that processes code for dynamic content display. It can run scripts, link to our MySQL databases, and view processed content to our web server.

We can use the following command for the installation of our components. The php-mysql kit will also be included:

```
 [root@Microhost ~]# yum install php php-mysql 
```

PHP should be built problem-free. To function with PHP we have to restart the Apache web server

```
 [root@Microhost ~]# systemctl restart httpd 
```

## Install php modules

We can optionally install additional modules to improve PHP's functionality.

You can type this into your program to see the choices for PHP modules and libraries:

```
 [root@Microhost ~]# yum search php 
```

The consequence is that you can load all optional components. For each one, you will be given a brief description:

```
php-bcmath.x86_64 : A module for PHP applications for using the bcmath library
php-cli.x86_64 : Command-line interface for PHP
php-common.x86_64 : Common files for PHP
php-dba.x86_64 : A database abstraction layer module for PHP applications
php-devel.x86_64 : Files needed for building PHP extensions
php-embedded.x86_64 : PHP library for embedding in applications
php-enchant.x86_64 : Enchant spelling extension for PHP applications
php-fpm.x86_64 : PHP FastCGI Process Manager
php-gd.x86_64 : A module for PHP applications for using the gd graphics library
. . .
```

We can install the php modules by the following command

```
 [root@Microhost ~]# yum install php-fpm 
```

If you wish to install more than one module, you may do so by listing one module, separated by a window, according to the command yum install:

```
 [root@Microhost ~]# yum install module1 module2 ... 
```

We can test the php using test page. We will edit create a php file at given location:

```
 [root@Microhost ~]# vi /var/www/html/info.php 
```

Now copy the content as given below in the file and save the file by **:wq**

```
<?php phpinfo(); ?>
```

We can access the page by following url:

```
http://your_server_ip/info.php
```

Thank You :)
