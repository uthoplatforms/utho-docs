---
title: "How To Create Redirects with Nginx"
date: "2022-09-18"
---

## Introduction

HTTP redirection, also called URL redirection, is a way to send a domain or address to a different one. There are many ways to use redirection, and there are a few different kinds to think about. Redirects are used when a website needs to send people who type in one address to a different one.

As you make content and manage servers, you will often need to send traffic from one place to another. This guide will talk about how these techniques can be used and how to do them in Apache and Nginx, which are the two most popular web servers.

## Prerequisites

- An Ubuntu 20.04 server set up
- A super user or any normal user with SUDO privileges.
- Nginx server installed on your Ubuntu server

## Analyzing Redirect Methods

Redirects can be used in many ways. If you already have a website and want to change your domain, you shouldn't just give up on your old domain. If the content on your site disappears without telling the browser where it is now, bookmarks and links to your site from other pages on the internet will stop working. If you change domains without redirecting, you'll lose traffic from people who used to visit your site and all the credibility you've worked hard to build.

Often, it's a good idea to register multiple versions of a name so that people who type in addresses that look like your main domain can find you. For example, if you have a domain called myspiders.com, you could also buy the domain names for myspiders.net and myspiders.org and point them both to your myspiders.com site. This lets you catch people who might be typing in the wrong address to get to your site. It can also stop another site from using a similar domain name and making money off of your online presence.

Sometimes, you need to change the names of pages on your site that have already been published and gotten traffic. In most cases, this would lead to a 404 Not Found error or, depending on your security settings, another error. You can avoid these by sending your visitors to a different page that has the right information they were looking for. There are a few different kinds of URL redirects, and each one tells the client browser something different. 302 temporary redirects and 301 permanent redirects are the most common.

### Temporary Redirects

Temporary redirects are useful if you need to serve the web content for a certain URL from a different place for a short time. For example, if you are doing maintenance on your site, you may need to use a temporary redirect to send all of your domain's pages to a page explaining that you will be back soon.

Temporary redirects tell the browser that the content is temporarily at a different location, but that the original URL should still be tried.

### Permanent Redirects

Permanent redirects are useful when your content has been moved to a new location forever.

This is useful for when you need to change domains or when the URL needs to change for other reasons and the old location will no longer be used. This redirect informs the browser that it should no longer request the old URL and should update its information to point to the new URL

### Forcing SSL

One common way to use redirects is to tell all traffic to a site to use SSL instead of HTTP.

You can make all requests for http://www.example.com go to https://www.example.com by using redirects.

### How to Redirect in Nginx

Redirects are an important part of Nginx and use directives that are often used. Most of the time, redirects can be set up by making a new server block for every URL.

Nginx server blocks can be saved in the main Nginx settings file in /etc/nginx/nginx.conf, but it's easier to keep track of them if you make a new file for each configuration in /etc/nginx/sites-available/. As you turn files on or off, Nginx creates symbolic links, which are like short-cuts, from files in sites-available/ to another folder called sites-available/. By default, Nginx is installed with a single site-specific configuration in /etc/nginx/sites-available/default that is enabled and linked to /etc/nginx/sites-enabled/default.

```
vi /etc/nginx/sites-available/default
```
By default, it contains a standard web server configuration that will listen on port 80 and look for an `index.html` file located in `/var/www/html` on your system.

![](images/image-78.png)

If you instead needed to redirect requests for example1.com to example2.com you could remove the existing directives from this server block and replace them with a permanent redirect:

```
vi /etc/nginx/sites-available/default
```
```
server {
	listen 80;
	server_name example1.com;
	return 301 $scheme://example2.com$request_uri;
}
```

The return directive runs a URL substitution and then sends back the redirection URL and the status code that was given to it.

In this case, it uses the $scheme variable to use whichever scheme was used in the original request (http or https). Then it gives back the 301 permanent redirect code and the new URL.

Using the rewrite directive, you can do something similar to the Apache folder redirection to send a folder to a different subdomain. When this directive is put in a server block, requests inside the images directory will be temporarily sent to the subdomain images.example.com:

```
/etc/nginx/sites-available/default
```
```
server {
…
rewrite ^/images/(.*)$ http://images.example.com/$1 redirect;
…
}
```

You could change redirect to permanent at the end of the statement to make the redirection last forever.

You'll need to restart Nginx for your changes to take effect. This can be done with systemctl on a new Ubuntu server:

```
systemctl restart nginx
```