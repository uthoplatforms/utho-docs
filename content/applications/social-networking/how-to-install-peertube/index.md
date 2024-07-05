---
slug: "Unveiling PeerTube: Your Comprehensive Installation Guide"
description: "This comprehensive guide walks you through the process of downloading, installing, and configuring PeerTube, the federated video sharing application"
keywords: ['what is PeerTube','configure PeerTube','install PeerTube','PeerTube configuration']
published: 2024-05-06
modified_by:
  name: Utho
title: "How to Install PeerTube"
authors: ["Pawan Kumar"]
---
There's growing interest in federated web applications as alternatives to mainstream social media platforms. [PeerTube](https://joinpeertube.org/), a distributed open-source platform similar to YouTube, offers users a space to share and enjoy videos without corporate influence. This guide walks you through downloading, installing, and configuring your own PeerTube instance on a Utho server.

## What is a Federated Web Application?
PeerTube is part of the federated web, or Fediverse, where independent servers interact in a decentralized network. Mastodon is a well-known example of such an application. Each server operates autonomously, allowing users to join public servers and access content across multiple servers. The Fediverse hosts a variety of content, including social networks, websites, videos, and more.

## What is PeerTube?
PeerTube, developed by a French non-profit, is a federated platform for decentralized video sharing. It's offered as a free and open-source application, empowering administrators to establish their own video platforms. PeerTube distinguishes itself by abstaining from recommendation algorithms or content manipulation, prioritizing user autonomy. While PeerTube instances can interconnect to create broader networks, each owner maintains independent operation, management, and moderation of their instance. The open-source nature of PeerTube enables users to propose source code modifications or innovate new features independently.

Here are some key features and benefits of PeerTube:
- Content Creation Tools: Upload, import, and manage videos with customizable metadata, subtitles, and privacy settings. Creators retain full copyright control.
- Live Streaming: Broadcast live videos directly on the platform.
Federation: Connect with other PeerTube servers and engage with content across the network.
- ActivityPub Integration: Seamlessly interact with Mastodon and other federated platforms.
- Bandwidth Management: Handle traffic spikes efficiently for popular videos.
- P2P Functionality: Enable peer-to-peer sharing, with the option to disable on a per-user basis.
- Customization: Personalize the user interface and themes to suit your preferences.
- NSFW Policies: Implement appropriate content guidelines.
- Playlist Feature: Create, share, and interact with playlists.
- Subscription System: Stay updated with your favorite channels.
- Advanced Search: Find specific content with ease.

