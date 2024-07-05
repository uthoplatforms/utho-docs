---
title: "NGINX Installation in CentOS 7"
date: "2022-06-22"
title_meta: "NGINX Installation in CentOS 7"
description: "NGINX Installation in CentOS 7"

keywords: ['nginx', "tls"]

tags: ["NGINX"]
icon: "nginx"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['content/Web-Servers/Nginx/nginx-installation-in-centos-7']
tab: true
---

**Step 1:** Add Nginx repository .

```
 # yum install epel-release
```

**Step 2:**  Install Nginx using following command .

```
 # yum install nginx 
```

**Step 3**: Start the service of Nginx .

```
 # systemctl start nginx 
```

**Step 4:** Check status of Ngnix .

```
 # systemctl status nginx 
```

**Step 5 :** If you are running a firewall, run the following commands to allow HTTP and HTTPS traffic:

```
 # sudo firewall-cmd --permanent --zone=public --add-service=http  
```

```
 # sudo firewall-cmd --permanent --zone=public --add-service=https 
```

```
 # sudo firewall-cmd --reload 
```

**Step 6 :** Now check the webserver while accessing the server IP address in the browser. 

**URL:** \- [http://server\_domain\_name\_or\_IP/](http://server_domain_name_or_ip/)

Thank you :)
