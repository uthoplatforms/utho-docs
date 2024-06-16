---
title: "VirtualHost creation in Tomcat 10/9/8/7"
date: "2022-09-25"
---

<figure>

![VirtualHost creation in Tomcat 10/9/8/7](images/VirtualHost-creation-in-Tomcat-10_9_8_7-1024x576.png)

<figcaption>

VirtualHost creation in Tomcat 10/9/8/7

</figcaption>

</figure>

## What is Tomcat

In this guide, you will learn how to create VirutalHost in Tomcat 10/9/8/7. But before that, first learn that Java applications are served via Apache Tomcat, a web server and servlet container. It is an open source implementation of the Jakarta Servlet, Jakarta Server Pages, and other Jakarta EE platform technologies.

## What is Virtual Host

Virtual hosting allows everyone on a single server to host multiple domains (websites). This is a principle of sharing resources between multiple hosting accounts. Virtual hosting is typically used by common hosting servers where multiple users can host several websites on a single server.

We built an IP 192.168.10.45 Linux server for Tomcat hosting only. Tomcat 9 installed and set up to run on port 80. Following that, we used Tomcat Admin panel to deploy two java web applications on tomcat. All apps now running on the following URLs:

```
http://192.168.10.45/Dir1
http://192.168.10.45/Dir2
```

We would now like to run all web applications on big domain names such as example.com and mydomain.org. End users may use the key domain name to access the web application.

## Prerequisites

- One Ubuntu 20.04 server
- A super user ( root ) or any normal user with SUDO privileges.
- Entry for your domain in /etc/hosts file so that domains are reachable from tomcat server
- A ubuntu server with already installed tomcat server. Please follow the [this guide](https://utho.com/docs/tutorial/how-to-install-tomcat-on-ubuntu/) to learn more about how to install tomcat 10 on ubuntu server

## Creating VirtualHost in Tomcat

First, navigate to Tomcat's install directory and edit **/config/server.xml** or **/conf/server.xml** file in the preferred editor to build virtual Tomcat hosts. For this tutorial, we will [use vi](https://www.vim.org/). Then build your application's virtual host. The virtual host below is:

- First application with domain name example.com and path /opt/tomcat/webapps/Dir1 document root.
- Second application with domain name mydomain.org and /opt/tomcat/webapps/Dir2 document root.

```
vi /config/server.xml
```
```
<Host name="example.com"  appBase="webapps" unpackWARs="true" autoDeploy="true">
 <Alias>www.example.com</Alias>
 
 <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
           prefix="example_access_log" suffix=".txt"
           pattern="%h %l %u %t %r %s %b" />
 
 <Context path="" docBase="/opt/tomcat/webapps/Dir1"
    debug="0" reloadable="true"/>
</Host>
 
 
<Host name="mydomain.org"  appBase="webapps" unpackWARs="true" autoDeploy="true">
 <Alias>www.mydomain.org</Alias>
 
 <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
           prefix="mydomain_access_log" suffix=".txt"
           pattern="%h %l %u %t %r %s %b" />
 
 <Context path="" docBase="/opt/tomcat/webapps/Dir2"
    debug="0" reloadable="true"/>
</Host>
```

After making the above changes we have to **restart** the Tomcat

And now, you have learnt how to create VirutalHost in Tomcat 10/9/8/7

Thank You :)
