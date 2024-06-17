---
title: "How to Set Up the .htaccess File in Apache"
date: "2020-06-01"
---

## Introduction

The purpose of this guide is to show you how to configure Apache htaccess (.htaccess) configuration. The guide covers subjects related to website file system permissions, redirects and limitations of IP address.

## Before You Begin

1. This guide uses sudo as much as possible. Finish the Securing Your Server parts to build a regular user account and hard SSH access, and remove redundant network services.
2. Update your system: ```
sudo apt-get update && sudo apt-get upgrade
```
3. [Install a Lamp](https://utho.com/docs/tutorial/installation-of-lamp-stack-on-ubuntu-16/) Stack to install Apache on your Microhost cloud server, complete the Apache portion.

## What is .htaccess

.htaccess is a web server configuration file for Apache. This is a very useful tool that can be used to alter the Apache configuration without editing Apache configuration files. The next parts explain how this configuration is generated and used to restrict directory lists and IP addresses and manage redirects.

## Enable .htaccess

.htaccess is not eligible by default. You must edit the configuration file to enable it.

1\. To open your settings file, use a text editor:

```
sudo nano /etc/apache2/sites-available/abc.com.conf
```

2\. After the VirtualHost block () add:

```file {title="/etc/apache2/sites-available/abc.com.conf" lang="aconf"}
….

Options Indexes FollowSymLinks AllowOverride All Require all granted
```

3\. Save the file, then restart apache:

```
sudo service apache2 restart
```

## Restrict Directory Listings

Visitors can view the directory and file structure by default, and gain access to web server files. It's best practice for a visitor to abc.com to know files on the server to view such files where directory access is limited. One way to restrict this is through .htaccess.

## Create .htaccess

1\. By default, CMS systems such as WordPress create .htaccess configurations. This guide assumes that there is no file named .htaccess, so you have to manually build one. Browse to the root directory of your site:

```
cd /var/www/html/abc.com/public_html
```

2\. Create an .htaccess file:

```file {title="/var/www/html/abc.com/public\_html/.htaccess" lang="aconf"}
Options -Indexes
```

3\. Now, if you navigate to your site, you'll see a forbidden Message. You will need to specify the file or directory that you would like to see.

## Restrict IPs

This section will guide you by restricting the access of specific IPs to your site. This is useful if you want to block some visitors from visiting your site. You may also set up this to prevent certain IPs from accessing certain sections of your site.

## Block IPs

1\. Create or update the .htaccess file stored in the Apache host directory:

```
cd /var/www/html/abc.com/public_html/ sudo nano .htaccess
```

2\. Delete line options (if applicable) from the preceding section and add following lines to block the destination IP addresses:

```file {title="/var/www/html/abc.com/public\_html" lang="aconf"}
order allow,deny This will deny the IP 192.0.2.deny from 192.0.2.This will deny all IP's from 192.0.2.through 192.0.2.deny from 192.0.2
```

## Handle Redirects

You can redirect traffic by configuring .htaccess. For the following examples, you should update the .htaccess file for your website's root directory so you can redirect your visitor to http:/abc.com/test1/index.html if they are trying to see http:/abc.com/main.html.

1\. Create an HTML test file to redirect a visitor to http:/abc.com/test1/index.html:

```
mkdir test1 sudo touch test1/index.html
```

2\. Add some basic content to the HTML test file:

```file {title="/var/www/html/abc.com/public\_html/test1/index.html" lang="aconf"}
This is the html file in test1.
```

3\. Open the .htaccess file in the root directory of your project. Remove all of the existing configurations in this file and add the following line:

```file {title="/var/www/html/abc.com/public\_html/.htaccess" lang="aconf"}
Redirect /main.html /test1/index.html
```

The first parameter in the 'Redirect' command is the HTTP status code. Specifying the status code is helpful in letting the browser know that the page has been moved to a new location. If you leave this parameter blank, it will default to a 302 code indicating that the redirect is temporary. Specifying 301 makes it clear that the page at the requested location has been permanently moved to a new location.

The next parameter is the Unix path to the file requested in the URL. This parameter requires a Unix path, not a URL. The path should be the location of the.htaccess file where the redirect configuration is set. The final parameter indicates where you want the visitor to be redirected to. In this case, traffic is redirected to / test1 / index.html; the Unix path or HTTP URL for this second parameter is acceptable.

1\. In the browser, navigate to abc.com/main.html. You should see the url redirect to abc.com/test1/index.html in the address bar, and the html test file should be displayed.

## Set the 404 Error Page

When a visitor attempts to access a page or resource that does not exist (for example, by clicking a broken link or typing an incorrect URL), the server will respond with a 404 error code. It is important that users receive feedback to explain the error. In the event of a 404 error, Apache will display an error page by default. However, most sites provide a custom error page. You can use the.htaccess settings to let Apache know what error page you want to display whenever a user attempts to access a non-existent page.

1\. This will redirect all requests for non-existent documents to the page in the root directory of the project called 404.html. Open the.htaccess file, and add the following line:

```file {title="/var/www/html/abc.com/public\_html/.htaccess" lang="aconf"}
ErrorDocument /404.html
```

2\. Create the `404.html` file:

```file {title="/var/www/html/abc.com/public\_html/404.html" lang="aconf"}
Error: Page not found
```

3\. Navigate to a non-existent page in your browser, such as www.abc.com/doesnotexist.html. The 404 message must be displayed.

Thankyou..
