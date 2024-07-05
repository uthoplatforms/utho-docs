---
slug: "Unlock the XMPP Magic: Installing Prosody Server on Ubuntu 9.10 (Karmic)"
deprecated: true
description: "Mastering Prosody: Your Comprehensive Guide to Installing and Using the Lightweight XMPP Server on Ubuntu 9.10 (Karmic)"
keywords: ["prosody", "prosody ubuntu karmic", "prosody.im", "xmpp", "real time messaging", "lua"]
tags: ["ubuntu"]
modified: 2024-05-08
modified_by:
  name: Utho
published: 2024-05-08
title: 'Installing Prosody XMPP Server on Ubuntu 9.10 (Karmic)'
relations:
    platform:
        key: how-to-install-prosody
        keywords:
            - distribution: Ubuntu 9.10
---
Prosody, a lightweight XMPP/Jabber server written in Lua, offers simplicity and efficiency. With lower resource requirements compared to its counterparts, Prosody is tailored for easy configuration and operation. While [ejabberd](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/) or [OpenFire](/docs/guides/install-openfire-on-ubuntu-12-04-for-instant-messaging/) might suit larger applications, Prosody shines as a resource-efficient solution for most independent and small-scale uses. It's an excellent choice for running XMPP servers for small user bases or for XMPP development.

Before diving into Prosody's installation and configuration, ensure you have a running and up-to-date installation of Ubuntu Karmic.

## Adding Software Repositories

Prosody's developers provide dedicated software repositories for Debian and Ubuntu to streamline distribution of current software versions. To access these repositories, append a line to your `/etc/apt/sources.list` file. For Karmic, this also involves enabling the universe repository. Modify your `/etc/apt/sources.list` file accordingly by removing the hash symbol in front of the universe lines:

         {{< file "/etc/apt/sources.list" >}}

## main & restricted repositories

deb http://us.archive.ubuntu.com/ubuntu/ karmic main restricted
deb-src http://us.archive.ubuntu.com/ubuntu/ karmic main restricted

deb http://security.ubuntu.com/ubuntu karmic-security main restricted
deb-src http://security.ubuntu.com/ubuntu karmic-security main restricted

## universe repositories

deb http://us.archive.ubuntu.com/ubuntu/ karmic universe
deb-src http://us.archive.ubuntu.com/ubuntu/ karmic universe
deb http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe
deb-src http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe

deb http://security.ubuntu.com/ubuntu karmic-security universe
deb-src http://security.ubuntu.com/ubuntu karmic-security universe

{{< /file >}}


At the end of the file, ensure to add this line for the Prosody repository:

        {{< file "/etc/apt/sources.list" >}}
        deb http://packages.prosody.im/debian karmic main

        {{< /file >}}

Now, to fetch the public key for the Prosody package repository, execute the following wget command. If wget isn't installed, you can do so by running apt-get install wget:

    wget http://prosody.im/files/prosody-debian-packages.key -O- | apt-key add -

After acquiring the key, update the package database by issuing the following command:

           apt-get update
           apt-get upgrade

## Install Prosody

With the repository configured correctly, we're all set to install the Prosody server. Utilize the following command:


           apt-get install prosody liblua5.1-sec0

Once `apt` completes the installation, the Prosody server, equipped with TLS/SSL support, will be successfully installed and ready for configuration. Prosody offers an init script that facilitates reloading the configuration file, starting, stopping, or restarting the XMPP server. Issue one of the following commands as needed:

          /etc/init.d/prosody reload
          /etc/init.d/prosody start
          /etc/init.d/prosody stop
          /etc/init.d/prosody restart

## Configure Prosody Server

The configuration file for Prosody resides in `/etc/prosody/prosody.cfg.lua` and is scripted in Lua syntax.

In Lua, comments (lines disregarded by the interpreter) are prefixed with two hyphen characters (e.g., `--`). The default configuration includes basic Lua-syntax instructions, which can serve as a guide if you're new to the language.

