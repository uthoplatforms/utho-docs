---
title: "How to change apache2 web folder in Ubuntu"
date: "2022-09-05"
Title_meta: GUIDE TO Change Apache2 Web Folder in Ubuntu

Description: Learn how to change the Apache2 web folder in Ubuntu with this step-by-step guide. Follow the instructions to modify the default web directory, update configuration files, and ensure proper permissions for a customized web server setup on your Ubuntu system.

Keywords: ['Apache2', 'Ubuntu', 'change web folder', 'web directory', 'server configuration']

Tags: ["Apache2", "Ubuntu", "Web Directory", "Server Configuration"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Web-Servers/Apache/how-to-change-apache2-web-folder-in-ubuntu']
tab: true

---

![How to change apache2 web folder in Ubuntu](images/How-to-change-apache2-web-folder-in-Ubuntu_utho.jpg)

**Introduction**

The web server of Apache is the most popular way to deliver web content. It serves over half of all active websites in the Internet and is extremely powerful and flexible.  
[Apache](https://en.wikipedia.org/wiki/Apache_HTTP_Server) divides its features and components into separate units, which can be independently customized and set up. A virtual Host is called the basic unit describing a single site or domain. Virtual hosts allow one server, through a matching system, to host several domains or interfaces. This is important for those who want to host more than one VPS site.  
Each configured domain will direct the visitor to a certain directory that contains information on this website without indicating that the same server is also responsible for other websites. This scheme can be expanded without software limits provided the traffic of all the sites is handled by your server.

\*To modify the document root of Apache, you must alter the files apache2.conf and 000-default.conf.

Apache is installed in the /var/www/html directory.

This is the default Apache root directory.

Either modify the Apache root directory or relocate the project to /var/www/html.

To change apache2 web folder follow below steps......

**Step.1** access putty

![](images/Screenshot_20-4.png)

**Step.2** .To change Apache's root directory

```
 #cd /etc/apache2/sites-available
```

![](images/Screenshot_21-3.png)

**Step.3** Then open the 000-default.conf file

```
#vi 000-default.conf
```

![](images/Screenshot_22-3.png)

**Step.4** Edit the DocumentRoot option mentioned at the above screenshot.  
DocumentRoot /var/www/my/html/change folder.

**Step.5** Then restart the apache server:

```
#sudo service apache2 restart
```

![](images/Screenshot_23-5.png)

I am hoping that you are familiar with how to alter the apache2 web folder.

**Thankyou**

Must Read : [4 Effective Ways to Determine the Name of a Plugged USB Device in Linux](https://utho.com/docs/tutorial/4-effective-ways-to-determine-the-name-of-a-plugged-usb-device-in-linux/)
