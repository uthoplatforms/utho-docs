---
slug: "Installing Nextcloud on Ubuntu 22.04: A Step-by-Step Guide"
description: 'Discover Nextcloud, the productivity platform, and learn how to effortlessly set it up and configure it on Ubuntu 22.04 with this comprehensive guide.'
keywords: ['Nextcloud Ubuntu','what is Nextcloud','how to install nextcloud','Ubuntu configure Nextcloud','download Nextcloud Ubuntu']
published: 2024-03-20
modified: 2024-03-20
modified_by:
  name: Utho
title: "Deploy Nextcloud on Ubuntu 22.04"
title_meta: "Installing Nextcloud on Ubuntu 22.04: A Step-by-Step Guide"
relations:
    platform:
        key: install-nextcloud
        keywords:
           - distribution: Ubuntu 22.04
authors: ["Pawan Kumar"]
---
[Nextcloud](https://nextcloud.com/) is a free and open-source tool for storing and sharing files, similar to Dropbox and Google Drive. With Nextcloud, you can access your documents and pictures online from one central place. This guide will walk you through downloading, installing, and setting up Nextcloud on Ubuntu 22.04 LTS. Additionally, it will show you how to configure your Ubuntu LAMP stack to support Nextcloud. Let's get started!

## What is Nextcloud?

Nextcloud stands out as a secure hub for storing and managing user data and documents. Built on an open-source foundation, it offers users greater control and flexibility compared to other platforms. Here are some key advantages of Nextcloud:

- Users can host their documents on their own servers, eliminating the need to rely on external vendors for hosting.
- Nextcloud provides detailed file access and sharing controls, along with workflow and audit logs.
- It includes a powerful search engine capable of querying the entire file collection.
- Nextcloud can monitor and record all data exchanges and communications.
- It offers end-to-end client-side encryption and robust key handling.
- Nextcloud includes real-time notifications, comments, and multi-user editing capabilities.
- The platform prioritizes security with third-party reviews and a well-funded Security Bug Bounty program.
- Nextcloud collaborates with developer and user communities to develop, optimize, and test new features.
- Users can extend Nextcloud's functionality through its [app store](https://apps.nextcloud.com/), where they can download additional extensions and customizations.

For a comprehensive comparison of Nextcloud features, check out the [Nextcloud feature comparison](https://nextcloud.com/compare/) page. And for detailed guidance on using Nextcloud, refer to the [Nextcloud product documentation](https://docs.nextcloud.com/server/24/admin_manual/contents.html).

## Before You Begin

1. If you haven't created a Utho account and Compute Instance yet, here's what you need to do:

1. Follow our guide to update your system. Additionally, you may want to set the timezone, configure your hostname, create a limited user account, and enhance SSH access security.

1. Before you can start using Nextcloud, you'll need to set up a LAMP Stack, which includes Apache web server, MariaDB/MySQL RDBMS, and PHP programming language. This guide will walk you through installing these components. You can find more detailed instructions in the [Utho guide to installing a LAMP stack on Ubuntu 22.04](/docs/guides/how-to-install-a-lamp-stack-on-ubuntu-22-04/).

1. To ensure Nextcloud runs smoothly and securely with HTTPS, it's important to configure a domain name for your server. For guidance on domain names and how to point them to your Utho server, refer to the [Utho DNS Manager guide](/docs/products/networking/dns-manager/).

{{< note respectIndent=false >}}

This guide is intended for users who are not logged in as the root user. Commands that need elevated privileges are preceded by `sudo`. If you're not familiar with the `sudo` command, you can learn more in the [Users and Groups](/docs/guides/linux-users-and-groups/) guide.

{{< /note >}}

## Setting Up Nextcloud Prerequisites

Nextcloud relies on a LAMP stack for smooth operation. In this section, you'll find step-by-step instructions on installing the Apache web server, MariaDB RDBMS, and PHP programming languages. Although these instructions are tailored for Ubuntu 22.04 users, they generally apply to Ubuntu 20.04 as well.

{{< note respectIndent=false >}}

It's worth noting that Nextcloud version 24 added support for PHP 8.1, which is the default PHP library package in Ubuntu 22.04. However, earlier Nextcloud versions require PHP 7.4. If you're using an older version of Nextcloud, you might need to downgrade your PHP packages accordingly.

{{< /note >}}

### Setting Up the Apache Web Server

Follow these instructions to install and test an Apache web server on Ubuntu 22.04.

1. Update and upgrade the Ubuntu packages by following these steps:

    ```command
    sudo apt update && sudo apt upgrade
    ```

1. Install the Apache web server using the `apt` package manager with the following command:

    ```command
    sudo apt install apache2
    ```

1. Configure the `ufw` firewall to allow the `Apache Full` profile, which allows HTTP and HTTPS connections for web access. Ensure that `OpenSSH` connections are also permitted. Once all changes are made, enable `ufw`.

    ```command
    sudo ufw allow OpenSSH
    sudo ufw allow in "Apache Full"
    sudo ufw enable
    ```

    {{< note respectIndent=false >}}

  The `Apache Full` profile allows both HTTP and HTTPS traffic. If you want to temporarily limit web traffic to HTTP requests only, switch to the `Apache` profile. However, if you need to block HTTP requests entirely and only allow HTTPS traffic, use the `Apache Secure` profile. Avoid using the `Apache Secure` profile until you have enabled HTTPS on the server.

    {{< /note >}}

1. Check the firewall settings by using the ufw status command:

    ```command
    sudo ufw status
    ```

    ```output
    Status: active

    To                         Action      From
    --                         ------      ----
    OpenSSH                    ALLOW       Anywhere
    Apache Full                ALLOW       Anywhere
    OpenSSH (v6)               ALLOW       Anywhere (v6)
    Apache Full (v6)           ALLOW       Anywhere (v6)
    ```

1. Enable the `mpm_prefork` Apache module and disable `mpm_event` by executing the following commands:

    ```command
    sudo a2dismod mpm_event
    sudo a2enmod mpm_prefork
    ```

1. Restart Apache using the `systemctl` utility:

    ```command
    sudo systemctl restart apache2
    ```

1. Verify that the web server is still active using `systemctl`:

    ```command
    sudo systemctl status apache2
    ```

1. Finally, visit the IP address of the web server in your browser to confirm that the server is working properly.

    ```command
    http://your_IP_address/
    ```

    {{< note respectIndent=false >}}

Navigate to the Utho Dashboard to locate the IP address for the Ubuntu system.

    {{< /note >}}

Upon opening your browser, you'll encounter the default Ubuntu/Apache2 welcome page. This page simply states "It works" and offers basic information about the server.
    
For more detailed guidance on configuring the Apache HTTP Server, consult the [Apache Documentation](https://httpd.apache.org/docs/2.4/).

### Installing the MariaDB RDBMS

Nextcloud stores its data in a relational database management system (RDBMS) like MySQL or MariaDB. This guide uses MariaDB, which is quite similar to MySQL but adheres more closely to the open-source philosophy. MariaDB offers additional features and improved performance and usability.

Here's how to install MariaDB on Ubuntu 22.04:

1. Use the apt package manager to install MariaDB with the following command:

    ```command
    sudo apt install mariadb-server
    ```

1. Verify the status of MariaDB to ensure it is installed correctly by executing the following command:

    ```command
    sudo systemctl status mariadb
    ```

1. Enable MariaDB in `systemctl` to ensure it automatically starts when the server boots by running this command:

    ```command
    sudo systemctl enable mariadb
    ```

1. Secure and configure MariaDB using the `mysql_secure_installation` utility.

    ```command
    sudo mysql_secure_installation
    ```

Enter your password when prompted. You don't need to switch to Unix socket authentication or change the root password. Answer `Y` (Yes) to the following questions:

    - `Remove anonymous users?`
    - `Disallow root login remotely?`
    - `Remove test database and access to it?`
    - `Reload privilege tables now?`

For more detailed information about MariaDB, refer to the [MariaDB Server Documentation](https://mariadb.com/kb/en/documentation/).

### Setting Up the Nextcloud Database in MariaDB

Once MariaDB is installed, the next step is to create a new database for Nextcloud and set up a user with the necessary permissions. Follow these instructions to configure the database:

1. Log in to MariaDB as the `root` user. If you've set up a root password, you'll be asked to enter it. Once authenticated, you'll see the MariaDB prompt.

    ```command
    sudo mysql -u root
    ```

    ```output
    Welcome to the MariaDB monitor.  Commands end with ; or \g.
    Your MariaDB connection id is 37
    Server version: 10.6.7-MariaDB-2ubuntu1.1 Ubuntu 22.04

    Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    MariaDB [(none)]>
    ```

1. Create the `nextcloud` database. After running this command and any following ones, MariaDB should confirm with `Query OK`.

    ```command
    CREATE DATABASE nextcloud;
    ```

1. Confirm that the database has been successfully created by using the `SHOW DATABASES` command.

    ```command
    SHOW DATABASES;
    ```

    ```output
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | mysql              |
    | nextcloud          |
    | performance_schema |
    | sys                |
    +--------------------+
    5 rows in set (0.001 sec)
    ```

1. Create a user and grant them full access rights to the database. Ensure to use a strong and secure password instead of 'password'.

    ```command
    GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost' IDENTIFIED BY 'password';
    ```

1. Flush the privileges to ensure that the recent changes take effect:

    ```command
    FLUSH PRIVILEGES;
    ```

1. Exit the database:

    ```command
    quit
    ```

### Installing PHP and Additional Components

Nextcloud is built using the PHP programming language. Version 24 of Nextcloud supports PHP release 8.1, which happens to be the default PHP version in Ubuntu packages. Therefore, you can install the regular `php` package. However, if you're using an older version of Nextcloud, PHP 7.4 is required.

{{< note respectIndent=false >}}

To install PHP 7.4, replace `php` with `php7.4` for all packages. For instance, `php-cli` should be replaced with `php7.4-cli`.
{{< /note >}}

Use the following commands to install PHP and the other required packages:

1. Install the core PHP package using `apt` with the following command:

    ```command
    sudo apt install php
    ```

1. Verify the PHP version number by running the following command:

    ```command
    php -v
    ```

    ```output
    PHP 8.1.2-1ubuntu2.6 (cli) (built: Sep 15 2022 11:30:49) (NTS)
    ```

1. Install the remaining PHP components by executing the following command:

    ```command
    sudo apt install php-apcu php-bcmath php-cli php-common php-curl php-gd php-gmp php-imagick php-intl php-mbstring php-mysql php-zip php-xml
    ```
1. Activate the required PHP extensions by running the following command:

    ```command
    sudo phpenmod bcmath gmp imagick intl
    ```

1. Install the `unzip` utility if it's not already installed on your system.

    ```command
    sudo apt install unzip
    ```

1. Install the `libmagic` package by executing the following command:

    ```command
    sudo apt install libmagickcore-6.q16-6-extra
    ```

## Downloading, Installing, and Configuring Nextcloud

You can download Nextcloud using `wget`. Once the downloaded file is unzipped, you'll need to create a virtual host for Nextcloud. Additionally, certain configurations and optimizations need to be applied to the system.

### Downloading and Installing Nextcloud

To download and install Nextcloud, follow these steps:

1. Download Nextcloud using `wget`. Visit the [Nextcloud installation page](https://nextcloud.com/install/) to find the URL for the latest stable release of Nextcloud. This page provides a link to the latest Nextcloud zip file. If you're looking for a specific release of Nextcloud, check the [Nextcloud changelog and archive](https://nextcloud.com/changelog/). Here's an example demonstrating how to download Nextcloud release 24.0.1:

    ```command
    wget https://download.nextcloud.com/server/releases/nextcloud-24.0.1.zip
    ```

1. Extract the archive to create a `nextcloud` folder in the same directory as the zip file.

    ```command
    unzip nextcloud-24.0.1.zip
    ```

1. **(Optional)** After extracting the contents, you may choose to delete or rename the archive. Additionally, you can rename the `nextcloud` directory to a more descriptive name, such as `nextcloud.yourdomainname`.

1. Adjust the folder permissions for the `nextcloud` directory:

    ```command
    sudo chown -R www-data:www-data nextcloud
    ```

1. Move the new directory to the server directory, which typically defaults to `/var/www/html` on most servers.

    ```command
    sudo mv nextcloud /var/www/html
    ```

1. Disable the default Apache landing page by executing the following command:

    ```command
    sudo a2dissite 000-default.conf
    ```

    {{< note respectIndent=false >}}

Do not reload Apache at this time, even if prompted. Apache should be reloaded later once all configurations are complete.

    {{< /note >}}

### Creating a Virtual Host Configuration File for Nextcloud

This section guides you through configuring a virtual host file for the Nextcloud application. The virtual host instructs Apache on how to manage and serve requests for the Nextcloud domain.

1. Create a new file named `nextcloud.conf` in the `etc/apache2/sites-available` directory:

    ```command
    sudo nano /etc/apache2/sites-available/nextcloud.conf
    ```

1. The file should contain the following details. Replace `DocumentRoot` with the server directory name followed by `/nextcloud`. In the `ServerName` attribute, use the actual domain name instead of `example.com`. Save the file after making all modifications.

       file {title="/etc/apache2/sites-available/nextcloud.conf" lang="aconf"}
       <VirtualHost *:80>
       DocumentRoot "/var/www/html/nextcloud"
       ServerName example.com

       <Directory "/var/www/html/nextcloud/">
        Options MultiViews FollowSymlinks
        AllowOverride All
        Order allow,deny
        Allow from all
        </Directory>

        TransferLog /var/log/apache2/nextcloud_access.log
        ErrorLog /var/log/apache2/nextcloud_error.log
        </VirtualHost>

1. Activate the site. However, refrain from reloading Apache at this point.

    ```command
    sudo a2ensite nextcloud.conf
    ```

    ```output
    Enabling site nextcloud.
    ```

### Optimizing PHP for Nextcloud

The default PHP setup works well for many applications. However, specific PHP settings need to be tweaked to ensure optimal performance and functionality for Nextcloud. Here's how to make these adjustments:

1. Open the `php.ini` file and modify the following settings. Sometimes, the parameter may be commented out and needs to be uncommented. To uncomment a parameter, remove the `;` character at the beginning of the line. Keep the rest of the lines unchanged.

    {{< note respectIndent=false >}}

To find the appropriate timezone for the `date.timezone parameter`, refer to [PHP timezone documentation](https://www.php.net/manual/en/timezones.europe.php).

If your server is using an older PHP version, replace `8.1` with the actual version number in the file name. For instance, to configure PHP 7.4, the file path would be `/etc/php/7.4/apache2/php.ini`.

    {{< /note >}}

    ```command
    sudo nano /etc/php/8.1/apache2/php.ini
    ```

    file {title="/etc/php/8.1/apache2/php.ini"}
    max_execution_time = 360
    memory_limit = 512M
    post_max_size = 200M
    upload_max_filesize = 200M
    date.timezone = Europe/London
    opcache.enable=1
    opcache.memory_consumption=128
    opcache.interned_strings_buffer=8
    opcache.max_accelerated_files=10000
    opcache.revalidate_freq=1
    opcache.save_comments=1


Activate additional Apache modules with the following command:

    ```command
    sudo a2enmod dir env headers mime rewrite ssl
    ```

Restart Apache server using the following command:

    ```command
    sudo systemctl restart apache2
    ```

Check the status of the Apache server to ensure it is still `active`. If the server is in a failed state, inspect the server error logs and make any required modifications to the `/etc/apache2/sites-enabled/nextcloud.conf` file.

    ```command
    sudo systemctl status apache2
    ```

## Setting Up Nextcloud Using the Web Interface

To complete the rest of the Nextcloud configuration, you can use the web interface. Follow these steps to configure and activate Nextcloud:

Open your web browser and navigate to your server's domain. The Nextcloud configuration page will be displayed. For instance, if your domain is `example.com`, enter http://example.com/ in the address bar.

On this page, perform the following tasks:

    - Create an administrative account by providing a username and password.
    - Keep the address for the **Data Folder** as it is.
    - In the `Configure the database` section, input details about the `nextcloud` database. Enter the username and password for the account created in MariaDB earlier. The database name should be `nextcloud`, and leave the final field set to `localhost`.
    - Click on `Install` to submit the form and complete the installation.

Nextcloud will proceed to set up the application, which may take a minute or two. On the following page, Nextcloud will prompt you to install a set of recommended applications. Click on **Install recommended apps** to proceed.

Nextcloud will then display a series of welcome slides. Navigate through these slides by clicking the right arrow symbol on the right-hand side of the page. Take the time to read through each slide, noting any important information.

On the last welcome page, choose **Start using Nextcloud** to access the Nextcloud dashboard.

You'll now see the Nextcloud Dashboard page in your browser.

## Securing and Optimizing Your Nextcloud Application

Nextcloud is now operational, but it's not yet as secure or efficient as it could be. For an enhanced Nextcloud experience, consider enabling the *Hypertext Transfer Protocol Secure* (HTTPS) protocol using the [Certbot](https://certbot.eff.org/). Additionally, there are a few more modifications to make in the `config.php` file.

### Setting up a SSL Certificate for Nextcloud (Optional)

Nextcloud functions without the *Hypertext Transfer Protocol Secure (HTTPS) protocol*, but it's highly recommended to use it. Nextcloud might display warnings on the "Settings" page if HTTPS is not configured. HTTPS encrypts data using the *Secure Sockets Layer* (SSL) technology, enhancing the security of Nextcloud data.

To enable HTTPS for your domain, your Utho server needs a signed public-key certificate from a trusted certificate authority. Most Ubuntu administrators utilize Certbot to install SSL certificates. Certbot is a free and open-source tool that automates the process of requesting [Let's Encrypt](https://letsencrypt.org/) for a website.

Follow these steps to configure HTTPS for your domain:

1. Update Snap, which is pre-installed on Ubuntu 22.04. Snap is used to download application bundles.

    ```command
    sudo snap install core && sudo snap refresh core
    ```

1. To prevent conflicts, uninstall the default Certbot package provided by Ubuntu:

    ```command
    sudo apt remove certbot
    ```

1. Install Certbot using `snap` with the following command:

    ```command
    sudo snap install --classic certbot
    ```

    ```output
    certbot 1.31.0 from Certbot Project (certbot-eff✓) installed
    ```

1. Utilize Certbot to obtain a certificate for your domain by running the following command:

    ```command
    sudo certbot --apache
    ```

1. Certbot will initiate the installation process. During this process, you'll be prompted to provide the following information. The prompts may vary based on whether you've used Certbot before. When prompted, input the following:

- A contact email for the domain owner.
- Agree to the terms of service by entering `Y`.
- Decide whether to share the email address with the Electronic Frontier Foundation.
- Specify the domain name to be registered. You may need to enter the domain both with and without the `www` prefix, or select it from a list.

Once the certificate is granted, Certbot will display information about the process and the certificate. Take note of the location of the newly-deployed certificate, as additional configuration will be added to this file in the next section.

    ```output
    Deploying certificate
    Successfully deployed certificate for example.com to /etc/apache2/sites-available/nextcloud-le-ssl.conf
    Congratulations! You have successfully enabled HTTPS on https://example.com
    ```

1. **(Optional)** Certbot offers automatic renewal and updating of the certificate. To conduct a trial run, utilize the `renew` command:

    ```command
    sudo certbot renew --dry-run
    ```

### Configuring Additional Security Measures for Nextcloud


At this stage, most of the configuration is finished. However, there are still some additional changes required for SSL configuration and the Nextcloud `config.php` file. The `config.php` file was generated during the initial Nextcloud setup via the web interface. To implement these additional security measures, follow these steps:

1. Adjust the permissions for the Nextcloud-specific `config.php` file to prevent access by other users. This file can be found inside the `config` directory within the Nextcloud domain directory.

    ```command
    sudo chmod 660 /var/www/html/nextcloud/config/config.php
    ```

1. Change the ownership of this file, ensuring that both the `root` account and the Apache web server share ownership:

    ```command
    sudo chown root:www-data /var/www/html/nextcloud/config/config.php
    ```

1. Open the Nextcloud `config.php` file and add the following two lines to the end of the array. Ensure these lines are inserted inside the final ) bracket. For the `default_phone_region`, use the country code where the server is located. You can find a complete list of country codes on the Wikipedia page for [ISO Alpha-2 codes](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). Here's an example using the country code for the United Kingdom:

    ```command
    sudo nano /var/www/html/nextcloud/config/config.php
    ```

        file {title="/var/www/html/nextcloud/config/config.php" lang="php"}
    ...
           'memcache.local' => '\\OC\\Memcache\\APCu',
           'default_phone_region' => 'GB',
            );


1. Edit the SSL certificate file and enable strict transport security. You recorded the name of this file when installing the certificate. Add the following line immediately after the line containing the `ServerName` attribute.

    ```command
    sudo nano /etc/apache2/sites-available/nextcloud-le-ssl.conf
    ```

       file {title="/etc/apache2/sites-available/nextcloud-le-ssl.conf"}
       ...
       Header always set Strict-Transport-Security "max-age=15552000; includeSubDomains"
       ...

Restart Apache to implement the recent changes:

    ```command
    sudo systemctl restart apache2
    ```

Refresh the Nextcloud dashboard. The site now utilizes HTTPS, and you'll notice that the URL in the browser bar begins with `https://`.

To check for any warnings in the Administration section of the Nextcloud Dashboard, start by clicking on the user ID icon located in the upper-right corner of the dashboard. Then, select **Settings** from the dropdown menu.

In the Dashboard's Settings section, navigate to **Overview** in the left-hand menu. You'll find it just below the **Administration** section heading.

Nextcloud currently shows the `Security & Setup Warnings`page. Take a moment to go through the details listed here and make sure there are no security or configuration warnings. Certain features that may be missing, like the email server, can be configured at a later time when it's convenient for you.

## Concluding Thoughts about Nextcloud on Ubuntu 22.04

Nextcloud is a powerful open-source alternative to Dropbox and Google Drive, offering shared online access to files, folders, and various content. You can download and set up Nextcloud on Ubuntu 22.04 using `wget` and configure it via a user-friendly web interface.

The Nextcloud dashboard provides users with tools to manage the site, as well as add or share files seamlessly. To run Nextcloud, you'll need a LAMP stack, which involves configuring the Apache web server, MySQL/MariaDB RDBMS, and PHP programming language.

It's advisable to install an SSL certificate to enable HTTPS for enhanced security. For detailed instructions on installation, configuration, and usage of Nextcloud, refer to the [Nextcloud User Documentation](https://docs.nextcloud.com/server/24/admin_manual/contents.html).
