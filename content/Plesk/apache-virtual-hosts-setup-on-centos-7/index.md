---
title: "Apache Virtual Hosts setup on CentOS 7"
date: "2020-06-12"
---

## Introduction

The web server of Apache is the most popular way to deliver web content. It serves over half of all active websites in the Internet and is extremely powerful and flexible.

Apache divides its features and components into separate units, which can be independently customized and set up. A virtual Host is called the basic unit describing a single site or domain. Virtual hosts allow one server, through a matching system, to host several domains or interfaces. This is important for those who want to host more than one VPS site.

Each configured domain will direct the visitor to a certain directory that contains information on this website without indicating that the same server is also responsible for other websites. This scheme can be expanded without software limits provided the traffic of all the sites is handled by your server.

## Prerequisites

- Log in to the server with the ssh.
- The account should have the root privilege.
- Update the server packages using **yum update -y**

## Installation of Apache

```
 [root@Microhost]# yum -y install httpd 
```

Now the Apache server has been installed. We will enable the apache service so that it will automatically up in boot time.

```
 [root@Microhost]# systemctl enable httpd.service 
```

Now we will start the service of apache server by following command:

```
 [root@Microhost]# systemctl start httpd.service 
```

\[ht\_message mstyle="alert" title="NOTE" " show\_icon="true" id="" class=""style="" \]The setup example in this guide makes one virtual host for domain1.com and another for domain2.com . These are mentioned in the entire guide, but you should replace your own domains or values as you follow. .\[/ht\_message\]

## Creation of virtual host directory

Our document root is set to individual directories in the /var/www directory and is the top level directory that Apache looks to find the content to serve. For each of the virtual host we plan to make, we will create a directory here.

We will create a public html directory that contains our current files in each of these directories.

These directories can be made by the mkdir command (using the -p flag to create a folder with a nested folder inside it):

```
 [root@Microhost]# mkdir -p /var/www/domain1.com/public_html 
```

```
 [root@Microhost]# mkdir -p /var/www/domain2.com/public_html 
```

We should also change some of our permissions to ensure that the general web directory and all the files and folders inside are allowed access to pages so that they can be served correctly:

```
 [root@Microhost]# chmod -R 755 /var/www 
```

You should now have the permissions on your web server to serve content and the content of the corresponding folders should be created by your user.

## Creation of test pages for each domains

Let us now create some content to serve, as we now have a directory structure in place.

Our pages are very simple because this is only for demonstration and testing. For each site that identifies the given domain, we will only make an index.php page.

Start with domain1.com. Let's start. In our editor, we can open an index.php file by typing:

```
 [root@Microhost]# vi /var/www/domain1.com/public_html/index.php 
```

copy the below content and paste it into the file.

```
<!DOCTYPE html>
<html>
<body>

<h1>My first PHP page</h1>

<?php
echo "Hello World!";
?>

</body>
</html>
```

Save the file using **:wq** and exit from the text editor.

We can perform the above steps for domain2 also, only we have to create a file on domain2.com location as given below:

```
 [root@Microhost]# vi /var/www/domain2.com/public_html/index.php 
```

copy the below content and paste it into the file.

```
<!DOCTYPE html>
<html>
<body>

<h1>My Second PHP page</h1>

<?php
echo "Hello World!";
?>

</body>
</html>
```

Save the file and exit from the text editor.

## Create New Virtual Host Files

This is the virtual host file to specify how the Apache web server is to answer the various domain requests and how our separate websites are configured.

In order for us to start by setting the directory in which our virtual hosts are saved, and the directory in which Apache is told that the virtual host is ready to serve visitors. All of our virtual host files are maintained by the sites-available directory, while the sites-enabled directory has symbolic links to virtual hosts that we want to publish. By typing, we can create both directories:

```
 [root@Microhost]# mkdir /etc/httpd/sites-available 
```

```
 [root@Microhost]# mkdir /etc/httpd/sites-enabled 
```

\[ht\_message mstyle="alert" title="NOTE" " show\_icon="true" id="" class=""style="" \]The Debian contributor introduced this directory layout but we include it here to make it even more flexible with our virtual hosts management (as this is easier to allow and uncheck virtual hosts temporarily)..\[/ht\_message\]

In sites-enabled directory, we should tell Apache to search for virtual hosts. In order to achieve this, we will edit the main Apache configuration file and add an optional line for further configuration of files. We can edit the file by following command.

```
 [root@Microhost]# /etc/httpd/conf/httpd.conf 
```

Add the following line at the end of the file content.

```
 IncludeOptional sites-enabled/*.conf 
```

save the file and exit from the text editor.

## Create the First Virtual Host File

Create a configuration file for domain1.com at the below location.

```
 [root@Microhost]# vi /etc/httpd/sites-available/domain1.com.conf 
```

The configuration file should look like :

```
<VirtualHost *:80>

    ServerName domain1.com
    ServerAlias www.domain1.com
    DocumentRoot /var/www/domain1.com/public_html
    ErrorLog /var/www/domain1.com/error.log
    CustomLog /var/www/domain1.com/requests.log combined
</VirtualHost>
```

save the file and exit from the text editor.

Now do the same process for another domain.

```
 [root@Microhost]# vi /etc/httpd/sites-available/domain2.com.conf 
```

The configuration file should look like

```
<VirtualHost *:80>

    ServerName domain2.com
    ServerAlias www.domain2.com
    DocumentRoot /var/www/domain2.com/public_html
    ErrorLog /var/www/domain2.com/error.log
    CustomLog /var/www/domain2.com/requests.log combined
</VirtualHost>

```

save the file and exit from the text editor.

## Enable the New Virtual Host Files

We must now enable our virtual host files so that Apache knows how to serve visitors. In this way, in the sites-enabled directory, we can create a symbolic link for each virtual host:

```
 [root@Microhost]# ln -s /etc/httpd/sites-available/domain1.com.conf /etc/httpd/sites-enabled/domain1.com.conf 
```

```
 [root@Microhost]# ln -s /etc/httpd/sites-available/domain2.com.conf /etc/httpd/sites-enabled/domain2.com.conf 
```

Now restart the apache services.

```
 [root@Microhost]# systemctl restart httpd 
```

Thank You :)
