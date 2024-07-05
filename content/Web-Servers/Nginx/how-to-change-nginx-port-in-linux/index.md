---
title: "How to change NGINX port in Linux"
date: "2022-06-22"
title_meta: "How to change NGINX port in Linux"
description: "Learn how to change NGINX port in Linux.
"
keywords: ["Nginx"]

tags: ["Nginx", "ubuntu"]
icon: "nginx"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Web-Servers/Nginx/how-to-change-nginx-port-in-linux']
tab: true
---

![](images/How-to-change-NGINX-port-in-Linux-1024x576.png)

**Step 1:** Check the default port by accessing the server IP address in the browser .

**Step 2:**  Login into the server using root credentials through putty .

![](images/pasted-image-0-20.png)

**Step 3:** Open Nginx configuration file with a text editor .

```
 # vi /etc/nginx/nginx.conf
```

Press ‘ I ’ for the insert/modification mode .

**Step 4:** Change the default port of the nginx to the custom port

Before change : port is 80 as shown below in screetshot

![](images/pasted-image-0-4-2.png)

After change : Port has been changed to 8081 (here) as shown in screenshot

![](images/pasted-image-0-5-2.png)

**Step 5** Restart the nginx and network service .

```
 # systemctl status nginx 
```

```
 # systemctl status network 
```

**Step 6:** Now check the webserver while accessing the server IP\_address:8081  in the browser. 

URL: - [http://server\_domain\_name\_or\_IP:8081](http://server_domain_name_or_ip:8081/)

![](images/pasted-image-0-8-2.png)

Thank You :)
