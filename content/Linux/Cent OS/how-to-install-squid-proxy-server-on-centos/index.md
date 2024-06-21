---
title: "How to Install Squid Proxy Server on CentOS"
date: "2022-04-25"
title_meta: "Guide to install Squid Proxy on CentOS 7"
description: 'This guide walks you through installing Squid, a popular and open-source web caching proxy server, on your CentOS system. Squid can improve network performance by caching frequently accessed web content, reducing bandwidth usage and improving user experience. It can also be used for content filtering or access control purposes.'

keywords:  ['Squid', 'CentOS', 'proxy server', 'web caching', 'content filtering', 'yum']
tags: ["Squid Proxy", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/how-to-install-squid-proxy-server-on-centos/']
tab: true
---

![](images/How-to-Install-Squid-Proxy-Server-on-CentOS-1024x576.png)

#### Prerequisites:

- CentOS operating system
- Access to a terminal window/command-line (Ctrl-Alt-T)
- A CentOS user with root or sudo priviledges
- The **yum package installer**, included by default
- A text editor, such as **vim**

**Let's begin the installation.**

Login to your server via Putty.

**Step 1:** Refresh CentOS Software Repositories

```
 sudo yum -y update 
```

**Step 2:** Install Squid Package on CentOS

```
 yum -y install squid 
```

Now start Squid by entering the following command:

```
 systemctl start squid 
```

To set up an automatic start at boot:

```
 systemctl enable squid 
```

Review the status of the service, use:

```
 systemctl status squid 
```

In the example below, we see that the state is ‘Active.’

![](images/squid-systemctl-status-centos.png)

#### **Configuring the Squid Proxy Server**

The Squid configuration file is found at **/etc/squid/squid.conf.**

1\. Open the file in your preferred text editor (**vim** was used in this example}:

```
 sudo vi /etc/squid/squid.conf 
```

2\. Navigate to find the **http\_port option**. Typically, this is set to listen on **Port 3218**. This port usually carries TCP traffic. If your system is configured for traffic on another port, change it here:

![](images/configure-squid-proxy-server-centos-port-number.png)

You may also set the proxy mode to **transparent** if you’d like to prevent Squid from modifying your requests and responses.

Change it as follows:

```
 http_port 1234 transparent 
```

3\. Navigate to the **http\_acacess deny all** option.

It is currently configured to block all HTTP traffic, and no web traffic is allowed as shown below.

![](images/configure-squid-proxy-server-centos-allow-access.png)

Change this to the following:

```
 http_access allow all 
```

4\. Restart the Squid service by entering:

```
 sudo systemctl restart squid 
```

Squid Proxy Server successfully installed.

Thank You!