For further details, consult the [PeerTube FAQ](https://joinpeertube.org/faq).

## Before You Begin

Set Up Your Utho Account and Compute Instance

Secure Your Compute Instance: Follow our guide on Setting Up and Securing a Compute Instance to update your system, configure timezone and hostname, create a limited user account, and enhance SSH access.

Configure Your Domain Name: Set up and configure a domain name to point to your Utho. This domain will provide access to your PeerTube instance. Check out the Utho DNS Manager guide for detailed instructions on managing DNS records.

Note: This guide assumes you're using a non-root user. Commands requiring elevated privileges are prefixed with `sudo`. If you're unfamiliar with the sudo command, refer to the [Users and Groups](/docs/guides/linux-users-and-groups/) guide.

## How to Install PeerTube

PeerTube must be used with the NGINX web server. It does not support Apache. PeerTube recommends PostgreSQL for the database. It is possible to use another database system, but this guide only provides instructions for PostgreSQL.

PeerTube is compatible with the NGINX web server and PostgreSQL database. This guide focuses on Ubuntu 22.04 LTS, but the instructions can be adapted for other Linux distributions. For a complete list of dependencies and instructions for other systems, consult the [PeerTube dependencies list](https://docs.joinpeertube.org/dependencies).

The PeerTube installation process is divided into several sections:

- Install Prerequisites: Install necessary packages.
Set Up Directories and Database: Create directories and configure PostgreSQL.
- Download and Install PeerTube: Get the PeerTube code and install it.
- Configure PeerTube: Customize PeerTube settings.
- Set Up Virtual Host and HTTPS: Configure NGINX virtual host file and enable HTTPS.
- Optimize and Activate PeerTube: Tune PeerTube settings and activate the PeerTube system service.

### How to Install the PeerTube Prerequisites

Here's how to install NGINX, PostgreSQL, and other necessary software utilities for PeerTube:

Make sure all packages are up-to-date and install essential utilities. Some packages may already be present on your system.

    ```command
    sudo apt-get update -y && sudo apt-get upgrade -y
    sudo apt-get install build-essential gnupg curl unzip
    ```

Use `curl` to download `Node.js` and install it.

    ```command
    sudo curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
    sudo apt-get install nodejs -y
    ```

Use `npm` to install the `yarn` package manager.

    ```command
    sudo npm install -g yarn
    ```

Ensure Python is installed (typically included in Ubuntu) and install additional Python development packages. Also, alias the python command to `python3` using the `python-is-python3` package.

    ```command
    sudo apt install python3-dev python-is-python3
    ```

Install NGINX on your system.

    ```command
    sudo apt install nginx
    ```

Ensure the web server is running.

    ```command
    sudo systemctl status nginx
    ```

    ```output
    nginx.service - A high performance web server and a reverse proxy server
        Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
        Active: active (running) since Tue 2022-12-06 14:35:39 UTC; 15min ago
    ```

Verify that the NGINX web server is running. You might need to exit the output by pressing the **Q** key.

Open access to the web server through the firewall and activate the firewall. Allow both HTTP and HTTPS traffic by granting permission for the `Nginx Full` profile. Verify the firewall configuration using `ufw status`.

    ```command
    sudo ufw allow OpenSSH
    sudo ufw allow in "Nginx Full"
    sudo ufw enable
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

Visit your domain URL to confirm that the web server is functioning correctly. You should see the NGINX landing page displaying the message "Welcome to nginx".

Install extra prerequisites, including the PostgreSQL database and Redis.

    ```command
    sudo apt install git ffmpeg postgresql postgresql-contrib g++ redis-server -y
    ```

Start the `redis` and `postgresql` services and configure them to launch automatically upon system reboot.

    ```command
    sudo systemctl start redis postgresql
    sudo systemctl enable --now nginx postgresql redis-server
    ```

### How to Create the Required Directories and Database

For the next section of the installation, create a dedicated PeerTube user and establish a PostgreSQL database for the application.

Generate a new user named `peertube` on your Utho and allocate a home directory for their use.

    ```command
    sudo useradd -m -d /var/www/peertube -s /bin/bash -p peertube peertube
    ```

Assign a secure, unique password for the `peertube` user.

    ```command
    sudo passwd peertube
    ```

Confirm that the directory permissions for the new user directory are properly set. All users should have read and execute permissions, while only the user should have write permissions. The permission string should be `drwxr-xr-x`.

Note:  If execute and read permissions are lacking for all users, add them using `sudo chmod +rx /var/www/peertube`.

    ```command
    ls -ld /var/www/peertube
    ```

    ```output
    drwxr-xr-x 2 peertube peertube 4096 Dec  7 11:07 /var/www/peertube
    ```

Switch to the new directory and create a PostgreSQL user. Provide a password for the new `peertube` user when prompted.

    ```command
    cd /var/www/peertube
    sudo -u postgres createuser -P peertube
    ```

Establish a PostgreSQL database for PeerTube's use.

    ```command
    sudo -u postgres createdb -O peertube -E UTF8 -T template0 peertube_prod
    ```

Enable two PostgreSQL extensions.

    ```command
    sudo -u postgres psql -c "CREATE EXTENSION pg_trgm;" peertube_prod
    sudo -u postgres psql -c "CREATE EXTENSION unaccent;" peertube_prod
    ```

    ```output
    CREATE EXTENSION
    CREATE EXTENSION
    ```

### How to Download and Install PeerTube

After all prerequisites are configured, use `wget` to download PeerTube and `yarn` to install it.

1.  Query the PeerTube API for details about the current release of PeerTube.

Note: The result of the query is assigned to the temporary `VERSION` variable. You must complete this section and install PeerTube before closing the terminal.

    ```command
    VERSION=$(curl -s https://api.github.com/repos/chocobozzz/peertube/releases/latest | grep tag_name | cut -d '"' -f 4) && echo "Latest PeerTube version is $VERSION"
    ```

    ```output
    Latest PeerTube version is v5.0.0
    ```

2.  Change to the `peertube` directory. As the `peertube` user, create and modify some directories.

    ```command
    cd /var/www/peertube
    sudo -u peertube mkdir config storage versions
    sudo -u peertube chmod 750 config/
    ```

3.  Change to the `versions` directory and download the PeerTube `.zip` file.

    ```command
    cd versions
    sudo -u peertube wget -q "https://github.com/Chocobozzz/PeerTube/releases/download/${VERSION}/peertube-${VERSION}.zip"
    ```

4.  Unzip the downloaded file and delete the `.zip` file.

    ```command
    sudo -u peertube unzip -q peertube-${VERSION}.zip && sudo -u peertube rm peertube-${VERSION}.zip
    ```

5.  Return to the `peertube` directory and create a symbolic link to the file.

    ```command
    cd /var/www/peertube
    sudo -u peertube ln -s versions/peertube-${VERSION} ./peertube-latest
    ```

6.  Change to the `peertube-latest` directory. Use `yarn` to install PeerTube.

    ```command
    cd ./peertube-latest
    sudo -H -u peertube yarn install --production --pure-lockfile
    ```

### How to Configure PeerTube

PeerTube uses two `.yaml` files to control its internal configuration. To configure these files, follow these steps.

Switch to the peertube directory.

    ```command
    cd /var/www/peertube
    ```

Duplicate the `default.yaml` configuration into the `config` directory. Refrain from modifying this file directly.

    ```command
    sudo -u peertube cp peertube-latest/config/default.yaml config/default.yaml
    ```

From the same directory, duplicate `production.yaml` into a new location.

    ```command
    sudo -u peertube cp peertube-latest/config/production.yaml.example config/production.yaml
    ```

Generate a secrets key and retain it for the subsequent step.

    ```command
    openssl rand -hex 32
    ```

Edit the `production.yaml` file.

    ```command
    sudo -u peertube nano config/production.yaml
    ```

`production.yaml` encompasses various settings, many of which can be altered later or adjusted via the web interface. Initially, modify the following parameters.

- In the `webserver` segment, modify `hostname` to match the domain name, which serves as the URL for the PeerTube instance. For instance, replace `example.com` with your actual domain name.
- Within the secrets section, append the secret key generated in the prior step to the peertube entry.
- In the `database section`, update the password field to correspond to the password for the peertube database user.
- Within the `admin` segment, adjust the `email` attribute to reflect the administrative email account.

Here's an illustrative example of the three sections:

Note: Before PeerTube can send emails, you need to provide the mail server details under the `smtp` section. Ensure that email functionality is enabled on your Utho server.


    ```file {title="/var/www/peertube/config/production.yaml" lang="aconf"}
    ...
    webserver:
      https: true
      hostname: 'example.com'
      port: 443
    ...
    secrets:
      # Generate one using `openssl rand -hex 32`
      peertube: 'your_secret_key'
    ...
    database:
      hostname: 'localhost'
      port: 5432
      ssl: false
      suffix: '_prod'
      username: 'peertube'
      password: 'your_password'
      pool:
        max: 5
    ...
    admin:
      email: 'admin@example.com'
    ...
    ```

### How to Configure the PeerTube Virtual Host File and Enable HTTPS

To ensure NGINX serves PeerTube files correctly, you need to set up a virtual host. Here's how:

Copy the provided peertube sample virtual host file to `/etc/nginx/sites-available`. Since most of the file is pre-filled, only minor adjustments are needed.

Utilize [Certbot](https://certbot.eff.org/ to set up a Let's Encrypt certificate and activate HTTPS support.

To configure NGINX to serve PeerTube properly, you need to set up a virtual host. Follow these step.

Copy the sample NGINX configuration file for PeerTube to the `sites-available` directory.

    ```command
    sudo cp /var/www/peertube/peertube-latest/support/nginx/peertube /etc/nginx/sites-available/peertube
    ```

Substituting the `WEBSERVER_HOST` and `PEERTUBE_HOST` variables with the domain name and the local port for PeerTube, execute the following commands. Replace `example.com` with your actual domain name in the first command.

    ```command
    sudo sed -i 's/${WEBSERVER_HOST}/example.com/g' /etc/nginx/sites-available/peertube
    sudo sed -i 's/${PEERTUBE_HOST}/127.0.0.1:9000/g' /etc/nginx/sites-available/peertube
    ```

Activate the `peertube` site.

    ```command
    sudo ln -s /etc/nginx/sites-available/peertube /etc/nginx/sites-enabled/peertube
    ```

Install the `snap` package manager, which is required for Certbot installation.

    ```command
    sudo apt install snapd
    sudo snap install core
    ```

Remove any previous `certbot` packages and proceed to install the latest version using `snap`. Establish a symbolic link to the directory.
    ```command
    sudo apt remove certbot
    sudo snap install --classic certbot
    sudo ln -s /snap/bin/certbot /usr/bin/certbot
    ```

Temporarily halt NGINX and utilize the `certbot` command to request and install a Let's Encrypt certificate. As the HTTPS configuration is already embedded within the `peertube` virtual host file, employ the `certonly` option to avoid overwriting existing content. Utilize the `--standalone` option to instruct certbot to utilize its internal webserver. Following certificate installation, reload NGINX.

During the certificate granting process, `certbot` requests certain information. To complete the certification, adhere to the following instructions:

- Provide an email address for the administrator when prompted. Let's Encrypt sends renewal notices and other relevant information to this address.
- Respond with `Y` to agree to the Terms of Service. If you enter `N`, Certbot terminates the request.
- When asked whether to register with the Electronic Frontier Foundation, enter either `Y` or `N`. This choice does not affect the installation process.
- Enter the domain name for the PeerTube server.

Upon completion, `certbot` confirms the certificate installation and schedules a task to renew the certificate before it expires.

Note: Since the virtual host file is pre-filled, NGINX must be stopped before running certbot. Otherwise, the certbot request will fail.

    ```command
    sudo systemctl stop nginx
    sudo certbot certonly --standalone --post-hook "systemctl restart nginx"
    sudo systemctl reload nginx
    ```

    ```output
    Successfully received certificate.
    Certificate is saved at: /etc/letsencrypt/live/example.com/fullchain.pem
    Key is saved at:         /etc/letsencrypt/live/example.com/privkey.pem
    This certificate expires on 2023-03-07.
    These files will be updated when the certificate renews.
    Certbot has set up a scheduled task to automatically renew this certificate in the background.
    ```

### How to Tune PeerTube and Activate the PeerTube Service

A few more steps are required to prepare PeerTube for use. PeerTube requires specific Transfer Control Protocol (TCP) settings compared to most applications. Additionally, PeerTube must be registered as a system service to allow users to control it using `systemctl`. To complete the PeerTube configuration, follow these steps:

Copy the PeerTube TCP file to the system settings.

    ```command
    sudo cp /var/www/peertube/peertube-latest/support/sysctl.d/30-peertube-tcp.conf /etc/sysctl.d/
    ```

Activate the PeerTube TCP settings.

    ```command
    sudo sysctl -p /etc/sysctl.d/30-peertube-tcp.conf
    ```

    ```output
    net.core.default_qdisc = fq_codel
    net.ipv4.tcp_congestion_control = bbr
    ```

Enable the PeerTube service. Duplicate the default service file into the /etc/systemd/system/ directory.

Note: For most instances, the default configuration should be adequate. To verify the service settings, examine the `/etc/systemd/system/peertube.service` file.

    ```command
    sudo cp /var/www/peertube/peertube-latest/support/systemd/peertube.service /etc/systemd/system/
    ```

Reload the `systemctl` daemon to apply the changes.

    ```command
    sudo systemctl daemon-reload
    ```

Configure the `peertube` service to start automatically upon system boot.

    ```command
    sudo systemctl enable peertube
    ```

Initiate the `peertube` service.

    ```command
    sudo systemctl start peertube
    ```

Verify the status of the `peertube` service and ensure it's `active`.

Note: If the service isn't running, inspect the service logs using the command `sudo journalctl -feu peertube`.

    ```command
    sudo systemctl status peertube
    ```

    ```output
    peertube.service - PeerTube daemon
        Loaded: loaded (/etc/systemd/system/peertube.service; enabled; vendor preset: enabled)
        Active: active (running) since Thu 2022-12-08 10:14:28 UTC; 17s ago
    ```

You may need to press the **Q** key to exit the output.

The default administrative account for PeerTube is named `root`. While the password can be retrieved from the logs defined in the `production.yaml` file, it's simpler to reset the password. Navigate to the `peertube-latest` directory and employ the following command to establish a unique, secure password for the PeerTube `root` account. Enter the new password when prompted.

    ```command
    cd /var/www/peertube/peertube-latest
    sudo -u peertube NODE_CONFIG_DIR=/var/www/peertube/config NODE_ENV=production npm run reset-password -- -u root
    ```

    ```output
    New password?
    User password updated.
    ```

## How to Use and Administer PeerTube

Although the various PeerTube configuration files can be edited to change the site behavior, most changes can be made through the administration panel. To access the panel, follow these steps.

1.  Navigate to the PeerTube domain. The web browser displays the default welcome page for PeerTube. Click the **Login** button on the upper-left-hand side to access the login page.

2.  To log in as an administrator, use `root` as the user name. Enter the administrator password configured in the previous section. Click the **Login** bar to continue.

3.  PeerTube now displays the main user and administration page. From here, the administrator can post or watch videos and manage the PeerTube instance. For more information on how to use and administer PeerTube, see the [PeerTube documentation](https://docs.joinpeertube.org/).

## Conclusion
PeerTube offers a federated platform for sharing and watching videos across distributed servers. It provides a plethora of features for both creators and viewers. To install PeerTube, begin by setting up NGINX, PostgreSQL, and other necessary packages. Then, download and install PeerTube using `wget` and `yarn`. Complete the installation by configuring NGINX virtual hosts, PeerTube .yaml files, and TCP settings. For additional information about PeerTube, visit the [PeerTube website](https://joinpeertube.org/).
