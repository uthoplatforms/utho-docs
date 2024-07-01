---
title: "How to install Apache Tomcat 9 on Windows Server"
date: "2022-12-23"
Title_meta: GUIDE TO Install Apache Tomcat 9 on Windows Server
Description: This guide provides step-by-step instructions on how to install Apache Tomcat 9 on Windows Server. Learn how to download Tomcat, configure Java environment variables, set up Tomcat as a Windows service, and deploy web applications, enabling efficient Java web development and server management.
Keywords: ['Apache Tomcat 9', 'Windows Server', 'install Tomcat', 'Java web development', 'server configuration', 'web application deployment']
Tags: ["Apache Tomcat 9", "Windows Server", "Install Tomcat", "Java Web Development", "Server Configuration", "Web Application Deployment"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-install-apache-tomcat-9-on-windows-server']
tab: true
---

![](images/How-to-install-Apache-Tomcat-9-on-Windows-Server_utho.jpg)

```
Introduction
```
[**Apache Tomcat version 9.0**](https://tomcat.apache.org/tomcat-9.0-doc/index.html) implements the Servlet 4.0 and JavaServer Pages 2.3 specifications from the Java Community Process, and includes many additional features that make it a useful platform for developing and deploying web applications and web services.

Firstly, we should have **[Java Development kit](https://en.wikipedia.org/wiki/Java_Development_Kit)** (JDK) isntalled on the machine. Please refer to the blog tagged below on **how to install JDK on Winodws Server**.

[How to install Java Development kit (JDK) on Windows Server.](https://utho.com/docs/tutorial/how-to-install-java-development-kit-on-windows-server/)

Now, We will download Apache Tomcat setup files.

[Download Apache Tomcat 9](https://tomcat.apache.org/download-90.cgi)

![](images/Screenshot_1-29-1024x574.png)

Step 1. Login to your winodws server

Step 2. Double click on the setup media of JDK to begin installation.

![](images/Screenshot_2-34-1024x548.png)

Step 3. Click **Next** to start installation.

![](images/Screenshot_3-27.png)

![](images/Screenshot_4-30.png)

![](images/Screenshot_5-24.png)

Set password for your Tomcat Manager

![](images/Screenshot_6-23.png)

Select the path as shown below:

![](images/Screenshot_7-19.png)

![](images/Screenshot_8-19.png)

![](images/Screenshot_9-15.png)

![](images/Screenshot_10-9.png)

![](images/Screenshot_11-9.png)

Apache Tomcat 9 successfully installed.

Step 4. Launch Tomcat application

![](images/Screenshot_12-9-1024x550.png)

It will launch in cmd

![](images/Screenshot_13-7-1024x596.png)

Step 5. hit [http://localhost:8080/](http://localhost:8080/) to view tomcat

![](images/Screenshot_14-5-1024x548.png)

Step 6. hit [http://localhost:8080/manager/](http://localhost:8080/manager/) to go to tomcat manager page.

Use username and password that we setup during installation.

![](images/Screenshot_15-5-1024x520.png)

![](images/Screenshot_16-4-1024x520.png)

Step 7. Open Tomcat 9 manager

![](images/Screenshot_17-3-1024x548.png)

Set the Apache service to start automatically. so that you don't have to start the service manually everytime.

![](images/Screenshot_18-3.png)

Apache Tomcat 9 installation completed on Windows Server.

Thank You.
