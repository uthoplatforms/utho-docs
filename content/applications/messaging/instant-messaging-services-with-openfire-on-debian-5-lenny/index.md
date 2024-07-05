---
slug: Instant-messaging-services-with-openfire-on-debian-5-lenny
deprecated: true
description: 'Getting started with Openfire on Debian 5 (Lenny), an open source instant messaging server built on the XMPP/Jabber protocol.'
keywords: ["openfire", "openfire on linux", "instant messaging", "real-time messaging", "xmpp server", "collaboration software", "chat software", "linux jabber server"]
tags: ["debian"]
modified: 2024-05-10
modified_by:
  name: utho
published: 2024-05-10
title: 'Instant Messaging Services with Openfire on Debian 5 (Lenny)'
relations:
    platform:
        key: how-to-install-openfire
        keywords:
            - distribution: Debian 5
authors: ["utho"]
---

[Openfire](http://www.igniterealtime.org/projects/openfire/) stands as a versatile open-source real-time collaboration server, leveraging the [XMPP protocol](http://en.wikipedia.org/wiki/Extensible_Messaging_and_Presence_Protocol) and accessible across various platforms. This comprehensive guide is your gateway to setting up Openfire on your CentOS 5 Utho.

If you haven't done so already, please follow the steps outlined in our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) before following these instructions, and make sure your system is fully updated. Initial configuration steps will be performed through the terminal; please make sure you're logged into your Utho as root via SSH.

## Install Prerequisites

Openfire requires a Java runtime engine (JRE). This tutorial uses the version provided by Sun Microsystems. Please note that although alternate Java runtime engines are available, Openfire may not work well with them.

Examine your `/etc/apt/sources.list` file to make sure you have the `non-free` repository enabled. You can use an editor like `nano` to edit configuration files through the shell; you would issue the command `nano /etc/apt/sources.list` to edit this one. Please consult the [nano manual page](http://www.nano-editor.org/dist/v1.2/nano.1.html) for information on using the editor. Your file should look similar to the following.

                          {{< file "/etc/apt/sources.list" >}}

# the main Debian packages.
                      deb http://mirror.cc.columbia.edu/pub/linux/debian/debian/ lenny main contrib non-free

Uncomment the deb-src line if you want 'apt-get source'

To work with most packages.

       deb-src http://mirror.cc.columbia.edu/pub/linux/debian/debian/ lenny main contrib

uncommenting the following line will enable security updates

       deb http://security.debian.org/ stable/updates main contrib non-free

       {{< /file >}}

If you've appended the `non-free` repository to your sources, update your package database with the command below:

          apt-get update
          apt-get upgrade

To ensure all prerequisite packages are installed on your server, issue the following command:

          apt-get install sun-java6-jre

The installation process will include the Sun Java6 JRE along with its necessary dependencies. You'll be prompted to accept the licensing agreement for Sun Java before proceeding.

## Adjust Firewall Settings

If you've implemented a firewall to regulate port access on your utho, ensure the following ports are open:

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

While additional ports may be required later for advanced XMPP services, these are the default ports utilized by Openfire.

## Install Openfire

Proceed to the download page for the
Visit the download page for the [Openfire RTC server](http://www.igniterealtime.org/downloads/index.jsp#openfire) and select the Debian package link. Upon redirection, initiate the download to your workstation. You can opt to cancel this download as a manual download link will be provided, which you can copy to your clipboard. Utilize `wget` on your utho to fetch the package, substituting the link with the current version. If wget is not installed, use `apt-get install wget`.

    wget http://www.igniterealtime.org/downloadServlet?filename=openfire/openfire_3.6.4_all.deb

Install the software using `dpkg` with the following command:

           dpkg -i openfire*.deb

Then, access the configuration file `/etc/openfire/openfire.xml`. Within the <interface> section, substitute your utho's public IP address, and ensure to remove the `<!-- -->` comment markers enclosing this section.

          {{< file "/etc/openfire/openfire.xml" xml >}}
          <interface>12.34.56.78</interface>

          {{< /file >}}

Restart Openfire using the following command:

           /etc/init.d/openfire restart

With that, the initial installation steps for Openfire are now finished. Let's proceed with the configuration via a web browser.

## Configure Openfire

Access your utho's IP address or FQDN (fully qualified domain name), appending port 9090 to the URL. For instance, if your utho's IP address is "12.34.56.78", navigate to http://12.34.56.78:9091 in your web browser. You'll encounter a language selection screen similar to this:


Following that, you'll be prompted to configure your domain and administration ports. Utilize the fully qualified domain name assigned to your utho in DNS. For detailed instructions on [configuring DNS with the utho Manager](/docs/products/networking/dns-manager/)

You have the option to utilize Openfire's internal database for account management or connect to an external database. Most users typically opt for the built-in option.

User profiles can be stored in the server database or fetched from LDAP or Clearspace. The default option is suitable for most users.

Enter the email address of the default administrative user and establish a robust password.

Once the initial web-based configuration is finalized, restart the Openfire server before attempting to log in with the default **"admin"** user account.

                 /etc/init.d/openfire restart

If you encounter difficulties logging in with the newly created credentials, utilize "admin/admin" as the username/password. It's recommended to update your credentials promptly for security reasons. Congratulations! You've successfully installed the Openfire RTC server on Debian Linux.
