---
slug: "Unleash Your Social Side: Easy Mastodon Setup for Ubuntu 20.04"
description: 'Discover the step-by-step process to set up Mastodon, the decentralized Twitter alternative in the Fediverse, on your Ubuntu 20.04 system. Join the decentralized social media revolution today'
keywords: ['mastodon', 'micro blog', 'microblogging', 'fediverse', 'twitter alternative', 'ubuntu 20.04']
tags: ['ubuntu', 'docker']
published: 2024-04-11
modified: 2024-04-11
modified_by:
  name: Utho
title: "Deploy Your Own Mastodon Server on Ubuntu 20.04"
title_meta: "How to Install a Mastodon Server on Ubuntu 20.04"
authors: ["Pawan Kumar"]
relations:
    platform:
        key: install-mastodon
        keywords:
           - distribution: Ubuntu 20.04
---
    {{< youtube "IPSbNdBmWKE" >}}

[*Mastodon*](https://docs.joinmastodon.org/) is a decentralized micro-blogging platform, similar to Twitter but with a twist. It allows users to follow others, share text, photos, and videos, and build communities based on open web standards. Unlike Twitter, Mastodon isn't controlled by a single entity; instead, it operates through multiple independent instances.

What makes Mastodon unique is its federated approach to social networking. Each instance functions autonomously, letting anyone create their community. However, users from different instances can still connect, share content, and communicate seamlessly.

Mastodon is part of the [Fediverse](https://en.wikipedia.org/wiki/Fediverse), a network of social platforms and websites that use the [*ActivityPub*](https://en.wikipedia.org/wiki/ActivityPub) protocol for communication. This allows Mastodon to interact with other platforms in the Fediverse, expanding its reach.

From small private servers to large public ones like [Mastodon.social](https://mastodon.social/about), which hosts over 540,000 users, Mastodon instances cater to various interests and principles. Each instance typically has its focus, and users can choose where they feel most at home.

## Before You Begin

1. If you haven't already, start by setting up a Utho account and creating a Compute Instance.

1. Follow our guide on Setting Up and Securing a Compute Instance. This will walk you through updating your system and additional steps like setting the timezone, configuring your hostname, creating a limited user account, and strengthening SSH access.

1. Finish the steps outlined in the Add DNS Records section to register a domain name and direct it to your Mastodon instance.

1. Get your SMTP server ready for Mastodon so it can send email notifications to users. These notifications include when users sign up, gain followers, receive messages, and for other Mastodon actions.

- You have the option to set up your SMTP server, and you can even host it on the same machine as your Mastodon server. Just follow the steps outlined in the [Configure an Email Server with Postfix, Dovecot, and MySQL on Debian, and Ubuntu](/docs/guides/email-with-postfix-dovecot-and-mysql/) guide.

{{< note >}}
In this guide, we utilize the PostgreSQL database as the backend for Mastodon. However, you have the flexibility to configure the SMTP server to work with the PostgreSQL database instead of MySQL.
{{< /note >}}

- If you prefer, you can opt for a third-party SMTP service. This guide offers instructions specifically for using [Mailgun](https://www.mailgun.com/) as your SMTP provider.

1. Throughout this guide, make sure to substitute all instances of `example.com` with the actual domain name you've chosen for your Mastodon instance.

{{< note >}}
This guide is tailored for non-root users. Whenever a command needs elevated privileges, it's preceded by `sudo`. If you're unfamiliar with the sudo command, refer to the [Linux Users and Groups](/docs/guides/linux-users-and-groups/) guide for clarification.
{{< /note >}}

## Setting Up Docker and Docker Compose
You can install Mastodon using its built-in [Docker Compose](https://docs.docker.com/compose/) file. Docker Compose takes care of installing and running all the necessary components for the Mastodon environment within Docker containers. If you're new to Docker, it's
advisable to go through the following guides:

- [An Introduction to Docker](/docs/guides/introduction-to-docker/)
- [How to Use Docker Compose](/docs/guides/how-to-use-docker-compose/)

### Install Docker

    {{< content "installing-docker-shortguide" >}}

### Install Docker Compose

    {{< content "install-docker-compose" >}}

## Download Mastodon

1. First, clone the Mastodon Git repository into your home directory. Then, navigate into the newly created Mastodon directory and fetch the latest Mastodon release.

    ```code
    cd ~/
    git clone https://github.com/tootsuite/mastodon.git
    cd mastodon
    git checkout $(git tag -l | grep -v 'rc[0-9]*$' | sort -V | tail -n 1)
    ```
{{< note >}}
Unless specified otherwise, all Docker Compose commands mentioned are to be executed within this directory.
{{< /note >}}

## Configuring Docker Compose

1. Open the `docker-compose.yml` file located in the `mastodon` directory using your preferred text editor.

1. Comment out the `build` lines by adding # in front of each one. Then, append a release number to the end of each `image: tootsuite/mastodon` line, such as `tootsuite/mastodon:v4.0.2`.

It's advisable to choose a specific release number instead of using `latest`. You can find a [list of Mastodon releases](https://github.com/mastodon/mastodon/releases) on the Mastodon GitHub page.

1. After making these changes, your docker-compose.yml file should resemble [the example Docker file](docker-compose.yml).

1. To create a new environment configuration file, copy the `.env.production.sample` file located in the current `mastodon` directory.

    ```command
    cp .env.production.sample .env.production
    ```

1. Utilize Docker and Mastodon to generate a new value for the `SECRET_KEY_BASE` setting.

    ```command
    SECRET_KEY_BASE=$(docker compose run --rm web bundle exec rake secret)
    ```

This command generates a random string of characters. If you encounter an error in the subsequent step, simply rerun the command to generate a new string.

1. Insert the generated `SECRET_KEY_BASE` value into the `.env.production` file using the sed command.

    ```command
    sed -i -e "s/SECRET_KEY_BASE=/&${SECRET_KEY_BASE}/" .env.production
    ```

1. Combine the previous two actions into a single step to set a value for the `OTP_SECRET` setting in `.env.production`.

    ```command
    sed -i "s/OTP_SECRET=$/&$(docker compose run --rm web bundle exec rake secret)/" .env.production
    ```

1. Generate values for the `VAPID_PRIVATE_KEY` and `VAPID_PUBLIC_KEY` settings.

    ```command
    docker compose run --rm web bundle exec rake mastodon:webpush:generate_vapid_key
    ```

1. Copy the output from the previous command, then open `.env.production` in your text editor. Paste the copied command output into the corresponding lines for `VAPID_PRIVATE_KEY` and `VAPID_PUBLIC_KEY`.

1. Complete the remaining fields in the `.env.production` file as follows:

- For `LOCAL_DOMAIN`, input your Mastodon server's domain name.

- Change `DB_USER` to `postgres` and leave `DB_PASS` empty.

- Set `DB_HOST` to `mastodon-db-1` and `REDIS_HOST` to `mastodon-redis-1`. In both cases, use mastodon as the `Mastodon` base folder name.

- Provide the necessary information from your `SMTP` provider for the SMTP fields. If you've set up your `SMTP server`, use its domain name for SMTP_SERVER and include the following lines:

        ```file {title=".env.production"}
        SMTP_AUTH_METHOD=plain
        SMTP_OPENSSL_VERIFY_MODE=none
        ```

Comment out the sections labeled as "optional" by adding a `#` before each line in those sections.

1. After completing this step, your `.env.production` file should look similar to the example environment file.

## Complete the Docker Compose Setup

1. Use the command below to build the Docker Compose environment:

    ```command
    docker compose build
    ```

1. Assign ownership of the Mastodon `public` directory to user `991`. This ensures that Mastodon, which defaults to user ID 991, has the required permissions.

    ```command
    sudo chown -R 991:991 public
    ```

1. Execute Mastodon's Docker Compose setup script. You'll be prompted to input details about the Docker Compose services and your Mastodon instance.

    ```command
    docker compose run --rm web bundle exec rake mastodon:setup
    ```

- Several prompts will ask for information already provided in the `.env.production` file. Ensure consistency by entering the same details as previously entered.

- When asked to create a Mastodon administrator user account, select 'Yes' (`Y`). Then, input your desired username, password, and email address for the account.

- For any other prompts, simply press **Enter** to accept the default values.

## Starting the Docker Compose Services
1. Start the Docker Compose services.

    ```command
    docker compose up -d
    ```

1. By default, Docker Compose services start automatically when the system boots up. Use the following command to manually stop them if needed.

    ```command
    docker compose down
    ```

## Setup an HTTP/HTTPS Proxy

1. Ensure that HTTP and HTTPS connections are allowed through the system's firewall.

    ```command
    sudo ufw allow http
    sudo ufw allow https
    sudo ufw reload
    ```

1. Install NGINX, which acts as a proxy for requests to your Mastodon server.

    ```command
    sudo apt install nginx
    ```

1. Copy the `nginx.conf` file provided with the Mastodon installation to the sites-available folder in `NGINX`. Use your Mastodon domain name instead of `example.com` in the file name.

    ```command
    sudo cp ~/mastodon/dist/nginx.conf /etc/nginx/sites-available/example.com.conf
    ```

1. Open the `example.com.conf` file with your preferred text editor, and replace all instances of `example.com` with the domain name for your Mastodon site. This domain name must match the one you used to set up Docker Compose for Mastodon.

1. Create a symbolic link of this file in the `sites-enabled` folder of NGINX.

    ```command
    cd /etc/nginx/sites-enabled
    sudo ln -s ../sites-available/example.com.conf
    ```

## Get an SSL/TLS Certificate

Since Mastodon operates over HTTPS, you'll need an SSL/TLS certificate. This guide employs [Certbot](https://certbot.eff.org) to obtain and install a free certificate from [Let's Encrypt](https://letsencrypt.org).

1. Update the [Snap](https://snapcraft.io/docs/getting-started) app store. Snap offers application bundles compatible with major Linux distributions and is included by default in Ubuntu releases from 16.04 onwards.

    ```command
    sudo snap install core && sudo snap refresh core
    ```

1. Ensure that any existing Certbot installation is removed.

    ```command
    sudo apt remove certbot
    ```

1. Install Certbot.

    ```command
    sudo snap install --classic certbot
    ```

1. Next, obtain a certificate for your site using Certbot.

    ```command
    sudo certbot certonly --nginx
    ```

Certbot will present a list of NGINX sites configured on your machine. Choose the one associated with the domain name you assigned to your Mastodon instance.

Certbot sets up an automatic renewal cron job for your certificate. You can test this renewal process with the following command:

    ```command
    sudo certbot renew --dry-run
    ```

Reopen the `/etc/nginx/sites-available/example.com.conf` file. Uncomment the `ssl_certificate` and `ssl_certificate_key` lines.

Restart NGINX to apply the changes.

    ```command
    sudo systemctl restart nginx
    ```

## Using Mastodon

In your web browser, go to your Mastodon site's domain. You'll land on the Mastodon login page, where you can sign in as the admin user you created earlier or create a new user.

To access your instance's administration page, navigate to `example.com/admin/settings/edit`. Here, you can customize the appearance and functionality of your instance.

If your instance is up but encountering issues, troubleshoot them using the Sidekiq dashboard. You can access it by selecting **Sidekiq** from the administration menu or visiting `example.com/sidekiq`.

