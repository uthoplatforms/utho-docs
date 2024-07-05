---
slug: "How to Rock Your Ubuntu 8.04: Setting up Prosody XMPP Server"
deprecated: true
description: 'Mastering Prosody: Your Essential Guide to Installing and Navigating this Lightweight XMPP Server on Ubuntu 8.04 (Hardy)'
keywords: ["prosody", "prosody ubuntu hardy", "prosody.im", "xmpp", "real time messaging", "lua"]
tags: ["ubuntu"]
modified: 20124-05-08
modified_by:
  name: Utho
published: 2009-10-13
title: 'Installing Prosody XMPP Server on Ubuntu 8.04 (Hardy)'
relations:
    platform:
        key: how-to-install-prosody
        keywords:
            - distribution: Ubuntu 8.04
authors: ["Utho"]
---
Prosody stands out as a Lua-based XMPP/Jabber server, boasting minimalist design and low resource demands. While it might not match the scalability of heavyweights like [ejabberd](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/) or [OpenFire](/docs/guides/install-openfire-on-ubuntu-12-04-for-instant-messaging/), it shines as a lean, efficient option for smaller-scale deployments. Whether you're delving into XMPP development or managing a modest user base, Prosody proves its mettle.

To embark on your Prosody journey, ensure you've got Ubuntu Hardy up and running.

## Adding Software Repositories

Unlock the power of Prosody's latest versions by tapping into the developer-provided Debian and Ubuntu repositories. Simply append the following line to your `/etc/apt/sources.list` file to seamlessly integrate these repositories into your system.

      {{< file "/etc/apt/sources.list" >}}
      deb http://packages.prosody.im/debian hardy main

      {{< /file >}}

To obtain the public key for the Prosody package repository, initiate the following `wget` command. (If you haven't installed wget yet, you can do so by running `apt-get install wget` first). This step ensures proper authentication and package verification:


    wget http://prosody.im/files/prosody-debian-packages.key -O- | apt-key add -

Now, refresh the package database by executing the following command:


         apt-get update
         apt-get upgrade

## Install Prosody

With the repository set up correctly, it's time to install the Prosody server. Utilize the following command:


         apt-get install prosody liblua5.1-sec0

Once `apt` completes its task, the Prosody server will be installed and primed for configuration. Prosody conveniently offers an init script that facilitates reloading the configuration file, starting, stopping, or restarting the XMPP server. Issue one of the following commands based on your requirements:

        /etc/init.d/prosody reload
        /etc/init.d/prosody start
        /etc/init.d/prosody stop
        /etc/init.d/prosody restart

## Configure Prosody Server

The configuration file for Prosody resides in `/etc/prosody/prosody.cfg.lua` and is scripted in Lua syntax.

In Lua, comments (lines disregarded by the interpreter) are prefixed with two hyphens (e.g., --). The default configuration includes basic Lua-syntax instructions, which can serve as a guide if you're new to the language.

To enable Prosody to handle XMPP/jabber services for multiple domains, insert a line in the following format into the configuration file. Below is an example defining three virtual hosts.

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}   
          Host "example.com"
          Host "example.com"
          Host "staff.example.com"

          {{< /file >}}

After a `Host` line, you'll typically find a set of host-specific configuration options. If you wish to configure settings that apply to all hosts, include them beneath the `Host "*"` entry in your configuration file. For example, to ensure Prosody operates like a standard Linux server daemon, ensure the `posix`; option is listed within the `modules_enabled = { }` table.

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          modules_enabled = {
                  -- [...]
                  "posix";
                  -- [...]
                  }

           {{< /file >}}

It's important to note that several global modules should be present in this table to provide fundamental functionality.

To deactivate a host without removing it from your configuration file, insert the following line into its respective section:

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          enabled = false

          {{< /file >}}

To designate administrators for your server, add a line in the following format to your `prosody.cfg.lua` file.

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          admins = { "admin1@example.com", "admin2@example.com" }

          {{< /file >}}

