---
slug: "Unleash Your Creativity: Setting Up Pixelfed in a Snap"
description: "Unlock the World of Visual Storytelling: A Step-by-Step Guide to Setting Up and Configuring Pixelfed, Your Decentralized Photo-Sharing Platform"
keywords: ['install Pixelfed', 'configure Pixelfed', 'How to install Pixelfed on Ubuntu']
tags: ['apache', 'nginx', 'web server']
published: 2024-05-14
modified_by:
  name: Utho
title: "How to Install and Configure Pixelfed"
title_meta: "Installing and Configuring Pixelfed"
authors: ["Pawan Kumar"]
---

[Pixelfed](https://pixelfed.org/) , a decentralized federated platform, enables users to share photos, art, and videos. This guide offers instructions on installing and setting up Pixelfed, along with comprehensive details on all necessary prerequisites.

## What is the Federated Web?

Mastodon, Pixelfed, and other applications collectively form the federated web, also recognized as the Fediverse. This network comprises independent yet interconnected servers, rendering federated applications decentralized without a central server or administrative hierarchy. Typically self-hosted, each server allows users to join any platform. Moreover, users can navigate across server boundaries to access or share content on multiple servers, making the Fediverse a popular choice for hosting various web content like websites, social networks, and blogs.

## What is Pixelfed?

Pixelfed serves as a decentralized and open-source substitute for corporate photo-sharing platforms like Instagram. Users have the option to self-host their Pixelfed account or join existing community servers. The platform is ad-free, freely accessible, and upholds privacy-conscious policies by refraining from storing or commercializing user data.

Key features of Pixelfed encompass:

- Chronological feeds devoid of algorithmic interference.
- Compatibility with the ActivityPub Protocol, facilitating interaction with Mastodon posts from Pixelfed accounts.
- Mobile compatibility.
- Album organization for photos.
- An extensive array of filters.
- Support for comments, direct messaging, and likes.
- "Stories" functionality for time-limited content sharing.
- A diverse set of privacy and safety features enabling users to manage followers manually, create follower-exclusive posts, mute/block accounts, report accounts, and disable comments.
For an exhaustive list of Pixelfed features, refer to the [Pixelfed features page](https://pixelfed.org/features)..

## Before You Begin

If you haven't already, create a Utho account and Compute Instance by referring to our Getting Started with Utho and Creating a Compute Instance guides.

Proceed with our Setting Up and Securing a Compute Instance guide to ensure your system is updated. Additionally, consider tasks like setting the timezone, configuring your hostname, creating a restricted user account, and fortifying SSH access.

Assign a domain name to your Pixelfed server and direct it to the server's IP address.

(**Optional**) If Pixelfed needs to send emails, activate email functionality on your Utho server.

Note: The steps outlined in this guide are tailored for non-root users. Commands requiring elevated privileges are prefixed with `sudo`. For those unfamiliar with the sudo command, consult the [Linux Users and Groups](/docs/guides/linux-users-and-groups/) guide.

## How to Configure the Server to Support Pixelfed

Pixelfed relies on various supporting applications, necessitating a complete LAMP or LEMP stack, along with extensive utilization of `composer` and `artisan` installation scripts.

The subsequent instructions delineate how to install Pixelfed on Ubuntu 22.04, but are broadly applicable to most Linux distributions.

Note: Given that Pixelfed is still in Beta mode, users may encounter defects. Consequently, websites demanding high availability should explore alternative options.

### Install the LEMP Stack and Other Prerequisites

Pixelfed necessitates a web server, a database, and the PHP programming language for operation. Among Linux stacks, either Apache or NGINX can serve as the web server, with this guide focusing on NGINX due to its seamless integration with Pixelfed. Although this guide highlights divergences in the Apache installation method, detailed Apache instructions are not provided. For the database, both MariaDB and MySQL are viable options, with identical configuration requirements. To set up a LEMP stack for Pixelfed, adhere to the following steps:

Begin by installing the NGINX web server.

        sudo apt install nginx

Utilize `systemctl` to confirm `NGINX's` active status.

    ```command
    sudo systemctl status nginx
    ```

    ```output
    nginx.service - A high performance web server and a reverse proxy server
        Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
        Active: active (running) since Tue 2022-11-29 13:01:56 UTC; 15s ago
    ```

Proceed to install the MariaDB database.

    ```command
    sudo apt install mariadb-server
    ```

Configure the `ufw` firewall to accommodate NGINX connections. Permit both HTTP and HTTPS connections by allowing the `Nginx Full` profile, essential since Pixelfed mandates HTTPS for uploads.

    ```command
    sudo ufw allow OpenSSH
    sudo ufw allow in "Nginx Full"
    sudo ufw enable
    ```

Note: After Pixelfed installation and HTTPS activation, contemplate adjusting the `ufw` profile to `Nginx HTTPS` for limited firewall access to HTTPS traffic exclusively.

Validate the firewall settings using the `ufw` status command.

    ```command
    sudo ufw status
    ```

    ```output
    Status: active

    To                         Action      From
    --                         ------      ----
    OpenSSH                    ALLOW       Anywhere
    Nginx Full                 ALLOW       Anywhere
    OpenSSH (v6)               ALLOW       Anywhere (v6)
    Nginx Full (v6)            ALLOW       Anywhere (v6)
    ```

Verify web access functionality by visiting the webserver's IP address. The browser should display NGINX's default welcome page, featuring the message “Welcome to nginx!”.

Note: Refer to the Utho Dashboard to determine the Utho's address.

Execute the `mysql_secure_installation` tool to bolster the database's security.

    ```command
    sudo mysql_secure_installation
    ```

Follow the prompts in the script, opting to maintain Unix socket authentication and the root password unchanged while responding affirmatively to the remaining queries.

    - `Remove anonymous users?`
    - `Disallow root login remotely?`
    - `Remove test database and access to it?`
    - `Reload privilege tables now?`

Install PHP along with all required packages.

    ```command
    sudo apt install -y php-gd php-bcmath php-ctype php-curl php-exif php-iconv php-intl php-json  php-imagick php-services-json php-mbstring php-tokenizer php-xml php-zip php-mysql php-fpm
    ```
Pixelfed necessitates additional packages, including image processing utilities. Install these, incorporating the Redis database and its PHP package, supporting utilities like `git` and composer, and image processing packages such as `jpegoptim`.

Note: Some utilities might already be pre-installed on the server.

    ```command
    sudo apt install -y php-redis ffmpeg redis git libgl-dev gcc libc6-dev libjpeg-dev  make jpegoptim optipng pngquant graphicsmagick gifsicle composer zip unzip
    ```

Note: For heightened security, especially in multi-user servers, contemplate creating a dedicated `pixelfed` user to execute Pixelfed. Remember to switch to this user during installation and configuration. Refer to the [Pixelfed Prerequisites information](https://docs.pixelfed.org/running-pixelfed/prerequisites/#creating-a-dedicated-app-user-and-using-unix-sockets-optional) for further details.

### Configure the Database and PHP

Once the LAMP/LEMP stack and mandatory packages are installed, additional configuration steps are necessary. The subsequent steps elucidate how to configure the database and virtual host to support Pixelfed:

Log in to the database as `root`.

    ```command
    sudo mysql -u root -p
    ```

Create the `pixelfed` database and user, and then flush the privileges before exiting the database. Remember to replace PASSWORD with a secure password.


    ```command
    CREATE DATABASE pixelfed;
    CREATE USER 'pixelfed'@'localhost' IDENTIFIED BY 'PASSWORD';
    GRANT ALL PRIVILEGES ON pixelfed.* TO 'pixelfed'@'localhost';
    FLUSH PRIVILEGES;
    EXIT
    ```

Update the php.ini file with revised settings for Pixelfed, typically located at `/etc/php/<php_version>/fpm/php.ini`, where php_version denotes the PHP release utilized on the server. Modify the following settings within the file:

- Adjust `post_max_size` to accommodate maximum submission sizes, considering higher-quality photos or animations.
- Ensure `file_uploads` is enabled.
- Set `upload_max_filesize` to a value <= the `post_max_size`.
- Determine the maximum number of attachments for uploads by setting `max_file_uploads`, with a default of `20`.
- Extend `max_execution_time` to 600 for increased performance.

Note: These settings are located in different sections of the file. In the `vi` editor, use the `/` key to search for the name of the setting.

    ```file {title="/etc/php/8.1/fpm/php.ini" lang="aconf"}
    max_execution_time = 600
    post_max_size = 8M
    file_uploads = On
    upload_max_filesize = 6M
    max_file_uploads = 20
    ```

### Configure a Virtual Host and HTTPS Support

The subsequent step involves creating a virtual host file for the Pixelfed domain. A virtual host allows for domain-specific configurations. Since Pixelfed necessitates HTTPS support, it can be enabled using the [Certbot](https://certbot.eff.org/) application. This guide outlines the essential steps for installing and utilizing Certbot. For comprehensive information about Certbot, Let's Encrypt certificates, and HTTPS, refer to the Utho guide to Using Certbot on NGINX.

Establish a `/var/www/html/domain_name` directory for the Pixelfed site. Replace example.com in the command with the actual domain name.

    ```command
    sudo mkdir -p /var/www/html/example.com
    ```

Duplicate the default site configuration file into the `sites-enabled` directory to ensure accurate formatting and inclusion of essential fields. Use the domain name in lieu of example.com.

    ```command
    sudo cp /etc/nginx/sites-enabled/default /etc/nginx/sites-available/example.com.conf
    ```

Edit the site's `.conf` file, removing the existing server configuration down to the line Virtual Host configuration for `example.com` and uncommenting the remaining lines. Subsequent changes must be made within the code block initiated by server. Update the `server_name` and root fields within the server block to reflect the Pixelfed domain. In the provided example, replace `example.com` with the actual domain name. Further alterations can be made after enabling HTTPS.

Note: When Pixelfed resides within the `/var/www/html/domain_name` directory, it generates a new public subdirectory. The root variable should point to this directory. For Apache, add this configuration to `/etc/apache2/sites-available/example.com.conf`. On Apache, the directive names are ServerName and DocumentRoot.


    ```file {title="/etc/nginx/sites-available/example.com.conf" lang="nginx"}
    server_name example.com www.example.com;
    root /var/www/html/example.com/public;
    ```

Create a symbolic link to the `sites-enabled` directory to activate the site.

    ```command
    sudo ln -s /etc/nginx/sites-available/example.com.conf /etc/nginx/sites-enabled/
    ```

Certbot now functions as a Snap package. To utilize Snap, install the `snapd` package and initialize it.

    ```command
    sudo apt install snapd
    sudo snap install core
    ```

Eliminate any existing certbot Ubuntu packages to prevent conflicts. Use snap to install the new certbot package, and create a symbolic link to the certbot directory.

    ```command
    sudo apt remove certbot
    sudo snap install --classic certbot
    sudo ln -s /snap/bin/certbot /usr/bin/certbot
    ```

Use `certbot` to install a certificate for the domain, appending the `--nginx` option to employ the NGINX plugin, which also simplifies virtual host file configuration.

Note: To request a certificate without modifying virtual host settings, use the `certonly` option, e.g., `sudo certbot certonly --nginx`. For Apache configuration, execute `sudo certbot --apache`.

    ```command
    sudo certbot --nginx
    ```

During installation, Certbot prompts for additional information such as email address and domain name, which can be configured as follows:

- Enter an email address: Provide an email address to receive urgent domain or registration notices.
- Accept the terms of service: Agree to the terms by entering Y. Enter N to cancel the certificate request. Review the terms by downloading the provided PDF file.
- Optional subscription to mailing list: Respond with Y or N regarding subscription to the EFF mailing list, which doesn't impact the installation process.
- Enter the domain name(s): Specify one or more domain names for the certificate. Certbot displays eligible domains based on virtual host file contents. Choose corresponding numbers for relevant domains, separating multiple names with spaces or commas. For domains not listed, input the domain name without http or https prefix (e.g., example.com). Request separate certificates for each domain with and without the www prefix.

Certbot communicates with Let’s Encrypt to obtain the certificate(s) and perform required challenges, typically through an HTTP challenge for ownership verification. If successful, Certbot confirms the granted request and displays certificate information, including expiration date (usually 90 days).

    ```output
    Requesting a certificate for example.com and www.example.com

    Successfully received certificate.
    Certificate is saved at: /etc/letsencrypt/live/example.com/fullchain.pem
    Key is saved at:         /etc/letsencrypt/live/example.com/privkey.pem
    This certificate expires on 2023-02-28.
    These files will be updated when the certificate renews.
    Certbot has set up a scheduled task to automatically renew this certificate in the background.

    Deploying certificate
    Successfully deployed certificate for example.com to /etc/nginx/sites-enabled/example.com.conf
    Successfully deployed certificate for www.example.com to /etc/nginx/sites-enabled/example.com.conf
    Congratulations! You have successfully enabled HTTPS on https://example.com and https://www.example.com
    ```

Edit the site configuration file at `/etc/nginx/sites-available/example.com.conf`, employing the sample file as a template. Implement the following changes:

- Append three `add_header` directives to the file's beginning.
- Include `index.htm` `index.php` in the index directive.
- Add `/index.php?$query_string` to the try_files directive within the location / block.
- Integrate location directives for `/favicon.ico` and `/robots.txt`.
- Incorporate `error_page`, `charset`, and `client_max_body_size` directives.
- Insert the location `~ \.php$` block, adjusting the `fastcgi_pass` directive to reflect the PHP release's filename (e.g., `php7.4-fpm.sock` for PHP release 7.4). Verify PHP release via php -v.
- Avoid modifying lines added by Certbot, starting from listen [::]:443.

Note: For Apache, append the aforementioned configuration to the /etc/apache2/sites-available/example.com.conf file, noting slight directive differences. Consult the Pixelfed Apache instructions or Apache documentation for guidance.

"
    ```file {title="/etc/nginx/sites-available/example.com.conf" lang="nginx"}
    server {
        add_header X-Frame-Options "SAMEORIGIN";
        add_header X-XSS-Protection "1; mode=block";
        add_header X-Content-Type-Options "nosniff";

        server_name example.com example.com;

        root  /var/www/html/example.com/public;
        index index.html index.htm index.php;

        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }

        location = /favicon.ico { access_log off; log_not_found off; }
        location = /robots.txt  { access_log off; log_not_found off; }

        charset utf-8;
        client_max_body_size 15M;   # or your desired limit
        error_page 404 /index.php;

        # pass PHP scripts to FastCGI server
        #
        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            # With php-fpm (or other unix sockets):
            fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        }
        listen [::]:443 ssl ipv6only=on; # managed by Certbot
        listen 443 ssl; # managed by Certbot
        ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
        ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot
        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
    }

    server {
        if ($host = www.example.com) {
            return 301 https://$host$request_uri;
        } # managed by Certbot

        if ($host = example.com) {
            return 301 https://$host$request_uri;
        } # managed by Certbot

        listen 80;
        listen [::]:80;

        server_name example.com www.example.com;
        return 404; # managed by Certbot
    }
    ```

Test the NGINX configuration, reloading NGINX if no errors are detected.

    ```command
    sudo nginx -t
    sudo systemctl reload nginx
    ```

## Install and Configure Pixelfed

### Install Pixelfed

The LEMP/LAMP stack and requisite settings are now prepared to host Pixelfed. Proceed by cloning and downloading Pixelfed using `git`.

Navigate to the root directory for the domain at `/var/www/html/example.com`, substituting example.com with the actual domain name.

    ```command
    cd /var/www/html/example.com/
    ```

Employ `git` to `clone` the Pixelfed files.

    ```command
    git clone -b dev https://github.com/pixelfed/pixelfed.git .
    ```

Recursively configure ownership and permissions for the newly created directories.

    ```command
    sudo chown -R www-data:www-data .
    sudo find . -type d -exec chmod 755 {} \;
    sudo find . -type f -exec chmod 644 {} \;
    ```

### Configure Pixelfed

Utilize the `composer` tool to initialize and configure PHP, alongside modifications to the .env file. For comprehensive guidance on Pixelfed configuration, consult the [Pixelfed Documentation](https://docs.pixelfed.org/).

From the same directory, employ `composer` to initialize PHP settings for Pixelfed.

    ```command
    sudo composer install --no-ansi --no-interaction --optimize-autoloader
    ```
Generate a new copy of the sample `.env` file.

    ```command
    sudo cp .env.example .env
    ```

Edit the .env file and update the following server variables, leaving other variables unchanged:

- APP_NAME: Display title throughout the application.
- APP_ENV: Set to debug for testing, production otherwise.
- APP_DEBUG: true for debugging, false otherwise.
- APP_URL: HTTP URL for Pixelfed domain (e.g., https://example.com).
- APP_DOMAIN: Domain name without HTTPS prefix (e.g., example.com).
- ADMIN_DOMAIN: Domain name.
- SESSION_DOMAIN: Domain name.
- DB_CONNECTION: Set to mysql for MySQL and MariaDB.
- DB_HOST: Set to 127.0.0.1.
- DB_PORT: 3306 for MySQL and MariaDB.
- DB_DATABASE: Set to pixelfed.
- DB_USERNAME: Set to pixelfed.
- DB_PASSWORD: Use the password provided during pixelfed user creation.

        ```command
        vi .env
        ```

    ```file {title="/var/www/html/example.com/.env" lang="env"}
    APP_NAME="Pixelfed Demo"
    APP_ENV=production

    APP_URL=https://example.com
    APP_DOMAIN="example.com"
    ADMIN_DOMAIN="example.com"
    SESSION_DOMAIN="example.com"
    TRUST_PROXIES="*"

    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=pixelfed
    DB_USERNAME=pixelfed
    DB_PASSWORD=<Database_Password>
    ```

(**Optional**) Enable Pixelfed to send emails by configuring MAIL_ variables in `.env` with the correct mail server settings. Refer to the [Pixelfed Email Settings example](https://docs.pixelfed.org/running-pixelfed/installation/#email-variables) example for instructions.

### Run the Pixelfed Installation Scripts

Pixelfed's setup involves executing numerous `php artisan` commands to configure various system components. Follow the steps below:


Return to the domain root directory and generate the secret key, replacing `example.com` with the domain name.

    ```command
    cd /var/www/html/example.com
    sudo php artisan key:generate
    ```

    ```output
    INFO  Application key set successfully.
    ```

Link the application storage directory.

    ```command
    sudo php artisan storage:link
    ```

    ```output
    INFO  The [public/storage] link has been connected to [storage/app/public].
    ```

Migrate the database.

    ```command
    sudo php artisan migrate --force
    ```

Import the city dataset to enable location data support.

    ```command
    sudo php artisan import:cities
    ```

    ```output
    Successfully imported 128769 entries!
    ```

Cache Pixelfed routes and views for enhanced performance.

    ```command
    php artisan route:cache
    php artisan view:cache
    ```

    ```output
    INFO  Routes cached successfully.
    INFO  Blade templates cached successfully.
    ```

Cache Pixelfed configuration.

    {{< note >}}
    Run this command each time the `.env` file changes.
    {{< /note >}}

    ```command
    sudo php artisan config:cache
    ```

    ```output
    INFO  Configuration cached successfully.
    ```
Activate the Horizon dashboard and initiate job queueing.

Note: Pixelfed supports other queueing methods, but `horizon` is the most straightforward. Consult the [Pixelfed documentation](https://docs.pixelfed.org/running-pixelfed/) for more details.

Note: While Pixelfed does offer support for alternative queueing methods, `horizon` stands out as the most user-friendly option. For further insights, refer to the Pixelfed documentation.

    ```command
    sudo php artisan horizon:install
    sudo php artisan horizon:publish
    ```

    ```output
    Horizon scaffolding installed successfully.
    INFO  Publishing [horizon-assets] assets
    ```

Add a cron job to schedule regular clean-up tasks. Open the root crontab file and append the following entry:

    ```command
    sudo crontab -e
    ```

1.  Add the following crontab entry to the bottom of the file.

    ```code
    * * * * * /usr/bin/php /usr/share/webapps/pixelfed/artisan schedule:run >> /dev/null 2>&1
    ```

### Enable the Pixelfed Service in Systemctl

Add a cron job to schedule regular clean-up tasks. Open the root `crontab` file and append the following entry:

To streamline Pixelfed's management, run it as a `systemctl` service. To integrate Pixelfed into the system daemon:

    ```command
    sudo vi /etc/systemd/system/pixelfed.service
    ```

Include the following service description, substituting `example.com` in the `ExecStart` parameter with the actual domain name.

Note: Apache users should modify `requires=nginx` to `requires=apache`.

    ```file {title="/etc/systemd/system/pixelfed.service" lang="systemd"}
    [Unit]
    Description=Pixelfed task queueing via Laravel Horizon
    After=network.target
    Requires=mariadb
    Requires=php-fpm
    Requires=redis
    Requires=nginx

    [Service]
    Type=simple
    ExecStart=/usr/bin/php /var/www/html/example.com/artisan horizon
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target
    ```

Activate the service by employing `systemctl enable`.

    ```command
    sudo systemctl enable --now pixelfed
    ```

Restart the service and confirm its active status.

    ```command
    sudo systemctl restart pixelfed.service
    sudo systemctl status  pixelfed.service
    ```

    ```output
    pixelfed.service - Pixelfed task queueing via Laravel Horizon
        Loaded: loaded (/etc/systemd/system/pixelfed.service; enabled; vendor preset: enabled)
        Active: active (running) since Thu 2022-12-01 10:24:42 UTC; 3s ago
    ```

### Create a Pixelfed Administrator and Use the Pixelfed Web Interface

To access the Pixelfed administration dashboard, start by establishing an administrative account using the `php artisan user:create` command. Additional user accounts can be generated via the Pixelfed web interface. Follow the steps below to create an administrator account:

Navigate to the root directory of the Pixelfed domain. Execute the `artisan` account creation script. In the provided example, replace `example.com` with the domain name.
    ```command
    cd /var/www/html/example.com
    sudo php artisan user:create
    ```

When prompted, furnish the following details:

- Name: Public-facing name for the account.
- Username: Internal account name.
- Email: Account email address.
- Password/Confirm Password: Secure password for the account.
- Make this user an admin? (yes/no): Respond yes to create an administrative account.
- Manually verify email address? (yes/no): Opt for no in this instance as the account creation is internal. Selecting yes requires email functionality enabled on both Pixelfed and Utho.
- Are you sure you want to create this user? (yes/no): Confirm creation by selecting yes.

    ```output
    Created new user!
    ```

Utilize a web browser to access the site domain name. The Pixelfed login page should appear. Input the administrator email address and password.

    Pixelfed will present the home page for the account.

    To enter the administration dashboard, click **Admin Dashboard** on the left-hand menu. When prompted, input the account password. The dashboard will be displayed by Pixelfed.

For further guidance on Pixelfed usage or administration, consult the Pixelfed documentation.

## Conclusion

Pixelfed stands as a federated open-source platform designed for photo, art, and video sharing. Each Pixelfed server operates independently within a distributed network, devoid of central authority. It can be downloaded via git and necessitates a LAMP or LEMP stack alongside several auxiliary applications. Installation and configuration of Pixelfed involve the use of composer and artisan scripts. Administrator accounts must be established beforehand through the console. While the Pixelfed web portal facilitates user account creation, administrators can oversee site management via the Pixelfed Dashboard. For comprehensive guidance on administering a Pixelfed server, refer to the Pixelfed documentation.
