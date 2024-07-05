---
slug:"Rocket into Chat: Effortless Installation of Rocket.Chat on Ubuntu 16.04
description: 'Discover how to install and get started with Rocket.Chat, a lightweight XMPP server, on Ubuntu 16.04 with this comprehensive guide covering installation and basic usage'
keywords: ["rocket.chat", "slack alternative", "chat", "xmpp"]
tags: ["ubuntu"]
modified: 2024-04-11
modified_by:
  name: Utho
published: 2024-04-11
title: 'Installing Rocket.Chat on Ubuntu 16.04'
authors: ["Utho"]
---
**Rocket.Chat** is an open-source chat software similar to Slack, packed with features for team productivity. Communicate with team members, make video and audio calls with screen sharing, create channels and private groups, upload files, and much more. Rocket.Chat's source code is hosted on GitHub, allowing you to develop new features and contribute to the project.

This guide walks you through deploying Rocket.Chat on a Utho running Ubuntu 16.04 LTS, using NGINX as a reverse proxy with SSL.

## Before You Begin

1.  If you haven't already, create a Utho account and a Compute Instance.

1.  Follow our guide on Setting Up and Securing a Compute Instance to update your system. You may also consider setting the timezone, configuring your hostname, creating a restricted user account, and improving SSH security.

1. Follow the steps to Add DNS Records and register a domain name that will direct to your Rocket.Chat server instance.

## Install Rocket.Chat

The fastest way to install Rocket.Chat is by using its *Snap*. Snaps are software packages that run on all major Linux systems in a containerized format. *Snapd* is the service that handles and manages snaps, and it's already installed by default on Ubuntu 16.04 LTS.

1.  Install Rocket.Chat

        sudo snap install rocketchat-server

1.  After installation, the Rocket.Chat service starts automatically. To verify if Rocket.Chat is running:

        sudo service snap.rocketchat-server.rocketchat-server status

Explore the [Rocket.Chat snaps documentation](https://rocket.chat/docs/installation/manual-installation/ubuntu/snaps/) to find additional helpful commands.

## Setting Up NGINX for Reverse Proxy and SSL Configuration
A reverse proxy acts as an intermediary server between internal applications and external clients, directing client requests to the appropriate server. Unlike many standalone server applications, NGINX offers advanced load balancing, security, and acceleration features. Using NGINX as a reverse proxy allows you to enhance any application with these capabilities. In this case, we'll use NGINX as a reverse proxy for Rocket.Chat.

### Install NGINX

1.  Download NGINX from the package manager:

        sudo apt install nginx

1.  Make sure NGINX is running and set to start automatically on reboot

        sudo systemctl start nginx
        sudo systemctl enable nginx

### Configuring NGINX as a Reverse Proxy

1.  Disable the default Welcome to NGINX page by removing the link to it, which is configured within `/etc/nginx/sites-enabled/default`. This link is pointing to a file within `/etc/nginx/sites-available/`.

        sudo ls -l /etc/nginx/sites-enabled

        {{< output >}}
        total 0
        lrwxrwxrwx 1 root root 34 Aug 16 14:59 default -> /etc/nginx/sites-available/default
        {{< /output >}}

Remove the link to disable the default site:

        sudo rm /etc/nginx/sites-enabled/default

1.  Create `/etc/nginx/sites-available/rocketchat.conf` and add the necessary configuration to point to your domain name and set up the reverse proxy. Replace `example.com` with your actual domain name:

        {{< file "/etc/nginx/conf.d/rocketchat.conf" nginx >}}
        server {
        listen 80;

        server_name example.com;

        location / {
        proxy_pass http://localhost:3000/;
        }
        }
        {{< /file >}}

1.  Enable the new configuration by creating a link to it from `/etc/nginx/sites-available/`:

        sudo ln -s /etc/nginx/sites-available/rocketchat.conf /etc/nginx/sites-enabled/

1.  Test the configuration:

        sudo nginx -t

1.  If no errors are reported, reload the new configuration:

        sudo nginx -s reload

### Generate SSL certificates using Certbot
Your Rocket.Chat site will utilize an SSL certificate from [Let's Encrypt](https://letsencrypt.org), a free certificate provider trusted by common web browsers. You can easily obtain and use a Let's Encrypt certificate using a popular tool called [Certbot](https://certbot.eff.org):

    {{< content "certbot-shortguide-ubuntu" >}}

## View Your Rocket.Chat Site

1.  After Certbot updates your NGINX configuration, test the new configuration to ensure it works:

        sudo nginx -t

1.  If no errors are reported, reload the new configuration:

        sudo nginx -s reload

1.  In a web browser, visit your domain address and follow the Rocket.Chat setup wizard steps.
