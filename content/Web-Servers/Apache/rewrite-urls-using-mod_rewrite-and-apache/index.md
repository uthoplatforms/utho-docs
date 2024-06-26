---
title: "Rewrite URLs using mod_rewrite and Apache"
date: "2020-05-02"
Title_meta: GUIDE TO Rewrite URLs using mod_rewrite and Apache

Description: This guide provides comprehensive instructions on rewriting URLs using mod_rewrite with Apache. Learn how to configure mod_rewrite to create clean, SEO-friendly URLs, redirect traffic, and improve website structure and usability on your Apache server.

Keywords: ['mod_rewrite', 'Apache', 'URL rewriting', 'SEO-friendly URLs', 'URL redirection', 'server configuration']

Tags: ["mod_rewrite", "Apache", "URL Rewriting", "SEO", "URL Redirection", "Server Configuration"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Web-Servers/Apache/rewrite-urls-using-mod_rewrite-and-apache/']
tab: true
---

You'll learn how to rewrite the URLs using mod rewrite and Apache in this article. Rewrite a URL is a server-side operation which can serve contents from a position of a file system that does not exactly suit the client's request. It could be helpful to enhance the readability of URLs by search engines and users, or to upgrade tools when the layout of your site changes.

![](images/Rewrite-URLs-using-mod_rewrite-and-Apache_utho.jpg)

## Before You Begin

1\. This guide assumes that you have followed our Getting Started and Securing Your Server guides, and that you have already configured your Apache installation. If you haven't, please refer to our Apache guides or LAMP stack guides.

2\. We're going to change Apache configuration files in this guide so make sure you have the correct permissions to do so.

3\. Update your system.

```
sudo apt-get update && sudo apt-get upgrade
```

## Rewrite URLs

In a `<Directory>` block (usually a virtual host file) or a `.htaccess` file, enable mod rewrite:

```file {title="Apache Configuration Option" lang="aconf"}
RewriteEngine on
```

You can set up several separate rules for rewrite. Such rules include a template comparable by the server against incoming requests. If a request fits a pattern for rewrite, the server will change the request as defined in the rule and will process the request. Here is an example of a law on rewrite:

```file {title="Apache Configuration Option" lang="aconf"}
RewriteRule ^post-id/([0-9]+)$ /posts/$1.html
```

Let's explain this rule: the first string is a pattern to match incoming requests. The second string specifies the actual files that are to be served. Use regular expression syntax to rewrite patterns. The `^` defines the beginning of the string, and the $defines the end of the string, which means that the rewrite engine will not rewrite strings that match only part of the pattern.

The string rewrites all URLs specifying paths beginning with `/post-id/` and containing one or more numbers (e.g. `[0-9]+)` in the `/posts/` directory, providing a corresponding `.html` file. The parenthetical term or terms in the pattern define a variable that is passed as `$1`, `$2`, `$3` and so on to the second string, depending on how many parentheticals are given in the first string.

You can build and implement several rewrite rules, then sequentially implement those rules. The order in which you define `RewriteRules` will affect the rules match.

Optionally, you can insert a directive on RewriteBase to change the rewrite rules actions. Suppose we:

- The `/srv/www/abc.com/public html/` directory defines such directives.
- Some users make a request in the form `http:/abc.com/post-id/200`, where 200 may be more than one digit in number.
- Some users send requests in the `http:/abc.com/page/title-of-page` type, where "page title" may represent any string of characters.
- The files are stored on the `/srv/www/abc.com/public html/objects/` directory and fit the requested object in name, but have an extension of `.html`.

```file {title="Apache Configuration Option" lang="aconf"}
RewriteEngine on RewriteBase /objects RewriteRule ^post-id/([0-9]+)$ $1.html RewriteRule ^page/([^/]+)$ $1.html
```

The rewrite rules outlined above will apply for:

- `http:/abc.com/post-id/200/` and the `/srv/www/abc.com/public html/ objects/200.html` file
- `http:/abc.com/page/free-toast/` and support the `/srv/abc.com/public html/objects/free-toast.html` file

This is useful if the file system positions don't match the URLs as requested by the client. This is also particularly useful when a single file, for example index.php, dynamically generates all requests.

## Rewrite URLs Under Specific Conditions

You can set the conditions under which a `RewriteRule` is to be used with the parameter `RewriteCond`. Let us take the following example from the WordPress application's default rewrite rules:

```file {title="Apache Configuration Option for WordPress" lang="aconf"}
RewriteEngine On RewriteBase / RewriteCond %{REQUEST_FILENAME} !-f RewriteCond %{REQUEST_FILENAME} !-d RewriteRule . /index.php [L]
```

In this example, all requests that begin with the top level of the context are affected by the rewrite rules. This is defined in the directive `RewriteBase /`. The context is determined by where the rewrite rules are specified in the virtual host, directory block, or `.htaccess` file.

The statements in RewriteCond instruct Apache to enforce only the rule that follows them if their requirements are fulfilled. The requested file name in the above example does not need to match an existing file on the file system! (`-f`) and does not match an existing directory on the file system! (`-d`).

If both conditions are true and there is no file or directory that matches the request, Apache will apply the rewrite rule. For example, if the user asks for `http:/abc.com/? Post=123` or `http:/abc.com/post/123` will the server return the result for `index.php? Post=123` or `the.php/post/123 index`, respectively.

Multiple RewriteCond is connected to logical AND operators in such a way that all conditions are true in order for the RewriteRule to apply to that request. You may also add a \[OR\] statement at the end of the RewriteCond directive to add a list of conditions to the logical OR and create a number of possible conditions where the request is rewritten by a single RewriteRule. For more information on rewrite conditions, see the Apache mod rewrite documentation.

## Redirection Codes in mod\_rewrite

Finally, there are a number of codes that you can add to the `RewriteRule` that change the behavior of the rewrite. Rewrite Rule in the previous example. `/index.php [L]`, we used the `[L]` option that stands for "last rule." This prevents Apache from applying any additional rewrite rules to that rule. There are a few common options:

- `F` tells the client that the URL is forbidden to respond with HTTP code 403.
- `N` forces mod rewrite to restart the rewrite process, and enables multi-stage rewrite.
- `R` informs the client that the requested page has passed, with the temporary redirection of HTTP code 302. Using `"R=301"` when the page has relocated permanently.

You may also specify multiple options at the end of a `RewriteRule` separating them with commas: `[L,R=301]`

Thankyou..
