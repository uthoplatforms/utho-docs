---
slug: "Unleash Your Data: OwnCloud Setup on CentOS Stream 8"
description: "Unlock the power of your data with ownCloud, a secure and free Dropbox alternative. Follow this guide to effortlessly install it on CentOS Stream 8"
keywords: ['owncloud on Centos']
published: 2024-03-21
modified: 2024-03-21
modified_by:
  name: Utho
title: ""Mastering Data Freedom: Setting Up ownCloud on CentOS Stream 8
title_meta: ""Unlocking Data Freedom: Step-by-Step Guide to Installing and Configuring ownCloud on CentOS Stream 8
tags: ["centos"]
relations:
    platform:
        key: how-to-install-owncloud
        keywords:
            - distribution: CentOS Stream 8
authors: ["Pawan Kumar"]
---

## Unlocking the Power of Data: Exploring the Potential of ownCloud

Discover the Power of [ownCloud](https://owncloud.com/): Your Private Cloud Solution for Data Synchronization, Storage, and Sharing. Experience Secure Collaboration for Your Projects and Teams, Ideal for Replacing Commercial Services like Dropbox or Box.

Here are some standout features of ownCloud:

- **Versioning**: Easily revert to previous file versions with file history.
- **Encryption**: Secure data transmission between client and server.
- **Drag and drop upload**: Effortlessly upload files by dragging them from your desktop file manager to your ownCloud instance.
- **Theming**: Personalize the appearance of your ownCloud instance to match your preferences.
- **Viewing ODF files**: Seamlessly view Open Document Format files like `.odt` documents and `.ods` spreadsheets.
- **Expansion via installable applications**: Access a wide range of official and third-party applications through the [ownCloud Marketplace](https://marketplace.owncloud.com/).
- Stay connected to your ownCloud server on the go with dedicated **mobile apps for** [Android](https://play.google.com/store/apps/details?id=com.owncloud.android) and [iOS](https://apps.apple.com/us/app/owncloud-file-sync-and-share/id1359583808)** syncing, uploading, downloading, and file viewing.

Why Host Your Own Cloud? Here are some common reasons:

- Secure Storage: Safeguard sensitive data without relying on third-party commercial services.
- Home Office Privacy: Establish a private cloud exclusively for household use, ideal for remote work setups.
- In-House Business Management: Maintain full control over company data by keeping it within your organization.
- Scalable Storage Solution: Scale your storage capacity as needed to accommodate growing data requirements.

In this guide, you'll learn how to install ownCloud on [CentOS Stream 8](https://www.centos.org/centos-stream/). The installation process involves a few simple steps: [install the LAMP (Linux Apache MySQL/MariaDB PHP) stack](/docs/guides/how-to-install-a-lamp-stack-on-centos-8/) (Linux Apache MySQL/MariaDB PHP) stack, creating a database and user, configuring Apache, and finally, setting up ownCloud using its graphical user interface.

{{< note >}}

For hassle-free installation of ownCloud on a Compute Instance, you can easily deploy ownCloud Server through the Utho Marketplace

{{< /note >}}

## Before You Begin

1. If you're new to Utho, start by creating a Utho account and setting up a Compute Instance. Check out our Getting Started with Utho and Creating a Compute Instance guides for detailed instructions.

1. Once your Compute Instance is up and running, follow our Setting Up and Securing a Compute Instance guide to ensure your system is updated and secured. You may want to adjust the timezone, configure your hostname, create a limited user account, and enhance SSH access for added security.

{{< note >}}

If you have a registered domain name that you want to point to your ownCloud instance, you can use the Utho DNS Manager to direct the domain to your Utho server. If you don't have a registered domain, you can simply replace "example.com" with the IP address of your Utho server when following the steps in the Create an Apache Configuration File section.

{{< /note >}}

## Install ownCloud
### Install the LAMP Stack
#### Install the Apache Web Server

ownCloud needs a complete LAMP (Linux, Apache, MySQL, PHP) stack to function properly. Here, you'll go through the process of installing a LAMP stack on your Utho. While Apache isn't the only option for a web server, it's strongly recommended by ownCloud developers over alternatives like NGINX and lightHTTP.

1. Install the Apache web server using the following command:

    ```command
    sudo dnf install httpd httpd-tools -y
    ```

1. After the installation is complete, enable and start Apache using the following commands:

    ```command
    sudo systemctl start httpd
    sudo systemctl enable httpd
    ```

1. Configure FirewallD to allow HTTP and HTTPS connections by executing the following commands:

    ```command
    sudo firewall-cmd --permanent --zone=public --add-service=http
    sudo firewall-cmd --permanent --zone=public --add-service=https
    sudo firewall-cmd --reload
    ```

1. Ensure you can reach the Apache server. Open a web browser, and enter in your Utho's IP address For example, enter in `http://192.0.2.0` and replace the IP address with your own. You should see the Apache welcome page.


Make sure you can access the Apache server. Open a web browser and type your Utho's IP address into the address bar. You should see the Apache welcome page if everything is set up correctly.

#### Install MariaDB

1. Install MariaDB:

    ```command
    sudo dnf install mariadb-server mariadb -y
    ```

1. Starting and Enabling the Database Service

    ```command
    sudo systemctl start mariadb
    sudo systemctl enable mariadb
    ```

1. Set a MariaDB Admin Password and Secure the Installation

    ```command
    sudo mysql_secure_installation
    ```

You'll be prompted to secure your MariaDB installation by changing the root password, removing anonymous user accounts, disabling root logins outside of localhost, and removing test databases. It's advisable to answer `yes` to these prompts for enhanced security. You can find more details about this script in the [MariaDB Knowledge Base](https://mariadb.com/kb/en/mariadb/mysql_secure_installation/).

#### Install PHP

Up to this point, you've successfully installed the Apache web server and MariaDB. Now, let's proceed with installing PHP 7.4.

1. Begin by adding the EPEL repository to your system to access the latest version of PHP:

    ```command
    sudo dnf install epel-release -y
    ```

1. CentOS Stream comes with PHP 7.2 installed by default, but to run ownCloud, you need PHP 7.4 or higher. Follow these steps to reset the PHP modules:

    ```command
    sudo dnf module reset php
    ```

1. Enable PHP 7.4:

    ```command
    sudo dnf module enable php:7.4
    ```

1. Install PHP along with all the necessary PHP modules by executing the following command:

    ```command
    sudo dnf install php php-opcache php-gd php-curl php-mysqlnd php-intl php-json php-ldap php-mbstring php-mysqlnd php-xml php-zip -y
    ```

1. Restart PHP-FPM and enable it to ensure the changes take effect:

    ```command
    sudo systemctl start php-fpm
    sudo systemctl enable php-fpm
    ```

1. Enable SELinux to permit Apache to execute PHP code through PHP-FPM:

    ```command
    sudo setsebool -P httpd_execmem 1
    ```

#### Create the ownCloud Database

Now that you've installed the prerequisites, let's create the ownCloud database and user. We'll be using commands within the MariaDB console for this.

1. Access the MariaDB console by typing the following command:

    ```command
    sudo mysql -u root -p
    ```

1. Create the ownCloud database by executing the following command:

    ```command
    CREATE DATABASE ownclouddb;
    ```

1. Create a new user with the necessary privileges, ensuring to use a strong and unique password. Replace PASSWORD with your chosen `PASSWORD`:

    ```command
    GRANT ALL ON ownclouddb.* TO 'ownclouduser'@'localhost' IDENTIFIED BY 'PASSWORD';
    ```

1. Flush the privileges of your database:

    ```command
    FLUSH PRIVILEGES;
    ```

1. Exit the database console by typing:

    ```command
    exit
    ```

### Download ownCloud

Before proceeding to download ownCloud, ensure you visit the [ownCloud downloads page](https://owncloud.com/download-server/) to verify the latest version available.

1. Install the wget utility by executing the following command:

    ```command
    sudo yum install wget
    ```

1. Download the latest version of ownCloud, which, at the time of writing this guide, is 10.11, using the following command:

    ```command
    wget https://download.owncloud.com/server/stable/owncloud-complete-latest.tar.bz2

    ```

1. Extract the downloaded ownCloud file using the following command:

    ```command
    tar -xjf owncloud-complete-latest.tar.bz2
    ```

1. After extracting the file, you'll notice a new directory named `owncloud`. Move this directory to the Apache document `root`. In this example, we'll use the default directory for Apache site files:

    ```command
    sudo mv owncloud /var/www/html/
    ```

1. Change the ownership of the `owncloud` directory to ensure that it's accessible by the Apache web server:

    ```command
    sudo chown -R apache: /var/www/html/owncloud
    ```

### Configure Apache for ownCloud

To serve your ownCloud instance through Apache, you need to create a [virtual host configuration file](https://httpd.apache.org/docs/2.4/vhosts/examples.html).

1. Create the Apache configuration file using the Nano text editor:

    ```command
    sudo nano /etc/httpd/conf.d/owncloud.conf
    ```

1. Paste the following text into the new file, ensuring to replace any occurrence of example.com with your actual domain name or your Utho's IP Address:

       file {title="etc/httpd/conf.d/owncloud.conf"}
       Alias /owncloud "/var/www/html/owncloud/"
       <Directory /var/www/html/owncloud/>
       Options +FollowSymlinks
       AllowOverride All

       <IfModule mod_dav.c>
        Dav off
       </IfModule>

       SetEnv HOME /var/www/html/owncloud
       SetEnv HTTP_HOME /var/www/html/owncloud

       </Directory>


1. Save and close the file by typing <kbd>Ctrl</kbd> + <kbd>O</kbd> and then <kbd>Ctrl</kbd> + <kbd>X</kbd>.

1. Restart the Apache server to apply the recent changes:

    ```command
    sudo systemctl restart httpd
    ```

1. Ensure that the Apache web server has write permissions for the ownCloud directory:

    ```command
    sudo setsebool -P httpd_unified 1
    ```

### Configure ownCloud

This section guides you through the web-based installation process:

1. Open a web browser and visit your site's domain. If you've configured a domain like http://example.com/owncloud, navigate there. Alternatively, if Apache points to your server's IP address, visit http://192.0.2.0/owncloud, replacing the example IP address with your own. This should display the ownCloud web-based installer.

{{< note title="Troubleshooting SELinux">}}

If you face permissions errors while accessing your ownCloud instance in the web browser, it indicates that SELinux needs to be configured. You can do this using the following commands:

```command
su
semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/owncloud/data(/.*)?'
semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/owncloud/config(/.*)?'
semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/owncloud/apps(/.*)?'
semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/owncloud/apps-external(/.*)?'
semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/owncloud/.htaccess'
semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/owncloud/.user.ini'
restorecon -Rv '/var/www/html/owncloud/'
exit
```

If you're still encountering issues, you can temporarily disable SELinux.

```command
sudo setenforce 0
```

Refer to the [A Beginner's Guide to SELinux on CentOS 8](/docs/guides/a-beginners-guide-to-selinux-on-centos-8/) guide to learn more about SELinux.
{{< /note >}}

- After accessing the web-based installer, create a username and password for the admin user.
- Click on the `Storage & Database` drop-down menu, then select `MySQL/MariaDB`.
- Enter the following details in the database information section:

Now you can input the necessary database information:

- Database User: `ownclouduser`
- Database password: Use the password you set for the ownCloud database user
- Database: `ownclouddb`
- Localhost:  Keep it as the default

Click on **Finish setup**. Once the installation is complete, the ownCloud login page will appear. Use the admin credentials you just created to log in. After logging in, you'll be directed to the main ownCloud page.

Congratulations! Your ownCloud instance is now up and running smoothly on CentOS 8 Stream.
