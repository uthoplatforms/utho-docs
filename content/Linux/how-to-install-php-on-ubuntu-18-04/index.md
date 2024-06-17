---
title: "How to install PHP on Ubuntu 18.04"
date: "2023-05-27"
---

**Description**

In this article we will learn How to install [PHP](https://utho.com/docs/tutorial/how-to-install-ncurses-library-on-ubuntu-20-04/) on Ubuntu 18.04. [PHP](https://en.wikipedia.org/wiki/Ubuntu), which stands for "Hypertext Preprocessor," is a general-purpose programming language that is open-source and extensively used. It is especially well-suited for the building of websites and may be integrated in HTML.

Follow the below steps to How to install PHP on Ubuntu 18.04.

## Step 1 Update server

In the first step, you need to use the sudo apt update command to download and install all the available updates. You can then use the sudo apt upgrade command to update packages to the current version, as shown below.

```
apt-get update
```
![updating package](images/image-1012-1024x112.png)

## Step 2 Install PHP 

At this point, you need to install PHP by running the command sudo apt-get install php.

```
apt-get install php
```
![installing php ](images/image-1013-1024x106.png)

## Step 3 checking the php version

When the installation is finished, use the php --version command to check the version of PHP that was installed. as shown below.

![checking the version of php](images/image-1014.png)

## Step 4 Test your First PHP Program

Congratulations PHP has now been set up correctly. Let's make our first PHP programme create a file testing.php and enter below lines and run it by typing php testing.php.

```
<?php
```phpinfo();
?>
```

```
php testing.php
```
![testing of my script](images/image-1015-1024x327.png)

I really hope that you have a complete understanding of all the processes to how to install PHP on Ubuntu 18.04.

Must Read :- [https://utho.com/docs/tutorial/how-to-install-ncurses-library-on-ubuntu-20-04/](https://utho.com/docs/tutorial/how-to-install-ncurses-library-on-ubuntu-20-04/)

**ThankYou**
