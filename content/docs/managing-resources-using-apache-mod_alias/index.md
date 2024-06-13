---
title: "Managing Resources using Apache mod_alias"
date: "2020-06-05"
---

In certain cases all of the services that an Apache host uses are stored in the `DocumentRoot` of that server. The `DocumentRoot` is a directory listed in the block of configuration for `<VirtualHost>`. This directory is intended to represent the various files, folders, and services on the file system that users access via HTTP. However, it is normal for administrators to provide HTTP access to a resource that is not located in the `DocumentRoot` on file system. For certain cases, although Apache may obey symbolic connections, this could be hard to sustain. It allows Apache to assign an `Alias` that links a place in the request to an alternate location.

This document describes how to manage resources on the file system using the `Alias` directive while still providing access through HTTP. In addition, this guide assumes that you have an Apache installation working and that you have access to change configuration files. If you have not installed Apache, you might want to find one of our installation guides for Apache or the installation guides for LAMP stacks. If you want a more comprehensive guide to Apache configuration, find the fundamentals of our Apache configuration and the layout of the Apache papers.

![](images/Managing-Resources-using-Apache-mod_alias_utho.jpg)

## Creating Aliases

Virtual Host configurations usually specify a `DocumentRoot` which, by default, specifies a directory called `public_html/`  or `public/`.  If the root document for the virtual host `abc.com`  is`/srv/www/abc.com/public_html/`,  then the file located at `/srv/www/abc.com/public_html/index.htm`  will be returned by a request for `http://www.abc.com/index.htm`.

If the administrator were to retain the file system `code/` resource at `/srv/git/public/` but make it accessible at `http://abc.com/code/`,, an alias will be required. In this example this is accomplished:

```file {title="Apache Configuration" lang="aconf"}
DocumentRoot /srv/www/abc.com/public_html/ Alias /code /srv/git/public

Order allow,deny Allow from all
```

Without the `Alias` instruction, a `http://abc.com/code/` request will return the resources available in the `/srv/www/abc.com/public_html/code/`. folder. The Alias will therefore direct Apache to serve content from the directory `/srv/git/public`. The segment `<Directory>` allows remote users to access the directory.

There are a variety of important factors to remember when using the Alias Directive:

- Directory blocks need to be generated after `Alias` has been declared for the destination of `Alias`. This makes it possible to allow access to and otherwise regulate the actions of certain pieces. That would be `/srv/git/public` in the example above.
- In general, trailing slashes should be avoided in the `Alias` Directives. If the above read `Alias /code/ /srv/git/public/`  the request for `http://abc.com/code`, code, without a trailing slash, it will be served from the `DocumentRoot`.
- The `Alias` directives must be generated either in the root-level configuration of the server (e.g. `httpd.conf`)  or in the `<VirtualHost>` configuration block.

In addition to `Alias`, Apache provides for an `AliasMatch` Directive that provides similar functionality. `AliasMatch`  includes an additional opportunity to alias a class of requests for a given resource to a place outside the `DocumentRoot`. Consider another fictitious `abc.com` virtual host configuration:

```file {title="Apache Configuration" lang="aconf"}
DocumentRoot /srv/www/abc.com/public_html/ AliasMatch /code/projects/(.+) /srv/git/projects/$
Order allow,deny Allow from all
```

In this example, resource requests for URLs such as `http:/abc.com/code/projects/my_app` and `http:/abc.com/code/projects/my_app2` will be served in /srv`/git/projects/my_app2` respectively. Nonetheless, `http:/abc.com/code/projects` will be accessed from `/srv/www/abc.com/public html/code/projects/` instead of `/srv/git/projects/` due to the `/code/projects/(.+)` trailing slash in the alias.

Although the possible use case for `Alias` is quite limited, the feature is very powerful to maintain a stable and well-organized web server.
