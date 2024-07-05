---
title: "Use lighttpd Web Server in Ubuntu 16.04 (Xenial Xerus)"
date: "2022-11-11"
---

Lighttpd provides a lightweight web server that can serve high loads with less memory than Apache servers. It is widely used on high-traffic platforms such as WhatsApp and xkcd.

This guide describes how the lighttpd ("light") web server is installed and configured on Ubuntu 16.04 (Xenial Xerus). See links at the end for more detail on other services typically available in web server stacks.

![](images/Use-lighttpd-Web-Server-in-Ubuntu-16.04-Xenial-Xerus-1024x576.png)

## Before You Begin

1. Complete the Getting Started Guide and set your Instance hostname and timezone.
2. Lighttpd is a network-oriented application that can expose you to vulnerabilities if your server is not protected. Search for a standard user account in the Securing Your server guide, harden SSH access and delete unnecessary networking services.
3. If you switch from another web server, such as Apache, remember to turn off the other server for test purposes, or to configure lighttpd to use an alternative port until it is properly configured.
4. Update your system:

```
apt-get update && apt-get upgrade
```

## Install Lighttpd

Load server from the list of Ubuntu packages:

```
apt-get install lighttpd
```

Upon deployment, make sure the server is running and activated. Visit http:/198.51.100.10:80 and your IP address will be replaced by 198.51.100.10. If you set up lighttpd for testing on an alternate port, please replace 80 with this version. You will see a lighttpd placeholder page containing some important information:

- Configuration files in /etc/lighttpd are stored.
- The "DocumentRoot" (which includes all HTML files) is located in the /var/www directory by default. You should customize this later.
- Ubuntu provides help scripts for allowing and deactivating server modules without editing the config file directly: light-enable-mod and light-disable-mod.

## Configure Lighttpd

The key lighttpd configuration file is at /etc/lighttpd/lighttpd.conf. This file lists the application modules that can be loaded and allows you to change the web server 's global settings.

The first configuration command is server.module, which lists the modules to be loaded when the lighttpd server is started or reloaded. To deactivate a function, add a # at the start of the line to comment on the module. Delete the # for module activation. This list can also include modules. For example, in the default file you can enable the mod rewrite module (URL requests rewriting) by uncommenting the appropriate line. Note that in the order they appear, these modules will be loaded.

The server.modules block is a list of other application and modules configuration settings. Most instructions are self-explanatory, but you may want to include not all of the options available by default. Several output settings you might want to add:

- Server.max-connections – Defines how many connections are allowed concurrently
- Server.max-keep-alive-requests – Sets the maximum number of requests to keep the link alive until the link ends.
- Server.max-worker-Enter the number of spawning worker processes. If you know Apache, a worker is similar to a child operation.
- Server.bind-Sets the lighttpd socket address, hostname, or path. You can create several lines to listen to different IP addresses. Binding to all interfaces is the default mode.

Some configurations depend on certain modules. For example, url.rewrite requires mod rewrite to be allowed, as it is module specific. However, for ease of use, most modules have their own configuration files and can be enabled and disabled via command line rather than by editing the configuration file.

## Enable and Disable Modules via Command Line

You may want to allow and disable modules via the command line for easy use. Lighttpd provides a simple way to do this so that each time a new module is needed the configuration must not be edited.

Run light-enabled-mod from the command line to see the list of modules available, a list of modules already enabled, and a prompt to enable a module. This can be done in one line as well. To activate the authentication module, for example:

```
lighty-enable-mod auth
```

This command creates a symbolic connection to the /etc/ lighttpd/conf-enabled configuration file of the module read in the main configuration file. Check for the .conf file in /etc/lighttpd/conf-available to change the configuration of a particular module.

There are many other modules in separate Ubuntu packages. Some good stuff are:

- lighttpd-mod-mysql-vhost-Uses MySQL database to control virtual hosts. This module works well when a large number of virtual hosts are managed.
- lighttpd-mod-webdav Apache supports WebDAV extensions for distributed Apache content authoring
- lighttpd-mod-magnet Checks the request management module

