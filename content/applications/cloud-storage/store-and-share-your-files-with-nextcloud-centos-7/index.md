---
slug: "Nextcloud on CentOS 7: Your Ultimate File Sharing Solution"
description: "Nextcloud is an open content hosting tool built for customization and security. We'll walk you through installing it on CentOS 7."
keywords: ["nextcloud", "cloud", "open source hosting"]
tags: ["centos", "lamp"]
lpublished: 2024-03-20
modified: 2024-03-20
modified_by:
  name: Utho
title: "Nextcloud on CentOS 7: Effortlessly Store and Share Your Files"
authors: ["Andrew Lescher"]
relations:
    platform:
        key: install-nextcloud
        keywords:
           - distribution: CentOS 7
---

Store and Share your Files with Nextcloud on CentOS

## Before You Begin

1.  If you haven't already, create a Utho account and set up a Compute Instance.

1.  Refer to our guide to update your system. Additionally, consider setting the timezone, configuring your hostname, creating a limited user account, and enhancing SSH access security.

1.  Install the *EPEL* repository by running the following command:

        yum install epel-release -y

## Install MariaDB Database Server

1. Add the MariaDB 10.2 repository to ensure yum installs the latest version.

       {{< file "/etc/yum.repos.d/MariaDB.repo" repo >}}
       [mariadb]
       name = MariaDB-10.2.3
       baseurl = http://yum.mariadb.org/10.2.3/centos7-amd64
       gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
       gpgcheck=1
       {{< /file >}}

2.  Install MariaDB and enable the service to start automatically on system startup with the following command:

        yum install mariadb mariadb-server -y
        systemctl start mariadb
        systemctl enable mariadb

3. Configure the MariaDB server using the `mysql_secure_installation` script. Respond to the prompts with the following replies:

        mysql_secure_installation

        Enter current password for root (enter for none): ENTER
        Set root password? [Y/n] Y
        Remove anonymous users? [Y/n] Y
        Disallow root login remotely? [Y/n] Y
        Remove test database and access to it? [Y/n] Y
        Reload privilege tables now? [Y/n] Y

4.  To set up a database and user for Nextcloud in MariaDB, log in as `root` using the password you set earlier. Make sure to create a strong password to replace the `CREATE-PASSWORD-HERE` placeholder.

        mysql -u root -p

        MariaDB [(none)]> CREATE DATABASE nextcloud;
        MariaDB [(none)]> GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextclouduser'@'localhost' IDENTIFIED BY 'CREATE-PASSWORD-HERE' WITH GRANT OPTION;
        MariaDB [(none)]> FLUSH PRIVILEGES;
        MariaDB [(none)]> quit

## Install Apache Web Server

1.  Install Apache and enable the service to start automatically on system startup using the following command:

        yum install httpd -y
        systemctl start httpd
        systemctl enable httpd

2. To prevent conflicts with Nextcloud's WebDAV modules, disable Apache's WebDAV modules.

        sudo sed -i 's/^/#&/g' /etc/httpd/conf.modules.d/00-dav.conf

3.  Restart Apache to apply the changes by running the following command:

        systemctl restart httpd

## Install PHP 7.1 and Required Modules

1.  Add the Remi repository to your system by executing the following command:

        rpm -Uvh http://rpms.remirepo.net/enterprise/remi-release-7.rpm

2.  Install the *yum-utils* package by running the following command:

        yum install yum-utils -y

3.  Update the system to populate the Remi repository by executing the following command:

        yum update -y

4.  Direct the system to use PHP 7.1 and proceed with the installation by issuing the following command:

        yum-config-manager --enable remi-php71
        yum install php71-php php-mbstring php-zip php71-php-opcache php71-php-mysql php71-php-pecl-imagick php71-php-intl php71-php-mcrypt php71-php-pdo php-ZendFramework-Db-Adapter-Pdo-Mysql php71-php-pecl-zip php71-php-mbstring php71-php-gd php71-php-xml -y

