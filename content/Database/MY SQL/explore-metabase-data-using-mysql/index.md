---
title: "Explore Metabase data using MySQL"
date: "2022-09-22"
title_meta: "Guide to explore Metabase data using SQL"
description: "Metabase gives you a way to ask questions about data in your browser. Metabase not only lets you run SQL queries, but it also lets you analyse data without SQL, make dashboards, and track metrics. This guide explains how to connect MySQL to Metabase and then use a reverse proxy to deploy on NGINX."
keywords: ["MYSQL", "Metabase"]

tags: ["MySQL", "Metabase"]
icon: "mysql"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Database/MY SQL/explore-metabase-data-using-mysql/']
tab: true
---

![featured image](images/Explore-Metabase-data-using-MySQL_utho.jpg)

**Introduction**

Metabase gives you a way to ask questions about data in your browser. Metabase not only lets you run SQL queries, but it also lets you analyse data without SQL, make dashboards, and track metrics. This guide explains how to connect MySQL to Metabase and then use a reverse proxy to deploy on NGINX.

From SQLite to PostgreSQL, there are a number of other databases that can be used. An easy-to-use interface makes it very easy to see the results. This makes Metabase a flexible way to share data, even with people who don't know much about analysis.

## Install Metabase

Java Runtime Environment

Install software-properties-common to add new repositories quickly:

```
#sudo apt-get install software-properties-common
```

Add the Java PPA:

```
#sudo add-apt-repository ppa:microhost/java
```

Update the list of sources:

```
#sudo apt-get update
```

Install the Java JDK 8:

```
#sudo apt-get install oracle-java8-installer
```

## MySQL Server

Install MySQL Server. Enter the root password if prompted:

```
#sudo apt install mysql-server
```

Connect as the root user:

```
#mysql -u root -p
```

Make a new user and database in Metabase.

```
#CREATE DATABASE employees;
```

```
#CREATE USER 'metabase_user' IDENTIFIED BY 'password';
```

```
#GRANT ALL PRIVILEGES ON employees.* TO 'metabase_user';
```

```
#GRANT RELOAD ON *.* TO 'metabase_user';
```

```
#FLUSH PRIVILEGES;
```

```
#exit;
```

## Get the Metabase

Metabase lets you get the jar file:

```
#wget http://downloads.metabase.com/v0.28.1/metabase.jar
```

Transfer the file into the /var directory so that it will be ready to run after a restart:

```
#sudo mv metabase.jar /var/metabase.jar
```

###### Using NGINX's Reverse Proxy

## Install NGINX

```
#sudo apt install nginx
```

Create a new NGINX configuration file with the following values, substituting your FDQN or public IP address for server name:

```
File: /etc/nginx/conf.d/metabase.conf
```

```
 server_name _;  
location / {  
proxy_pass http://microhost:3330/;  
proxy_redirect http://microhost:3330/ $scheme://$host/;  
proxy_http_version 1.1;  
proxy_set_header Upgrade $http_upgrade;  
proxy_set_header Connection "Upgrade";  
}
```

Make sure there are no problems with the setup:

```
#sudo nginx -t
```

Restart NGINX:

```
#sudo systemctl restart nginx
```

## Start Metabase on reboot

Check where your JDK binary is located:

```
#which java
```

This should output a path like /usr/bin/java when done correctly.

Make sure that Metabase is started up properly by creating a systemd configuration file. ExecStart= should be set to the JDK path from the previous section. Make careful to substitute User with your own Unix username when you run this:

```
File: /etc/systemd/system/metabase.service
```

```
Unit  
Description=Metabase server  
After=syslog.target  
After=network.target[Service]  
User=username  
Type=simple  
Service  
ExecStart=/usr/bin/java -jar /var/metabase.jar  
Restart=always  
StandardOutput=syslog  
StandardError=syslog  
SyslogIdentifier=metabase  
Install  
WantedBy=multi-user.target
```

Make the necessary adjustments:

```
#sudo systemctl start metabase
```

Verify if Metabase is operational:

```
#sudo systemctl status metabase
```

## If you are using firewall

The use of UFW is highly recommended for securing your database against unwanted access. It is a prudent practise to leave ports 80/443 and SSH open by default:

```
#sudo ufw allow http
```

```
#sudo ufw allow https
```

```
#sudo ufw allow ssh
```

```
#sudo ufw enable
```

Check the firewall rules:

```
#sudo ufw status
```

**\*\*To access the metabase hit your IP on the browser\*\***

**Thank you**