When these packages are mounted, you can enable them with light-enable-mod.

Restart lighttpd to load changes:

```
systemctl restart lighttpd.service
```

## Virtual Host Setup with Simple Vhost

This segment covers a basic virtual hosting setup. The Simple Vhost module lets you set virtual hosts in a user defined directory named for the domains below a root server with their respective document roots. Make sure all other virtual hosting modules are disabled before continuing.

1. Start by allowing simple-vhost: ```
lighty-enable-mod simple-vhost
```
2. Restart lighttpd to load your changes: ```
systemctl restart lighttpd.service
```
3. Modify the following settings in your `/etc/lighttpd/conf-available/10-simple-vhost.conf` file:

```file {title="/etc/lighttpd/conf-available/10-simple-vhost.conf" lang="aconf"}
simple-vhost.server-root = "/var/www/html" simple-vhost.document-root = "htdocs" simple-vhost.default-host = "abc.com"
```

The server root specifies the base directory for all host directories.

The document-root determines the subdirectory in which the pages to be served are located. In some Apache configurations, this is similar to the public html directory, but in the above configuration it is called htdocs.

When lighttpd receives a request and is unable to find a compatible directory, the content is served from the default host.

Requests are tested in the above configuration for existing directory names within /var/www/html. If a directory exists which matches the requested domain, the result is served by the respective htdocs. If it does not exist, content from htdocs is served in the default host directory.

In order to explain this definition, presume that /var / www / html only contains the abcA.com and abc.com directories, both of which contain content htdocs folders:

- When an application for an abcA.com URL is made, the content of /var/www/html/abcA.com / htdocs is served.
- When a request is made for a server URL that does not have a directory, contents will be served with /var/www/html/abc.com/htdocs as abc.com is the default host.

Build host directories for each subdome in the same way for subdomains. For example , in order to use abcSub as a subdomain of abc A.com, create an abcSub. abc A.com directory with a content htdocs directory. Make sure that any subdomains you want to use are added to DNS records.

4\. Restart the web server again to reload changes:

```
systemctl restart lighttpd.service
```

## Virtual Host Setup with Enhanced Vhost

Enhanced virtual hosting works somewhat different from Simple by building a document root based on a wildcard pattern. Before beginning, make sure all other virtual hosting modules are disabled.

1. To allow the enhanced virtual hosting module, run the following command: ```
lighty-enable-mod evhost
```
2. Restart lighttpd to load the configuration changes: ```
systemctl restart lighttpd.service
```
3. You must modify /etc/lighttpd/conf-available/10-evhost.conf to achieve the same directory structure by evhost as with simple-vhost above:

```file {title="/etc/lighttpd/conf-available/10-evhost.conf" lang="aconf"}
evhost.path-pattern = "/var/www/html/%0/htdocs/"
```

4\. Modify the server.document-root configuration file in the main lighttpd:

```file {title="/etc/lighttpd/lighttpd.conf" lang="aconf"}
server.document-root = "/var/www/html/abc.com/htdocs"
```

When abc.com is requested and /var/www/html/abc.com/htdocs/ is found, the configuration set to Steps 3 and 4 is the root of the document when you send requests. The 0 percent of the pattern means that a request will be verified for domain and Top Level Domain (TLD) called host files. The.document-root server directive defines a default host used when there is no corresponding directory.

5\. Restart lighttpd to load the configuration changes:

```
systemctl restart lighttpd.service
```

The convention on naming these virtual hosts is derived from the domain names. Take an example of the following web address: http:/abcSub2.abcA.com/ We read domain names from top level on the right to bottom level on the left. Therefore, com is the TLD, abc A is the domain, abcSub is subdomain 1 and abcSub2 is the subdomain 2.

To change the lighttpd format of the host directory, define the pattern to the directory in which the content lives. The table below shows the host directory format used for each pattern as root document. It also shows which host file will be used to serve content, with an example request from the above URL:

![](images/Screenshot-lighthttpd.png)

## Create Virtual Host Directories

