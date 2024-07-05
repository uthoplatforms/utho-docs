---
slug: "Unveiling Hubzilla: Your Ultimate Guide to Installation and Configuration"
description: Dive into the realm of Hubzilla, the federated and decentralized application, with this comprehensive guide. Learn how to effortlessly install and configure Hubzilla to embark on your decentralized journey
og_description: 'This guide provides an introduction to the distributed and decentralized Hubzilla application and explains how to install and configure it.'
keywords: ['Hubzilla','install Hubzilla','configure Hubzilla','Hubzilla federated']
published: 2024-05-06
modified_by:
  name: Utho
title: "Install and Configure Hubzilla"
title_meta: "How to Install and Configure Hubzilla"
authors: ["Pawan Kumar"]
---
Recent advancements have reignited interest in federated web applications, with much focus on the Twitter alternative, *Mastodon*. However, the landscape is rich with other noteworthy options, including the federated Hubzilla application. [*Hubzilla*](https://hubzilla.org//page/hubzilla/hubzilla-project) empowers users to establish interconnected websites and channels. This guide serves as an introduction to Hubzilla, detailing its installation and configuration.

## Exploring the Federated Web

The *federated web*, also known as the *Fediverse* or "federated universe," comprises independent servers capable of communicating and interacting with one another. This ecosystem hosts various web content, spanning from websites and social networks to blogs. Key federated web applications include Hubzilla, Friendica, Mastodon, and Pleroma.

Communication protocols built on open standards facilitate seamless interaction across software boundaries. Users can utilize a single account or identity to access content across different servers within the federation. Access control lists play a crucial role in governing distribution and content creation rights.

## Understanding Hubzilla
Hubzilla stands out as a highly adaptable, open-source application, enabling users to construct interconnected websites and tools in a modular fashion. Its features encompass websites, social media functionality, file and photo sharing, forums, chat rooms, and calendars.

At the core of Hubzilla's decentralized services lies the ZOT web framework, facilitating secure communications, content distribution, permissions, and user identities across the federation. Leveraging *ZOT*, Hubzilla channels utilize the *Open Web Auth (OWA)* protocol for seamless identification and authentication, without the need for Domain Name System (DNS) dependency. While OWA bears similarities to OpenID, it boasts enhanced efficiency and versatility. Moreover, Hubzilla extends support for OpenID, allowing users to log in to off-grid sites.

Hubzilla's operational requirements include standard web server technologies like the Linux LAMP stack or its equivalent, comprising the Apache web server, MySQL or MariaDB relational database management system (RDBMS), and the PHP programming language.

Hubzilla employs specific terminology to elucidate the relationship between servers and application users:

-   **Hub**: An individual server running Hubzilla, capable of hosting users and web content. Hubzilla allows for the creation of personal hubs by anyone interested.

-   **Grid**: A decentralized network comprising interconnected hubs. This network facilitates communication between hubs and their users through the ZOT protocol and Hubzilla's messaging system. While hubs typically connect to the wider grid, they can also function as standalone systems.

-   **Channel**:  A distinct entity within the Hubzilla grid, representing either an application or a user. Channels can manifest as web pages, blogs, forums, or individual user profiles. Users engage with the Hubzilla grid via their personalized channel, known as the me channel. Each channel possesses its own stream and can establish connections with other channels. For instance, users can subscribe to forums, while blog updates can be directed to a designated web page. Channels are uniquely identified by their tag `channelname@hub.domain` and are cryptographically secured with their authentication rights.

Hubzilla adheres to the federated principle of *nomadic identity*, wherein a channel or account isn't bound to a specific server and lacks traditional server-based account constraints. Channel authentication occurs autonomously across the grid, enabling users to seamlessly transition their identity from one hub to another, along with their data and connections. Additionally, Hubzilla facilitates channel cloning across multiple hubs, bolstering resilience against external threats like power outages and censorship.

Hubzilla incorporates a sophisticated access control mechanism, offering granular grid-wide user permissions. The platform's content channels are identity-aware, enabling a seamless *single sign-on (SSO)* experience across various channels—a departure from the independent user authentication typical of traditional websites. Each channel's content includes access control permissions, enabling users to access tailored channel feeds, with the ability to set permissions for users on disparate hubs.

Hubzilla is a comprehensive and versatile application offering an array of features, including:

- Diverse plug-ins facilitating the creation of channel content and web applications. The Comanche description language serves as a toolkit for building web pages, while users can personalize their pages with customizable interfaces, themes, and widgets.
- Built-in social networking capabilities, including chat rooms, with federation support connecting to Diaspora, GNU Social, Mastodon, and other platforms. Hubzilla also functions as a client application for Twitter, WordPress, and more, facilitating the distribution of new updates.
- Cloud file storage functionality with sharing and publishing options governed by Hubzilla's access control mechanism.
- Wiki page creation for each channel.
- An "affinity slider" enabling users to regulate their proximity to connections or channels, thus curating their content feed. Only channels within the affinity slider's range are displayed.
- Connection filtering based on user-defined criteria.
- Single sign-on functionality across the grid.
- Access-controlled photo album feature.

A robust calendar feature facilitating event sharing and coordination, supporting popular calendar formats.

A flexible API for third-party usage, allowing configuration and access to hubs, as well as content creation.

However, using Hubzilla does come with some drawbacks:

- Certain features and tools may not be immediately intuitive.
- Documentation on Hubzilla is fragmented, disorganized, and incomplete.
- Hubzilla lacks a robust mobile implementation.
- It boasts a smaller user base compared to Mastodon or other federated applications.

For further insights into Hubzilla and its features, refer to the [Hubzilla Documentation](https://hubzilla.org/help/en/about/about#Features).

## Before You Begin

- If you have not already done so, create a Utho account and Compute Instance.

- Proceed with our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide to update your system. Additionally, consider configuring your timezone, hostname, creating a limited user account, and enhancing SSH access for added security.

- Allocate a domain name for your Hubzilla hub and direct it to your server's IP address.

- Enable email functionality on your Utho server to facilitate Hubzilla's transmission of registration emails containing verification codes. A functional mail server is essential for authenticating new users. For detailed instructions on setting up a mail server, peruse [guides on email](/docs/guides/email).

Note: This guide is crafted for a non-root user. Commands necessitating elevated privileges are prefixed with `sudo`. If you're unfamiliar with the sudo command, refer to the [Users and Groups](/docs/guides/linux-users-and-groups/) guide for assistance.

## How to Install Hubzilla

Before downloading and installing Hubzilla, Linux users must install and configure some form of LAMP stack. A virtual host for the Hubzilla site is also mandatory. The following instructions are designed for Ubuntu 22.04 LTS, but are generally applicable to most Linux distributions.

### Install the LAMP Stack

Hubzilla relies on a LAMP stack comprising the Apache web server, the MariaDB database, and the PHP programming language. While MariaDB is the preferred choice, MySQL can be used as a substitute. Although other substitutions are feasible and may enhance performance, Hubzilla does not offer explicit support for them.

Begin by ensuring that Ubuntu packages are up to date. If prompted, reboot the system.


    ```command
    sudo apt-get update -y && sudo apt-get upgrade -y
    ```

- Proceed to install the Apache web server and the MariaDB relational database management system (RDBMS).

    ```command
    sudo apt install apache2 mariadb-server -y
    ```

- Install PHP, inclusive of all necessary PHP libraries, along with supplementary utilities.

Note:  Hubzilla mandates PHP release 8.0 or higher. The ensuing command installs the current PHP release 8.1. To verify the PHP release in use, execute the command `php -v`.

    ```command
    sudo apt install openssh-server git php php-fpm php-curl php-gd php-mbstring php-xml php-mysql php-zip php-json php-cli imagemagick fail2ban wget libapache2-mod-fcgid -y
    ```

Utilize the `systemctl` command to verify that Apache and MariaDB are operational and configured to initiate automatically upon system boot.

    ```command
    sudo systemctl start apache2
    sudo systemctl enable apache2
    sudo systemctl start mysql
    sudo systemctl enable mysql
    ```

Confirm Apache's status by executing the `systemctl status` command.

    ```command
    sudo systemctl status apache2
    ```

    ```output
    apache2.service - The Apache HTTP Server
        Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
        Active: active (running) since Wed 2022-11-16 13:38:25 UTC; 10min ago
    ```

Adjust the firewall settings to permit Apache connections, ensuring that SSH connections remain unrestricted.

Note: If you plan to employ SSL to safeguard the Hubzilla installation, activate the `Apache Full` profile. Alternatively, enable the `Apache` profile. Notably, Hubzilla prioritizes testing the well-known HTTPS port, hence its accessibility should be restricted unless HTTPS is utilized. Utilizing HTTPS is strongly encouraged for heightened security. While this guide does not delve into HTTPS configuration for brevity, the fundamental Hubzilla installation process remains unchanged in both scenarios.



    ```command
    sudo ufw allow OpenSSH
    sudo ufw allow in "Apache"
    sudo ufw enable
    ```

- Verify that the firewall is enabled.


    ```command
    sudo ufw status
    ```

    ```output
    Status: active

    To                         Action      From
    --                         ------      ----
    OpenSSH                    ALLOW       Anywhere
    Apache                     ALLOW       Anywhere
    OpenSSH (v6)               ALLOW       Anywhere (v6)
    Apache (v6)                ALLOW       Anywhere (v6)
    ```

- Access the server's IP address or domain name through a web browser. The page should display the `Apache2 Default` Page.

### Configure the LAMP Stack

Strengthen the security of your MariaDB installation by running the `mysql_secure_installation` utility. Respond to the prompts with the following guidelines:

- If a database password for the root account exists, input it when prompted with Enter current password for root (enter for none):. If there is no password set, simply press the Enter key.

- When prompted with Switch to unix_socket authentication and Change the root password?, respond with n.

- For `Remove anonymous users?`, `Disallow root login remotely?`, `Remove test database and access to it?` and `Reload privilege tables now?`, enter `Y`.

        ```command
        sudo mysql_secure_installation
        ```

- Access MariaDB and provide the root password if prompted. Proceed to create a hubzilla database for the application.

    ```command
    sudo mysql
    ```

Create a `hubzilla` database to be utilized by the application.

    ```command
    CREATE DATABASE hubzilla;
    ```

Generate a user for the newly established database, substituting `mypassword` with a unique password.

    ```command
    CREATE USER 'hubzilla'@'localhost' IDENTIFIED BY 'mypassword';
    ```

Allocate all privileges for the `hubzilla` database to the newly created user. Afterward, flush the privileges and exit.

    ```command
    GRANT ALL PRIVILEGES ON hubzilla.* TO 'hubzilla'@'localhost';
    FLUSH PRIVILEGES;
    EXIT;
    ```

Hubzilla mandates the `mpm_event` module. To activate it, commence by stopping Apache and disabling `php-8.1` and `mpm_prefork`.

Note: Disable the `PHP` module associated with the current PHP release. The module name follows the format `php-releasenumber`, where the release number represents the major and minor PHP release versions. Utilize the command php -v to identify this release information.

    ```command
    sudo systemctl stop apache2
    sudo a2dismod php8.1
    sudo a2dismod mpm_prefork
    ```

- Enable the PHP `rewrite` and `mpm_event` modules.

    ```command
    sudo a2enmod rewrite
    sudo a2enmod mpm_event
    ```

- Establish a connection between Apache and the `fpm` mechanism. Enable the specified modules and configurations.

    ```command
    sudo a2enconf php8.1-fpm
    sudo a2enmod proxy
    sudo a2enmod proxy_fcgi
    ```

- Restart Apache and confirm its `active` status. In the event of a restart failure, execute the `sudo apachectl configtest` command to diagnose and rectify any encountered errors.

    ```command
    sudo systemctl restart apache2
    sudo systemctl status apache2
    ```

### Configure A Hubzilla Virtual Host

Hubzilla necessitates its dedicated virtual host for seamless operation. To create and enable the `hubzilla.conf` file within `etc/apache2/sites-available`, proceed with the following steps:

- Navigate to the `/etc/apache2/sites-available/` directory and create the new `hubzilla.conf` file.

    ```command
    cd /etc/apache2/sites-available
    sudo nano hubzilla.conf
    ```

Populate the file with the provided information. Replace `example.com` with the appropriate Hubzilla domain name in both the `ServerAdmin` and `ServerName` fields. Additionally, substitute the PHP release number of the local PHP instance for `8.1` in the `ProxyPassMatch` variable, followed by php8.1-fpm. Save the changes and close the file.


    ```file {title="/etc/apache2/sites-available/hubzilla.conf" lang="aconf"}
    <VirtualHost *:80>
        ServerAdmin webmaster@example.com
        ServerName example.com
        DocumentRoot /var/www/html/hubzilla
        ProxyPassMatch ^/(.*\.php(/.*)?)$ unix:/run/php/php8.1-fpm.sock|fcgi://localhost/var/www/html/hubzilla
        <Directory /var/www/html/>
            Options Indexes FollowSymLinks
            AllowOverride All
            Order allow,deny
            allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/hubzilla_error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/hubzilla_access.log combined
    </VirtualHost>
    ```

- Enable the newly created site.

    ```command
    sudo a2ensite hubzilla
    ```

- **Optional** To bolster security measures, disable the default Apache site.

    ```command
    sudo a2dissite 000-default.conf
    ```

- Restart Apache and verify its operational status.

    ```command
    sudo systemctl restart apache2
    sudo systemctl status apache2
    ```

## Installing and Configuring Hubzilla

The LAMP stack is now fully configured and prepared for Hubzilla installation. Utilize `git` to download Hubzilla and install the necessary add-ons. Additionally, adjust permissions to ensure Hubzilla functions correctly and set up a cron job for regular maintenance tasks.

- Navigate to the `/var/www/html` directory.

    ```command
    cd /var/www/html
    ````

- Clone the latest release of Hubzilla from the code base using `git`.

    ```command
    sudo git clone https://framagit.org/hubzilla/core.git hubzilla
    ```

- Enter the `hubzilla` directory and proceed with the add-ons installation.

    ```command
    cd hubzilla
    sudo util/add_addon_repo https://framagit.org/hubzilla/addons addons-official
    ```

- Create the `store` directory and ensure it has writable permissions.

    ```command
    sudo mkdir -p "store/[data]/smarty3"
    ```

- Modify ownership and permissions for the `hubzilla` directory as required.

    ```command
    sudo chown -R www-data:www-data /var/www/html/hubzilla/
    sudo chmod -R 755 /var/www/html/hubzilla/
    ```

- **Optional** For accessing Hubzilla via HTTPS, a recommended practice by Hubzilla, install certbot and employ it to request and install a Let's Encrypt certificate.

- Integrate a `cron` task to update the site every 10 minutes. Access the list of `root` cron jobs by executing the `crontab -e` command.

    ```command
    sudo crontab -e
    ```

- Append the following line to the end of the cron file. The `usr/bin/php` denotes the path to the PHP installation. Before proceeding, validate the PHP location by running the which `php` command.

    ```command
    */10 * * * *    cd /var/www/html/hubzilla; /usr/bin/php Zotlabs/Daemon/Master.php Cron > /dev/null 2>&1
    ```

## Configuring Hubzilla

To initialize Hubzilla and prepare it for deployment, follow these steps to configure it via the simple web interface:

Open a web browser and navigate to the domain name of the Hubzilla server. Ensure to use the https prefix if TLS is enabled; otherwise, use `http`.

The web server presents the `Hubzilla Server - Setup` page. Verify that all options are selected and click **Next** to proceed.

On the `Database Connection` page, input the login name, password, and database name for the MariaDB database. Both the login name and database name should be set as hubzilla. For the password, enter the password associated with the hubzilla user. Click Submit to proceed.

Hubzilla will then prompt you to the `Site settings` page. Here, provide an email address for the site administrator and select the default time zone. Ensure that the `Website URL` matches the domain name of the Hubzilla server. Click Submit to proceed.

Note:  The administration email must be a valid email address. Hubzilla sends a verification email to this address to confirm the registration.

Upon successful installation confirmation by Hubzilla, a link to the `registration page` is provided to register as a new member. Registration is necessary before utilizing Hubzilla's functionalities. Click the provided link to register immediately. Alternatively, registration can be completed later by visiting the domain name of the Hubzilla server.

On the `Registration` page, enter an account name, a short nickname representing the channel on the site, a valid email address, and a password. If registering as an administrator, use the administrator email provided earlier in the Hubzilla site settings. Agree to the terms of service by selecting the checkbox and click Register

Note: After selecting `Register`, Hubzilla sends an email to the provided email address for account verification. Ensure that email functionality is enabled on the Utho to complete this process.

For non-production Hubzilla testing, the mail server requirements can be removed by editing a Hubzilla configuration file. Open the `.htconfig.php` file located at `/var/www/html/hubzilla` and change the 1 in the specified line.

    ```file {title="/var/www/html/hubzilla/.htconfig.php"}
    App::$config['system']['verify_email'] = 1;
    ```

    To instead be a `0`:

    ```file {title="/var/www/html/hubzilla/.htconfig.php"}
    App::$config['system']['verify_email'] = 0;
    ```

    This configuration change is not recommended on production installations.
    {{< /note >}}

After registration, Hubzilla sends an email containing a verification token to the provided account address. Enter the registration token on the subsequent page to finalize the registration process.

For detailed instructions on utilizing Hubzilla's features, refer to the [Hubzilla user guide](https://hubzilla.org/help/en/member/member_guide#Overview).

## Conclusion
Hubzilla is a federated website that enables users to create interconnected web pages, blogs, social media feeds, and other interactive content. Each Hubzilla hub is interconnected with other hubs to form a grid. Channels serve as the central entities in Hubzilla, representing either users or web entities. Utilizing a nomadic identity model, Hubzilla enables users to access different hubs through an independent account. Moreover, a robust access control system governs the creation and viewing of channel content.

To begin using Hubzilla, start by installing and configuring a LAMP stack, which includes Apache, MariaDB, and PHP. Proceed by installing Hubzilla using git, followed by configuring it and creating an administrator account via Hubzilla's online interface. For further insights into [Hubzilla website](https://hubzilla.org//page/hubzilla/hubzilla-project), visit the Hubzilla website.
