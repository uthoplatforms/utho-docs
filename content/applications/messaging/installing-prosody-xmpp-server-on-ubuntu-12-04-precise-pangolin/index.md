---
slug: "Unleash Your Chat Powers: Setting Up Prosody XMPP Server on Ubuntu 12.04 Precise Pangolin"
deprecated: true
description: '"Discover the Easy Way: Get Started with Prosody XMPP Server on Ubuntu 12.04 (Lucid) – Your Complete Installation and Usage Guide'
keywords: ["prosody", "prosody ubuntu", "prosody.im", "xmpp", "real time messaging", "lua"]
tags: ["ubuntu"]
modified: 2024-05-09
modified_by:
  name: Utho
published: 2024-05-09
title: 'Installing Prosody XMPP Server on Ubuntu 12.04 (Precise Pangolin)'
relations:
    platform:
        key: how-to-install-prosody
        keywords:
            - distribution: Ubuntu 12.04
authors: ["Utho"]
---
"Prosody, a lightweight XMPP/Jabber server written in Lua, offers simplicity and efficiency. Compared to alternatives like [Ejabberd](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/) or [OpenFire](/docs/guides/install-openfire-on-ubuntu-12-04-for-instant-messaging/), it consumes fewer resources, making it ideal for independent or small-scale applications. Whether you're managing a small user base or diving into XMPP development, Prosody shines.

Before diving into installation and configuration, ensure you're running an updated Ubuntu 12.04 (Precise Pangolin) instance and have followed our guide on Setting Up and Securing a Compute Instance, logging in via SSH as root.

## Adding Software Repositories

To access the latest Prosody versions conveniently, developers provide software repositories for Debian and Ubuntu. Simply append the following line to your `/etc/apt/sources.list file:"`

            {{< file "/etc/apt/sources.list" >}}
            deb http://packages.prosody.im/debian precise main

            {{< /file >}}

To fetch the public key for the Prosody package repository, run the following `wget` command. If wget isn't installed, you can get it by running apt-get install wget first to ensure smooth authentication and package verification:

    wget http://prosody.im/files/prosody-debian-packages.key -O- | apt-key add -

Refresh the package database with this command:

            apt-get update
            apt-get upgrade

## Install Prosody

Now that the repository is set up, let's proceed with installing the Prosody server. Execute the following command:

    apt-get install prosody lua-sec-prosody

Once `apt` completes the installation, Prosody, along with TLS/SSL support, will be installed and ready for configuration. Prosody provides an init script for easy management, allowing you to reload configurations, start, stop, or restart the XMPP server. Use one of the following commands as needed:

          /etc/init.d/prosody reload
          /etc/init.d/prosody start
          /etc/init.d/prosody stop
          /etc/init.d/prosody restart

## Configure Prosody Server

The configuration file for Prosody can be found at `/etc/prosody/prosody.cfg.lua` and is written in Lua syntax.

Note: In Lua, comments (lines ignored by the interpreter) start with two hyphen characters (e.g., --). The default configuration contains basic instructions in Lua syntax, which can be handy if you're new to the language.

