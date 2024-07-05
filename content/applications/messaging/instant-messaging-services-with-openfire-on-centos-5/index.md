---
slug: "Spark Up Your Communication: Openfire Instant Messaging on CentOS 5"
deprecated: true
description: 'Dive into the world of Openfire on CentOS 5, a dynamic open-source instant messaging server powered by the XMPP/Jabber protocol'
keywords: ["openfire", "openfire centos", "openfire on linux", "instant messaging", "real-time messaging", "xmpp server", "collaboration software", "chat software", "linux jabber server"]
tags: ["centos"]
modified: 2024-05-10
modified_by:
  name: Utho
published: 2024-05-10
title: Instant Messaging Services with Openfire on CentOS 5
relations:
    platform:
        key: how-to-install-openfire
        keywords:
            - distribution: CentOS 5
authors: ["Utho"]
---

[Openfire](http://www.igniterealtime.org/projects/openfire/) stands as a versatile open-source real-time collaboration server, leveraging the [XMPP protocol](http://en.wikipedia.org/wiki/Extensible_Messaging_and_Presence_Protocol) and accessible across various platforms. This comprehensive guide is your gateway to setting up Openfire on your CentOS 5 Utho.

If you haven't done so already, please follow the steps outlined in our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) before following these instructions, and make sure your system is fully updated. Initial configuration steps will be performed through the terminal; please make sure you're logged into your Utho as root via SSH.

## Install Prerequisites

Openfire necessitates a Java Runtime Engine (JRE), preferably from Sun Microsystems. While alternative JREs exist, Openfire's compatibility may vary with them.

Navigate to the Sun Microsystems [Java download page](http://java.com/en/download/manual.jsp) and locate the appropriate Linux RPM links. For 32-bit CentOS, copy the "Linux RPM" link; for 64-bit CentOS, opt for "Linux x64 RPM."

In your terminal, download the installation package using the following command. If `wget` is not installed, obtain it first with `yum install wget`. Replace the link below with the current Java version link.

    wget http://javadl.sun.com/webapps/download/AutoDL?BundleId=33879

Rename and extract the downloaded file with the subsequent command:

            mv jre* jre-install.bin
            chmod +x jre-install.bin
           ./jre-install.bin

Upon prompt, agree to the Java license terms by typing "yes" to proceed. The installation will generate and install a Java RPM package on your server. Confirm the installation with:

            java -version

A confirmation message in the terminal should indicate the installed Java version.

## Adjust Firewall Settings

If a firewall regulates port access on your Utho, ensure the following ports are open:

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

While additional ports may be required later for advanced XMPP services, these ports are default for Openfire.

## Install Openfire

Proceed to the download page for the [Openfire RTC server](http://www.igniterealtime.org/downloads/index.jsp#openfire) and select the RPM package link. You'll be directed to a page for manual download, which you can copy to your clipboard. Employ `wget` on your Utho to fetch the package, substituting the command with the current version link.

    wget http://www.igniterealtime.org/downloadServlet?filename=openfire/openfire-3.6.4-1.i386.rpm

Proceed with the software installation using `rpm`

          rpm -i downloadServlet?filename=openfire*.rpm

Then, access the configuration file `/etc/openfire/openfire.xml`. Within the <interface> section, substitute the Utho's public IP address, and ensure to remove the `<!-- -->` comment markers enclosing this section.

          {{< file "/opt/openfire/conf/openfire.xml" xml >}}
          <interface>12.34.56.78</interface>

           {{< /file >}}

Restart Openfire with the following command:

           /etc/rc.d/init.d/openfire restart

This completes the initial installation steps for Openfire. Next, we'll continue with configuration through a web browser.

## Configure Openfire
Access your Utho's IP address or FQDN (fully qualified domain name) in your browser, appending port 9090. For instance, if your Utho's IP address is "12.34.56.78", navigate to http://12.34.56.78:9091. Upon doing so, you'll encounter a language selection screen resembling the following:

Following that, you'll be prompted to set up your domain and administration ports. Employ the fully qualified domain name assigned to your Utho in DNS. For additional guidance on [configuring DNS with the Utho Manager](/docs/products/networking/dns-manager/).

Navigate your browser to your Utho's IP address or FQDN (fully qualified domain name, if DNS points to your Utho's IP) on port 9090. For instance, if the Utho's IP were "12.34.56.78", access http://12.34.56.78:9091. You'll encounter a language selection screen akin to this:

Next, configure your domain and administration ports. Utilize the fully qualified domain name assigned to your Utho in DNS. Opt for Openfire's internal database for account management, or link to an external database.

User profiles can be stored in the server database or fetched from LDAP or Clearspace. Most users opt for the default setting.

Specify the email address of the default administrative user and establish a robust password.

Post completion of the initial web-based setup, restart the Openfire server before logging in with the default **"admin"** user account.

                 /etc/rc.d/init.d/openfire restart

Should you encounter difficulties logging in with the newly created credentials, utilize "admin/admin" as the username/password. Immediate credential update is recommended for security. Congratulations! Openfire RTC server is now successfully installed on CentOS 5.
