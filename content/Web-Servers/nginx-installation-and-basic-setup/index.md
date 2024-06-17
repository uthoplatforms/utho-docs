---
linktitle: "NGINX - Setup"
title: "NGINX: Installation and Basic Setup"
date: "2020-06-09"
featured: true
---
![](images/NGINX_-Installation-and-Basic-Setup_utho.jpg)

## Before You Begin

- You need root access of the server or a `sudo`  privileged user account.
- Set the hostname of your cloud server.
- Update your server.

## Install NGINX

## Stable Versus Mainline

The first decision on your installation is whether you want NGINX Open Source stable or mainline version. Stable is recommended and what this set of guides uses should be. Information on NGINX here.

## Binary Versus Compiling from Source

Below are the three ways to install NGINX Open Source are available:

**A pre-built binary from your Linux distribution’s repositories.** This is the best way to install because you can install the nginx package using your package manager. However, for distributions that supply binaries, you are going to run an older version of NGINX as opposed to the current stable or mainline version. Patches can also be slower to land in upstream distro repositories.

**A pre-built binary from NGINX Inc.’s repository.** That is the way that sequence is built. It is still a simple installation process that just needs to be connected to your device and then configured as usual. The most beneficial approach is the traditional upstream setup with quicker updates and new releases than a Linux distribution. Compile time options are commonly different from NGINX binaries in distribution repositories and `nginx -V`  can be used to display the built-in of your binary.

**Compiling from source.**  It is the most complex, but not yet impractical deployment approach after the NGINX documentation. Source code is always modified by patches and maintained on the newest stable or mainline updates, so construction can be automated easily. This is the most flexible installation method because any compiling options and flags you select can be included or skipped. For instance, one common reason why people compile their own NGINX build is that they can use a server with a newer OpenSSL version than their Linux distribution.

## Installation Instructions

The NGINX admin guide provides simple and specific instructions for any installation method and version of NGINX you like, so we won't represent it here. When your installation is done, return to the show.

## Configuration Notes

As the use of the NGINX web server has grown, NGINX, Inc. has worked to distance NGINX from configurations and terminology that were used in the past when trying to ease adoption for people already accustomed to Apache.

If you know Apache well, you will know that multiple site configurations are stored in `/etc/apache/sites-available/`, (so called virtual hosts in Apache terminology), which are symlinked to files inside `/etc/apache/sites-enabled/`. However, multiple NGINX guides and blog posts recommend the same settings. As you might expect, that lead to some confusion, with the hypothesis that NGINX uses the `../sites-available/`  and `../sites-enabled/` and users of `www-data`  regularly. That's not the case.

Sure, it may. The NGINX packages of deposits in Debian and Ubuntu have modified their configuration for quite a while to that purpose, so it is definitely a working configuration to support sites that are stored in `/sites-available/` and symlinked with `/sites-enabled/`. But, it's unnecessary and the only one that does it is the Debian Linux family. Do not push Apache settings on NGINX.

Multiple site configuration files can then be stored as an `abc.com.conf`, or `abc.com.disabled` in `/etc/nginx/conf.d/`. Do not add `server`  blocks to `/etc/nginx/nginx.conf` directly, as your configuration is fairly straightforward. This file is intended to configure the server process rather than individual websites.