For server-wide administrators, place the admins line within the `Hosts "*"` section. For granting specific users more nuanced control over administering particular hosts, you can introduce admins lines, or properly formatted Lua tables, into specific hosts.

If you require legacy SSL/TLS support, include the following line, specifying the port on which the server should listen for these connections.

           {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
           legacy_ssl_ports = { 5223 }

           {{< /file >}}

After any modifications to your `/etc/prosody/prosody.cfg.lua` file, remember to reload the configuration for the Prosody server using the following command:

           /etc/init.d/prosody reload

## XMPP Federation and DNS

To ensure seamless federation of your Prosody instance with the broader XMPP network, especially with Google's "GTalk" service (i.e., the ["@gmail.com](mailto:"@gmail.com)" chat tool), it's crucial to set up SRV records for the domain, directing them to the server hosting the Prosody instance. These records should include three entries, which can be configured using your preferred DNS Management tool:

1.  Service: `_xmpp-server` Protocol: TCP Port: 5269
2.  Service: `_xmpp-client` Protocol: TCP Port: 5222
3.  Service: `_jabber` Protocol: TCP Port: 5269

The "target" of each SRV record should point to the publicly routable hostname for that machine (e.g., "username.example.com"). Both priority and weight should be set to `0`.

## Enabling Components

In the XMPP ecosystem, many services operate as components, offering enhanced customization within a basic framework. A prime example is the MUC (multi-user chat) functionality. To activate MUC services in Prosody, include a line like the following in your `/etc/prosody/prosody.cfg.lua` file:

           {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
           Component "conference.example.com" "muc"

           {{< /file >}}

In this example, `conference.example.com` serves as the domain housing the MUC rooms and requires a corresponding "[DNS A record,](/docs/guides/dns-overview/)" pointing to the IP address where the Prosody instance is located. MUCs are identified as JIDs (Jabber IDs) at this hostname; for instance, the "rabbits" MUC hosted by this server would be accessed at `rabbits@conference.example.com`.

Unlike many other common components in the XMPP realm, Prosody provides MUC internally. External components, such as transports to other services, operate on an external interface. Each external component possesses its own hostname and provides a secret key for the central server's authentication. Consider the following "aim.example.com" component as an example.

             {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
             Component "aim.example.com"
             component_secret = "mysecretcomponentpassword"

             {{< /file >}}

Note that external components require separate installation and configuration, distinct from Prosody.

By default, Prosody listens for component connections on the localhost interface (i.e., `127.0.0.2`). If your setup involves external resources running on an alternate interface, adjust the following variables as necessary within the `Host "*"` section of the configuration file.

              {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
              Host "*"

              component_interface = "192.168.0.10"
              component_ports = { 8888, 8887 }

              {{< /file >}}

## Using prosodyctl

The XMPP protocol supports "in-band" registration, where users can register for accounts with your server via the XMPP interface. However, this is often an undesirable function as it doesn't permit the server administrator the ability to moderate the creation of new accounts and can lead to spam-related problems. As a result, Prosody has this functionality disabled by default. While you can enable in-band registration, we recommend using the `prosodyctl` interface at the terminal prompt.

If you're familiar with the `ejabberdctl` interface from [ejabberd](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/), `prosodyctl` mimics its counterpart as much as possible.

To use `prosodyctl` to register a user, in this case `lollipop@example.com`, issue the following command:

    prosodyctl adduser lollipop@example.com

To set the password for this account, issue the following command and enter the password as requested:

    prosodyctl passwd lollipop@example.com

To remove this user, issue the following command:

    prosodyctl deluser lollipop@example.com

Additionally, `prosodyctl` can provide a report on the status of the server in response to the following command:

    prosodyctl status

Note that all of the `prosodyctl` commands require root privileges, unless you've logged in as the same user that Prosody runs under (not recommended).