5.  By default, PHP allows a file upload size of 2MB. You can adjust this to your preferred value. Below is an example to set a 512MB file upload size with no limit for the post size:

        sudo cp /etc/php.ini /etc/php.ini.bak
        sudo sed -i "s/post_max_size = 8M/post_max_size = 0/" /etc/php.ini
        sudo sed -i "s/upload_max_filesize = 2M/upload_max_filesize = 512M/" /etc/php.ini

6.  Restart Apache:

        systemctl restart httpd

## Install Nextcloud 12

1.  Refer to the [Nextcloud download page](https://nextcloud.com/install/#instructions-server) for the latest version. Then, replace 12.0.4 in the command below with the correct version number:

        cd /opt
        sudo yum install wget
        wget https://download.nextcloud.com/server/releases/nextcloud-12.0.4.zip

2.  Unzip the package:

        sudo yum install unzip
        unzip nextcloud-x.y.z.zip

3.  Move the entire unzipped Nextcloud folder to the root web directory and grant permissions to the `apache` user for all contents by executing the following command:

        cp -r nextcloud /var/www/html

4.  Grant permissions to the Nextcloud folder and all its contents to the Apache user. First, determine which user Apache is running with the following command. Then, replace `apache:apache` in the second command with the output if it differs:

        ps -ef | egrep '(httpd|apache2|apache)' | grep -v `whoami` | grep -v root | head -n1 | groups $(awk '{print $1}')
        chown apache:apache -R /var/www/html/nextcloud

5.  Navigate to the `nextcloud` root web directory and complete the Nextcloud installation process by accessing it via a web browser.

        cd /var/www/html/nextcloud
        sudo -u apache php occ maintenance:install --database "mysql" --database-name "nextcloud"  --database-user "nextclouduser" --database-pass "yourpassword" --admin-user "admin" --admin-pass "adminpassword"

6.  If the installation is successful, you'll see the following message:

        Nextcloud was successfully installed

7.  Since these files are now accessible over the internet, it's important to enhance security by setting stronger permissions.

        find /var/www/html -type f -print0 | sudo xargs -0 chmod 0640
        find /var/www/html -type d -print0 | sudo xargs -0 chmod 0750

8.  Update the URL in the `config.php` file to match the `nextcloud` subfolder added within the document root. Ensure that the `overwrite.cli.url` and `htaccess.RewriteBase`lines are aligned with this change.

        {{< file "/var/www/html/nextcloud/config/config.php" php >}}
        . . .

        ),
        'datadirectory' => '/var/www/html/nextcloud/data',
        'overwrite.cli.url' => 'http://localhost/nextcloud',
        'htaccess.RewriteBase' => '/nextcloud',
        'dbtype' => 'mysql',
        'version' => '12.0.3.3',
        'dbname' => 'nextcloud',

        . . .
        {{< /file >}}

9. Update the `.htaccess` file with the URL changes accordingly.

        sudo -u apache php /var/www/nextcloud/occ maintenance:update:htaccess

10. Go to `your-Utho-IP-address/nextcloud` (replace your-Utho-IP-address with your `Utho's IP address`) in your web browser. You should see the Nextcloud login page, where you can log in using the admin user credentials you created earlier.

To check the status of your Nextcloud environment, use the following `occ` command:

        sudo -u apache /var/www/html/nextcloud/ php occ status

## Where to Go from Here

Once you've successfully installed Nextcloud, you may want to link it to your own domain or set up SSL encryption for secure browsing. Check out Nextcloud's guide on [Enabling SSL](https://docs.nextcloud.com/server/12/admin_manual/installation/source_installation.html#enabling-ssl) for more information.

Although this guide used Apache, you can also install Nextcloud with NGINX. Head over to the [Nextcloud NGINX Configuration](https://docs.nextcloud.com/server/12/admin_manual/installation/nginx.html) documentation for instructions.

Additionally, you can enhance your Nextcloud experience with [Nextcloud Talk](https://nextcloud.com/talk/), an add-on for secure text and video conferencing. Learn how to [Install Nextcloud Talk](/docs/guides/install-nextcloud-talk/) with our guide.
