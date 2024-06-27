---
title: "Basics Information of Apache Configuration"
date: "2020-06-06"
---

![](images/Basics-Information-of-Apache-Configuration_utho.jpg)

The Apache HTTP web server is the de facto standard for general HTTP services in many respects. Thanks to its large number of modules, it offers versatile support for proxy servers, URL rewriting, and granular access control. In addition, web developers also prefer Apache to support server-side scripting using CGI, FastCGI, and embedded interpreters. Such features make it easier to execute complex code quickly and efficiently. Although there are many popular alternatives to Apache, also within the bounds of open source, the scope of Apache use is remarkable.

Apache does not without any expense the exceptional degree of flexibility; this takes on the form of a configuration system that is often confusing and sometimes complicated. We have therefore developed this document and a number of other guides to tackle this issue and explore some more advanced and optional Apache HTTP Sever features.

If you would like to have a working web server and install Apache for the first time, we suggest using the corresponding "Apache development guide" for your Linux distribution. Try trying a suitable LAMP guide for your delivery, if you need a more full LAMP stack. This guide assumes that you have an up to date Linux setup, installed Apache successfully, and logged into a root access shell session.

## Apache Basics

The default Apache settings vary widely between different Linux distributions. All distributions from Debian, Ubuntu, and Gentoo refer to Apache as "Apache2" and bring configuration files into the directory `/etc/apache2/`. Certain distributions such as Fedora, CentOS and Arch Linux refer to Apache as "httpd," and store `/etc/httpd/` configuration files. While we allow you to learn about the default configuration of your Apache server, most configurational choices do not differ between the operating systems. The main obstacles to Apache's setup are to grasp the regular configurations of the implementations and their variations from the upstream Apache.

You may use the "init" scripts to manage basic Apache features to safely and quickly start, stop or restart the server. The init script can also reload the configuration and check the server status. Edit the correct command to access these functions:

```
/etc/init.d/apache2 start /etc/init.d/apache2 stop /etc/init.d/apache2 restart /etc/init.d/apache2 reload /etc/init.d/apache2 status
```

If you use a distribution referred to as httpd by Apache, the commands are as follows:

```
/etc/init.d/httpd start /etc/init.d/httpd stop /etc/init.d/httpd restart /etc/init.d/httpd reload /etc/init.d/httpd status
```

The path to the script may be `/etc/rc.d/init.d/`  for some versions instead of `/etc/init.d/`.

In the init script with the command `mod_disk_cache`  on Debian-based distributions contain the functions to monitor the htcache functionality:

```
/etc/init.d/apache2 start-htcacheclean /etc/init.d/apache2 stop-htcacheclean
```

Further functions from the command line interface are also supported. You can use the following command in Debian and Ubuntu systems to test the syntax of your Apache configuration files without restarting the server and trying it:

```
apache2ctl -t
```

In CentOS and Fedora systems, use the following form:

```
httpd -t
```

Furthermore, the `apache2ctl -S`  or `httpd -S`  Commands provide an update on virtual machines that currently run, which include the port the host listens on, the virtual host's name (i.e. the domain) and site configuration information including file names and line numbers.

The "master" file for Apache is usually located in the httpd.conf. This file is in the `apache2.conf`  file in Debian distributions, with a user-specific configuration in the `httpd.conf`  file, including the master file, the master file contains some other files. To receive a list of the following items, submit an order according to your distribution:

```
grep "Include" /etc/apache2/apache2.conf grep "Include" /etc/apache2/httpd.conf grep "Include" /etc/httpd/httpd.conf
```

Note that the order of these files can affect the web server behavior. When later options contradict options set in previous files, then earlier options are overridden. Knowing the current default configuration can be a valuable learning experience.

## Configuration File Organization

One of the most common use cases for the Apache web server is to use its “virtual hosting” capabilities, which allow a single instance of Apache to serve numerous websites and subdomains. Because most websites don’t tend to use a significant amount of system resources, virtual hosting is often a great way to fully utilize a web server. As a result of this capability, configuration files for virtual hosts can be complex and difficult to organize. There are two major approaches to the solution for this problem.

## Symbolic Links and the Debian Way

To order to enhance usability, the Debian project enables users to effectively prevent changing the web server's "foundation configuration." As a result, Debian and Ubuntu use symbolic links to allow and disable various configuration options for administrators without fully erasing configuration options.

Make sure that your apache.conf or httpd.conf file has the correct line if you are using an operating system other than Debian or choose to use the sites-enabled organization to customize your settings.

```file {title="/etc/httpd/httpd.conf or /etc/apache2/apache2.conf" lang="aconf"}
Include /etc/httpd/sites-enabled/ Include /etc/apache2/sites-enabled/
```

You need to use a command like: `mkdir -p /etc/httpd/sites-enabled/`. if you haven't created this directory yet. Now the setup options for any files that you store in those directories will be included on your Apache server. Please use the following command to create a connection to this directory:

```
ln -s /etc/httpd/vhosts/abc.com /etc/httpd/sites-enabled/abc.com
```

The syntax for creating symbolic links is `ln -s` followed by the original or "goal" file and the path to the link to be connected. If you skip the final word, `ln` will build a connection in the current directory with the same name as the target file. The original file will not be affected if you delete a connection. Apache can follow multiple layers of symbolic connections, but this can be confusing for itself.

One advantage of this is that a virtual host can keep its configuration files close to the other associated host files. All your virtual host-related services are mostly located in a `/srv/www/abc.com`/ directory. Under the `DocumentRoot`, directory, logs directory and application support directories, `public_html`/,`logs/`and application/ are stored. This organization helps you to find it convent to store your configuration file with your virtual host in `/srv/www/abc.com`/. This makes it easy to back up and transfer a virtual host, since all files are in one directory. The symbolic connection can be generated as following if the virtual host set up file is located at `/srv/www/abc.com/apache.conf` you might create the symbolic link as follows:

```
ln -s /srv/www/abc.com/apache.conf /etc/apache2/sites-available/abc.com
```

You can use the `a2ensite` and `a2dissite` software to handle virtual host files if you are using a Debian distribution. You can also use`/etc/apache2/sites-enabled/abc.com` to manually connect your configuration files. When you do not use a Debian distribution, the symbolic relation may look the same, but file names and locations can change somewhat:

```
ln -s /srv/www/abc.com/apache.conf /etc/httpd/conf.d/abc.conf
```

## Create a Single Virtual Hosts file

For each virtual host, the Debian method of maintaining a single configuration file can be useful in managing many noninterlinked sites or for the editing of various distinct and unprivileged users. However, there are circumstances in which too many virtual host files may cause confusion and increase the maintenance burden by setting the same configuration group for a collection of hosts.

For these instances, the easiest way to keep Apache installed would be to provide a single file. This is the chosen host organization in some distributions, such as CentOS, Fedora, and Arch Linux. When checking Apache's default distribution configuration, there's normally a `conf.d/`directory where you can store user-created configuration. When you want to merge multiple virtual host configuration files into one file, create a `vhosts.conf` in the `conf.d/` folder and add the entire configuration options in that file. The`conf.d/`folder is located within the /etc/ directory of Apache, either: `/etc/apache2/conf.d/`or, where your distribution is concerned, `/etc/httpd/conf.d/` .

Both settings are essentially similar, but can be implemented conveniently in different scenarios. The structure of the configuration file you choose depends on the specifications of your particular application.
