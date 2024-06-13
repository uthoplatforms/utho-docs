---
title: "How to install Apache on CentOS 7"
date: "2022-09-18"
---

![Install apache on centos](images/How-to-install-apache-web-server-on-centos-7.7-1024x576.png)

Apache is a well-known HTTP Server that is free, open source, and runs on Unix-like operating systems like Linux and Windows OS. Since it came out 20 years ago, it has been the most popular web server. Many sites on the Internet use it to run. It is easy to install and set up so that a Linux or Windows server can host one or more websites.

In this article, we'll show you how to use the command line to install, set up, and manage Apache HTTP web server on a CentOS 7 or RHEL 7 server.

## Prerequisites

- A CentOS 7 Server Minimal Install
- Super user ( root ) or any normal user
- yum repository configured

## Apache Important Files and Directoires

- The default server root directory (top level directory containing configuration files): **/etc/httpd**
- The main Apache configuration file: **/etc/httpd/conf/httpd.conf**
- Additional configurations can be added in: **/etc/httpd/conf.d/**
- Configurations for modules: **/etc/httpd/conf.modules.d/**
- Apache default server document root directory (stores web files): **/var/www/html**

```
Install Apache Web Server
```
First update the system software packages to the latest version.

```
yum update -y
```
Next, do the following with the YUM package manager to install Apache HTTP server from the default software repositories.

```
yum install httpd -y
```
## Configure Apache HTTP Server on CentOS 7

After installing Apache web server, you can start it for the first time and set it to start automatically when the system starts up.

```
systemctl start httpd
systemctl enable httpd
```
You can confirm the status of apache server by using following command

```
systemctl status httpd
```
### Configure firewalld to Allow Apache Traffic

The firewall that comes with CentOS 7 is set up to block Apache traffic by default. To let web traffic through on Apache, change the system firewall rules to allow HTTP and HTTPS packets to come in.

```
firewall-cmd --permanent --add-service http
firewall-cmd --permanent --add-service https
firewall-cmd --reload
```
### Test Apache HTTP Server on CentOS 7

If you now go to the following URL, a default Apache page will be shown.

```
http://server-ip
```

![](images/image-79-1024x402.png)
