---
slug: Installing Seafile with Nginx on Ubuntu 16.04
description: 'This tutorial demonstrates the installation process of Seafile, a free and open-source cross-platform file hosting tool equipped with server applications for Linux and Windows, on Ubuntu 16.04'
keywords: ["Seafile", " nginx", " Ubuntu 16.04", " file server", " media", " sharing"]
tags: ["ubuntu", "nginx"]
modified: 2024-03-20
modified_by:
  name: Utho
published: 2024-03-20
title: Install Seafile with NGINX on Ubuntu 16.04
authors: ["Utho"]
---


Seafile is a versatile file hosting tool designed for various platforms, including Linux and Windows. It offers graphical user clients for Android, iOS, Linux, macOS, and Windows. Key features include support for file versioning, snapshots, two-factor authentication, and WebDAV. Seafile can be integrated with NGINX or Apache for HTTPS connections.

Seafile is available in [two editions](https://www.seafile.com/en/product/private_server/): the free and open-source Community Edition, and the paid Professional Edition. While the Pro edition is free for up to 3 users, this guide focuses on using Seafile Community Edition with NGINX for serving HTTPS connections, and MySQL for backend support. Additionally, this application stack may benefit from large amounts of disk space, making our Block Storage service a suitable choice.

## Prepare Ubuntu

{{< note >}}

This tutorial is intended for a non-root user. Commands that need elevated privileges are prefixed with `sudo`. If you're unfamiliar with the `sudo` command, refer to the[Linux Users and Groups](/docs/guides/linux-users-and-groups/) guide.

{{< /note >}}

1.  Update the system:

        apt update && apt upgrade

2.  Create a standard user account with root privileges. For instance, let's name the user *sfadmin*:

        adduser sfadmin
        adduser sfadmin sudo

3.  Sign out of your Utho's root user account and log back in as *sfadmin*:

        exit
        ssh sfadmin@<your_utho's_ip>

4.  Now that you're logged into your Utho as *sfadmin*, follow our guide to strengthen SSH access.

5.  Configure UFW rules. UFW, Ubuntu's iptables controller, simplifies firewall rule setup. For additional information on UFW, refer to our guide [Configure a Firewall with UFW](/docs/guides/configure-firewall-with-ufw/). Set up rules to allow SSH and HTTP(S) access with:

        sudo ufw allow ssh
        sudo ufw allow http
        sudo ufw allow https
        sudo ufw enable

Next, verify the status of your rules and list them numerically:

        sudo ufw status numbered

        The output should be:

        Status: active

        To                         Action      From
        --                         ------      ----
        [ 1] 22                         ALLOW IN    Anywhere
        [ 2] 80                         ALLOW IN    Anywhere
        [ 3] 443                        ALLOW IN    Anywhere
        [ 4] 22 (v6)                    ALLOW IN    Anywhere (v6)
        [ 5] 80 (v6)                    ALLOW IN    Anywhere (v6)
        [ 6] 443 (v6)                   ALLOW IN    Anywhere (v6)

{{< note respectIndent=false >}}

If you prefer not to allow SSH on port 22 for both IPv4 and IPv6 through UFW, you can remove it. For instance, you can delete the rule permitting SSH over IPv6 with `sudo ufw delete 4`.

{{< /note >}}

6.  Configure the hostname for your Utho. Let's name it *seafile* as an example:
        sudo hostnamectl set-hostname seafile

7. Add the new hostname to /etc/hosts. The second line in the file should resemble this:

       {{< file "/etc/hosts"  conf >}}
       127.0.1.1    members.utho.com     seafile
       {{< /file >}}

8.  Upon initial boot, your Utho's timezone defaults to UTC. While this adjustment is optional, if you prefer to change it, utilize:

        sudo dpkg-reconfigure tzdata

## Install and Configure MySQL

1.  During installation, you'll be prompted to set a password for the root MySQL user. Ensure to install the package `mysql-server-5.7`, not `mysql-server`. This is due to an upstream issue that causes problems when starting the MySQL service if you install using the `mysql-server` package.

        sudo apt install mysql-server-5.7

2.  Run the *mysql_secure_installation* script:

        sudo mysql_secure_installation

For additional information on MySQL, refer to our guide: [Install MySQL on Ubuntu](/docs/guides/install-mysql-on-ubuntu-14-04/)

## Generating a TLS Certificate for NGINX

If you don't possess an SSL/TLS certificate yet, you can generate one. Note that this certificate will be self-signed, leading web browsers to raise concerns about an insecure connection. You should verify the SHA256 fingerprint of the certificate in the browser against that on the server, and permanently accept the exception in the browser to trust this certificate.

1.  Navigate to the directory where we'll store the certificate files and create the server's certificate along with its key:

        cd /etc/ssl
        sudo openssl genrsa -out privkey.pem 4096
        sudo openssl req -new -x509 -key privkey.pem -out cacert.pem

## Install and Configure NGINX

1.  Install NGINX from Ubuntu's repository:

        sudo apt install nginx

2.  Create the site configuration file. The only line you need to modify below is server_name. For additional HTTPS configuration options, refer to our guide on [TLS Best Practices with NGINX](/docs/guides/getting-started-with-nginx-part-4-tls-deployment-best-practices/).

        {{< file "/etc/nginx/sites-available/seafile.conf" nginx >}}
        server{
        listen 80;
        server_name example.com;
        rewrite ^ https://$http_host$request_uri? permanent;
        proxy_set_header X-Forwarded-For $remote_addr;
        }
        server {
        listen 443 ssl http2;
        ssl on;
        ssl_certificate /etc/ssl/cacert.pem;
        ssl_certificate_key /etc/ssl/privkey.pem;
        server_name example.com;

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        add_header   Strict-Transport-Security "max-age=31536000; includeSubdomains";
        add_header   X-Content-Type-Options nosniff;
        add_header   X-Frame-Options DENY;
        ssl_session_cache shared:SSL:10m;
        ssl_ciphers  "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH !RC4";
        ssl_prefer_server_ciphers   on;

        fastcgi_param   HTTPS               on;
        fastcgi_param   HTTP_SCHEME         https;

        location / {
        fastcgi_pass    127.0.0.1:8000;
        fastcgi_param   SCRIPT_FILENAME     $document_root$fastcgi_script_name;
        fastcgi_param   PATH_INFO           $fastcgi_script_name;

        fastcgi_param    SERVER_PROTOCOL        $server_protocol;
        fastcgi_param   QUERY_STRING        $query_string;
        fastcgi_param   REQUEST_METHOD      $request_method;
        fastcgi_param   CONTENT_TYPE        $content_type;
        fastcgi_param   CONTENT_LENGTH      $content_length;
        fastcgi_param    SERVER_ADDR         $server_addr;
        fastcgi_param    SERVER_PORT         $server_port;
        fastcgi_param    SERVER_NAME         $server_name;
        fastcgi_param   REMOTE_ADDR         $remote_addr;

        access_log      /var/log/nginx/seahub.access.log;
        error_log       /var/log/nginx/seahub.error.log;
        fastcgi_read_timeout 36000;
        client_max_body_size 0;
        }

        location /seafhttp {
        rewrite ^/seafhttp(.*)$ $1 break;
        proxy_pass http://127.0.0.1:8082;
        client_max_body_size 0;
        proxy_connect_timeout  36000s;
        proxy_read_timeout  36000s;
        proxy_send_timeout  36000s;
        send_timeout  36000s;
        proxy_request_buffering off;
        }

        location /media {
        root /home/sfadmin/sfroot/seafile-server-latest/seahub;
        }
        }
        {< /file >}}

