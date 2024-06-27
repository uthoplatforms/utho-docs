---
title: "Upgrading WordPress Manually on Linux sever"
date: "2022-06-22"
title_meta: "Upgrading WordPress Manually on Linux sever"
description: "Upgrading WordPress Manually on Linux sever"
keywords:  ['ssh logins', 'linux', 'MOTD']
tags: ["linux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/upgrading-wordpress-manually-on-linux-sever']
---

![](images/Upgrading-WordPress-Manually-on-Linux-sever_utho.jpg)

Step 1. login to wordpress by accessing the server IP\_address/wp-admin or domain\_name/wp-admin in the browser.

![](images/cc-1-1024x611.png)

Step 2. check the current version of wordpress , i.e 5.0.13 here.

![](images/cc1-1-1024x594.png)

Step 3. Download the most recent version of WordPress from the official WordPress website to your local machine.

Please visit [https://wordpress.org/download/releases/](https://wordpress.org/download/releases/) for more information.

Step 4. extract the zip file and then connect the server using FileZilla.

NOTE : hostname : serve\_ip , username , password , port : 22. 
As shown in the below screenshot.

![](images/cc2-1-1024x307.png)

Step 5. Navigate to your website's root directory (for example, /var/www/html/wordpress) on the server side and delete the ‘wp-admin' and ‘wp-includes' directories.

![](images/cc3-1-1024x231.png)

Step 6. Now, Copy the ‘wp-includes’ and ‘wp-includes’ directories from the unzipped new version WordPress to the website root directory(which replaces the directories deleted in step 5 at website root directory).

Step 7 : Do not delete any files or directories in the website's root directory after step 6. In this section just copy all of the files from the new version WordPress directory to the existing ‘wordpress' website root directory (by this act it overwrite the existing files with same name).

![](images/cc4-1-1024x424.png)

Step 8. Your wp-config.php file will be unaffected because the default config file for WordPress will be wp-config-sample.php. Examine wp-config-sample.php for any changes to the wp-config.php file. You can also edit database records & rename the wp-config-sample.php file as wp-config.php ,afterwards just delete the older file(wp-config.php).

Step 9. Now, from your browser, navigate to the WordPress admin page at ‘ip address (or)domain name/wp-admin' and update the WordPress database.

![](images/cc5-1024x380.png)

Step 10. Login into the wordpress and check the current version of wordpress .

![](images/cc6-1024x536.png)

Wordpress has been updated.

Thank you!!
