---
slug: "FarmOS Installation: Cultivate Your Digital Farm"
keywords: ["farmos", "Drupal", "LAMP"]
tags: ["drupal", "lamp", "cms"]
description: 'This guide shows you how to prepare a system for, then install and set up the agribusiness management web app, farmOS.'
published: 2024-08-11
modified: 2024-08-11
modified_by:
    name: Utho
title: 'How to Install farmOS - a Farm Recordkeeping Application'
aliases: ['/applications/project-management/install-farmos/']
authors: ["Pawan Kumar"]
---

## What is farmOS?

[farmOS](http://farmos.org/) is a unique web application designed to empower farmers to efficiently manage and track every aspect of their farm operations. Built on Drupal and licensed under [GPL V.3](https://www.gnu.org/licenses/gpl-3.0.en.html) offers a valuable free-software solution for farms to utilize.

This guide walks you through the process of installing, setting up, and hosting your own farmOS web application on a Utho running Ubuntu 20.04.

## Before You Begin

1. Take a moment to review our [Getting Started](/docs/products/platform/get-started/) guide and follow the instructions to setting your Utho's hostname](/docs/products/compute/compute-instances/guides/set-up-and-secure/#configure-a-custom-hostname) and [timezone](/docs/products/compute/compute-instances/guides/set-up-and-secure/#set-the-timezone). You can find detailed steps for configuring a custom hostname here and setting the timezone here.

1. Refer to our [Securing Your Server](/docs/products/compute/compute-instances/guides/set-up-and-secure/)  guide for essential steps to enhance the security of your server. This includes tasks such as [create a standard user account](/docs/products/compute/compute-instances/guides/set-up-and-secure/#add-a-limited-user-account), [harden SSH access](/docs/products/compute/compute-instances/guides/set-up-and-secure/#harden-ssh-access), [remove unnecessary network services](/docs/products/compute/compute-instances/guides/set-up-and-secure/#remove-unused-network-facing-services) , and [create firewall rules](/docs/products/compute/compute-instances/guides/set-up-and-secure/#configure-a-firewall) tailored to your web server. Depending on your specific application, you may need to create additional firewall exceptions.

       {{< content "limited-user-note-shortguide" >}}

1. Install and set up a [LAMP stack on Ubuntu 20.04](/docs/guides/how-to-install-a-lamp-stack-on-ubuntu-20-04/).. You can skip the MySQL configuration steps provided in that guide and follow the instructions below.

## MySQL Setup

1.  Configure your database for PHP. When prompted, select `apache2` as the web server for automatic configuration, choose `Yes` to automatically configure a database, and then enter and confirm your database root password.

        sudo apt install php-mysql phpmyadmin

1.  After configuring MariaDB, log in with your database root password.

        mysql -u root -p

1.  Now, create a database and a database user with the necessary privileges. Replace `secure_password` with a password of your choice:

        CREATE DATABASE farmdb;
        CREATE USER 'farm_user'@'localhost' IDENTIFIED BY 'secure_password';
        GRANT ALL PRIVILEGES ON farmdb.* TO 'farm_user'@'localhost';
        FLUSH PRIVILEGES;


1.  Exit MariaDB:

        quit

## Download and Install farmOS

1. Go to the document root of your website. If you set up Apache on Ubuntu 20.04 using our [LAMP stack on Ubuntu 20.04](/docs/guides/how-to-install-a-lamp-stack-on-ubuntu-20-04/), your document root should be at `/var/www/html/example.com/public_html/`. Replace `example.com` with the name of your own document root.

        cd /var/www/html/example.com

1. Download the farmOS tarball. At the time of writing, the latest version is farmOS 7.x-1.7. Visit [Drupal's download page](https://www.drupal.org/project/farm)  to find the latest core tarball.

        sudo wget https://ftp.drupal.org/files/projects/farm-7.x-1.7-core.tar.gz

{{< note type="alert" respectIndent=false >}}
Make sure the version number of the tarball matches the farmOS version you want to download.
{{< /note >}}

1.  Unpack the contents of the downloaded tarball into the document root of your site:

        sudo tar -zxvf farm-7.x-1.7-core.tar.gz -C public_html --strip-components=1

1.  farmOS relies on a PHP graphics library called GD. Install GD and other necessary dependencies:

        sudo apt install php-gd php-xml php-xmlrpc

## Configure Apache 2.4

1. Activate Apache's [rewrite module](https://httpd.apache.org/docs/current/mod/mod_rewrite.html). This module is essential because farmOS relies on [Clean URLs](https://www.drupal.org/getting-started/clean-urls), which are enabled by default.

        sudo a2enmod rewrite

Use your preferred text editor to add rewrite conditions for your farmOS site's document root in Apache's configuration file. If you followed our [LAMP stack on Ubuntu 20.04](/docs/guides/how-to-install-a-lamp-stack-on-ubuntu-20-04/) guide to install and configure Apache, your site's configuration file can be found at `/etc/apache2/sites-available/example.com.conf`.

    {{< file "/etc/apache2/sites-available/example.com.conf" conf >}}
    <Directory /var/www/html/example.com/public_html>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
    RewriteEngine on
    RewriteBase /
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^(.*)$ index.php?q=$1 [L,QSA]
    </Directory>
    {{< /file >}}

1.  Change the ownership of your site's document root from `root` to `www-data`. This enables you to install modules, themes, and update Drupal without needing FTP credentials.

        sudo chown -R www-data:www-data /var/www/html/example.com

1.  After making the ownership change, restart Apache to apply all modifications.

        sudo systemctl restart apache2

## Configure farmOS

Open your Utho's domain or [IP address](/docs/guides/find-your-uthos-ip-address) in a web browser. This displays the initial step of the farmOS/Drupal web configuration.

The first screen prompts you to select a profile and a language:

Drupal checks the installation correctness in the **Verify requirements** section. Then, it proceeds to configuring the database. Here, you'll input the database information created earlier in this tutorial.

Once farmOS connects to the database, you'll configure your farmOS site. Here, you'll define the site name and create the main user account.

The following section prompts you to select the modules you want to install. You can install or uninstall modules later, but here you can install specific modules tailored to your farm's needs.

Finally, after module installation, you'll land on the farmOS dashboard.

Once the installation completes, consider resetting file permissions to enhance security. If you set up your Apache server following our [LAMP stack on Ubuntu 20.04](/docs/guides/how-to-install-a-lamp-stack-on-ubuntu-20-04/) guide, your document root should be in the `/var/www/html/example.com/public_html/` directory.

        sudo chmod 644 sites/default
        sudo chmod 644 ./sites/default/settings.php

## Add Users

To add users to your farmOS distribution, head to the **People** tab under **Manage**. From there, you can create new users.

After creating each user, use the **People** tab to ensure they were added successfully.

## Next Steps

### Registering a Domain Name for farmOS
If you want to register a domain name like `yourfarm.com`, follow our [DNS Manager](/docs/products/networking/dns-manager/) guide to add your Fully Qualified Domain Name (FQDN), such as `farmos.yourfarm.com`, to the Utho Manager. Having an FQDN allows you and others to access farmOS via a URL instead of using your Utho's public IP address. If you're only using farmOS internally, you can skip this step.

### Generate a Google API Key
farmOS has the ability to integrate with Google Maps. To enable this feature, you'll need a GoogleAPI key. The farmOS official documentation includes a section on obtaining and using GoogleAPI keys. By interfacing with Google Maps, you can save specific geographical areas in farmOS. This allows you to precisely identify the locations of farmOS projects and tasks using the Google Maps API.