To enable Prosody to provide XMPP/jabber services for multiple domains, insert a line in the following format into the configuration file. This example defines three virtual hosts.

       {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
       VirtualHost "example.com"
       VirtaulHost "example.com"
       VirtualHost "staff.example.com"

       {{< /file >}}

Following a `VirtualHost` line, you'll typically find host-specific configuration options. If you wish to configure options for all hosts, add these options before the first VirtualHost declaration in your configuration file. For example, to ensure Prosody functions as a standard Linux server daemon, ensure the `posix`; option is listed within the `modules_enabled = { }` table

        {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
        modules_enabled = {
                  -- [...]
                  "posix";
                  -- [...]
                  }

         {{< /file >}}

It's crucial to include several global modules in this table to provide basic functionality.

To deactivate a virtual host without removing it from your configuration file, add the following line to its respective section of the file:

        {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
        enabled = false

        {{< /file >}}

To specify administrators for your Prosody server, adhere to the following format in your `prosody.cfg.lua` file.

        {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
        admins = { "admin1@example.com", "admin2@example.com" }

         {{< /file >}}

For server-wide administrators, add entries to the `admins` section, located in the global portion of the configuration file. To grant specific users more nuanced control over administering particular hosts, you can include an admins line, or more accurately, Lua tables, for specific hosts.

If you require legacy SSL/TLS support, ensure that the following entry in the `modules_enabled` is activated:

       {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
        modules_enabled = {
                  -- [...]
                  "legacyauth";
                  -- [...]
                  }

        legacy_ssl_ports = { 5223 }

        {{< /file >}}

After any modifications to your `/etc/prosody/prosody.cfg.lua` file, remember to reload the Prosody server's configuration by issuing the following command:

          /etc/init.d/prosody reload

## XMPP Federation and DNS

To guarantee seamless federation of your Prosody instance with the wider XMPP network, particularly with services like Google's "GTalk" (i.e., the "["@gmail.com](mailto:"@gmail.com)" chat tool), you must configure SRV records for the domain to point to the server where the Prosody instance is hosted. Three records are needed, which can be set up using your preferred DNS Management tool:

1.  Service: `_xmpp-server` Protocol: TCP Port: 5269
2.  Service: `_xmpp-client` Protocol: TCP Port: 5222
3.  Service: `_jabber` Protocol: TCP Port: 5269

The "target" of each SRV record should direct to the publicly routable hostname for that machine (e.g., "username.example.com"). Both the priority and weight should be set to `0`.

## Enabling Components

In the XMPP ecosystem, many services are delivered through components, allowing for enhanced customization within a basic framework. A prime example is the MUC (multi-user chat) functionality. To activate MUC services in Prosody, include a line like the following in your `/etc/prosody/prosody.cfg.lua` file.

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          Component "conference.example.com" "muc"

          {{< /file >}}

In this example, `conference.example.com` serves as the domain housing the MUC rooms and requires a corresponding "[DNS A record](/docs/guides/dns-overview/)" pointing to the IP Address where the Prosody instance is hosted. MUCs are identified as JIDs (Jabber IDs) at this hostname; for instance, the "rabbits" MUC hosted by this server would be accessible at `rabbits@conference.example.com`.

Unlike many other common components in the XMPP realm, Prosody provides MUC internally. Other components, such as transports to other services, operate on an external interface. Each external component has its own hostname and provides a secret key for the central server's authentication. Consider the "aim.example.com" component as an example.

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          Component "aim.example.com"
          component_secret = "mysecretcomponentpassword"

          {{< /file >}}

Note that external components will need to be installed and configured independently of Prosody.

Typically, Prosody listens for connections from components on the localhost interface (i.e. on the `127.0.0.1` interface;). If you're connected to external resources that are running on an alternate interface, specify the following variables as appropriate in the global section of the configuration file before the first `VirtualHost` declaration.

         {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
         component_interface = "192.168.0.10"
         component_ports = { 8888, 8887 }

        {{< /file >}}

## Using prosodyctl

The XMPP protocol supports "in-band" registration, allowing users to register for accounts via the XMPP interface. However, this functionality is often undesirable as it lacks server administrator moderation and can lead to spam-related issues. Therefore, Prosody disables this feature by default. While you can enable in-band registration, it's recommended to use the `prosodyctl` interface in the terminal.

If you're familiar with the ejabberdctl interface from [ejabberd,](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/), `prosodyctl` mirrors its functionality as closely as possible.

To register a user, such as `lollipop@example.com`, using prosodyctl, issue the following command:

            prosodyctl adduser lollipop@example.com

To set the password for this account, use the following command and enter the password as prompted:

            prosodyctl passwd lollipop@example.com

To delete this user, issue the following command:

            prosodyctl deluser lollipop@example.com

Additionally, `prosodyctl` can provide a server status report in response to the following command:

            prosodyctl status

Keep in mind that all `prosodyctl` commands require root privileges unless you're logged in as the same user that Prosody runs under (not recommended).
