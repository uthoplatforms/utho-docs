---
title: "Install Apache 2 Web Server in CentOS 5"
date: "2022-11-11"
Title_meta: GUIDE TO Install Apache 2 Web Server in CentOS 5

Description: Follow this guide to install Apache 2 Web Server on CentOS 5. Learn step-by-step instructions to set up and configure Apache 2, enabling you to host and manage websites effectively on your CentOS 5 system.

Keywords: ['Apache 2', 'CentOS 5', 'install Apache', 'web server', 'server setup']

Tags: ["Apache 2", "CentOS 5", "Web Server", "Server Setup"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Web-Servers/Apache/install-apache-2-web-server-in-centos-5/']
tab: true
---

This tutorial demonstrates how the Apache web server can be installed and configured on CentOS 5. Every setup is done via the terminal; make sure that you have logged in as root via SSH. If you have not followed the initiation guide, it is best to do so before this guide begins. Also note that you may want to consider using our CentOS LAMP Guide if you want to mount a full LAMP stack.

![](images/Install-Apache-2-Web-Server-in-CentOS-5_utho.jpg)

## Set the Hostname

Please ensure that you follow our instructions for setting your hostname before downloading and configuring the components mentioned in this section. Make sure the following commands are set correctly:

```
hostname hostname -f
```

You will show your short hostname in the first command and your fully qualified FQDN domain name in the second command.

## Install Apache HTTP Server

Ensure that your system is up-to-date with a command:

```
yum update
```

You will want to make sure your installation of CentOS is configured to allow inbound traffic to port 80; by issuing a shell request, you can customize the built-in firewall.

Type the following order for Apache HTTP Server installation:

```
yum install httpd
```

Issue the following web server launch command:

```
/etc/init.d/httpd start
```

To make sure that Apache starts after the next reboot process, issue the command below:

```
chkconfig httpd on
```

## Install Support for Scripting

The following commands are optional and should be executed if you need support from Apache in PHP, Ruby, Python or Perl for server-side scripting.

To add support for Ruby, please issue the following command:

```
yum install ruby
```

Note that only Ruby programming language support is installed. If scripts and applications are to be run in ruby on web pages, some kind of CGI handler will be required.

Issue the following order to enable Perl support:

```
yum install mod_perl
```

To add support for Python, edit the following command:

```
yum install mod_python
```

If you need MySQL support in Php, you do need Php MySQL support to install:

```
yum install MySQL-python
```

The following command is given to install PHP support, including can support bundles:

```
yum install php php-pear
```

If you still plan to run PHP with mysql, then install mySQL support too:

```
yum install php-mysql
```

## Configure Apache

The httpd.conf file located at: /etc / httpd / conf / httpd.conf includes all Apache settings. We recommend that you back up this file to your home directory, as follows:

```
cp /etc/httpd/conf/httpd.conf ~/httpd.conf.backup
```

All files in /etc / httpd / conf.d/ ended with the.conf extension are considered by default to be configuration files. We suggest adding the non-standard configuration options into these folders in the directory. Regardless of how you arrange your configuration files, it is strongly recommended that you make frequent backups of established working conditions.

Now we're going to set up virtual hosting to host many domains (or subdomains) with the server. Such websites can be managed by different users, or by a single user, if you wish.

Before beginning, we recommend you merge all virtual hosting configuration into a single vhost.conf file located in the /etc / httpd / conf.d/ directory. Open this file in your favorite text editor and we'll continue with virtual hosting setup.

## Configure Name-based Virtual Hosts

First, we have to configure Apache to "listen" only for IP address requests. Apache listens to requests on all IP addresses by default.

```file {title="Apache Virtual Host Configuration" lang="aconf"}
NameVirtualHost \*:80
```

Now, for any site you need to host with this api, you can build virtual host entries. Two examples of "example.org" and "example.net" websites are given here:

```file {title="Apache Virtual Host Configuration" lang="aconf"}
ServerAdmin admin@example.org ServerName example.org ServerAlias www.example.org DocumentRoot /srv/www/example.org/public_html/ ErrorLog /srv/www/example.org/logs/error.log CustomLog /srv/www/example.org/logs/access.log combined

ServerAdmin webmaster@example.net ServerName example.net ServerAlias www.example.net DocumentRoot /srv/www/example.net/public_html/ ErrorLog /srv/www/example.net/logs/error.log CustomLog /srv/www/example.net/logs/access.log combined
```

Notes concerning this configuration example:

- All files for the pages you are hosting are located in the folders below /srv / www. Such directories can be symbolically connected to other locations if you choose to have them elsewhere.
- ErrorLog and CustomLog entries are suggested, but are not needed for more fine grained logging. The log directories must be generated before you restart Apache if they are specified (as shown above).

Before using the above configuration, you will need to build the necessary folders. The following commands for the above configuration may be used for this:

```
mkdir -p /srv/www/example.org/public_html mkdir -p /srv/www/example.org/logs mkdir -p /srv/www/example.net/public_html mkdir -p /srv/www/example.net/logs
```

After your virtual hosts have been set up, first issue the following command to run Apache:

```
/etc/init.d/httpd start
```

Virtual domain hosting will now function-if you have configured DNS to show your domain at the IP address of your Instance. Note that as many virtual hosts as you need can be installed with Apache.

Whenever you have modified an option in your vhost.conf file, remember to reload the configuration with the following command:

```
/etc/init.d/httpd reload
```

## Configuration Options

The huge amount of versatility offered in its configuration files is one of Apache's strengths and obstacles. The main configuration file is located in /etc / httpd / conf / httpd.conf for the Apache 2 installation in CentOS 5, but the configuration for Apache is also downloaded in a certain order from directories at a variety of different locations. In the following order the configuration files are read, with later items preceding earlier and probably conflicting options listed.

1. /etc/httpd/conf/httpd.conf
2. In order to read.conf files, ordered alphabetically by name of file in /etc / httpd / conf.d/ directory.

Note, subsequent files triumph over previously listed files. Files will be read in an order dependent on an alpha-numeric sort of file name in a directory of the specified configuration files.

Apache will follow symbolic links to read configuration files so that you can create links to files which are actually elsewhere in your file system in these directories and locations.

For most situations we are advised not modifying the default configuration file for compliance with best practice, because most Apache controls can be handled from files that are included in the conf.d/ directory, and administrators may avoid unintended conflicts. When you want to edit httpd.conf, backup the default set-up file and backups of known working states for your device. In case your modifications render unintended errors, you can easily return your server to a working state.

```
cp /etc/httpd/conf/httpd.conf /etc/httpd/conf/httpd-conf.backup-1
```

As mentioned above, as in our LAMP guide for CentOS 5.2 configuration files for virtual host sites, you should be able to break site-specific configuration information into additional files, for instance in /etc / httpd / conf.d / vhost.conf.

## Install Apache Modules

One of the key strengths of Apache is its highly versatile configuration. There are few web-serving tasks which Apache can not perform with its support for a large number of modules. The /etc / httpd / modules/ directory is default for the modules. Configuration for standard modules is stored in /etc / httpd / conf / httpd.conf while configuration settings are usually set to .conf files in /etc / httpd / conf.d/ for optional modules that are enabled with Yum.

Check for lines beginning with LoadModule statements in the "Conf" files to see if a module is available. A list of currently available modules should be created using the following two grep commands:

```
grep ^LoadModule /etc/httpd/conf/httpd.conf grep ^LoadModule /etc/httpd/conf.d/*
```

Edit the related file and make a comment on the LoadModule statement by prefixing the line of the hash (e.g. #) for deactivation of an active module (at your own risk).

Using the following commands to obtain a list of Apache modules available in the CentOS repository:

```
yum search mod_
```

Each of these modules can then be activated with the command:

```
yum install mod_[module-name]
```

The modules should be disabled and prepared for use after installation, but additional settings should be added to allow access to the features of the modules. For more detail on configuring different modules, see the [Apache Module Documentation.](https://httpd.apache.org/docs/2.0/mod/)

## Understanding .htaccess Configuration

The.htaccess file is a webmaster and developer experience with the Apache configuration gui. In these files, configuration options allow you to monitor the actions of Apache by directory. You can lock a directory behind a password wall to prevent general access to it, for example. Furthermore, the .htaccess specific directory files are common places where URL re-writing rules are defined.

Note that all directories below the file are protected by options listed in a.htaccess file. In addition, note that the higher level settings can be defined for all options provided in.htaccess files. You can set directory level options using < Directory > blocks within your virtual host if this kind of configuration structure is suitable for your setup.

## Password Protecting Directories

We need to create a.htpasswd file in a non-web accessible directory. For instance, if /srv / www / example.com / public html/ is the root of a video host document, use /srv / www / example.com/. Join the folder:

```
cd /srv/www/example.com/
```

Using the htpasswd command, we will create a new password entry for a user named thisl:

```
htpasswd -c .htpasswd cecil
```

Note, you can specify an alternate name for a password file (e.g .. htpasswd), which might be prudent if you wanted to store a number of.htpasswd files for different directories at the same location.

These usernames and passwords do not (and should not) match the usernames and passwords of the system. You can also specify how the passwords are encrypted / hashed with the-m flag for MD5, or-s for SHA hashes. Note that when you add additional users to the.htpasswd file, you should not use the-c option (which creates a new file)

Add the following lines to the.htaccess file for the directory you want to protect:

```file {title=".htaccess" lang="aconf"}
AuthUserFile /srv/www/example.com/.htpasswd AuthType Basic AuthName "Advanced Choreographic Information" Require valid-user
```

Note that the AuthName is presented to the user as an explanation in the authentication dialog for what they are asking to access on the server.

## Rewriting URLs with mod\_rewrite

The mod rewrite engine is very powerful and is available for use by default. Although mod rewrite's capabilities far exceed the scope of this section, we hope to provide a brief outline and some common use cases.

Enable mod rewrite with the following line in the < Directory > block or.htaccess file:

```file {title="Apache Virtual Host Configuration or .htaccess" lang="aconf"}
RewriteEngine on
```

Now, a number of separate rewriting rules can be developed. Such rules provide a prototype in which the server compares incoming requests and the server offers an alternate page if a request fits a rewrite pattern. Here is a rewriting rule example:

```file {title="Apache Virtual Host Configuration or .htaccess" lang="aconf"}
RewriteRule ^post-id/([0-9]+)$ /posts/$1.html
```

Let this rule be analyzed. First of all the pattern to suit incoming requests is the first string. Within this second line the files to be handled are listed. Regular expressions are used for patterns in mod rewrite, which means that the ^ matches the beginning and the $matches the end of the string, which implies that the rewriter won't rewrite strings partially matching the template.

This string rewrite all paths that start in /post-id/ with a number (eg. \[0-9\]+), which represent the corresponding.html file in the /posts/ directory. The word in the template or parenthetic words describes a variable that has been transferred to the second string as $1, $2, $3, etc.

There are many other options for using mod rewrite to allow users to see and interact with useful URLs while maintaining a file structure that makes sense from a development or deployment point of view.
