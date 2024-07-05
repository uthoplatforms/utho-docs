---
slug: Setting Up and Configuring ownCloud on Debian 10
description: "Install ownCloud: Your Secure File Sharing Solution on Debian 10"
keywords: ['ownCloud on Debian']
published: 2024-03-21
modified_by:
  name: Utho
title: "Setting up ownCloud on Debian 10: A Step-by-Step Guide"
title_meta: "How to Install and Configure ownCloud on Debian 10: A Step-by-Step Guide"
tags: ["debian"]
aliases: ['/guides/how-to-install-owncloud-debian-10/']
relations:
    platform:
        key: how-to-install-owncloud
        keywords:
            - distribution: Debian 10
authors: ["Pawan Kumar"]
---

## Understanding ownCloud

ownCloud enables you to create a private cloud for syncing data, storing files, and sharing them. It serves as an alternative to platforms like Dropbox or Box. This software fosters secure collaboration among your projects and teams.

ownCloud boasts numerous compelling features:

- **Versioning**: Easily access previous file versions for rollback.
- **Encryption**: Safeguard data during transmission between client and server.
- **Drag and drop upload**: Seamlessly transfer files from your desktop to ownCloud.
- **Theming**: Customize the appearance of your ownCloud instance.
- **Viewing ODF files**: Open and view `.odt`documents and `.ods` spreadsheets.
- **Expansion via installable applications**: Explore a variety of official and third-party apps on the [ownCloud Marketplace](https://marketplace.owncloud.com/) Marketplace.
- **A mobile app for [Android](https://play.google.com/store/apps/details?id=com.owncloud.android) and [iOS](https://apps.apple.com/us/app/owncloud-file-sync-and-share/id1359583808)**: Stay connected with your ownCloud server for syncing, uploading, downloading, and file viewing through mobile apps.

Why host your own cloud? Here are some common reasons:

- Safeguard sensitive data without relying on third-party services.
- Establish a private cloud for household use, particularly if working from home.
- Keep all business data in-house for better control and security.
- Secure an expandable storage solution tailored to your needs.

This guide helps you install ownCloud on Debian 10, a dependable operating system. The process involves a few simple steps:

- [install the LAMP (Linux Apache MySQL/MariaDB PHP) stack](/docs/guides/how-to-install-a-lamp-stack-on-debian-10/).
- Create a database and a database user.
- Configure Apache.
- Set up ownCloud using its graphical user interface.

## Before You Begin

1.  If you haven't already, create a Utho account and set up a Compute Instance.

1.  Refer to our guide for updating your system. Additionally, consider configuring the timezone, hostname, creating a limited user account, and enhancing SSH access for added security.

{{< note >}}

If you own a domain name and want to link it to your ownCloud server, utilize the Utho DNS Manager to direct the domain to your Utho server's IP address. If you don't have a domain, simply use the IP address of your Utho server instead of a domain name when configuring Apache, as outlined in the [Create an Apache Configuration File](#create-an-apache-configuration-file) section.

{{< /note >}}

## Install ownCloud
### Install Apache and PHP

In this section, we'll install the Apache web server along with all the required PHP components.

1. Connect to your Utho via SSH

1. Install Apache and all the required PHP packages:

        sudo apt-get install apache2 mariadb-server libapache2-mod-php openssl php-imagick php-common php-curl php-gd php-imap php-intl php-json php-ldap php-mbstring php-mysql php-pgsql php-smbclient php-ssh2 php-sqlite3 php-xml php-zip php-apcu -y


1. After installing the packages, initiate and enable Apache using the following commands:

        sudo systemctl start apache2
        sudo systemctl enable apache2

### Install the database

ownCloud relies on a database to store data. Instead of MySQL, this installation utilizes MariaDB. MariaDB is a secure fork of MySQL, prioritizing security measures.

1. Install MariaDB:

        sudo apt-get install mariadb-server -y

1. Start and enable the database using the following commands:

        sudo systemctl start mariadb
        sudo systemctl enable mariadb

1. Set a MariaDB admin password and secure the installation by running a tool inherited from MySQL:

        sudo mysql_secure_installation

When prompted, press **Enter** (since there is no existing MariaDB admin password). Respond with **y** (for "yes") to set the admin password, then enter and confirm a new secure password for the MariaDB admin user. Lastly, you'll be asked four questions; answer **y** (for "yes") to each of them.

### Create the ownCloud Database

Now that you have installed the prerequisites, let's create the ownCloud database and user. The commands in this section are issued from within the MariaDB console.

1. Access the MariaDB console:

        sudo mysql -u root -p


1. create your ownCloud database:

        CREATE DATABASE ownclouddb;

1. Create a new user with the necessary privileges, including a strong and unique password. Ensure to replace PASSWORD with your chosen password:

        GRANT ALL ON ownclouddb.* TO 'ownclouduser'@'localhost' IDENTIFIED BY 'PASSWORD';

1. Flush the privileges of your database:

        FLUSH PRIVILEGES;

1. Finally, exit the database console:

        exit


### Download ownCloud

Now, before downloading ownCloud, it's essential to verify the most recent version available by visiting the [ownCloud downloads page](https://owncloud.com/download-server/).


1. Download ownCloud by replacing `10.6.0` with the desired version. As of writing this guide, the latest version is 10.6.0.

        wget https://download.owncloud.org/community/owncloud-10.5.0.zip

1. Unzip the downloaded file using the following command:

        unzip owncloud-10.5.0.zip

    {{< note respectIndent=false >}}

If unzip is not already installed, you can install it using the following command:

    sudo apt-get install zip -y

{{< /note >}}

1. After unzipping the file, you'll find a new directory named owncloud. Move this directory to the Apache document root. In this example, we'll use the default directory for Apache site files:

        sudo mv owncloud /var/www/html/

1. Change the ownership of the `owncloud` directory:

        sudo chown -R www-data: /var/www/html/owncloud

### Create an Apache Configuration File

Apache necessitates a [virtual host configuration file](https://httpd.apache.org/docs/2.4/vhosts/examples.html) to serve your ownCloud instance to the web.

1. Use the Nano text editor to create an Apache configuration file:

        sudo nano /etc/apache2/sites-available/owncloud.conf

1. Paste the following text into the new file, replacing mentions of `example.com` with your own domain name or your Utho IP Address:

       {{< file "/etc/apache2/sites-available/owncloud.conf">}}
       <VirtualHost \*:80>
       ServerAdmin admin@example.com
       DocumentRoot /var/www/html/owncloud
       ServerName example.com
       <Directory /var/www/html/owncloud>
       Options FollowSymlinks
       AllowOverride All
       Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/example.com_error.log

       CustomLog ${APACHE_LOG_DIR}/your-domain.com_access.log combined

       </VirtualHost>
       {{</ file >}}

1. Save and close the file by typing `Ctrl + O` and then pressing Enter. After that, press `Ctrl + X` to exit Nano.

1. To enable the `rewrite`, `mime`, and `unique_id` Apache modules, use the following commands:

        sudo a2enmod rewrite mime unique_id

1. Restart the Apache server using the following command:

        sudo systemctl restart apache2

The command-line setup is now finished.

### Configure ownCloud

This section guides you through the web-based installation process:

Open a web browser and go to your site's domain. If you've configured a domain, visit http://example.com/owncloud. If you've set up Apache with your server's IP address, use http://192.0.2.1/owncloud, replacing the example IP address with your own. This should display the ownCloud web-based installer.

Create a username and password for the admin user. Then, click on the `Storage & Database` dropdown menu and select `MySQL/MariaDB`.

Now, you'll see the database information section. Enter the following details:

- Database User: `ownclouduser`
- Database Password: the password you assigned to the ownCloud database user
- Database Name: `ownclouddb`
- Host: keep it as `localhost` (the default setting)

Click on the `Finish setup` button. Once the installation is complete, you'll be directed to the ownCloud login page. Use the admin username and password you just created to log in. After logging in, you'll be redirected to the main ownCloud page.

    ![ownCloud is installed and ready to use as your private cloud](ownCloud_Debian_004.jpeg "ownCloud is installed and ready to use as your private cloud")

Congratulations! You've successfully set up ownCloud on Debian 10.
