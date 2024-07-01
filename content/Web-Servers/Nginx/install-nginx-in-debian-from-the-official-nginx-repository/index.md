---
title: "Install NGINX in Debian from the Official NGINX Repository"
date: "2020-04-20"
title_meta: "Install NGINX in Debian from the Official NGINX Repository"
description: "Install NGINX in Debian from the Official NGINX Repository"

keywords: ['nginx', "ubuntu"]

tags: ["NGINX"]
icon: "nginx"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Web-Servers/Nginx/install-nginx-in-debian-from-the-official-nginx-repository']
tab: true
---

These instructions install Debian 9 NGINX Mainline from the official repository of NGINX Inc. For other distributions

1\. Open a text editor to `/etc/apt/sources.list`  and add the next line to the bottom:

```file {title="/etc/apt/sources.list" lang="aconf"}
deb http://nginx.org/packages/mainline/debian/ stretch nginx
```

2\. Import the signing key for the repository and add it to apt:

```
sudo wget http://nginx.org/keys/nginx_signing.key sudo apt-key add nginx_signing.key
```

3\. Install NGINX:

```
sudo apt update sudo apt install nginx
```

4\. Ensure that NGINX runs and can start reboots automatically:

```
sudo systemctl start nginx sudo systemctl enable nginx
```

Thankyou..
