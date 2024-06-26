---
title: "URLs Redirect with Apache Web Server"
date: "2020-06-16"

Title_meta: GUIDE TO URLs Redirect with Apache Web Server

Description: This guide explores how to redirect URLs using the Apache Web Server. Learn how to configure redirections using Apache's mod_rewrite module to manage URL redirects, handle domain changes, and improve SEO by ensuring consistent and user-friendly URL paths on your web server.

Keywords: ['Apache Web Server', 'URL redirection', 'mod_rewrite', 'SEO', 'web server configuration']

Tags: ["Apache Web Server", "URL Redirection", "mod_rewrite", "SEO", "Web Server Configuration"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Web-Servers/Apache/urls-redirect-with-apache-web-server/']
tab: true
---

You can learn how to redirect URLs with Apache in this section. Redirecting a URL allows you to return an HTTP status code that guides the client to a specific URL, making it useful for instances where a piece of content has been transferred. Redirect is a member of the mod alias module of Apache.

![](images/URLs-Redirect-with-Apache-Web-Server-1-1024x576.png)

## Before You Begin

1\. This guide assumes you have followed our Guides to Getting Started and Securing Your Server and you have installed your Apache installation already. If you have not, please refer to our Apache or LAMP stack guides.

2\. You can change the configuration files for Apache in this guide so make sure you have the correct permissions to do so.

3\. Update your system.

Based on the distribution of Linux, the Apache virtual host configuration files are located at various locations. CentOS 7, for example: `/etc/httpd /conf.d/vhost.conf;` Ubuntu 16.04: `/etc/apache2/sites-available/abc.com.conf`. For example. In order to be brief, the configuration file extracts from this guide will lead you to the configuration option for Apache.

After making changes, remember to reload Apache configuration:

**CentOS 7**

```
sudo systemctl restart httpd
```

**Ubuntu 16.04**

```
sudo systemctl restart apache2
```

## The Redirect Directive

You can find `Redirect` settings in your main Apache configuration file but we suggest that you keep them in your virtual host files or directory blocks. You can also use the `Redirect` statements in files with `.htaccess`. Here is an example of how `Redirect` could be used:

```file {title="Apache configuration option" lang="aconf"}
Redirect /username http://team.abc.com/~username/
```

If no reason is provided, `Redirect` will submit a temporary (302) status code, and the client will be told that the /user name resource has temporarily moved to `http:/team.abc.com/~user/.`

No matter where they are located, `Redirect` statements must define the redirected resource's complete file path, following the domain name. Such statements must provide even the full URL of the new location of the site.

You may also have a claim to return a HTTP status specific to:

```file {title="Apache configuration option" lang="aconf"}
Redirect permanent /username http://team.abccom/~username/ Redirect temp /username http://team.abc.com/~username/ Redirect seeother /username http://team.abc.com/~username/ Redirect gone /username
```

- `Permanent` informs the customer the tool has permanently shifted. That returns a status code of 301 HTTP.
- `Temp` is the default action, which informs the client that the resource has temporarily relocated. This returns a 302 HTTP status code.
- `Seeother` informs the user that the requested tool was replaced with a different one. This returns a 303 HTTP status code.
- `Gone` informs the user that they have permanently removed the tool they are searching for. You don't need to define a definite URL when using this statement. That returns a status code of 410 HTTP.

The HTTP status codes can also be used as claims. Here is an example using the options with the status code:

```file {title="Apache configuration option" lang="aconf"}
Redirect /username http://team.abc.com/~username/ Redirect /username http://team.abc.com/~username/ Redirect /username http://team.abc.com/~username/ Redirect /username
```

`RedirectPermanent` and `RedirectTemp` can also execute permanent and temporary redirects, respectively:

```file {title="Apache configuration option" lang="aconf"}
RedirectPermanent /username/bio.html http://team.abc.com/~username/bio/ RedirectTemp /username/bio.html http://team.abc.com/~username/bio/
```

Redirects can also be achieved using the regex patterns, using `RedirectMatch`:

```file {title="Apache configuration option" lang="aconf"}
RedirectMatch (.\*).jpg$ http://static.abc.com$1.jpg
```

This match every request for a file with an extension of `.jpg` and replaces it with a position on a domain. The parentheses allow you to get a specific part of the request and insert it as a variable (specified by `$1`, `$2`, etc.) into the URL of the new site. For instance:

- A request is forwarded to `http:/www.abc.com/avatar.jpg` and to `http:/static.abc.com/avatar.jpg`
- Re-direct a submission for `http:/www.abc.com/images/avatar.jpg` to `http:/static.abc.com/images/avatar.jpg`.

## Beyond URL Redirection

Apache also helps you to edit URLs using mod rewrite, in addition to redirecting users. Although the features are identical, the key difference being that rewriting a URL requires the server returning a request other than the one received by the client, while redirecting simply returns a status code, and the client then asks for the "correct" answer.

Rewriting a request on a more realistic level does not affect the contents of the browser's address bar, and can be useful in hiding URLs with sensitive or vulnerable data.

Even though redirection helps you to quickly adjust the positions of different tools, in some cases you can find that rewriting suits your needs better. See Apache's mod rewrite documentation for more detail.

Thankyou..
