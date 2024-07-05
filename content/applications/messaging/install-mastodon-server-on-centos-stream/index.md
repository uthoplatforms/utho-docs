---
slug: "Set Your Mastodon Journey Ablaze: Installing Mastodon on CentOS Stream"
description: "Discover the Steps to Launch Your Own Mastodon Server on CentOS Stream"
keywords: ['mastodon','micro blog','microblogging','fediverse','twitter alternative','centos stream']
tags: ['centos', 'docker']
published: 2024-05-07
modified_by:
  name: Pawan Kumar
title: "Install a Mastodon Server on CentOS Stream 8"
title_meta: "How to Install a Mastodon Server on CentOS Stream 8"
relations:
    platform:
        key: install-mastodon
        keywords:
           - distribution: CentOS Stream 8
authors: ["Pawan Kumar"]
---

{{< youtube "IPSbNdBmWKE" >}}

[*Mastodon*](https://docs.joinmastodon.org/) stands out as an open-source, self-hosted microblogging platform akin to Twitter. Users can follow others and share text, images, and videos. Yet, unlike Twitter, Mastodon is decentralized, removing the need for a central governing body.

Mastodon's uniqueness lies in its federated approach to social networking. Each instance operates independently, fostering diverse communities. Despite this independence, users from different instances can still interact, exchange content, and connect.

From small, niche communities to large public hubs, Mastodon instances cater to varied interests or values. Notably, [Mastodon.social](https://mastodon.social/about), the platform's largest instance, boasts over 540,000 users and upholds a robust [Code of Conduct](https://mastodon.social/about/more).

## Before You Begin

1. If you have not already done so, create a Utho account and Compute Instance.

Proceed with the steps outlined in the [Add DNS Records](/docs/guides/set-up-web-server-host-website/#add-dns-records) section to associate a domain name with your Mastodon instance.

Enable FirewallD to manage your machine's firewall regulations. For guidance, consult the [firewall cmd list](/docs/guides/introduction-to-firewalld-on-centos/).

Prepare an SMTP server to facilitate email notifications for various Mastodon activities, including user registration, follower notifications, and message alerts.

- You have the option to create your SMTP server, potentially hosting it on the same machine as your Mastodon server. Our [Email with Postfix, Dovecot, and MySQL](/docs/guides/email-with-postfix-dovecot-and-mysql/) guide offers detailed instructions for this setup.

Note: This guide assumes the use of a PostgreSQL database as the backend for Mastodon. Alternatively, you can configure the SMTP server with a PostgreSQL database instead of MySQL.

- Alternatively, opt for a third-party SMTP service. We provide instructions for utilizing [Mailgun](https://www.mailgun.com/) as your SMTP provider.

Substituting all instances of `example.com` mentioned in this guide with your actual domain name ensures accurate configuration for your Mastodon instance.

Note: This guide assumes the use of a non-root user. Commands necessitating elevated permissions are prefixed with `sudo`. If you're unfamiliar with the sudo command, refer to the [Users and Groups](/docs/guides/linux-users-and-groups/) guide for guidance.

## Install Docker and Docker Compose
Mastodon offers installation via its bundled [Docker Compose](https://docs.docker.com/compose/) file, which orchestrates all prerequisites within Docker containers. If you're new to Docker, it's advisable to acquaint yourself with the following guides:

- [Introduction to Docker](/docs/guides/introduction-to-docker/)

- [How to Use Docker Compose](/docs/guides/how-to-use-docker-compose/)

### Install Docker
These steps pertain to the installation of Docker Community Edition (CE). For detailed instructions, refer to the official [installation page](https://docs.docker.com/install/).

Begin by removing any existing Docker installations from your system.

        sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine

Proceed to install `yum-utils`.

        sudo yum install yum-utils

Next, add the stable Docker repository to your system.

        sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

Update the package index and install Docker CE.

        sudo yum update
        sudo yum install docker-ce docker-ce-cli containerd.io

Should you be prompted to accept the GPG key, ensure to do so only after verifying that the fingerprint matches the following:

          {{< output >}}
          060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35
          {{< /output >}}

Initiate Docker and configure it to launch automatically upon system startup.

        sudo systemctl start docker
        sudo systemctl enable docker

Add your restricted Linux user account to the `docker` group. Remember to log out and log back in for the changes to take effect after executing the following command:

        sudo usermod -aG docker $USER

Mastodon actively engages in the [*Fediverse*](https://en.wikipedia.org/wiki/Fediverse), a network of interconnected social platforms and websites utilizing the [*ActivityPub*](https://en.wikipedia.org/wiki/ActivityPub) protocol. This interoperability allows Mastodon servers to communicate with one another and facilitates communication between Mastodon and other platforms within the Fediverse.

Confirm the successful installation by executing the built-in "Hello World" program.

             docker run hello-world

### Install Docker Compose

            {{< content "install-docker-compose" >}}

## Download Mastodon

Begin by installing Git.

            sudo yum install git

Clone the Mastodon Git repository into your home directory, then navigate into the resulting Mastodon directory.

        cd ~/
        git clone https://github.com/mastodon/mastodon.git
        cd mastodon

Unless specified otherwise, all subsequent Docker Compose-related commands should be executed from within this directory.

## Configure Docker Compose

Utilize your preferred text editor to open the `docker-compose.yml` file.

Comment out the `build lines` by prefixing each with #, and append a release number to the end of each `image: tootsuite/mastodon` line, such as `tootsuite/mastodon:v4.0.2`.

While using latest as the release is an option, it's advisable to opt for a specific release number. You can find a chronological list of Mastodon releases on the Mastodon GitHub page.

The modified `docker-compose.yml` file should resemble the sample Docker file.

Duplicate the .env.production.sample file to create a new environment configuration file.

        cp .env.production.sample .env.production

Execute the following commands to generate a `SECRET_KEY_BASE` and `OTP_SECRET`. Copy the output, then paste it into the corresponding lines in the `.env.production` file.

        echo SECRET_KEY_BASE=$(docker compose run --rm web bundle exec rake secret)
        sed -i -e "s/SECRET_KEY_BASE=/&${SECRET_KEY_BASE}/" .env.production
        echo OTP_SECRET=$(docker compose run --rm web bundle exec rake secret)
        sed -i -e "s/OTP_SECRET=/&${OTP_SECRET}/" .env.production

If either of the `sed` commands fails, retry the previous command and attempt the sed command again.

Generate the `VAPID_PRIVATE_KEY` and `VAPID_PUBLIC_KEY` using the provided command. Copy the output and replace the existing `VAPID_PRIVATE_KEY` and `VAPID_PUBLIC_KEY` lines in the `.env.production` file with the copied values.


        docker compose run --rm web bundle exec rake mastodon:webpush:generate_vapid_key

Proceed to complete the remaining fields in the `.env.production` file:

- For `LOCAL_DOMAIN`, input your Mastodon server's domain name.

- Adjust `DB_USER` to `postgres` and leave `DB_PASS` blank.

- Set `DB_HOST` to `mastodon-db-1` and `REDIS_HOST` to `mastodon-redis-1`. In both cases, mastodon corresponds to the name of the Mastodon base folder.

- Populate the `SMTP` fields with your SMTP provider's details. If you've set up your `SMTP server`, use its domain name for SMTP_SERVER and append the following lines:

            SMTP_AUTH_METHOD=plain
            SMTP_OPENSSL_VERIFY_MODE=none

- To disable optional sections, add a `#` before each line within those sections.

After making the adjustments, your `.env.production` file should mirror [the example environment file](env.production).

## Complete the Docker Compose Setup

Proceed to construct the Docker Compose environment.

        docker compose build

Assign ownership of the Mastodon `public` directory to user `991`. This ensures that the default Mastodon user ID possesses the requisite permissions.

        sudo chown -R 991:991 public

Configure the firewall to permit connections from localhost to the database Docker container.

        sudo firewall-cmd --zone=public --add-masquerade --permanent
        sudo firewall-cmd --permanent --zone=public --change-interface=docker0
        sudo firewall-cmd --permanent --zone=public --add-port=5432/tcp
        sudo firewall-cmd --reload

Execute Mastodon's Docker Compose setup script. You'll be prompted to input details regarding Docker Compose services and your Mastodon instance.

        docker compose run --rm web bundle exec rake mastodon:setup

- Several prompts may repeat fields you've already completed in the `.env.production` file. Ensure consistency between the information entered here and in the file.

- When prompted to create a Mastodon administrator user account, opt to proceed (`Y`). Enter the desired username, password, and email address for the account.

- For any other prompts, accept the default values by pressing **Enter**.

## Initiate the Docker Compose Services

Initiate the Docker Compose services.

        docker compose up -d

By default, Docker Compose services start automatically at system startup unless manually halted. To manually stop the Docker Compose services, execute the following command:

        docker compose down

## Setup an HTTP/HTTPS Proxy

Allow HTTP and HTTPS connections through the system's firewall.

        sudo firewall-cmd --permanent --zone=public --add-service=http --add-service=https
        sudo firewall-cmd --reload

Install NGINX to serve as a reverse proxy for your Mastodon server.

        sudo yum install nginx

Establish `sites-available` and `sites-enabled` directories within the NGINX directory. The former stores NGINX server-block files, while the latter contains symbolic links to the server blocks to be publicly accessible.

        sudo mkdir /etc/nginx/sites-available
        sudo mkdir /etc/nginx/sites-enabled

Open the `/etc/nginx/nginx.conf` file in your preferred text editor and append the provided lines to the http section. This ensures that NGINX looks for server-block files within the sites-enabled directory. Additionally, comment out the default server section to prevent the NGINX welcome page from displaying.


        include /etc/nginx/sites-enabled/*.conf;
        server_names_hash_bucket_size 64;

Comment out the `server` section by adding a `#` to the start of each line that makes up the section. This ensures that the default NGINX welcome page does not display.

Copy the included `nginx.conf` file from the Mastodon installation to the `sites-available` NGINX folder, using your Mastodon domain name in place of `example.com` within the file name.


        sudo cp ~/mastodon/dist/nginx.conf /etc/nginx/sites-available/example.com.conf

Edit the `example.com.conf` file with your preferred text editor, substituting all occurrences of `example.com` with your Mastodon site's domain name.

Create a symbolic link to this file within the `sites-enabled` NGINX folder.

        cd /etc/nginx/sites-enabled
        sudo ln -s ../sites-available/example.com.conf

## Get an SSL/TLS Certificate

Mastodon operates over HTTPS, necessitating an SSL/TLS certificate. This guide utilizes [Certbot](https://certbot.eff.org) to obtain and install a free certificate from [Let's Encrypt](https://letsencrypt.org). Certbot can be installed via the [Snap](https://snapcraft.io/docs/getting-started) app store, which provides application bundles compatible with major Linux distributions.

Add the Extra Packages for Enterprise Linux (EPEL) repository.

        sudo dnf install epel-release
        sudo dnf upgrade

Install Snap, create the required symbolic links for Snap to function correctly, and enable Snap classic. After executing these commands, log out and log back in to ensure the changes are applied.

        sudo yum install snapd
        sudo systemctl enable --now snapd.socket
        sudo ln -s /var/lib/snapd/snap /snap

Begin by updating and refreshing Snap.

        sudo snap install core && sudo snap refresh core

Ensure any existing Certbot installation is removed.

        sudo yum remove certbot

        Proceed to install Certbot and create a symbolic link for it.

        sudo snap install --classic certbot
        sudo ln -s /snap/bin/certbot /usr/bin/certbot

Obtain a certificate for your site

        sudo certbot certonly --nginx

Certbot will prompt you to choose from the NGINX sites configured on your machine. Select the one associated with the domain name you've set up for your Mastodon instance.

Certbot comes equipped with a cron job that automatically renews your certificate before it expires. You can verify the automatic renewal with the following command:

        sudo certbot renew --dry-run

Revisit the `/etc/nginx/sites-available/example.com.conf` file and uncomment the `ssl_certificate` and `ssl_certificate_key` lines.

Restart the NGINX server.

        sudo systemctl restart nginx

Execute the following command to allow NGINX to establish network connections under SELinux:

        sudo setsebool -P httpd_can_network_connect 1

## Using Mastodon

Open your web browser and visit your Mastodon site's domain. You'll encounter the Mastodon login page, where you can log in as the administrator user you previously created or register a new user.

Access your instance's administration page by navigating to `example.com/admin/settings/edit`. This page empowers you to customize the appearance, functionality, and settings of your instance.

Should your instance encounter any issues while running, troubleshoot them through the **Sidekiq** dashboard. You can access it by selecting Sidekiq from the administration menu or visiting `example.com/sidekiq`.

Once you're prepared to introduce your instance to the broader community, add it to the list on [Instances.social](https://instances.social/admin) by completing the administrator form.
