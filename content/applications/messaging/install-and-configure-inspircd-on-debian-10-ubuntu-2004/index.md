---
slug: "Setting Up InspIRCd: Your Guide for Debian 10 and Ubuntu 20.04"
description: 'Deploy your own InspIRCd server effortlessly with this guide tailored for Debian 10 and Ubuntu 20.04. Learn to set up, configure, and customize your IRC server with InspIRCd, renowned for its simplicity, lightweight nature, and extensibility through modular design'
keywords: ['irc server', 'inspircd']
tags: ['debian', 'ubuntu']
published: 2024-04-09
modified_by:
  name: Utho
title: "Setting Up and Configuring InspIRCd on Debian 10 and Ubuntu 20.04"
authors: ["Pawan Kumar"]
---
Internet Relay Chat (IRC) is a real-time, text-based communication protocol widely used for group chats and discussion forums. IRC networks consist of multiple servers, each hosting numerous channels for group conversations.

InspIRCd is a robust, open-source IRC server known for its stability, performance, and modular design. This guide will walk you through setting up your own InspIRCd server on Ubuntu and Debian.

## Before You Begin

1. If you haven't already, create a Utho account and a Compute Instance. Refer to our guides on Getting Started with Utho and Creating a Compute Instance.

2. Follow our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide to update your system and perform essential security configurations, including timezone setup, hostname configuration, user account creation, and SSH access hardening.

Note: This guide is tailored for non-root users. Commands that need elevated privileges are preceded by `sudo`. If you're unfamiliar with the `sudo` command, refer to the [Linux Users and Groups](/docs/guides/linux-users-and-groups/) guide.

## Install InspIRCd

1. Install the InspIRCd package.

        sudo apt install inspircd

1. Next, ensure that ports 22 and 6667 are open in your firewall. If you're using UFW for firewall management, execute the following commands:

        sudo ufw allow 22/tcp
        sudo ufw allow 6667/tcp
        sudo ufw reload

## Configure InspIRCd

Then, access the InspIRCd configuration file located at `/etc/inspircd/inspircd.conf` and make the necessary modifications as outlined below.

