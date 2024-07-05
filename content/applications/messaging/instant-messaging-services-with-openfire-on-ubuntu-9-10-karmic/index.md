---
slug: Setting Up Instant Messaging Services with Openfire on Ubuntu 9.10 (Karmic)
deprecated: true
description: 'Begin your journey with Openfire, an open-source instant messaging server based on the XMPP/Jabber protocol, tailored for Ubuntu 9.10 (Karmic)'
keywords: ["openfire", "openfire ubuntu 9.10", "openfire linux", "instant messaging", "real-time messaging", "xmpp server", "collaboration software", "chat software", "linux jabber server"]
tags: ["ubuntu"]
modified: 2024-06-17
modified_by:
  name: Utho
published: 2024-06-17
title: 'Instant Messaging Services with Openfire on Ubuntu 9.10'
relations:
    platform:
        key: how-to-install-openfire
        keywords:
            - distribution: Ubuntu 9.10
authors: ["Utho"]
---
Openfire is a real-time collaboration server based on the `XMPP protocol`, designed for various platforms. This guide will assist you in getting started with Openfire on your Ubuntu 9.10 (Karmic) Utho.

Before proceeding, ensure you have followed the steps outlined in our Setting Up and Securing a Compute Instance guide and have fully updated your system. Initial configuration steps will be executed via the terminal, so ensure you are logged into your Utho as root via SSH.

## Install Prerequisites

To run Openfire, you need a Java Runtime Environment (JRE). This tutorial uses the version provided by Sun Microsystems. While other JREs are available, they may not be fully compatible with Openfire.

Ensure the multiverse repository is enabled in your `/etc/apt/sources.list` file. You can edit this file using the nano editor in the shell. Use the command `nano /etc/apt/sources.list` to open it. For guidance on using nano, refer to the nano manual page. Your sources.list file should resemble the example below.


{{< file "/etc/apt/sources.list" >}}

       deb http://us.archive.ubuntu.com/ubuntu/ karmic main restricted
       deb-src http://us.archive.ubuntu.com/ubuntu/ karmic main restricted

       deb http://us.archive.ubuntu.com/ubuntu/ karmic-updates main restricted
       deb-src http://us.archive.ubuntu.com/ubuntu/ karmic-updates main restricted

       deb http://us.archive.ubuntu.com/ubuntu/ karmic universe
       deb-src http://us.archive.ubuntu.com/ubuntu/ karmic universe
       deb http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe
       deb-src http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe

       deb http://us.archive.ubuntu.com/ubuntu/ karmic multiverse
       deb-src http://us.archive.ubuntu.com/ubuntu/ karmic multiverse
       deb http://us.archive.ubuntu.com/ubuntu/ karmic-updates multiverse
       deb-src http://us.archive.ubuntu.com/ubuntu/ karmic-updates multiverse

       deb http://security.ubuntu.com/ubuntu karmic-security main restricted
       deb-src http://security.ubuntu.com/ubuntu karmic-security main restricted
       deb http://security.ubuntu.com/ubuntu karmic-security universe
       deb-src http://security.ubuntu.com/ubuntu karmic-security universe
       deb http://security.ubuntu.com/ubuntu karmic-security multiverse
       deb-src http://security.ubuntu.com/ubuntu karmic-security multiverse

      {{< /file >}}

If you previously added the `multiverse` repositories to your sources, update your package database with the following command:

    apt-get update
    apt-get upgrade

Next, use the command below to install the necessary prerequisite packages on your server:

    apt-get install sun-java6-jre

The installation process will include the Sun Java6 JRE and its required dependencies. During installation, you will be prompted to accept the licensing agreement for Sun Java before proceeding.

## Adjust Firewall Settings

If you use a firewall to control port access on your Utho, ensure that the following ports are open:


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

Additional ports may be required later for advanced XMPP services, but these are the default ports used by Openfire.

## Install Openfire

To download the Openfire RTC server, visit the Openfire download page and click on the "deb" package link. This will redirect you to another page where the download will start automatically. You can cancel this download and instead copy the manual download link provided to your clipboard. On your Utho server, use `wget` to retrieve the package (substitute the link for the current version in the command below). If `wget` is not installed, you can install it using `apt-get install wget`.

    wget http://www.igniterealtime.org/downloadServlet?filename=openfire/openfire_3.6.4_all.deb

To install the software, use `dpkg` with the following command:

    dpkg -i openfire_3.6.4_all.deb

Next, modify the configuration file `/etc/openfire/openfire.xml`. In the <interface> section, replace the `<!-- -->` comment markers with your Utho's public IP address.

      {{< file "/etc/openfire/openfire.xml" xml >}}
      <interface>12.34.56.78</interface>

       {{< /file >}}

To restart Openfire, use the following command:

        /etc/init.d/openfire restart

These are the initial installation steps for Openfire. Next, we will proceed with the configuration through a web browser.

## Configure Openfire

To configure Openfire through your web browser, follow these steps:

Open your web browser and enter your Utho's IP address or FQDN (fully qualified domain name) followed by port 9090. For example, if your Utho's IP address is "12.34.56.77".

Select your preferred language on the language selection screen.

Configure your domain and specify administration ports. Use the fully qualified domain name assigned to your Utho in DNS.

Choose whether to use Openfire's internal database for account management or connect to an external database.

Select where user profiles will be stored: server database, LDAP, or Clearspace.

Enter the email address and set a strong password for the default administrative user.

After completing the web-based configuration, restart the Openfire server before logging in with the default `admin` user account.
These steps will guide you through setting up Openfire on Ubuntu 9.10 (Karmic) via a web interface.

               /etc/init.d/openfire restart

If you encounter login issues with the credentials you created, use "admin/admin" as the username and password. Remember to update these credentials promptly for security reasons. Congratulations! You've successfully installed the Openfire RTC server on Ubuntu 9.10.

## More Information

Feel free to refer to the following resources for more information on this topic. While these are provided to be helpful, please note that we cannot guarantee the accuracy or currency of externally hosted materials.
