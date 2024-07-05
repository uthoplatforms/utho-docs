---
slug: "Unleash Seamless Communication: Installing Prosody XMPP Server on Ubuntu 10.04 Lucid"
deprecated: true
description: 'Discover the steps to seamlessly install, configure, and set up a basic configuration of Prosody, a lightweight XMPP server, on Ubuntu 10.04 (Lucid) with this comprehensive guide'
keywords: ["prosody", "prosody ubuntu lucid", "prosody.im", "xmpp", "real time messaging", "lua"]
tags: ["ubuntu"]
modified: 2024-05-08
modified_by:
  name: Utho
published: 2024-05-08
title: 'Installing Prosody XMPP Server on Ubuntu 10.04 (Lucid)'
relations:
    platform:
        key: how-to-install-prosody
        keywords:
            - distribution: Ubuntu 10.04
authors: ["Utho"]
---

Prosody, a lightweight XMPP/Jabber server written in Lua, offers simplicity and efficiency. With lower resource requirements compared to its counterparts, Prosody is tailored for easy configuration and operation. While [ejabberd](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/) or [OpenFire](/docs/guides/install-openfire-on-ubuntu-12-04-for-instant-messaging/) might suit larger applications, Prosody shines as a resource-efficient solution for most independent and small-scale uses. It's an excellent choice for running XMPP servers for small user bases or for XMPP development.

Before diving into Prosody's installation and configuration, ensure you have a running and up-to-date installation of Ubuntu Karmic.

## Adding Software Repositories

Prosody's developers provide dedicated software repositories for Debian and Ubuntu to streamline distribution of current software versions. To access these repositories, append a line to your `/etc/apt/sources.list` file. For Karmic, this also involves enabling the universe repository. Modify your `/etc/apt/sources.list` file accordingly by removing the hash symbol in front of the universe lines:

         {{< file "/etc/apt/sources.list" >}}
         deb http://packages.prosody.im/debian lucid main

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

For global administration, populate the admins section within the global configuration file. For more specific user privileges, utilize the admins line or Lua tables for individual hosts.

To enable legacy SSL/TLS support, confirm that the specified entry in the `modules_enabled` table is activated.

         {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          modules_enabled = {
                  -- [...]
                  "legacyauth";
                  -- [...]
                  }

         legacy_ssl_ports = { 5223 }

         {{< /file >}}

Remember to reload the Prosody server configuration each time you make changes to the `/etc/prosody/prosody.cfg.lua` file by executing the command:

          /etc/init.d/prosody restart

## XMPP Federation and DNS

To ensure seamless federation of your Prosody instance with the wider XMPP network, particularly with services like Google's "GTalk" (e.g., ["@gmail.com](mailto:"@gmail.com)", it's crucial to set up SRV records for your domain. These records should point to the server hosting your Prosody instance.

1.  Service: `_xmpp-server` Protocol: TCP Port: 5269
2.  Service: `_xmpp-client` Protocol: TCP Port: 5222
3.  Service: `_jabber` Protocol: TCP Port: 5269

Create three SRV records in your DNS Management tool, with the "target" pointing to the publicly routable hostname of your machine, and both the priority and weight set to `0`.

## Enabling Components

Within the XMPP ecosystem, various services are modularized into components, facilitating customization within a flexible framework. One prevalent example is the Multi-User Chat (MUC) functionality. To activate MUC services in Prosody, simply include a line like the following in your `/etc/prosody/prosody.cfg.lua` file:

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          Component "conference.example.com" "muc"

          {{< /file >}}

In this scenario, `conference.example.com` serves as the domain housing the MUC rooms, necessitating a corresponding "[DNS A record,](/docs/guides/dns-overview/)" pointing to the IP Address of the running Prosody instance. MUCs are identifiable as JIDs (Jabber IDs) under this hostname. For example, a MUC named "rabbits" hosted by this server would be accessible at `rabbits@conference.example.com`.

Unlike many other typical components within the XMPP ecosystem, MUC is internally handled by Prosody. However, various other components, such as transports to different services, operate on an external interface. Each external component possesses its unique hostname and furnishes a secret key enabling the central server's authentication. For instance, consider the "aim.example.com" component as an illustration.

           {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
           Component "aim.example.com"
           component_secret = "mysecretcomponentpassword"

           {{< /file >}}

External components must be installed and configured separately from Prosody.

By default, Prosody listens for connections from components on the localhost interface (i.e., on the `127.0.0.2` interface). If your external resources operate on an alternate interface, ensure to specify the relevant variables in the global section of the configuration file before the first `VirtualHost` declaration.

        {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
        component_interface = "192.168.0.10"
        component_ports = { 8888, 8887 }

        {{< /file >}}

## Using prosodyctl

The XMPP protocol offers "in-band" registration, enabling users to create accounts directly via the XMPP interface. However, this feature is often discouraged as it lacks moderation control, potentially leading to spam issues. Hence, Prosody disables this functionality by default. While it can be enabled, we recommend utilizing the prosodyctl interface for user registration.

Similar to ejabberdctl in [ejabberd,](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/), `prosodyctl` offers a comparable interface.

To register a user, such as `lollipop@example.com`, execute the following command:

           prosodyctl adduser lollipop@example.com

To set the password for this account, enter the following command and input the desired password when prompted:

           prosodyctl passwd lollipop@example.com

To remove this user, use the following command:

          prosodyctl deluser lollipop@example.com

Moreover, `prosodyctl` can generate a server status report by executing the following command:

          prosodyctl status

Please note that all `prosodyctl` commands necessitate root privileges, unless you've logged in as the same user that Prosody operates under (not recommended).