The NGINX method also acts as the ngnix user in the nginx community, so note when you change permissions to website folders. See _[Creating NGNIX Plus Configuration Files](https://www.nginx.com/resources/admin-guide/configuration-files/)_ .

Ultimately, [as the NGINX docs point out](https://www.nginx.com/resources/wiki/start/topics/examples/server_blocks/), Virtual Host is an Apache concept, even though it is used in the Debian and Ubuntu file nginx.conf and some of the old documentation of NGINX. The NGINX equivalent is a Server Block, so this is the phrase you will see on NGINX in this show.

## NGINX Configuration Best Practices

There are a wide variety of tweaks to NGINX to best suit your needs. Nonetheless, others are unique to your use; what works well for one person can not work for another.

This series provides configurations that are fairly generic for use in almost any production scenario, but on which you can create for your own specialized setup. None of the following is considered best practice, and none of them are mutually related. They are not necessary to the operation of your site or server, but unintended and unwanted effects if they are not taken into account.

Two short points:

- First preserve your default `nginx.conf`  file so that you have something to recover if your personalization is so difficult that NGINX breaks. ```
cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.backup-original
```
- After applying the following update, reload your configuration with: ```
nginx -s reload
```

## Use Multiple Worker Processes

Add or edit the below line in `/etc/nginx/nginx.conf`, in the area just before the `http` block. This is the called `main` block, or context, though it’s not marked in `nginx.conf` like the `http` block is. The first choice would be to set it to `auto`, or the amount of CPU cores available to your cloud server.

```
worker_processes auto;
```

For more details, see the sections on worker processes in [the NGINX docs](https://nginx.org/en/docs/ngx_core_module.html#worker_processes) and [this NGINX blog post](https://www.nginx.com/blog/tuning-nginx/).

## Disable Server Tokens

The version number of NGINX is available by default when the server is linked, whether by good 201 curl or 404 returned to a client. Disabling server tokens makes it harder to evaluate versions of NGINX and hence harder for an attacker to execute version-specific attacks.

Server tokens enabled:

Server tokens disabled:

Add the below line to the `http` block of `/etc/nginx/nginx.conf`:

```
server_tokens off;
```

## Set Your Site’s Root Directory

The NGINX directory serves sites that vary depending on how you have installed them. NGINX supplied from NGINX Inc. uses `/usr/share/nginx/` at the time of this writing.

NGINX docs warn of the loss of site data when upgrading NGINX by depending on the default location .. You can use `/var/www/`,`/srv/`, or any other location where package or device updates are not affected . See _[Using the default document root](https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/#using-the-default-document-root)_ and _[Not using standard document root locations](https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/#not-using-standard-document-root-locations)_.

The `/var/www/abc.com/`  sequence will be used in its examples. Replace `abc.com`  , which can be accessed with your Cloud server's IP address or domain name.

1\. The root directory of your site or sites should be linked to `/etc/nginx/conf.d/abc.com.conf` corresponding server block:

```
root /var/www/abc.com;
```

2\. Then make that directory:

```
mkdir -p /var/www/abc.com
```

## Serve Content Over IPv4 and IPv6

Add a second IPv6 `listen`  Directive to the `/etc/nginx/conf.d/abc.com.conf`: `server`  block:

```
listen [::]:80;
```

If your site is using SSL/TLS, you would add:

```
listen [::]:443 ssl;
```

## Static Content Compression

You do not want to uniformly allow gzip compression because you run the risk of vulnerability to the [CRIME](https://en.wikipedia.org/wiki/CRIME)  and [BREACH](http://www.breachattack.com/)  exploits depending on the content and session cookies.

In NGINX compression has been disabled [for years now](https://mailman.nginx.org/pipermail/nginx/2012-September/035600.html) by default, so that it is not vulnerable out of the box to CRIME. Modern browsers have also taken steps to combat these attacks, but web servers may be irresponsibly configured.

At the other hand, if you completely disable gzip compression, you remove such vulnerabilities and use less CPU cycles at the cost of reducing the efficiency of the web. Different mitigations are possible on the server and the introduction of TLS 1.3 will contribute further to this. For now, the only way, unless you know what you are doing, is only to compress static site content like images, Text, and CSS.

Below is an example of how to use cat `/etc/nginx/mime.types` to display the mime forms available. If you want the `gzip`  guidelines to extend to all sites supported by NGINX in the `http`  domain, it is better to use them for specific sites and content types only in the server domain.

```
gzip on; gzip_types text/html text/plain text/css image/*;
```

If NGINX serves many websites, some use SSl / TLS and some don't, an example would look as in the following example. The `gzip`  directive is applied to the `server`  block of the HTTP site to keep it disabled for the HTTPS domain.

```file {title="/etc/nginx/conf.d/abc1.com.conf" lang="aconf"}
server { listen 80; server_name example1.com; gzip on; gzip_types text/html text/css image/jpg image/jpeg image/png image/svg; }
```

```file {title="/etc/nginx/conf.d/abc2.com.conf" lang="aconf"}
server { listen ssl; server_name example2.com; gzip off; }
```

There are several other options available for the gzip module of NGINX. See NGINX docs for more detail, and you can also use the ngx http gzip static module which suits static content compression if you want to compile NGINX builds.

## Configuration Recap

To summarize where we have been so far:

- The stable version of NGINX Open Source has been installed from the nginx.org repository.
- A basic website can be accessed:

The root directory is located in /var/www/example.com/

The configuration file is located in /etc/nginx/conf.d/abc.com.conf.

```file {title="/etc/nginx/conf.d/abc.com.conf" lang="aconf"}
server { listen default_server; listen [::]:default_server; server_name abc.com www.abc.com; root /var/www/abc.com; index index.html; gzip on; gzip_comp_level 3; gzip_types text/plain text/css application/javascript image/\*; }
```

- Changes that we want NGINX to apply universally are in the `/etc/nginx/nginx.conf`  http block. Our additions are at the bottom of the block, so we know what's added compared to what's provided by default.

Now, `nginx.conf`  looks like the following example. Please note that `nginx.conf`  does not contain any `server` blocks:

```file {title="/etc/nginx/nginx.conf" lang="aconf"}
user nginx; worker_processes auto;

error_log /var/log/nginx/error.log warn; pid /var/run/nginx.pid;

events { worker_connections 1024; }

http { include /etc/nginx/mime.types; default_type application/octet-stream;

```
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';

access_log  /var/log/nginx/access.log  main;

sendfile        on;
#tcp_nopush     on;

keepalive_timeout  65;

#gzip  on;

include /etc/nginx/conf.d/*.conf;


server_tokens       off;
```

}
```

Thankyou...