Whether you are using simple-vhost or evhost, before lighttpd can serve content, you need to create directories. Once you have installed the necessary instructions, build the necessary directories to replace abc.com with your name of domain:

```
mkdir -p /var/www/html/abc .com/htdocs/
```

The following command generates two additional virtual hosts for the top-level fields.net and.org:

```
mkdir -p /var/www/html/{abc.net/htdocs, abc.org/htdocs}
```

The following command creates two additional virtual hosts from the evhost abc for the subdomains:

```
mkdir -p /var/www/html/{abcSub/htdocs,abcSub2/htdocs}
```

## Virtual Hosting Best Practices

The way you set up virtual hosting on your web server depends, what sites you host, how many domains you traffic, and how many workflows you run. We suggest that you host all of your domains into a single directory (e.g. /var/www/html) and connect the directories symbolically to useful locations.

For example, you can build a series of user accounts for the "blog editor." You can then connect the document root of each domain to a folder in the editor's home folder. For the abc-user user account that manages the abc.com website:

```
ln -s /home/abc-user/abc.com/ /var/www/html/abc.com
```

You may also use symbolic connections to host the same files for many essentially host domains. For example , in order to see abc .org for example in the files of abc .com, build the following link:

```
ln -s /var/www/html/ abc .org/ /var/www/html/ abc .com
```

No matter what you decide, we advise you to build a structured method to manage virtual hosting to simplify any device modifications.

## Run Scripts with FastCGI

You can execute these scripts with FastCGI, if you need your web server to execute dynamic content. In order to run a script, FastCGI outputs the Web server interpreter rather than run the "inside" scripts on the Web server. This is in contrast to approaches such as mod perl, mod python, and mod php, but in high-traffic circumstances this way is often more effective.

To set up FastCGI, make sure that an interpreter for your chosen language is installed on your system:

To install Python:

```
apt-get install python
```

To install Ruby:

```
apt-get install ruby
```

To install PHP 7 for CGI interfaces

```
apt-get install php7.0-cgi
```

Perl version 5.22.1 is included by default in Ubuntu 16.04. You will also need to install and set up a database system depending on the program you plan to run.

On the basis of file extensions that can be sent to individual managers, Lighttpd sends CGI requests to CGI managers. You can also submit requests for an extension to several servers and lighttpd loads these FastCGI connections automatically.

Installing a php7.0-cgi package and allowing FastCGI with light-enable-mod quickcgi-php for instance, configures the standard FastCGI handler in /etc/lighttpd/conf-enabled/15-fastcgi-php.conf. Although the handler probably needs to be changed, the default settings are an effective example:

```file {title="/etc/lighttpd/conf-enabled/15-fastcgi-php.conf" lang="aconf"}
fastcgi.server += ( ".php" => (( "bin-path" => "/usr/bin/php-cgi", "socket" => "/var/run/lighttpd/php.socket", "max-procs" => 1, "bin-environment" => ( "PHP_FCGI_CHILDREN" => "4", "PHP_FCGI_MAX_REQUESTS" => "10000" ), "bin-copy-environment" => ( "PATH", "SHELL", "USER" ), "broken-scriptfilename" => "enable" )) )
```

Add the following entry to your configuration file to map more than one file extension to a single FastCGI handler:

```file {title="/etc/lighttpd/conf-enabled/15-fastcgi-php.conf" lang="aconf"}
fastcgi.map-extensions = ( ".[ALT-EXTENSION]" => ".[EXTENSION]" )
```

## Things to Keep in Mind

Though lighttpd is an efficient and capable web server, its behavior has two warnings:

Server side does not function in lighttpd in the same way as in Apache's mod ssi, which allows you to dynamically insert content from one file into another. Even though this is an effective way to assemble content quickly, lighttpd 's handling of the script via SSI is not a recommended workflow. See project documentation for lighttpd on mod ssi.

Because of how FastCGI works, the operation of lighttpd web applications requires extra configuration, particularly for users who use web server-embedded interpreters (e.g. mod perl, mod python, mod php). See the lighttpd project documentation on optimizing FastCGI output for more detail.

Thankyou..
