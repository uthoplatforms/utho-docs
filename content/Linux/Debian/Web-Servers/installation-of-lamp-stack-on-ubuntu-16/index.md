---
title: "Installation of LAMP Stack on Ubuntu 16"
date: "2020-04-23"
---

LAMP, which operates Linux as the operating system, is an open-source web-developing platform using Apache as the web-based server and PHP as an object-oriented scripting language. (Instead of PHP, Perl or Python are sometimes used.)

## Prerequisites

- Before you begin with this guide, you should have root user account privilege set up on your ubuntu server.
- Log in to the server with (SSH).
- Update the server packages using the command **sudo apt-get update**

## Installation of Apache Server

The Apache web server is the most popular web server in the world, making hosting websites a great default option.

We can install the Apache server by following command:

```
 [root@Microhost ~]# sudo apt-get install apache2 
```

### Place Global ServerName to Avoid Syntax Warnings

Next, to delete the alert message, we'll add a line in apache configuration file /etc/apache2/apache2.conf file. When you do not set ServerName globally and search your Apache configuration for the syntax error then you will receive the following warning:

```
 [root@Microhost ~]# apache2ctl configtest 
```

```
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
Syntax OK
```

We will edit the file by following command

```
 [root@Microhost ~]# vi /etc/apache2/apache2.conf 
```

Now, we will add the below line in the configuration file

ServerName server\_IP

Save and exit from the file

Next, check for syntax errors by typing

```
 [root@Microhost ~]# apache2ctl configtest 
```

The output will be shown as below

```
 Syntax OK 
```

Now, restart the apache service:

```
 [root@Microhost ~]# systemctl restart apache2 
```

### Set Firewall to Allow Web Traffic

Then, if the server configuration instruction you have followed to enable the UFW firewall, ensure that HTTP and HTTPS traffic is enabled by your firewall. You should ensure that UFW has an Apache application profile such as:

```
 [root@Microhost ~]# ufw app list 
```

```
Output
Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH
```

When looking at the full Apache profile, it will display that ports 80 and 443 are traffic enabled:

```
 [root@Microhost ~]# ufw app info "Apache Full" 
```

```
Output:
Profile: Apache Full
Title: Web Server (HTTP,HTTPS)
Description: Apache v2 is the next generation of the omnipresent Apache web
server.

Ports:
  80,443/tcp
```

Allow incoming traffic for this profile

```
 [root@Microhost ~]# ufw allow in "Apache Full" 
```

Now check the apache server while accessing the server IP

http:// server\_ip

The output will be shown as below:

![](images/ubuntu.png)

If you see this web page, your firewall will now make your web servers properly configured and available.

## Installation of Mysql

We can Install the Mysql by following command:

```
 [root@Microhost ~]# apt-get install mysql-server 
```

A list of the installing packages, along with the quantity of disk space, will be displayed to you. Enter Y to proceed.

Your server will request that you select and confirm a "root" MySQL user's password during the installation. This is an administrative MySQL account with privileges increased. Make sure that this password is strong and unique and do not leave it blank.

When the installation is complete, a simple security script is required to remove dangerous defaults and to unlock access to our database system. Running the interactive script:

```
 [root@Microhost ~]# mysql_secure_installation 
```

The password you specified for your MySQL root account will be requested to enter.

The level of password validation will be requested. Keep in mind that if you enter 2, at the highest level, you will be faced with an error when you try to enter a password that is not based on common dictionary words, numbers, Uppercase and lowercase letters, and specific characters.

```
There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
```

You will be shown a password strength for the current root password and asked if you want to change this password when you enabled the validation of the password. If the password you are using is satisfactory, enter n at the prompt for "no":

```
Using existing password for root.

Estimated strength of the password: 100
Change the password for root ? ((Press y|Y for Yes, any other key for No) :n 
```

For the remaining questions, press Y at each prompt and press the Enter key. This will remove certain anonymous users and the test database, deactivate remote root logins and load these new rules so that the MySQL changes are immediately met.

## Installation of PHP

PHP is our setup component to process code for dynamic content display. It can execute scripts, access the MySQL databases, and display the processed content to our web server.

We can install the PHP with some required extension by following command:

```
 [root@Microhost ~]# apt-get install php libapache2-mod-php php-mcrypt php-mysql
```

The above command will install the php .

In most cases, when a directory is requested, we'll want to modify how Apache serves files. Apache will first search a file called index.html when a user requests a directory from the server. We want to tell our web server to prefer a PHP file, So we will first let Apache look for the index.php.

We need to make changes in the directory **dir.conf** . which location will be /etc/apache2/mods-enabled/dir.conf . We can perform this task by following command:

```
 [root@Microhost ~]# vi /etc/apache2/mods-enabled/dir.conf 
```

The output will look like:

```
<IfModule mod_dir.c>
    DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>
```

We have to move **index.php** at the first position as per the output given below:

```
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```

Save the file and exit from the text editor using**:wq**

Now, We have to restart the services of the apache server using following command:

```
 [root@Microhost ~]# systemctl restart apache2
```

### Installation of PHP modules

Optionally, several additional modules can be installed to improve PHP functionality.

We can use the following command to list the available php modules:

```
 [root@Microhost ~]# apt-cache search php- | less
```

The output will be shown as below:

```
libnet-libidn-perl - Perl bindings for GNU Libidn
php-all-dev - package depending on all supported PHP development packages
php-cgi - server-side, HTML-embedded scripting language (CGI binary) (default)
php-cli - command-line interpreter for the PHP scripting language (default)
php-common - Common files for PHP packages
php-curl - CURL module for PHP [default]
php-dev - Files for PHP module development (default)
php-gd - GD module for PHP [default]
php-gmp - GMP module for PHP [default]
```

We can install multiple modules by the following command :

```
 [root@Microhost]# apt-get install package1 package2 … 
```

We can test the PHP execution while placing the test file at the html directory as given below:

```
 [root@Microhost]# vi /var/www/html/info.php 
```

Now , enter the code given below. You can enter in insert mode while pressing **i** on keyboard:

```
<?php
phpinfo();
?>
```

Save the file and exit from the text editor:

You can access the test file using below url:

```
 http://your_server_IP/info.php 
```

The output will be shown as below:

![](images/info-1024x509.png)

The LAMP installation has been completed:

Thank You :)
