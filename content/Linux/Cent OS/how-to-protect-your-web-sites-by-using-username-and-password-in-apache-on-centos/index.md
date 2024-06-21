---
title: "How to Protect your Web Sites by using Username and password in Apache on CentOS."
date: "2022-10-04"
title_meta: "Apache on CentOS"
description: ' This guide explains how to configure basic HTTP authentication with username and password protection for your websites on an Apache web server running on CentOS. This adds an extra layer of security by requiring users to provide valid credentials before accessing specific content.'

keywords:  [' Apache, CentOS, website security, HTTP authentication, username, password.']
tags: ["Apache" , "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/how-to-protect-your-web-sites-by-using-username-and-password-in-apache-on-centos/']
tab: true
---

![How to Protect your Web Sites by using Username and password in Apache on CentOS](images/How-to-Protect-your-Web-Sites-by-using-Username-and-password-in-Apache-on-CentOS-1024x576.png)

This article is about to Protect your Web Sites by using Username and password in Apache on [CentOS](https://www.centos.org/). When you're in charge of online projects, you often have to limit who can see them to keep them safe from the outside world. There could be a number of reasons for this, such as the fact that you don't want search engine crawlers to see your site while it's still being built.

In this tutorial, I'll show you how to make Apache web server protect different web [site directories](https://utho.com/docs/tutorial/how-to-reset-debian-root-password/) with passwords. There are many ways to do this, but we will only talk about the two most common ones.

## Requirements

To protect in Apache on CentOS you need,

- yum repository configured Centos server
- A super user( root) or any normal user with SUDO privileges.

## Configuration to protect default site.

To protect the main web root directory `/var/www/html`, open your Apache’s configuration file and change to the highlighted content from the following code.

## For On Apache 2.2 Version:

```
<Directory /var/www/html> 
Options Indexes Includes FollowSymLinks MultiViews 
AllowOverride All
Order allow,deny
Allow from all 
</Directory>
```

## On Apache 2.4 Version:

```
# vi /etc/httpd/conf/httpd.conf
```

```
<Directory /var/www/html> 
Options Indexes Includes FollowSymLinks MultiViews 
AllowOverride All 
Require all granted 
</Directory>
```

And now after saving the file, restart the httpd service.

```
# systemctl restart httpd
```

Now, we'll use the htpasswd command to make a username and password for our protected directory. This command is used to handle basic authentication user files.

Example and syntax of the htpasswd command is as follow.

```
# htpasswd -c /path/filename username 
```

Here, -c is used to create a file in which the username and password will be saved. Note that, the password will be hashed format.

Now we will create the credential file in /etc/httpd directory for the user- useradmin

```
# htpasswd -c /etc/httpd/credfile useradmin 
```

```
# vi /var/www/html/.htaccess 
```

Now in this file add the following content.

```
AuthType Basic
AuthName "Restricted Access"
AuthUserFile /etc/httpd/credfile
require user useradmin
```

Now you can save the file and test your setup. Open your web browser and type in your IP address or domain name, such as:

```
http://serverip 
```

<figure>

![](images/image-242-edited.png)

<figcaption>

Prompt you will get after browsing server-ip

</figcaption>

</figure>

<figure>

![](images/image-243.png)

<figcaption>

Enter the credentials created by htpasswd command

</figcaption>

</figure>

<figure>

![](images/image-244-1024x356.png)

<figcaption>

The default page of the site

</figcaption>

</figure>
