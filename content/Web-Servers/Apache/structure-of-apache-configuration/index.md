---
title: "Structure Of Apache Configuration"
date: "2022-11-10"
Title_meta: GUIDE TO Structure Of Apache Configuration

Description: Explore the structure of Apache configuration in this guide. Learn about the layout and organization of Apache configuration files, including main configuration files, virtual host configurations, and directory-specific settings, enabling effective management and customization of your Apache web server.

Keywords: ['Apache configuration', 'Apache structure', 'web server configuration', 'virtual hosts', 'directory settings']

Tags: ["Apache Configuration", "Web Server Configuration", "Virtual Hosts"]
icon: "apache"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Web-Servers/Apache/structure-of-apache-configuration/']
tab: true
---

In this article we will discuss Structure Of Apache Configuration, The `<VirtualHost>`  block allows administrators to change the web [server](https://utho.com/docs/tutorial/category/webserver-tutorial/) behaviour, per host or per domain; all choices in the `<VirtualHost>`  block are available for the entire domain. We do not, however, have the option on a directory basis for defining options. Thankfully, Apache provides more configuration possibilities.

This article deals with many ways of configuring the web server per directory and per file level in very narrow terms. See our other Apache configuration guidelines or official Apache documentation for more information on different options

![Structure Of Apache Configuration](images/Structure-Of-Apache-Configuration_utho.jpg)

## Directory and Options

The `<Directory>`  block refers to the filesystem directory and defines the behavior of [Apache](https://en.wikipedia.org/wiki/The_Apache_Software_Foundation). This block is in angle brackets and begins with "Directory" and the directory path within the file system. The directory and its sub directories as defined are the options set in a directory block. An example of a directory block is the following:

```file {title="Virtual Host Entry in an Apache Configuration file" lang="aconf"}
Order Allow,Deny Allow from all Deny 55.1
```

## Additional Information

- Directory blocks can't be nested within each other.
- Blocks can be nested in the directory inside the blocks `<VirtualHost>`.
- The wildcard character can contain the path contained in a directory block. The asterisk matches any character sequence, and a question mark matches any character. This may be helpful if you need a `DocumentRoot`  option for all virtual hosts to control:

```file {title="Apache Configuration File" lang="aconf"}
Directory /srv/www/\*/public_html
```

## File and Location Options

### File Options

Use the `<Files>` command if you need additional control over those files in a directory of your server. The web server behavior for a single file is governed by this directive. Every file with the given name is subject to the `<Files>` directives. For example, any file called `roster.htm` in a file system is protected by the following example directive:

```file {title="Files Directive in an Apache Configuration file" lang="aconf"}
Order Allow,Deny Deny from all
```

If the `<VirtualHost>` block is present, all files called `roster.htm` would have to be included in DocumentRoot or in `DocumentRoot`  directories. This may apply. When the`<Files>` directive is enclosed in the`<Directory>` section, all `roster.htm` files within or inside the directory sub-directories of the specified directory are subject to the options defined.

### Location Options

Although `<Directory>` and `<Files>` block the behavior of Apache for filesystem locations, the `<Location>`  directive controls the behavior of Apache in relation to the specific route that the client asks for. If a user requests `http://www.abc.com/webmail/inbox/`,  the web server must look at`/srv/www/abc.com/public_html/webmail/inbox/`in the `webmail/inbox/`  directories of the `DocumentRoot`. One common use for this function is to allow a script to process requests made on a given path. For example, this block directs all path requests to a `mod_python` script:

```file {title="Location Directive in an Apache Configuration file" lang="aconf"}
SetHandler python-program PythonHandler modpython PythonPath "['/srv/www/abc.com/application/inbox'] + sys.path"
```

Note, after the options in the `<Directory>` blocks defined, the options defined in the `<Location>`  directive are processed and can override any options set in these blocks.

## Override Options with htaccess

In addition to the above listed configuration methods, Apache reads configuration options by default for a directory from a file in that directory. This file is commonly known as`.htaccess`. Look for your `httpd.conf` and related files for the following configuration options:

```file {title="/etc/httpd/httpd.conf or /etc/apache2/apache2.conf" lang="aconf"}
AccessFileName .htaccess

Order allow,deny Deny from all
```

The first line tells Apache to view configuration options in publicly available directories in`.htaccess`  files. The second directive `<Files ~ "^\.ht">` tells Apache to refuse all requests for the use of any named file that starts with the characters`.ht`. It prohibits access to software choices for guests.

You can change the `AccessFileName` to specify another name where Apache can search for these configuration options. If you change this option, be sure to update the `<Files>` directive to prevent unintentional public access.

Any option that can be specified in a block of `<Directory>` can be specified in a file`.htaccess`. `.htaccess` files are particularly useful in cases where the website operator is a user who has access to edit files in the public directory of the website, but not to any of the Apache configuration files.

For every request,`.htaccess` files are processed and refer to all requests made for files in the current directory. The directives also refer in the filesystem hierarchy to all folders "behind" the current directory.

Despite the strength and versatility that`.htaccess`  files offer, there are drawbacks to using them:

- If files with `.htaccess` are allowed, Apache will need to check and process the directives in the `.htaccess`  file on any request. In addition, Apache will search for`.htaccess` files in directories above the current file system directory. This can cause a minor degradation of performance if access files are allowed, and is true even if not used.
- Options set in files of `.htaccess` can override options set in blocks of `<Directory>` which can cause confusion and unwanted actions. Any option set in a`.htaccess` file can be placed in a block under `<Directory>`.
- Allowing non-privileged users to change configurations of web servers poses a potential security risk, but the AllowOverride options may greatly reduce this risk.

If you wish to disable`.htaccess`  files for a directory or directory tree, specify the option below in any directory block.

```file {title="Apache Directory block" lang="aconf"}
AllowOverride None
```

\[ht\_message mstyle="alert" title="NOTE" " show\_icon="true" id="" class=""style="" \]For a directory that falls inside a directory where overrides have been disabled, you can define AllowOverride Everything.\[/ht\_message\]

## Match” Directives and Regular Expressions

Apache also allows server administrators some additional flexibility in how folders, files, and positions are defined, in addition to the simple guidelines mentioned above. These "Match" blocks allow administrators to define one set of configuration options for a directory, file, and location class. Take an example here:

```file {title="DirectoryMatch Block in an Apache Configuration file" lang="aconf"}
Order Allow,Deny Allow from all Deny 55.1
```

This block defines a set of options for any directory that matches the standard `^.+/images` expression. In other words, any path which begins with a number of characters and ends with images will match these options, including the following paths: `/srv/www/abc.com/public_html/images/`, `/srv/www/abc.com/public_html/objects/images`, and `/home/username/public/www/images`.

Apache also allows an alternate syntax for defining paths within a standard directory block using regular expressions. Adding a tilde (e.g. `~`)  between `Directory`  and the path causes the specified path to be read as a regular expression. Regular expressions are a basic pattern matching syntax, and normal and custom expression variants are provided by Apache, and Perl.

The following block is equivalent to the previous block while `DirectoryMatch` is preferred:

```file {title="Directory Regular Expression Block in Apache Configuration file" lang="aconf"}
Order Allow,Deny Allow from all Deny 55.1
```

Apache offers similar features to link a class of locations or files to a single collection of configuration directives using regular expressions. Consequently any of the following choices define appropriate configurations:

```file {title="File and Location Match Directives" lang="aconf"}
Order allow,deny Deny from all

Order allow,deny Deny from all

Order Deny,Allow Deny from all Allow 192.
Order Deny,Allow Deny from all Allow 192.168
```

The directives above for `<Files>`  and `<FilesMatch>`  are similar to those for `<Location>` and `<LocationMatch>`.

## Order of Precedence

With so many different configuration options locations, one option can be listed in one file or block only later to find it overridden by another. One of the key challenges of using the Apache HTTP server is how all the configuration choices that are disparately placed combine to generate different web server behaviors.

The following list offers a guide to the priority Apache uses when combining configuration options. Later operations can override options specified earlier.

1\. Blocks are first read in `<Directory>`.

2\. .htaccess files are read simultaneously with `<Directory>`  blocks, but can override the options defined in `<Directory>`  blocks if AllowOverride option allows.

3\. Next read: `<DirectoryMatch>`  and `<Directory ~>`.

4\. `<Files>` and `<FilesMatch>` are read after defining directory behaviors.

5\. Finally, one reads `<Location>` and `<LocationMatch>`.

In general, the choices `<Directory>` are sorted in sequence from the shortest to the longest. In other words, before setting options for`/srv/www/abc.com/public_html/objects`  , options for`/srv/www/abc.com/public_html/objects/images`will be handled regardless of the order they appear in the configuration file. All other instructions are interpreted in the order in the configuration file they appear in.

Thankyou....
