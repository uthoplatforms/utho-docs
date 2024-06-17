---
title: "How To Create Temporary and Permanent Redirects with Apache on Ubuntu"
date: "2022-09-18"
---

![How to create temporary and permanent redirects with apache](images/How-To-Create-Temporary-and-Permanent-Redirects-with-Apache-1024x576.png)

## Introduction

HTTP redirection, also called URL redirection, is a way to send a domain or address to a different one. There are many ways to use redirection, and there are a few different kinds to think about. Redirects are used when a website needs to send people who type in one address to a different one.

As you make content and manage servers, you will often need to send traffic from one place to another. This guide will talk about how these techniques can be used and how to do them in Apache and Nginx, which are the two most popular web servers.

## Prerequisites

- An Ubuntu 20.04 server set up
- A super user or any normal user with SUDO privileges.
- Apache server installed on your Ubuntu server

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

### How to Redirect in Apache

Apache has a few tools that it can use to redirect. Tools from the mod alias module can be used to set up simple redirects, and the mod rewrite module can be used to set up more complex redirects.

#### Using the Redirect Directive

With Apache, you can use the "Redirect" directive, which is part of the mod alias module and is turned on by default, to redirect a single page. At least two arguments are needed for this directive: the old URL and the new URL.

Apache server blocks can be saved in the main Apache settings file in /etc/apache2/apache2.conf, but it's easier to manage if you make a new file for each configuration in /etc/apache2/sites-available/. As you turn files on or off, Apache creates symbolic links (shortcuts) from files in sites-available/ to files in another folder called sites-available/. By default, Apache is installed with a single site-specific configuration in /etc/apache2/sites-available/000-default.conf that is enabled and linked to /etc/apache2/sites-enabled/000-default.conf.

```
vi /etc/apache2/sites-available/000-default.conf
```
By default, it contains a standard web server configuration that will listen on port 80 and look for an `index.html` file located in `/var/www/html` on your system.

If you instead needed to send requests for domain1.com to domain2.com, you could remove the existing directives from this server block and replace them with a permanent redirect:

![](images/image-77.png)

This tells the browser that any requests for www.domain1.com should be sent to www.domain2.com. This only applies to one page, not the whole site.

By default, the Redirect directive sets up a temporary redirect called a 302. If you want to make a permanent redirect, you can do it in either of the two ways below:

Method 1:

```
vi /etc/apache2/sites-available/000-default.conf
```
```
<VirtualHost *:80>
	ServerName www.domain1.com
Redirect 301 /oldlocation http://www.domain2.com/newlocation
</VirtualHost>
```

Method 2:

```
vi /etc/apache2/sites-available/000-default.conf
```
```
<VirtualHost *:80>
	ServerName www.domain1.com
Redirect permanent /oldlocation http://www.domain2.com/newlocation
</VirtualHost>
```

### Using the RedirectMatch Directive

You can redirect more than one page by using the RedirectMatch directive, which lets you use regular expressions to set up patterns that match directories.

This will allow you to redirect entire directories instead of just single files.

RedirectMatch looks for patterns in parentheses and then uses "$1", where "1" is the first group of text, to refer to the matched text in the redirect. Numbers are given in order to the next groups.

For example, if you wanted to match every request for something within the /images directory to a subdomain named images.example.com, you could use the following:

```
vi /etc/apache2/sites-available/default
```
```
<VirtualHost *:80>
…
RedirectMatch ^/images/(.*)$ http://images.example.com/$1
…
</VirtualHost>
```

As with the Redirect directive, you can choose the type of redirect by putting the redirect code before the URL location rules.

You'll need to restart Apache for your changes to take effect. This can be done with systemctl on a new Ubuntu server:

```
systemctl restart apache2
```
### Using mod\_rewrite to Redirect

The mod rewrite module is the most flexible way to set up redirect rules, but it is also the most complicated. How to Rewrite URLs with mod rewrite for Apache has more information about how to work with mod rewrite.
