---
slug: Install-configure-owncloud-ubuntu-16-04
description: "This guide demonstrates the secure setup of ownCloud, a file-hosting service enabling file sharing across multiple devices, on Ubuntu 16.04."
keywords: ["owncloud", "install owncloud", "cloud storage ubuntu"]
tags: ["ubuntu"]
published: 2024-03-19
modified: 2024-03-19
modified_by:
  name: Pawan Kumar
title: "Installing and Configuring ownCloud on Ubuntu 16.04"
title_meta: "Installing and Configuring ownCloud on Ubuntu 16.04"
relations:
    platform:
        key: how-to-install-owncloud
        keywords:
            - distribution: Ubuntu 16.04
authors: ["Pawan Kumar"]
---

OwnCloud is a free, cloud-based file hosting service that you can install on your Utho. It's easy to set up, comes with many features ready to use, and offers a wide range of plugins. With ownCloud, you can access your files from various operating systems, web browsers, and mobile devices.

{{< note >}}
To automatically install ownCloud on a Compute Instance, consider deploying ownCloud Server through the Utho Marketplace.
{{< /note >}}

## Before You Begin

1.  If you haven't already, create a Utho account and set up a Compute Instance.

1.  Check out our guide on Setting Up and Securing a Compute Instance to update your system. You might also want to adjust the timezone, set up your hostname, create a restricted user account, and strengthen SSH security.

3.  [Install and configure a LAMP stack](/docs/guides/install-lamp-stack-on-ubuntu-16-04/).

## Install ownCloud

Add the repository key to apt and install ownCloud using the following commands:

    sudo wget -nv https://download.owncloud.org/download/repositories/9.1/Ubuntu_16.04/Release.key -O Release.key
    sudo apt-key add - < Release.key
    sudo sh -c "echo 'deb http://download.owncloud.org/download/repositories/9.1/Ubuntu_16.04/ /' > /etc/apt/sources.list.d/owncloud.list"
    sudo apt update
    sudo apt install owncloud

## Configure MySQL

1.  Log in to your MySQL database by entering your root password:

        mysql -u root -p

2.  Create a new database for ownCloud, replacing strong_password with a new, secure password:

         {{< highlight sql >}}
         CREATE DATABASE ownCloud;
         CREATE USER ownCloud@localhost;
         SET PASSWORD FOR 'ownCloud'@'localhost' = PASSWORD('strong_password');
         {{< /highlight >}}

3.  Assign the new user to the database:

        {{< highlight sql >}}
        GRANT ALL PRIVILEGES ON ownCloud.* to ownCloud@localhost;
        FLUSH PRIVILEGES;
        exit
        {{< /highlight >}}

4.  Log into MySQL using the newly created user credentials:

        mysql -u ownCloud -p

5.  You can verify the current user in MySQL by running the `SELECT current_user();` command:

         {{< highlight sql >}}
         SELECT current_user();
         {{< /highlight >}}

This command will display something similar to:

        +--------------------+
        | current_user()     |
        +--------------------+
        | ownCloud@localhost |
        +--------------------+
        1 row in set (0.00 sec)

## Creating an Administrator Account

1. After installing ownCloud and configuring MySQL, open your web browser and go to `ip_address_or_domain/owncloud` (replace `ip_address_or_domain` with your server's IP address or domain name). Then, create an administrator account.

2.  Click on `Storage & Database` and provide the login details for the database:

    Welcome to ownCloud:

## Setting Up ClamAV and Configuring ownCloud

1.  Install [ClamAV](https://www.clamav.net/), an open-source antivirus engine compatible with ownCloud's antivirus plugin, to enhance your security measures.

        sudo apt install clamav clamav-daemon

The `clamav` package initiates a daemon on your system.

2.  Enable the antivirus feature in ownCloud. Navigate to `Apps` in the menu, then click `Not enabled` to locate the "Antivirus App for files". Finally, click `Enable` to activate it.

3.  Configure the antivirus mode in ownCloud to align with the adjustments made to your system.

4.  To add new users and groups, navigate to the `Users` option in the dropdown menu located in the upper right-hand corner.

## Secure the System
Now that ownCloud is installed and configured, it's essential to secure your system. The official documentation provides a comprehensive guide on [hardening your server](https://doc.owncloud.org/server/9.0/admin_manual/configuration_server/harden_server.html), which includes steps such as enabling HTTPS and managing JavaScript assets.
