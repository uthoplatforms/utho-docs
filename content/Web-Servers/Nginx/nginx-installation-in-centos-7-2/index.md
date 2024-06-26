---
title: "NGINX Installation in CentOS 7"
date: "2022-06-22"
title_meta: "GUIDE TO Install NGINX  on CentOS 7"
description: 'This guide walks you through installing Nginx, a powerful and efficient web server, on your CentOS 7 system. Nginx is known for its speed, scalability, and flexibility, making it a popular choice for powering modern web applications.'

keywords:  ['CentOS 7', 'Nginx', 'web server', 'installation']
tags: ["NGINX", "CentOS 7"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/nginx-installation-in-centos-7-2/']
tab: true
---

![](images/NGINX-Installation-in-CentOS-7-1024x576.png)

**Step 1:** Add Nginx repository

```
 # yum install epel-release 
```

**Step 2:**  Install Nginx using following command

```
 # yum install nginx 
```

**Step 3:** Start the service of Nginx

```
 # systemctl start nginx 
```

**Step 4:** Check status of Ngnix

```
 # systemctl status nginx 
```

**Step 5:** If you are running a firewall, run the following commands to allow HTTP and HTTPS traffic:

Command :

```
 # sudo firewall-cmd --permanent --zone=public --add-service=http  
```

```
 # sudo firewall-cmd --permanent --zone=public --add-service=https 
```

```
 # sudo firewall-cmd --reload 
```

**Step 6:** Now check the webserver while accessing the server IP address in the browser. 

URL: - [http://server\_domain\_name\_or\_IP/](http://server_domain_name_or_ip/)

![](images/pasted-image-0-8-3.png)

Thank you :)
