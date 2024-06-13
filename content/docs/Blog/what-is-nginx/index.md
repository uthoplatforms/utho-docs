---
title: "What is NGINX?"
date: "2020-04-20"
---

NGINX is an open source web server with high load balancing capabilities, reverse proxy and cache features. It was planned initially to solve problems of scaling and rivalry with existing web servers. It is one of today's most popular web servers, due to its event-based asynchronous architecture.

## Before You Begin

1\. In the Start and Securing your Application Guides, set up your cloud application.

2\. You can customize this using our DNS Manager guide if you want your site's custom domain name.

3\. You should not forget to update your file `/etc/hosts` with the public IP address and the full domain name of your site, as explained in the hosts file section of the Getting Started Update Guide your system your network.

## Install NGINX

Now, it is safer to use the version included in the CentOS repositories to install NGINX on CentOS 8:

```
sudo yum update sudo dnf install @nginx
```

## Add a Basic Site

1\. Build your site’s new tab. Substitute abc.com domain name for your article.

```
 sudo mkdir -p /var/www/abc.com 
```

2\. Use SELinux’s chcon command to change the file security context for web content:

```
sudo chcon -t httpd_sys_content_t /var/www/abc.com -R sudo chcon -t httpd_sys_rw_content_t /var/www/abc.com -R 
```

3\. In the `/var/www/abc.com` directory you may add your site's files. Creates an index file with a basic example of `"Microhost Cloud."` Build a new `/var/www/abc.com/index.html` file with your preferred text editor. Substitute `abc.com` by the name of your website or the public IP address of your cloud server.

```file {title="/var/www/abc.com/index.html" lang="aconf"}
My Basic Website

```
Microhost Cloud!
```
```

## Configure NGINX

In `/etc/nginx/sites-available` NGINX site-specific settings files are stored and symlinked to a `/etc/nginx /sites-enabled/.` Typically, for each domain or sub-domain you host, you generate a new server block file in the directory you have accessed . Then, a connection to your files will be set up in the website-enabled directory.

1\. Make your configuration files directories:

```
sudo mkdir -p /etc/nginx/{sites- available,sites-enabled} 
```

2\. In the text editor of your choosing, build your site configuration file. Replace `abc.com` with your site's domain name or IP address in the server name directive and `/var/www/abc.com` with your own root address in the root directive.

```file {title="/etc/nginx/sites-available/abc.com" lang="aconf"}
server { listen 80; listen [::]:80; server_name abc.com; root /var/www/abc.com; index index.html; location / { try_files $uri $uri/ =404; } }
```

3\. To allow your configuration, set a new `symlink` for the directory `/etc/nginx/sites-enabled/`

```
sudo ln -s /etc/nginx/sites-available/abc.com /etc/nginx/sites-enabled/
```

4\. To include a directive in a `/etc/nginx/sites-enabled/*` directory, update NGINX configuration file `/etc/nginx/nginx.conf`. It must be part of the http block of your settings files  include `/etc/nginx/conf.d/*`.conf; line to list the inclusive directives.

```file {title="/etc/nginx/nginx.conf" lang="aconf"}
… http { … include /etc/nginx/conf.d/_.conf; include /etc/nginx/sites-enabled/_; … }
```

5\. Enable the traffic firewall:

```
sudo firewall-cmd --zone=public --permanent --add-service=http sudo firewall-cmd --zone=public --permanent --add-service=https sudo firewall-cmd --reload
```

## Test and Enable NGINX

1\. This command helps you to check your NGINX setup:

 ```
sudo nginx -t 
```

2\. Start the service with the commands below:

```
sudo systemctl enable nginx sudo systemctl start nginx
```

3\. Check that it’s running:

```
sudo systemctl status nginx 
```

4\. Browse the domain name or IP address of your cloud server in the browser. Your easy page should be seen.
