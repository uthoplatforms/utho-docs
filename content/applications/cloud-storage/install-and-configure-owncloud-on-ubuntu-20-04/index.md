---
slug: "Installing and Configuring OwnCloud on Ubuntu 20.04: A Step-by-Step Guide"
description: "We will guide you through each step of setting up ownCloud, which involves installing Apache, PHP packages, and configuring Apache domain names"
keywords: ['ownCloud on Ubuntu']
published: 2024-03-20
modified_by:
  name: Utho
title: "Setting Up ownCloud on Ubuntu 20.04"
title_meta: "Installing and Configuring ownCloud on Ubuntu 20.04: A Guide"
tags: ["ubuntu"]
relations:
    platform:
        key: how-to-install-owncloud
        keywords:
            - distribution: Ubuntu 20.04
authors: ["Pawan Kumar"]
---

## Understanding ownCloud

[ownCloud](https://owncloud.com/) allows you to create a private cloud for synchronizing data, storing files, and sharing them securely. It serves as a viable alternative to commercial platforms such as Dropbox or Box, facilitating secure collaboration among projects and teams.

ownCloud boasts a plethora of compelling features:

- **Versioning**: Enables rolling back to previous file versions.
- **Encryption**: Safeguards user data during transmission between client and server.
- **Drag and drop upload**: Easily upload files from your desktop file manager to ownCloud.
- **Theming**: Personalize the appearance of your ownCloud instance.
- **Viewing ODF files**: View Open Document Format files like `.odt` documents and `.ods` spreadsheets.
- **Expansion via installable applications**: Access a variety of official and third-party applications through the [ownCloud Marketplace](https://marketplace.owncloud.com/) Marketplace.
- **Mobile apps** allow you to interact with your ownCloud server, such as for syncing, uploading, downloading, and viewing files. [Android](https://play.google.com/store/apps/details?id=com.owncloud.android) and [iOS](https://apps.apple.com/us/app/owncloud-file-sync-and-share/id1359583808)**: Stay connected to your ownCloud server with mobile apps for syncing, uploading, downloading, and file viewing.

What are the motivations behind hosting your own cloud? Here are some typical reasons:

- To store sensitive data securely, without relying on third-party commercial services.
- Working from home necessitates a private cloud exclusively accessible to household members.
- Small business owners prefer to maintain all operations in-house.
- Seeking an adaptable storage solution that can scale as needed.

In this guide, you'll be taken through the process of installing ownCloud on Ubuntu 20.04, renowned for its user-friendly server environment. The installation of ownCloud on Ubuntu 20.04 involves just a few straightforward steps. You'll start by [install the LAMP (Linux Apache MySQL/MariaDB PHP) stack](/docs/guides/how-to-install-a-lamp-stack-on-ubuntu-18-04/), followed by creating a database and user, configuring Apache, and finally setting up ownCloud through its intuitive graphical user interface.

{{< note >}}

For effortless deployment of ownCloud on a Compute Instance, contemplate utilizing the Utho Marketplace to deploy ownCloud Server.

{{< /note >}}

## Before You Begin

1. If you haven't created a Utho account and Compute Instance yet, do so now.

2. Refer to our guide for updating your system. Additionally, you might want to adjust the timezone, configure the hostname, create a restricted user account, and enhance SSH access.

{{< note >}}

If you own a registered domain name that you wish to direct to your ownCloud instance, utilize the Utho DNS Manager to point the domain to the Utho server where you intend to install ownCloud. If you don't have a registered domain name, substitute "example.com" with the IP address of the Utho server when proceeding with the steps outlined in the [Create an Apache Configuration File](#create-an-apache-configuration-file) section.

{{< /note >}}

## Install ownCloud
### Install the LAMP Stack

To run ownCloud, you need a complete LAMP setup, which stands for Linux, Apache, MySQL, and PHP. Here, we'll walk you through installing the LAMP stack on your Linode. While you can choose different web servers, like NGINX or lightHTTP, the ownCloud developers suggest using Apache for better compatibility and performance. Let's get started with the installation.

1. Install the LAMP stack effortlessly using a single command:

        sudo apt-get install lamp-server^ -y

1. After the installation finishes, activate and initiate Apache:

        sudo systemctl start apache2
        sudo systemctl enable apache2

1. Initiate and enable the MySQL database:

        sudo systemctl start mysql
        sudo systemctl enable mysql

1. Create a MySQL admin password and secure the installation:

        sudo mysql_secure_installation

During this process, you'll have the option to modify the MariaDB root password, delete anonymous user accounts, deactivate root logins from external sources, and eliminate test databases. It's advisable to respond with `yes` to these prompts. For further details on the script, you can refer to the [MariaDB Knowledge Base](https://mariadb.com/kb/en/mariadb/mysql_secure_installation/).

1. Install PHP along with all necessary PHP packages:

        sudo apt-get install php php-opcache php-gd php-curl php-mysqlnd php-intl php-json php-ldap php-mbstring php-mysqlnd php-xml php-zip -y

1. Restart Apache to apply any changes:

        sudo systemctl restart apache2

### Create the ownCloud Database

Now that you've installed the prerequisites, it's time to set up the ownCloud database and user. The commands in this section are executed within the MariaDB console.

1. Access the MariaDB command-line interface:

        sudo mysql -u root -p

1. create your ownCloud database:

        CREATE DATABASE ownclouddb;

1. Create a new user with the required privileges, ensuring to use a strong and unique password. Replace `PASSWORD` with your chosen password:

        GRANT ALL ON ownclouddb.* TO 'ownclouduser'@'localhost' IDENTIFIED BY 'PASSWORD';


1. Flush your database's privileges:

        FLUSH PRIVILEGES;


1. Finally, exit the database console:

        exit

### Download ownCloud

Now that the system is prepared for ownCloud, it's time to download the software. Before proceeding, visit the [ownCloud downloads page](https://owncloud.com/download-server/) to ensure you have the latest version.

1. Download ownCloud. As of the creation of this guide, the latest version is `10.4.0`. Replace `10.4.0` with your desired version when downloading.

        wget https://download.owncloud.org/community/owncloud-10.5.0.zip

1. Unzip the downloaded file:

        unzip owncloud-10.5.0.zip

    {{< note respectIndent=false >}}

If needed, install `unzip` using the command:

    sudo apt-get install zip -y

{{< /note >}}

1. After unzipping the file, a new directory named `owncloud` will be created. Move this directory to the Apache document `root`. In this example, the default directory for Apache site files is used.

        sudo mv owncloud /var/www/html/

1. Change the ownership of the `owncloud` directory:

        sudo chown -R www-data: /var/www/html/owncloud

### Setting Up an Apache Configuration File

Apache necessitates a [virtual host configuration file](https://httpd.apache.org/docs/2.4/vhosts/examples.html) to serve your ownCloud instance on the web.

1. Create an Apache configuration file using the Nano text editor:

        sudo nano /etc/apache2/sites-available/owncloud.conf

1. Paste the following text into the new file. Replace instances of example.com with your own domain name or your Utho's IP Address:

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

1. Save and close the file by pressing `Ctrl + O` and then `Ctrl + X`.

1. To enable the `rewrite`, `mime`, and `unique_id` Apache modules:

        sudo a2enmod rewrite mime unique_id

1. Restart the Apache server to apply the changes:

        sudo systemctl restart apache2

The command line part of the installation is finished.

### Configure ownCloud

This section covers the web-based part of the installation:

1. Open a web browser and go to your site's domain. If you've set up a domain name, it would be something like `http://example.com/owncloud`. If you've configured Apache to point to your server's IP address, go to `http://192.0.2.0/owncloud`, replacing the example IP address with your own. This should bring up the ownCloud web-based installer.

1. Provide a username and password for the admin user. Then, from the `Storage & Database` drop-down menu, select `MySQL/MariaDB`.

1. You'll now see the database information section. Fill in the following details:

    - Database User: `ownclouduser`
    - Database password: the password you set for the ownCloud database user
    - Database: `ownclouddb`
    - Localhost: leave as the default

1. Click on **Finish setup**. Once the installation is complete, the ownCloud login page will appear. Log in using the newly-created admin credentials. Upon successful login, you'll be directed to the main ownCloud page.

Congratulations! You have successfully set up ownCloud on Ubuntu 20.04.
