---
title: "How to install OwnCloud on Debian server"
date: "2023-05-16"
title_meta: "GUIDE How to install OwnCloud on Debian"
description: "A detailed guide on how to install OwnCloud, a self-hosted file sync and share server, on Debian."

keywords: ['Debian', 'OwnCloud', 'installation', 'self-hosted', 'file sync', 'file share', 'Linux', 'server']

tags: ["OwnCloud"]
icon: "Debian"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Debian/how-to-install-owncloud-on-debian-server/']
tab: true
---

<figure>

![How to install OwnCloud on Debian server](images/How-to-install-OwnCloud-on-Debian-server.png)

<figcaption>

How to install OwnCloud on Debian server

</figcaption>

</figure>

In this article, we will learn how to install OwnCloud on Debian server. OwnCloud is a major open-source file sharing and cloud collaboration tool whose services and features are similar to those given by DropBox and Google Drive. However, unlike Dropbox, OwnCloud does not have the datacenter capacity to store hosted files. Nevertheless, you can still share things such as papers, photos, and videos to name a few, and view them across multiple devices such as smartphones, tablets, and PCs.

## Prerequisites

- Any normal user with SUDO privileges or super user
- LAMP installed on server. If you have installed LAMP, you can follow the below steps of this separate and [detailed guide](https://utho.com/docs/tutorial/how-to-install-lamp-on-ubuntu-18-10/)

## Steps to install OwnCloud on Debian

Step 1: Before you start, use the following apt command to update the system files and sources.

```
apt-get update && apt-get upgrade -y
```
### 1- Install Php7.4 and Apache 2 modules

Step 2: OwnCloud is built on PHP and is typically accessed via a web interface. For this reason, we are going to install the Apache webserver to serve Owncloud files as well as PHP 7.2 and additional PHP modules necessary for OwnCloud to function smoothly.

```
apt install apache2 libapache2-mod-php7.4 openssl php-imagick php7.4-common php7.4-curl php7.4-gd php7.4-imap php7.4-intl php7.4-json php7.4-ldap php7.4-mbstring php7.4-mysql php7.4-pgsql php-smbclient php-ssh2 php7.4-sqlite3 php7.4-xml php7.4-zip
```
If you are facing any issue which states that php7.4 packages not found. In this case, you can follow this guide to [install php7.4 on your machine](https://utho.com/docs/tutorial/install-multiple-version-of-php-on-ubuntu-server/) as well. Note that, owncloud do not run on PHP greater than 8.0 version.

Step 3: Now, start and enable **Apache** service to run on boot, run the commands.

```
systemctl enable --now apache2
```
Step 4: And now, check the PHP installed version on your machine

```
php -v
```
### 2\. Install MySQL server on your Debian

Step 5: Install MySQL on your machine.

```
apt-get install mysql-server -y
```
Step 6: And, now start and enable you mysql services and make sure to configure it as well.

```
systemctl enable --now mysql
```
Step 6.1: Change the identification method of user root, this is important to do the mysql\_secure\_installation

```
mysql
```mysql> alter user 'root'@'localhost' identified with 'mysql_native_password';j
mysql> exit;
```

<figure>

![Change the identification of user root](images/image-1045.png)

<figcaption>

Change the identification of user root

</figcaption>

</figure>

Step 7: Now setup your mysql service.

```
mysql_secure_installation
```
<figure>

![Installation of mysql](images/image-1046.png)

<figcaption>

Installation of mysql

</figcaption>

</figure>

### 3- Configure OwnCloud database

Step 8: Now that you've armed the applications, it's time to set up the ownCloud database and user. The tasks in this part are run from the MySQL shell.

```
mysql -u root -p
```
mysql>  CREATE DATABASE DBowncloud;
mysql>  CREATE user 'Userowncloud'@'localhost' identified by 'password';
mysql>  GRANT ALL ON 'DBowncloud.* TO 'USERowncloud'@'localhost';
mysql>  FLUSH PRIVILEGES;
mysql>  EXIT;
```

### 4- Download the OwnCloud source code.

Step 9: The setup is ready for ownCloud at this point. Check the ownCloud files page to make sure you're getting the most current version of the software before you actually download it.

You can visit [this link](https://download.owncloud.com/server/stable/) and choose you desired version of OwnCloud.At the time this guide was written, version 10.10.0 was the most recent. Put the version you want to download in place of 10.10.0.

```
wget https://download.owncloud.com/server/stable/owncloud-10.10.0.zip
```
<figure>

![Downloading of OwnCloud ](images/image-1047.png)

<figcaption>

Downloading of OwnCloud

</figcaption>

</figure>

Step 10: Unzip the latest package using the below link.

```
unzip owncloud-10.10.0.zip
```
Step 11: Move the content of this folder to the DocumentRoot path, specified in your virutal host's entry.

```
mv owncloud/* /var/www/html
```
Step 12: Turn on the Apache modules rewrite, mime, and unique\_id:

```
a2enmod rewrite mime unique_id
```
Step 13: Restart your apache2 service, mysql and php fpm services and go to browser to check your Owncloud installation on your server

```
systemctl restart apache2 mysql php7.4-fpm
```
http://server_ip     # Search your server ip on your browser
```

<figure>

![Owncloud install on Ubuntu  |Owncloud installed on Ubuntu | ](images/image-1048.png)

<figcaption>

Owncloud install on Debian

</figcaption>

</figure>

And this is how you have learnt how to install OwnCloud on Debian server