3.  Deactivate the default site configuration and activate the one you just created:

        sudo rm /etc/nginx/sites-enabled/default
        sudo ln -s /etc/nginx/sites-available/seafile.conf /etc/nginx/sites-enabled/seafile.conf

4.  Perform the NGINX configuration test and restart the web server. If the test fails, it will provide a brief description of the issue to help you troubleshoot.

        sudo nginx -t
        sudo systemctl restart nginx

## Configure and Install Seafile

1.  The [Seafile manual](https://manual.seafile.com/deploy/using_mysql.html) suggests using a specific directory structure to simplify upgrades. We'll follow a similar approach here, but instead of using the example `haiwen` directory mentioned in the Seafile manual, we'll create a directory named `sfroot` in the `sfadmin` home folder.

        mkdir ~/sfroot && cd ~/sfroot

2.  Download the Seafile CE 64-bit Linux server. You'll need to obtain the exact link from [seafile.com](https://www.seafile.com/en/download/). Once you have the URL, utilize wget to download it to *~/sfadmin/sfroot*.

        wget <link>

3.  Extract the tarball and relocate it upon completion:

        tar -xzvf seafile-server*.tar.gz
        mkdir installed && mv seafile-server*.tar.gz installed

4.  Install the necessary dependency packages for Seafile:

        sudo apt install python2.7 libpython2.7 python-setuptools python-imaging python-ldap python-mysqldb python-memcache python-urllib3

5.  Execute the installation script:

        cd seafile-server-* && ./setup-seafile-mysql.sh

During the installation process, you'll be prompted to respond to several questions and select settings. For those with recommended defaults, opt for those settings.

6.  Start the server.

        ./seafile.sh start
        ./seahub.sh start-fastcgi

The `seahub.sh` script will establish an admin user account used for logging into Seafile. You'll be prompted to provide a login email and create a password.

7. Now, Seafile should be accessible via a web browser using either your Utho's IP address or the `server_name` you designated earlier in NGINX's `seafile.conf` file. Nginx will automatically redirect to HTTPS. As mentioned previously, your browser will alert you about an insecure HTTPS connection due to the self-signed certificate you generated. After confirming to proceed to the site, you'll encounter the Seafile login page.

## Configuring Seafile to Start Automatically on Server Bootup

The `seafile.sh` and `seahub.sh` scripts won't run automatically if your Utho reboots.

1.  Generate the systemd unit files:

        {{< file "/etc/systemd/system/seafile.service" >}}
        [Unit]
        Description=Seafile Server
        After=network.target mysql.service

        [Service]
        Type=oneshot
        ExecStart=/home/sfadmin/sfroot/seafile-server-latest/seafile.sh start
        ExecStop=/home/sfadmin/sfroot/seafile-server-latest/seafile.sh stop
        RemainAfterExit=yes
        User=sfadmin
        Group=sfadmin

        [Install]
        WantedBy=multi-user.target
        {{< /file >}}



          {{< file "/etc/systemd/system/seahub.service" >}}
          [Unit]
          Description=Seafile Hub
          After=network.target seafile.service

          [Service]
          Type=oneshot
          ExecStart=/home/sfadmin/sfroot/seafile-server-latest/seahub.sh start-fastcgi
          ExecStop=/home/sfadmin/sfroot/seafile-server-latest/seahub.sh stop
          RemainAfterExit=yes
          User=sfadmin
          Group=sfadmin

          [Install]
          WantedBy=multi-user.target
          {{< /file >}}


2.  Then enable the services:

        sudo systemctl enable seafile
        sudo systemctl enable seahub

    You can confirm their successful startup with:

        sudo systemctl status seafile
        sudo systemctl status seahub

3.  Ensure the startup scripts are functional by rebooting your Utho. Upon booting up, both the Seafile and Seahub services should be active when inspecting their status with the previous commands. Additionally, you should still be able to access Seafile via a web browser.

## Updating Seafile

There are different methods to update Seafile, depending on whether you're upgrading between milestones (e.g., version 5 to 6) or between point releases (e.g., 5.1.0 to 5.1.1). Refer to the [Seafile Manual](https://manual.seafile.com/deploy/upgrade.html) for upgrade instructions that align with your requirements.
