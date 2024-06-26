---
title: "Update PHP 5.4 version to PHP 7.4"
date: "2022-06-22"
---

![Update PHP 5.4 version to PHP 7.4](images/Update-PHP-5.4-version-to-PHP-7.4_utho.jpg)

## INTRODUCTION Update PHP version from 5.4 to PHP 7.4

PHP is a general-purpose scripting language geared toward web development. It was originally created by Danish-Canadian programmer Rasmus Lerdorf in 1993 and released in 1995. The PHP reference implementation is now produced by The PHP Group. Update PHP version from 5.4 to PHP 7.4. Differences Between PHP5 and 7. PHP 5 uses the old version of the engine called Zend II, therefore its performance, in terms of speed is way below that of PHP 7. PHP 7 uses a brand new model of engine known as PHP-NG or Next generation. This engine considerably **e**nhances performance with optimized memory usage. PHP 7 enables programmers to declare the return type of the functions as per the expected return value. Thus, it makes the code robust and accurate.

* * *

Step 1. Check the version of the [php](https://utho.com/docs/tutorial/how-to-install-php-in-centos-7/).

```
# php - v  
```

![](images/DD.png)

Current version is PHP 5.4 .

Step 2. Install Remi Repository and EPEL Repository by using following commands . you must perform the following below given command.

```
 # wget [https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm](https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm) 
```

```
 # wget [http://rpms.remirepo.net/enterprise/remi-release-7.rpm](http://rpms.remirepo.net/enterprise/remi-release-7.rpm) 
```

```
 # rpm -Uvh remi-release-7.rpm epel-release-latest-7.noarch.rpm 
```

Step 3. After installing the repository, you must perform the following additional configurations.

```
 # yum install yum-utils 
```

```
# yum-config-manager --enable remi-php74 
```

Step 4. For installation PHP 7.4. you must perform the following given command

```
 # yum install php php-opcache php-gd php-curl php-mysqlnd  
```

Step 5. after the installation. You have to update the packages.

Step 6. Check the version of PHP now. You must perform the following below given command.

```
 # php -v  
```

![](images/DD10.png)

PHP has been updated to 7.4.

We have learned in this article to update the PHP 5.4 to 7.4. PHP is a general-purpose scripting language geared toward web development. It was originally created by Danish-Canadian programmer.

Thank you!! 0000000000000000000000000000000000000000000000000000000000000000000000000000
