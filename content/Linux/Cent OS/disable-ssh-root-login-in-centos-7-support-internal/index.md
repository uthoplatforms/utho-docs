---
title: "Disable SSH root login in Centos 7"
date: "2022-06-22"
title_meta: "Guide for Disable SSH root login in Centos 7"
description: "Learn how to secure your CentOS 7 server by disabling SSH root login. This guide provides step-by-step instructions to modify SSH configuration settings to prevent root access via SSH on CentOS 7, enhancing server security and mitigating potential security risks.
"
keywords: ["disable SSH root login CentOS 7", "CentOS 7 SSH root login disable", "secure SSH CentOS 7", "prevent root SSH access CentOS 7", "disable root SSH access CentOS 7", "CentOS 7 SSH security", "CentOS 7 secure SSH configuration", "CentOS 7 SSH hardening"]

tags: ["Disable SSH root login", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/disable-ssh-root-login-in-centos-7-support-internal/']
tab: true
---

![](images/Disable-SSH-root-login-in-Centos-7-Support-Internal-1-1024x576.png)

Today, everyone knows that Linux systems comes with root user access and by default the root access is enabled for outside world. For security reason it’s not a good idea to have ssh root access enabled for unauthorized users. Because any hacker can try to brute force your password and gain access to your system.

So, its better to have another account that you regularly use and then switch to root user by using ‘su –‘ command when necessary. Before we start, make sure you have a regular user account and with that you su or sudo to gain root access.  

In Linux, it’s very easy to create separate account, login as root user and simply run the ‘adduser‘ command to create separate user. Once user is created, just follow the below steps to disable root login via SSH.

To disable root login, open the main ssh configuration file /etc/ssh/sshd\_config with your choice of editor.  

```
 # vi etc/ssh/sshd_config 
```

Search for the following line in the file.  

```
 #PermitRootLogin no 
```

Now need to untag this line (remove #) then save the file and exit.

PemitRootLogin no

Next, we need to restart the SSH daemon service.

```
 # systemctl restart ssh.service 
```

:: Now need to check the server with new ssh connection, that actually root access is working or not

![](images/pasted-image-0-4-6.png)

Thank You.
