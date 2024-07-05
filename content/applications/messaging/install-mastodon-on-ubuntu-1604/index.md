---
slug: "Ubuntu Unleashed: Easy Mastodon Installation on 16.04"
description: "Unlocking Mastodon: Your Guide to Installing and Getting Started with the Twitter Alternative"
keywords: ["mastodon", "twitter alternative", "micro blog", "fediverse"]
tags: ["ubuntu"]
modified: 2024-05-07
modified_by:
  name: Utho
published: 2024-05-07
title: "Installing a Mastodon Server on Ubuntu 16.04"
title_meta: "How to Install a Mastodon Server on Ubuntu 16.04"
relations:
    platform:
        key: install-mastodon
        keywords:
           - distribution: Ubuntu 16.04
deprecated: true
authors: ["Utho"]
---

## What is Mastodon?

[**Mastodon**](https://joinmastodon.org/)  is a social network like Twitter, but it's different in some key ways. On Mastodon, you can follow people and share messages, pictures, and videos, just like on Twitter. But unlike Twitter, Mastodon doesn't have one big company in charge of everything.

Instead, anyone can start their own Mastodon server, invite friends to join, and create their own community. Even though each server is run privately, people from different servers can still talk to each other.

{{< youtube "IPSbNdBmWKE" >}}

### The Fediverse
All these separate servers together make up what's called the [Fediverse](https://en.wikipedia.org/wiki/Fediverse). Mastodon can talk to any server that uses the [ActivityPub](https://en.wikipedia.org/wiki/ActivityPub) or [OStatus](https://en.wikipedia.org/wiki/OStatus) protocols (like [GNU Social](https://en.wikipedia.org/wiki/GNU_social))). Mastodon is just one of many services that use these protocols.

Mastodon servers are usually open for anyone to join, and they come in all sizes. Some are small, and some are really big (like [Mastodon.social](https://mastodon.social), which has over 180,000 users). These servers often focus on specific topics or ideas. Each Mastodon server can make its own rules about what's allowed, just like [Code of Conduct](https://mastodon.social/about/more))

## Before You Begin


This guide walks you through setting up a Mastodon server on a Utho running Ubuntu 16.04, utilizing Docker Compose for installation. If you're using a different Linux distribution, you might need to make minor adjustments to the provided commands.

Begin by creating a Utho account and Compute Instance if you haven't already.

Follow our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide to update your system. Additionally, consider configuring the timezone, hostname, creating a limited user account, and enhancing SSH access. Replace instances of `example.com` in this guide with your Mastodon site’s domain name.

Proceed with the [Add DNS Records](/docs/guides/set-up-web-server-host-website/#add-dns-records) process to register a domain name that will direct to your Mastodon Utho.

As Mastodon serves its content over HTTPS, it's necessary to acquire an SSL/TLS certificate. You can obtain a free certificate from Let's Encrypt](https://letsencrypt.org) using [Certbot](https://certbot.eff.org):

        sudo apt install software-properties-common
        sudo add-apt-repository ppa:certbot/certbot
        sudo apt update
        sudo apt install certbot
        sudo certbot certonly --standalone -d example.com

These instructions will acquire a certificate and save it to `/etc/letsencrypt/live/example.com/` on your Utho server.

    {{< note type="secondary" title="Why not use the Docker Certbot image?" isCollapsible=true >}}
    When running Certbot, typically you include a command with the [`--deploy-hook` option](https://certbot.eff.org/docs/api/hooks.html#certbot.hooks.deploy_hook) to reload your web server. However, if your web server operates within its own container during deployment, the Certbot container won't be able to directly reload it. You'll need another workaround to facilitate this architecture.
    {{< /note >}}

Mastodon sends email notifications to users for various events, such as user sign-ups or follow requests. To enable this feature, you'll need to configure an SMTP server for sending these messages.

    {{< content "email-warning-shortguide" >}}

One option is to set up your own mail server using the [Email with Postfix, Dovecot, and MySQL](/docs/guides/email-with-postfix-dovecot-and-mysql/) guide. You can install this mail server on the same Utho as your Mastodon server or on a different one. If you choose this route, ensure to create a `notifications@example.com` email address for notifications. If you opt for installing the mail server on the same Utho as your Mastodon deployment, you can utilize your Let's Encrypt certificate located in `/etc/letsencrypt/live/example.com/` for the mail server as well.

Alternatively, if you prefer not to host your own email server, you can utilize a third-party SMTP service. This guide will also provide instructions for configuring [Mailgun](https://www.mailgun.com/) as your SMTP provider.

This guide utilizes Mastodon's Docker Compose deployment method. Prior to proceeding, ensure you have [install Docker and Docker Compose](/docs/guides/install-mastodon-on-ubuntu-1604/#install-docker). If you're new to Docker, it's advisable to review the [Introduction to Docker](/docs/guides/introduction-to-docker/) and [How to Use Docker Compose](/docs/guides/how-to-use-docker-compose/) guides.

### Install Docker

          {{< content "installing-docker-shortguide" >}}

### Install Docker Compose

          {{< content "install-docker-compose" >}}

## Set Up Mastodon

Mastodon has a number of components: [PostgreSQL](/docs/guides/configure-postgresql/#what-is-postgresql) and [Redis](https://redis.io/) are used to store data, and three different Ruby on Rails services are used to power the web application. These are all combined in a Docker Compose file that Mastodon provides.

### Download Mastodon and Configure Docker Compose

1. SSH into your Utho and clone Mastodon's Git repository:

        cd ~/
        git clone https://github.com/tootsuite/mastodon
        cd mastodon

The Git repository contains an example `docker-compose.yml` file that requires adjustments for your deployment. The initial modification involves specifying a release version of the Mastodon Docker image. You can find a chronological [list of releases](https://github.com/tootsuite/mastodon/releases) on the Mastodon GitHub page, and it's advisable to select the latest available version.

Open `docker-compose.yml` in your preferred text editor, comment out the build lines, and append a version number to the `image` lines for the `web`, `streaming`, and `sidekiq` services:

          {{< file "docker-compose.yml" yaml >}}
          version: '3'

  services:

          # [...]

  web:

          #    build: .
          image: tootsuite/mastodon:v2.4.2
          # [...]

  streaming:

          #    build: .
          image: tootsuite/mastodon:v2.4.2
          # [...]

  sidekiq:

         #    build: .
         image: tootsuite/mastodon:v2.4.2
         # [...]

         {{< /file >}}

Note: This tutorial employs Mastodon [v2.4.2](https://github.com/tootsuite/mastodon/releases/tag/v2.4.2). At the time of writing, there was a [known issue](https://github.com/tootsuite/mastodon/issues/8001) with [v2.4.3](https://github.com/tootsuite/mastodon/releases/tag/v2.4.3) during installation.

Configure the volumes for the `db`, `redis`, `web`, and `sidekiq` services as specified in this snippet:

         {{< file "docker-compose.yml" yaml >}}
          version: '3'

services:
  db:

              # [...]
volumes:

              - ./postgres:/var/lib/postgresql/data

redis:

              # [...]
volumes:

              - ./redis:/data

web:

              # [...]

volumes:

           - ./public/system:/mastodon/public/system
           - ./public/assets:/mastodon/public/assets
           - ./public/packs:/mastodon/public/packs

sidekiq:

              # [...]

volumes:

             - ./public/system:/mastodon/public/system
             - ./public/packs:/mastodon/public/packs
              {{< /file >}}

Insert a new nginx service after the `sidekiq` service and before the final `networks` section. NGINX will serve as a proxy for HTTP and HTTPS requests to the Mastodon Ruby on Rails application.

             {{< file "docker-compose.yml" yaml >}}
             version: '3'

services:

             # [...]

  sidekiq:

             # [...]

  nginx:
    build:
      context: ./nginx
      dockerfile: Dockerfile

    ports:
              - "80:80"
              - "443:443"
    volumes:

             - /etc/letsencrypt/:/etc/letsencrypt/
             - ./public/:/home/mastodon/live/public
             - /usr/share/nginx/html:/usr/share/nginx/html

restart: always

depends_on:

            - web
            - streaming

networks:

            - external_network
            - internal_network

networks:

            # [...]
            st
            {{< /file >}}

Compare your modified `docker-compose.yml` with this full version of the file to ensure all necessary changes have been applied.

Within the Mastodon Git repository, create a new directory named nginx.

        mkdir nginx

Inside the nginx directory, create a file named Dockerfile and paste the following contents:

          {{< file "nginx/Dockerfile" dockerfile >}}
          FROM nginx:latest

          COPY default.conf /etc/nginx/conf.d
          {{< /file >}}

reate another file named `default.conf` within the nginx directory and insert the following contents. Replace every occurrence of `example.com` with your domain name:

    ```file {title="nginx/default.conf" lang="nginx"}
    map $http_upgrade $connection_upgrade {
      default upgrade;
      ''      close;
    }

    server {
      listen 80;
      listen [::]:80;
      server_name example.com;
      # Useful for Let's Encrypt
      location /.well-known/acme-challenge/ { root /usr/share/nginx/html; allow all; }
      location / { return 301 https://$host$request_uri; }
    }

    server {
      listen 443 ssl http2;
      listen [::]:443 ssl http2;
      server_name example.com;

      ssl_protocols TLSv1.2;
      ssl_ciphers HIGH:!MEDIUM:!LOW:!aNULL:!NULL:!SHA;
      ssl_prefer_server_ciphers on;
      ssl_session_cache shared:SSL:10m;

      ssl_certificate     /etc/letsencrypt/live/example.com/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

      keepalive_timeout    70;
      sendfile             on;
      client_max_body_size 0;

      root /home/mastodon/live/public;

      gzip on;
      gzip_disable "msie6";
      gzip_vary on;
      gzip_proxied any;
      gzip_comp_level 6;
      gzip_buffers 16 8k;
      gzip_http_version 1.1;
      gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

      add_header Strict-Transport-Security "max-age=31536000";

      location / {
        try_files $uri @proxy;
      }

      location ~ ^/(packs|system/media_attachments/files|system/accounts/avatars) {
        add_header Cache-Control "public, max-age=31536000, immutable";
        try_files $uri @proxy;
      }

      location @proxy {
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header Proxy "";
        proxy_pass_header Server;

        proxy_pass http://web:3000;
        proxy_buffering off;
        proxy_redirect off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;

        tcp_nodelay on;
      }

      location /api/v1/streaming {
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header Proxy "";

        proxy_pass http://streaming:4000;
        proxy_buffering off;
        proxy_redirect off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;

        tcp_nodelay on;
      }

      error_page 500 501 502 503 504 /500.html;
    }
    ```

### Configure Mastodon

The configuration settings for Mastodon are stored in the `.env.production` file located at the root of the Mastodon Git repository.

Generate the `.env.production` file and insert the following contents. Substitute all instances of example.com with your domain name. Fill in the `SMTP_SERVER` and `SMTP_PASSWORD` fields with the appropriate domain and credentials from your mail server:

              {{< file ".env.production" >}}
              LOCAL_DOMAIN=example.com
              SINGLE_USER_MODE=false
              SECRET_KEY_BASE=
              OTP_SECRET=
              VAPID_PRIVATE_KEY=
              VAPID_PUBLIC_KEY=

# Database settings

              DB_HOST=db
              DB_PORT=5432
              DB_NAME=postgres
              DB_USER=postgres
              DB_PASS=

# Redis settings

              REDIS_HOST=redis
              REDIS_PORT=6379
              REDIS_PASSWORD=

# Mail settings

              SMTP_SERVER=your_smtp_server_domain_name
              SMTP_PORT=587
              SMTP_LOGIN=notifications@example.com
              SMTP_PASSWORD=your_smtp_password
              SMTP_AUTH_METHOD=plain
              SMTP_OPENSSL_VERIFY_MODE=none
              SMTP_FROM_ADDRESS=Mastodon <notifications@example.com>
              {{< /file >}}

If you're utilizing Mailgun for your mail service, erase all lines within the `Mail settings` section and input the following options:

              {{< file ".env.production" >}}
              SMTP_SERVER=smtp.mailgun.org
              SMTP_PORT=587
              SMTP_LOGIN=your_mailgun_email
              SMTP_PASSWORD=your_mailgun_email_password
              {{< /file >}}

Utilize Docker and Mastodon to generate a new value for the `SECRET_KEY_BASE` setting:

        SECRET_KEY_BASE=$(docker-compose run --rm web bundle exec rake secret)

This generates a random string of characters. If you encounter an error in the subsequent step, rerun the command to generate another string.

Insert the `SECRET_KEY_BASE` setting into `.env.production` using the `sed` command:

        sed -i -e "s/SECRET_KEY_BASE=/&${SECRET_KEY_BASE}/" .env.production

Combine the previous two steps into one to set a value for the `OTP_SECRET` setting in `.env.production`:

        sed -i "s/OTP_SECRET=$/&$(docker-compose run --rm web bundle exec rake secret)/" .env.production

Generate values for `VAPID_PRIVATE_KEY` and `VAPID_PUBLIC_KEY` settings:

        docker-compose run --rm web bundle exec rake mastodon:webpush:generate_vapid_key

Copy the output from the previous command, open `.env.production` in your text editor, and paste the command output into the corresponding lines for `VAPID_PRIVATE_KEY` and `VAPID_PUBLIC_KEY`.

Build the Docker Compose file:

        docker-compose build

### Complete the Mastodon Setup

Assign Mastodon as the owner of the public directory within the Mastodon Git repository:

        sudo chown -R 991:991 public

Note: The UID under which Mastodon operates is `991`, but there is no mastodon user established on your system.

Create a directory designated to house [Let's Encrypt challenge files](https://letsencrypt.org/how-it-works/)  for forthcoming certificate renewals:

        sudo mkdir -p /usr/share/nginx/html

Establish the database:

            docker-compose run --rm web bundle exec rake db:migrate

Pre-compile Mastodon's assets

            docker-compose run --rm web bundle exec rake assets:precompile

This operation may take some time to complete.

Launch Mastodon:

             docker-compose up -d

### Create your Mastodon User

Navigate to your domain using a web browser:

Provide a new username, email address, and password to create a new user for your Mastodon instance. When communicating with users from other Mastodon servers, your complete username will be `@your_username@example.com`.

Mastodon will attempt to dispatch a confirmation email. Check your email inbox and follow the provided link to verify your registration. If you haven't configured email notifications, you can manually confirm the new user by executing the `confirm_email` task on the Docker container:

        docker-compose run web bundle exec rake mastodon:confirm_email USER_EMAIL=your_email_address

Execute the `make_admin` task on your Docker container to grant administrative privileges to the newly created user for the Mastodon instance:

        docker-compose run web bundle exec rake mastodon:make_admin USERNAME=your_mastodon_instance_user

## Troubleshooting

If your Mastodon site doesn't load in your browser, try reviewing the logs generated by Docker for more information. To see these errors:

Shut down your containers:

        cd mastodon
        docker-compose down

Launch Docker Compose in an interactive mode to observe the logs produced by each container:

        docker-compose up

To terminate Docker Compose and return to the command prompt, press `CTRL-C`.

If your Mastodon site is accessible but certain functionalities are malfunctioning, utilize the tools accessible in Mastodon's administrative settings, accessible through `example.com/admin/settings/edit`. The Sidekiq dashboard presents the status of jobs initiated by Mastodon:

## Maintenance
For updating your Mastodon version, refer to the instructions provided in the Mastodon GitHub's [Docker Guide](https://github.com/tootsuite/documentation/blob/master/Running-Mastodon/Docker-Guide.md#updating). Additionally, review the [Mastodon release notes](https://github.com/tootsuite/mastodon/releases).

### Renew your Let's Encrypt Certificate

Open your Crontab file in your preferred text editor:

        sudo crontab -e

Append a line to schedule Certbot to run at 11PM daily. Ensure to replace `example.com` with your domain:

        0 23 * * *   certbot certonly -n --webroot -w /usr/share/nginx/html -d example.com --deploy-hook='docker exec mastodon_nginx_1 nginx -s reload'

Validate your new scheduled task using the `--dry-run` option:

        sudo bash -c "certbot certonly -n --webroot -w /usr/share/nginx/html -d example.com --deploy-hook='docker exec mastodon_nginx_1 nginx -s reload' --dry-run"

## Using your New Mastodon Instance
Now that your new instance is up and running, you're ready to engage with the Mastodon network. The official [Mastodon User's Guide](https://github.com/tootsuite/documentation/blob/master/Using-Mastodon/User-guide.md) provides detailed insights into the application and its network mechanics. Additionally, Mastodon's founder, Eugen Rochko, offers a non-technical blog post outlining his best practices for community building: [How to start a Mastodon server](https://medium.com/tootsuite/how-to-start-a-mastodon-server-dea0dec56028).
