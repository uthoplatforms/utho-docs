---
slug: Setting-Up-Prosody-XMPP-Server-on-Ubuntu-10-10-Maverick
deprecated: true
description: 'Learn how to effortlessly install, configure, and set up Prosody, a nimble XMPP server, on Ubuntu 10.10 (Maverick) with this comprehensive guide'
keywords: ["prosody", "prosody ubuntu lucid", "prosody.im", "xmpp", "real time messaging", "lua"]
tags: ["ubuntu"]
modified: 2024-05-08
modified_by:
  name: Utho
published: 2024-05-08
title: 'Install Prosody XMPP Server on Ubuntu 10.10'
relations:
    platform:
        key: how-to-install-prosody
        keywords:
            - distribution: Ubuntu 10.10
authors: ["Utho"]
---
Prosody, a lightweight XMPP/Jabber server written in Lua, offers simplicity and efficiency. With lower resource requirements compared to its counterparts, Prosody is tailored for easy configuration and operation. While [ejabberd](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/) or [OpenFire](/docs/guides/install-openfire-on-ubuntu-12-04-for-instant-messaging/) might suit larger applications, Prosody shines as a resource-efficient solution for most independent and small-scale uses. It's an excellent choice for running XMPP servers for small user bases or for XMPP development.

## Adding Software Repositories

Prosody's developers provide dedicated software repositories for Debian and Ubuntu to streamline distribution of current software versions. To access these repositories, append a line to your `/etc/apt/sources.list` file.

         {{< file "/etc/apt/sources.list" >}}
         deb http://packages.prosody.im/debian maverick main

         {{< /file >}}

To download the public key for the Prosody package repository, simply execute the following `wget` command. If you don't have `wget` installed, you can do so by running apt-get install `wget`. This step ensures authentication and package verification.

    wget http://prosody.im/files/prosody-debian-packages.key -O- | apt-key add -

To refresh the package database, use the following command:

          apt-get update
          apt-get upgrade

## Install Prosody

Once the repository is enabled, proceed to install the Prosody server using:

    apt-get install prosody liblua5.1-sec0

After `apt` completes the installation, Prosody, with TLS/SSL support, will be successfully installed and ready for configuration. Utilize Prosody's init script to manage the XMPP server with commands such as start, stop, or restart.

              /etc/init.d/prosody reload
              /etc/init.d/prosody start
              /etc/init.d/prosody stop
              /etc/init.d/prosody restart

## Configure Prosody Server

The configuration file for Prosody can be found at `/etc/prosody/prosody.cfg.lua`, written in Lua syntax.

In Lua, comments are marked by two hyphen characters (--). The default configuration includes basic instructions in Lua syntax, aiding those unfamiliar with the language.

To enable Prosody to handle XMPP/jabber services for multiple domains, insert a line in the following format into the configuration file, defining each virtual host as needed.

       {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
        VirtualHost "example.com"
        VirtualHost "example.com"
        VirtualHost "staff.example.com"

{{< /file >}}

Following a `VirtualHost` line there are generally a series of host-specific configuration options. If you want to set options for all hosts, add these options before the first `VirtualHost` declaration in your configuration file. For instance, to ensure that Prosody behaves like a proper Linux server daemon make sure that the `posix;` option is included in the `modules_enabled = { }` table.


{{< file "/etc/prosody/prosody.cfg.lua" lua >}}
modules_enabled = {
                  -- [...]
                  "posix";
                  -- [...]
                  }

{{< /file >}}

Make sure the modules_enabled table incorporates crucial global modules for fundamental functionality.

To disable a virtual host without removing it from the configuration file, add the following line to its respective section:

           {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
           enabled = false

           {{< /file >}}

For server administration, adhere to the following format to specify administrators in your `prosody.cfg.lua` file.

           {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
           admins = { "admin1@example.com", "admin2@example.com" }

           {{< /file >}}

To add server-wide administrators, add entries to the `admins` section, as above, in the global section of the configuration file. To grant specific users more granular control to administer particular hosts, you can add an `admins` line, or more properly tables in Lua, to specific hosts.

For enabling legacy SSL/TLS support, ensure the following entry in the `modules_enabled` is activated:

            {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
            modules_enabled = {
                  -- [...]
                  "legacyauth";
                  -- [...]
                  }

            legacy_ssl_ports = { 5223 }

            {{< /file >}}

Don't forget to reload the Prosody server's configuration after any modifications to your `/etc/prosody/prosody.cfg.lua` file, using the following command:

             /etc/init.d/prosody restart

## XMPP Federation and DNS

To ensure that your Prosody instance will federate properly with the rest of the XMPP network, particularly with Google's "GTalk" service (i.e. the ["@gmail.com](mailto:"@gmail.com)" chat tool,) we must set the SRV records for the domain to point to the server where the Prosody instance is running. We need three records, which can be created in the DNS Management tool of your choice:

1.  Service: `_xmpp-server` Protocol: TCP Port: 5269
2.  Service: `_xmpp-client` Protocol: TCP Port: 5222
3.  Service: `_jabber` Protocol: TCP Port: 5269

The "target" of the SRV record should point to the publicly routable hostname for the machine, with both the priority and weight set to `0`.

## Enabling Components

In the XMPP landscape, various services are offered as components, facilitating customization within a basic framework. A prime example is the Multi-User Chat (MUC) functionality. To activate MUC services in Prosody, add a line like the following to your `/etc/prosody/prosody.cfg.lua` file:

            {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
            Component "conference.example.com" "muc"

            {{< /file >}}

In this scenario, `conference.example.com` serves as the domain for MUC rooms and necessitates a corresponding "[DNS A record,](/docs/guides/dns-overview/)" pointing to the IP Address of the Prosody instance. MUCs are identified as JIDs (Jabber IDs) at this hostname. For instance, the "rabbits" MUC hosted by this server would be located at `rabbits@conference.example.com`.

While MUC is internally provided by Prosody, other components, such as transports to other services, operate on an external interface. Each external component has its own hostname and provides a secret key for authentication by the central server. For example, see the "aim.example.com" component.

           {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
           Component "aim.example.com"
           component_secret = "mysecretcomponentpassword"

           {{< /file >}}

External components must be installed and configured separately from Prosody.

By default, Prosody listens for connections from components on the localhost interface (i.e., on the `127.0.0.2` interface). If you're connected to external resources running on an alternate interface, you should specify the following variables in the global section of the configuration file before the first VirtualHost declaration.

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          component_interface = "192.168.0.10"
          component_ports = { 8888, 8887 }

          {{< /file >}}

## Using prosodyctl

The XMPP protocol supports "in-band" registration, where users can register for accounts with your server via the XMPP interface. However, this is often an undesirable function as it doesn't permit the server administrator the ability to moderate the creation of new accounts and can lead to spam-related problems. As a result, Prosody has this functionality disabled by default. While you can enable in-band registration, we recommend using the `prosodyctl` interface at the terminal prompt.

If you're familiar with `ejabberdctl` from [ejabberd,](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/) `prosodyctl` functions similarly.

To register a user (e.g., lollipop@example.com), use the following command:

         prosodyctl adduser lollipop@example.com

To set the password for this account, use:

         prosodyctl passwd lollipop@example.com

To remove a user, issue:

         prosodyctl deluser lollipop@example.com

Additionally, `prosodyctl` provides server status reports via:

        prosodyctl status

Note: `prosodyctl` commands require root privileges, except for the user under which Prosody runs (not recommended).