To enable Prosody to serve XMPP/jabber services for multiple domains, add a line following this format to the configuration file. Below is an example defining three virtual hosts.

         {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
         VirtualHost "example.com"
         VirtaulHost "example.com"
         VirtualHost "staff.example.com"

         {{< /file >}}

After a `VirtualHost` line, you'll typically find a set of host-specific configuration options. If you wish to apply settings across all hosts, place these options before the first VirtualHost declaration in your configuration file. For example, to ensure Prosody operates as a standard Linux server daemon, ensure the posix; option is listed in the `modules_enabled = { }` table.

           {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
           modules_enabled = {
                  -- [...]
                  "posix";
                  -- [...]
                  }

            {{< /file >}}

It's essential to include various global modules in this table to ensure basic functionality.

To deactivate a virtual host without removing it from your configuration file, include the following line within its respective section:

             {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
             enabled = false

             {{< /file >}}

To designate administrators for your server, adhere to this format within your `prosody.cfg.lua` file:

              {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
              admins = { "admin1@example.com", "admin2@example.com" }

              {{< /file >}}

For server-wide administrators, populate entries within the `admins` section as shown above, within the global configuration section. To grant specific users more tailored control over administering particular hosts, you can add an admins line, or more accurately, Lua tables, to specific hosts.

Should you require legacy SSL/TLS support, ensure the following entry in the `modules_enabled` is activated:

               {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
                modules_enabled = {
                  -- [...]
                  "legacyauth";
                  -- [...]
                  }

                legacy_ssl_ports = { 5223 }

                {{< /file >}}

Do not forget to reload the configuration for the Prosody server after making any changes to your `/etc/prosody/prosody.cfg.lua` file, by issuing the following command:

    /etc/init.d/prosody restart

## XMPP Federation and DNS

To ensure that your Prosody instance will federate properly with the rest of the XMPP network, particularly with Google's "GTalk" service (i.e. the ["@gmail.com](mailto:"@gmail.com)" chat tool,) we must set the SRV records for the domain to point to the server where the Prosody instance is running. We need three records, which can be created in the DNS Management tool of your choice:

1.  Service: `_xmpp-server` Protocol: TCP Port: 5269
2.  Service: `_xmpp-client` Protocol: TCP Port: 5222
3.  Service: `_jabber` Protocol: TCP Port: 5269

The "target" of the SRV record should point to the publicly routable hostname for that machine (e.g. "username.example.com"). The priority and weight should both be set to `0`.

## Enabling Components

In the XMPP world, many services are provided in components, which allows for greater ease of customization within a basic framework. A common example of this is the MUC or multi-user chat functionality. To enable MUC services in Prosody you need to add a line like the following to your `/etc/prosody/prosody.cfg.lua` file.

            {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
            Component "conference.example.com" "muc"

            {{< /file >}}

In this example, `conference.example.com` is the domain housing the MUC rooms and requires a corresponding "[DNS A record,](/docs/guides/dns-overview/)" pointing to the IP address of the Prosody instance. MUCs will be identified as JIDs (Jabber IDs) at this hostname. For instance, the "rabbits" MUC hosted by this server would be accessed at `rabbits@conference.example.com`.

Unlike MUC, which is internally provided by Prosody, many other common components in the XMPP world operate externally. Each external component has its hostname and provides a secret key for authentication. See the example of the "aim.example.com" component below.

             {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
              Component "aim.example.com"
              component_secret = "mysecretcomponentpassword"

              {{< /file >}}

Note that external components require separate installation and configuration from Prosody.

By default, Prosody listens for component connections on the localhost interface (i.e., `127.0.0.2`). If you're connected to external resources running on an alternate interface, specify the following variables appropriately in the global section of the configuration file before the first `VirtualHost` declaration.

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          component_interface = "192.168.0.10"
          component_ports = { 8888, 8887 }

          {{< /file >}}

## Using prosodyctl

The XMPP protocol supports "in-band" registration, where users can register for accounts with your server via the XMPP interface. However, this is often an undesirable function as it doesn't permit the server administrator the ability to moderate the creation of new accounts and can lead to spam-related problems. As a result, Prosody has this functionality disabled by default. While you can enable in-band registration, we recommend using the `prosodyctl` interface at the terminal prompt.

If you're familiar with the `ejabberdctl` interface from [ejabberd,](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/) `prosodyctl` mimics its counterpart as much as possible.

To register a user (e.g., `username@example.com`) using `prosodyctl`, execute the following command:

            prosodyctl adduser username@example.com

To set a password for this account, use this command and follow the prompts to enter the password:

           prosodyctl passwd username@example.com

To delete this user, run the following command:

           prosodyctl deluser username@example.com

Additionally, `prosodyctl` can generate a server status report with the following command:

           prosodyctl status

Keep in mind that all `prosodyctl` commands require root privileges unless you're logged in as the same user that Prosody operates under (though this isn't recommended).
