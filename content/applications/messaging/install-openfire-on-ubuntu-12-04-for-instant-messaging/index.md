---
slug: "Spark Your Communication: Easy Openfire Setup for Instant Messaging on Ubuntu 12.04"
deprecated: true
description: 'Discover how to set up Openfire, a leading collaborative instant messaging application built on the XMPP protocol, on your Utho running Ubuntu 12.04 with this comprehensive guide'
keywords: ["openfire", "ubuntu 12.04", "instant messaging", "xmpp server", "collaboration software", "chat software", "linux jabber server", "JRE", "configure openfire"]
tags: ["ubuntu"]
modified: 2024-04-11
modified_by:
  name: Pawan Kumar
published: 2024-04-11
title: 'Install Openfire on Ubuntu 12.04 for Instant Messaging'
relations:
    platform:
        key: how-to-install-openfire
        keywords:
            - distribution: Ubuntu 12.04
authors: ["Pawan Kumar"]
---
[Openfire](http://www.igniterealtime.org/projects/openfire/) is a free, open-source instant messaging server designed for real-time collaboration. It operates on the [XMPP protocol](http://en.wikipedia.org/wiki/Extensible_Messaging_and_Presence_Protocol) and supports various platforms. This guide walks you through setting up Openfire on your Ubuntu 12.04 LTS (Precise Pangolin) Utho.

Before proceeding, ensure you've followed the steps in our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide to set up and secure your system. Make sure your system is up-to-date. You'll perform initial configuration tasks via the terminal, so ensure you're logged into your Utho as the root user using SSH.

## Prerequisites

Before installing Openfire, make sure your system is up to date:

       apt-get update && apt-get upgrade

Openfire relies on a Java runtime engine (JRE). This guide uses the OpenJDK available in the Ubuntu repository. Keep in mind that while other Java runtime engines exist, Openfire may not be compatible with them.

       apt-get install openjdk-7-jre

Install the OpenJDK by running the following command:

## Adjust Firewall Settings

If you're using a firewall to control access to ports on your Utho, ensure that the following ports are open:

-   3478 - STUN Service (NAT connectivity)
-   3479 - STUN Service (NAT connectivity)
-   5222 - Client to Server (standard and encrypted)
-   5223 - Client to Server (legacy SSL support)
-   5229 - Flash Cross Domain (Flash client support)
-   7070 - HTTP Binding (unsecured HTTP connections)
-   7443 - HTTP Binding (secured HTTP connections)
-   7777 - File Transfer Proxy (XMPP file transfers)
-   9090 - Admin Console (unsecured)
-   9091 - Admin Console (secured)

You might need to open more ports later to support advanced XMPP services, but for now, these are the default ports that Openfire uses.

## Install Openfire

Installing Openfire is straightforward and can be done in just a few simple steps. Here's how:

1. Go to the download page for the [Openfire RTC server](http://www.igniterealtime.org/downloads/index.jsp#openfire), and click the link for the `.tar.gz` file. This will start the download to your workstation. You can cancel this download because a manual download link will be provided that you can copy to your clipboard.

2.  Use `wget` on your Utho to fetch the package. Replace the link with the current version in the command below:

        wget http://www.igniterealtime.org/downloadServlet?filename=openfire/openfire_3_7_1.tar.gz

3.  Rename the downloaded file by running the following command:

        mv downloadServlet\?filename\=openfire%2Fopenfire_3_7_1.tar.gz openfire_3_7_1.tar.gz

4.  Extract the software by running this command:

        tar -xvzf openfire_3_7_1.tar.gz

5.  Move the openfire folder to the /opt directory:

         mv openfire /opt/

6.  Edit the configuration file located at `/opt/openfire/conf/openfire.xml`. Replace 198.51.100.0 in the `<interface>` section with your Utho's public IP address. Remove the <!-- --> comment markers surrounding the `<network>` section. This step isn't mandatory, but it's useful if your Utho has multiple IP addresses and you want to restrict access to a specific one.

        {{< file "/opt/openfire/conf/openfire.xml" xml >}}
        <interface>198.51.100.0</interface>

         {{< /file >}}

7.  Create a symbolic link for the daemon script in `/etc/init.d` so you can start the daemon with a simple service call:

        ln -s /opt/openfire/bin/openfire /etc/init.d/

8.  Start Openfire:

        service openfire start

That wraps up the initial installation steps for Openfire. Now, let's move on to configuration via a web browser.

## Configure Openfire

1. Open your web browser and navigate to your Utho's IP address or fully qualified domain name (FQDN) if it's associated with your Utho's IP. If your Utho's IP is `198.51.100.1`.

2. Configure your domain and ports for administration using the FQDN assigned to your Utho in DNS.

3.  Choose whether to use Openfire's internal database for account management or connect to an external database. Most users will opt for the built-in option.

4.  User profiles can be stored in the server database or pulled from LDAP or Clearspace. The default option is suitable for most users.

5. Provide the email address of the default administrative user and select a secure password.

6.  Once the initial web-based configuration is done, restart the Openfire server before attempting to log in with the default `"admin"` user account. Run the following commands one by one:

        service openfire stop
        service openfire start

If you encounter login issues with the credentials you just set up, use "admin/admin" as the username/password. Remember to update your credentials immediately for security reasons.

Congratulations! You've successfully installed the Openfire RTC server on Ubuntu 12.04 LTS.