Note: For detailed explanations of each configuration tag and its values updated in this section's steps, consult InspIRCd's documentation on [configuration options](https://docs.inspircd.org/3/configuration/).

1. Navigate to the server tag within the configuration file. Adjust the `name` value to match your IRC server's hostname while retaining the `id` value unchanged. Customize the other values within the `server` tag according to your preferences for the IRC server's description and naming.

       {{< file "/etc/inspircd/inspircd.conf" >}}
       <server name="irc.example.com"
       description="Example IRC Server"
       id="1AB"
       network="ExampleNet">
       {{< /file >}}

1. Admin Contact Information: Find the `admin` tag and update it with the server administrator's contact information.

       {{< file "/etc/inspircd/inspircd.conf" >}}
       <admin name="Example User"
       nick="example-user"
       email="example-user@example-email.com">
       {{< /file >}}

1. Bind Tag: Locate the bind tag and remove the value from the address field to allow the server to respond to outside requests. This step removes the limitation to local connections (`127.0.0.2`).

       {{< file "/etc/inspircd/inspircd.conf" >}}
       <bind address="" port="6667" type="clients">
       {{< /file >}}

1. Power Tag: Find the power tag and set passwords for shutting down (`diepass`) and restarting (`restartpass`) the IRC server.

       {{< file "/etc/inspircd/inspircd.conf" >}}
       <power diepass="shutdown-password" restartpass="restart-password" pause="2">
       {{< /file >}}

1. Operator User: Locate the `oper` tag and define the operator user, who has administrative authority on the IRC server. Set a username (`name`) and password for the operator user, and leave the type as `NetAdmin`.

       {{< file "/etc/inspircd/inspircd.conf" >}}
       <oper name="example-user"
       password="password"
       host="*@*"
       type="NetAdmin">
       {{< /file >}}

- The `host` value in the `oper` tag defines the `username@hostname` masks from which a user can log in as the operator.

- In the example provided, setting *@* as the host value allows any user to log in as the operator from any address mask.

- To enhance the security of your IRC server, you can restrict operator login to specific addresses. For instance, the example below only permits operator login from the localhost, IP address, or domain of the IRC network:

          *@localhost *@192.0.2.0 *@irc.example.com

- You can further limit access by replacing the asterisks (*) with the username of a specific user on the machine. Multiple address masks can be entered, separated by space.

## Complete the InspIRCd Setup

1. Open the `/etc/inspircd/inspircd.motd` file and add a "message of the day" for your IRC server. This message displays when users connect to your IRC server or use the `/motd` command. It's a good place to remind users to review the server's rules, which you'll set up in the next step.

1. Open the `/etc/inspircd/inspircd.rules` file and define the usage rules for your IRC server. Users can view these rules using the `/rules` command. Make sure these rules set clear expectations for acceptable behavior on your server.

Note: Not all IRC clients support the `/rules` command. For instance, the popular WeeChat client doesn't support it. If you anticipate users using such clients, consider including your rules in the message of the day.

1. Restart the InspIRCd service to apply the changes.

        sudo systemctl restart inspircd

## Test the InspIRCd Installation

To confirm that your IRC server is functioning correctly, connect to it using an IRC client. This guide demonstrates using the widely-used WeeChat client.

For detailed information about WeeChat and how to use it, consult the [Using WeeChat for Internet Relay Chat](/docs/guides/using-weechat-for-irc/) guide.

1. Install the WeeChat IRC client on your system.

        sudo apt install weechat

1. Once the installation is finished, launch WeeChat.

        weechat

1. Replace irc.example.com with your IRC network's domain name, and `example-irc-alias` with the desired alias for the connection.

        /server add example-irc-alias irc.example.com

To exit the WeeChat interface, simply type `/quit`.

WeeChat saves the connection details under the specified alias. You can reconnect to the server later, even after restarting WeeChat, using this alias.

1.Connect to the IRC server using the created alias:

        /connect example-irc-alias

1. Replace `example-user` and `password` with the username and password configured for the operator user.

        /oper example-user password

## Add SSL Certification

Using SSL certification on your IRC server isn't necessary but significantly boosts security by encrypting information sent and received. Follow these steps to request and download a free certificate from [Let's Encrypt](https://letsencrypt.org) using [Certbot](https://certbot.eff.org) and apply it to your IRC server.

### Install Certbot

1. Install the [Snap](https://snapcraft.io/docs/getting-started) app store, which offers application bundles compatible with major Linux distributions. If you're using Ubuntu (since version 16.04), Snap should already be installed:

        sudo apt install snapd

1. Update and refresh Snap to ensure you have the latest version.

        sudo snap install core && sudo snap refresh core

1. Remove any existing Certbot installations.

        sudo apt remove certbot

1. Install Certbot.

        sudo snap install --classic certbot

1. Create a symbolic link for Certbot.

        sudo ln -s /snap/bin/certbot /usr/bin/certbot

### Download a Certificate

1. Open port `80` in your firewall to allow Certbot to verify your domain information.

        sudo ufw allow 80/tcp
        sudo ufw reload

1. Download the standalone certificate for your IRC server's domain name (`irc.example.com`).

        sudo certbot certonly --standalone --preferred-challenges http -d irc.example.com

1. Certbot includes a cron job for automatic certificate renewal. You can test the renewal process with the following command:

        sudo certbot renew --dry-run

### Add the Certificate to InspIRCd

1. Create a directory to store the SSL files for InspIRCd.

        sudo mkdir /etc/inspircd/ssl

1. Copy the certificate and private key files into the new InspIRCd SSL directory.

        sudo cp /etc/letsencrypt/live/irc.example.com/fullchain.pem /etc/inspircd/ssl/cert.pem
        sudo cp /etc/letsencrypt/live/irc.example.com/privkey.pem /etc/inspircd/ssl/key.pem

1. Ensure that the InspIRCd user has the required permissions for the files.

        sudo chown -R irc:irc /etc/inspircd

1. Open the InspIRCd configuration file (`/etc/inspircd/inspircd.conf`) again, and add the following lines below the existing bind tag:

       {{< file "/etc/inspircd/inspircd.conf" >}}
       <bind address="" port="6697" type="clients" ssl="gnutls">
       <gnutls
       certfile="/etc/inspircd/ssl/cert.pem"
       keyfile="/etc/inspircd/ssl/key.pem"
       priority="SECURE192:-VERS-SSL3.0">
       <module name="m_ssl_gnutls.so">
       {{< /file >}}

### Connect to the IRC Server Using SSL

To connect to the IRC server now, you need to use port 6697. In WeeChat, you can do this in one of two ways:

Note: In both of the examples that follow, replace `example-irc-alias` with your server's WeeChat alias and `irc.example.com` with your server's domain name.

1. Change the alias's address settings to specify the new port. This assumes that the alias has SSL enabled, which is the case by default. This is the simplest option.

       /set irc.server.example-irc-alias.addresses irc.example.com/6697

1. Alternatively, you can delete the existing alias and create a new one using the appropriate port and specifying SSL. While it is not necessary to delete the existing alias first, doing so frees the alias for reuse. It also removes inoperative connection information, since the IRC server no longer accepts connections on the default port used by the existing alias.

       /server del example-irc-alias
       /server add example-irc-alias irc.example.com/6697 -ssl

Once you've updated the alias, you can connect to the IRC server. WeeChat's notifications indicate that SSL is being used for the connection.

        /connect example-irc-alias

### Set Up Automatic Renewals

Certificates from Let's Encrypt expire after 90 days. Certbot automatically renews your certificate, but the renewed certificate needs to be copied to the `inspircd` folder. The following steps show you how to set up a Cron job to copy the certificate files automatically.

You can learn more about using Cron in the [Schedule Tasks with Cron](/docs/guides/schedule-tasks-with-cron/) guide.

1. Create a `/etc/inspircd/cron` directory. Using your preferred text editor, create and open a `copy-inspircd-certs.sh` file in that directory. The Nano text editor is used in this example.

        sudo mkdir /etc/inspircd/cron
        sudo nano /etc/inspircd/cron/copy-inspircd-certs.sh

1. Enter the following lines in the `copy-inspircd-certs.sh` file.

       {{< file "/etc/inspircd/cron/copy-inspircd-certs.sh" >}}

##!/bin/bash

     cp /etc/letsencrypt/live/debian-test.nathanielps.com/fullchain.pem /etc/inspircd/ssl/cert.pem
     cp /etc/letsencrypt/live/debian-test.nathanielps.com/privkey.pem /etc/inspircd/ssl/key.pem
     chown -R irc:irc /etc/inspircd
     {{< /file >}}

The first line specifies the program used to execute the script. The subsequent lines replicate the commands used earlier to copy the certificate files into the `inspircd` directory and ensure that the InspIRCd user has the necessary permissions for them. The `sudo` portion of the commands has been omitted since the cron job is to be run by the root user.

1. Make the file executable.

        sudo chmod +x /etc/inspircd/cron/copy-inspircd-certs.sh

1. Open the root user's `crontab`. You'll be prompted to choose a text editor; select your preferred one.

        sudo crontab -e

1. Add the job to the `crontab` by entering the following line:

        0 0 * * * /etc/inspircd/cron/copy-inspircd-certs.sh

## Next Steps

Your IRC server is now up and running and ready for a community. Explore the [#irchelp](https://www.irchelp.org/) website for more information about using and running an IRC server. You can also visit that channel to get ideas for what else you can do with your new server. Additionally, you can use the [IRC Networks and Server Lists](https://www.irchelp.org/networks/) to discover existing IRC communities and observe how other popular IRC servers operate.
