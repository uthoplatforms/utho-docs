---
title: "How to install Owncloud on RHEL 8"
date: "2023-07-09"
title_meta: "How to install Owncloud on RHEL 8"
description: "How to install Owncloud on RHEL 8"
keywords:  ['owncloud', 'linux']
tags: ["linux", "owncloud"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/how-to-install-owncloud-on-rhel-8']
---

<figure>

![How to install owncloud on Redhat](images/How-to-install-owncloud-on-Redhat.jpg)

<figcaption>

How to install owncloud on Redhat

</figcaption>

</figure>

In this article, we will learn how to install OwnCloud on RHEL 8 server. OwnCloud is a major open-source file sharing and cloud collaboration tool whose services and features are similar to those given by DropBox and Google Drive. However, unlike Dropbox, OwnCloud does not have the [datacenter](https://www.cisco.com/c/en/us/solutions/data-center-virtualization/what-is-a-data-center.html) capacity to store hosted files. Nevertheless, you can still share things such as papers, photos, and videos to name a few, and view them across multiple devices such as smartphones, tablets, and PCs.

## Prerequisites

- Any normal user with SUDO privileges or super user

- LAMP installed on server. If you have installed LAMP, you can follow the below steps of this separate and [detailed guide](https://utho.com/docs/tutorial/how-to-install-lamp-on-ubuntu-18-10/)

## Steps to install OwnCloud on Redhat server 8

### Installing Apache( httpd) server

Step 1- Install httpd, or aka apache server and modssl package on your machine.

```
yum install httpd mod_ssl -y
```
<figure>

![Install Http webserver
](images/image-1201-1024x314.png)

<figcaption>

Install Http webserver

</figcaption>

</figure>

### Installing PHP 7.4

Step 2- To install php 7.4 on your machine, you need to first install the remi-release repository

```
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm -y
yum install yum-utils
```
<figure>

![Install remi repo to install php](images/image-1202-1024x314.png)

<figcaption>

Install remi repo to install php

</figcaption>

</figure>

Step 3: Now, install the required packages of php7.4.

```
yum-config-manager --enable remi-php74  # enable the php7.4 repo
```
```
yum install php php-mbstring php-gd php-mcrypt php-pear php-pspell php-pdo php-xml php-mysqlnd php-process php-pecl-zip php-xml php-intl php-zip php-zlib -y
```
![](images/image-1203-1024x314.png)

you will see something like below screenshot while installing the required packages of php7.4.

![](images/image-1204-1024x439.png)

### Install Mariadb server

Step 4- Now, we will install mariadb-server on your machine.

```
yum install mariadb-server -y
```

<figure>

![](images/image-1205-1024x334.png)

<figcaption>

Installing the mariadb server

</figcaption>

</figure>

Step 5: Start and enable the apache webserver's service and mariadb-server's service.

```
systemctl enable --now httpd mariadb
```
Step 6: Now, complete the installation of mariadb-server using mysql\_secure\_installation.

```
mysql_secure_installation
```
After executing the above command. You will be asked to enter the mariadb-server's password. Here, just press blank enter. Then press Yes or y.

After that you need to enter the password you want to set for root user.

After setting up the password for the root user. You just need to press 'y' and press enter.

### Install Owncloud on machine

Step 7: Now, we need to install Owncloud server. For this, we will add the official owncloud repo for Redhat enterprise linux 8 server. You can also install owncloud using downloading the packages and install it manually.

```
cd /etc/yum.repos.d/ && wget wget https://download.opensuse.org/repositories/isv:ownCloud:server:10/RHEL_8/isv:ownCloud:server:10.repo
``` --no-check-certificate

```
yum install owncloud-complete-files -y
```
<figure>

![Install the Owncloud on centos](images/image-1206-1024x210.png)

<figcaption>

Install the Owncloud on rhel 8

</figcaption>

</figure>

### Setting up the database of Owncloud

Step 8: Now, login to your root user of mariadb using the password you have set perviously.

```
mysql -u root -p
```
```
CREATE DATABASE owncloud;
CREATE USER 'ownclouduser'@'localhost' identified by 'STRONG_PASSWORD';
GRANT ALL PRIVILEGES ON owncloud.* TO 'owncloud'@'localhost';
FLUSH PRIVILEGES;
exit
```
Step 9: Now, manage your trusted sites of your owncloud. This is the list of IP or domains, by which you want your owncloud accessible. This is generally the server' ip or domain pointed to the server on which install Owncloud.

```
cd /var/www/html/owncloud
vi config/config.php
```
<figure>

![trusted sites of owncloud](images/image-1207.png)

<figcaption>

trusted sites of owncloud

</figcaption>

</figure>

Step 10: Now, you are almost ready to access your owncloud server. You just need to restart the httpd services.

```
systemctl restart httpd
```
Step 11: If you faces any issue, you can find the below command usefull.

```
cd /var/www/html/owncloud
```
```
sudo -u apache ./occ maintenance:install \
```   --database "mysql" \
   --database-name "owncloud" \
   --database-user "ownclouduser"\
   --database-pass "password" 

```
systemctl restart httpd
```
And this is you will install owncloud on Rhel 8 server.
