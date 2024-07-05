---
slug: "Unleash the Power of Prosody: Installing XMPP Server on Ubuntu 9.04 (Jaunty)"
deprecated: true
description: 'Installation and basic usage guide for Prosody, a lightweight XMPP server on Ubuntu 9.04 (Jaunty).'
keywords: ["prosody", "prosody ubuntu jaunty", "prosody.im", "xmpp", "real time messaging", "lua"]
tags: ["ubuntu"]
modified: 2024-05-08
modified_by:
  name: Utho
published: 2024-05-08
title: 'Installing Prosody XMPP Server on Ubuntu 9.04 (Jaunty)'
relations:
    platform:
        key: how-to-install-prosody
        keywords:
            - distribution: Ubuntu 9.04
authors: ["Utho"]
---
Prosody, crafted in Lua, stands as a lightweight XMPP/Jabber server, emphasizing simplicity and efficiency. With its minimal resource footprint, Prosody outshines its counterparts, offering easy configuration and seamless operation. While ejabberd](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/) or [OpenFire](/docs/guides/install-openfire-on-ubuntu-12-04-for-instant-messaging/) might be preferable for larger deployments, Prosody reigns supreme for independent and small-scale applications, proving its prowess as a resource-efficient solution. Whether for managing a modest user base or delving into XMPP development, Prosody emerges as a prime candidate.

Before diving into Prosody's installation and configuration, ensure you have Ubuntu Jaunty up to date.

## Adding Software Repositories

Prosody's developers offer dedicated software repositories for Debian and Ubuntu, streamlining distribution of the latest software versions. To access these repositories, append the following line to your `/etc/apt/sources.list` file. For Jaunty, this involves enabling the universe repository. Modify your `/etc/apt/sources.list` file accordingly by removing the hash symbol in front of the universe lines:

          {{< file "/etc/apt/sources.list" >}}

## main & restricted repositories

deb http://us.archive.ubuntu.com/ubuntu/ jaunty main restricted
deb-src http://us.archive.ubuntu.com/ubuntu/ jaunty main restricted

deb http://security.ubuntu.com/ubuntu jaunty-security main restricted
deb-src http://security.ubuntu.com/ubuntu jaunty-security main restricted

## universe repositories

deb http://us.archive.ubuntu.com/ubuntu/ jaunty universe
deb-src http://us.archive.ubuntu.com/ubuntu/ jaunty universe
deb http://us.archive.ubuntu.com/ubuntu/ jaunty-updates universe
deb-src http://us.archive.ubuntu.com/ubuntu/ jaunty-updates universe

deb http://security.ubuntu.com/ubuntu jaunty-security universe
deb-src http://security.ubuntu.com/ubuntu jaunty-security universe

{{< /file >}}

To complete the setup, add the following line to the end of the file for the Prosody repository:


           {{< file "/etc/apt/sources.list" >}}
           deb http://packages.prosody.im/debian jaunty main

           {{< /file >}}

Next, to acquire the public key for the Prosody package repository, execute the following wget command. If wget isn't installed yet, you can do so by running apt-get install wget:

    wget http://prosody.im/files/prosody-debian-packages.key -O- | apt-key add -

Once the key is obtained, refresh the package database with the following command:

            apt-get update
            apt-get upgrade

## Install Prosody

With the repository properly configured, we're now set to install the Prosody server. Execute the following command:

    apt-get install prosody liblua5.1-sec0

Once apt completes its task, the Prosody server will be successfully installed and ready for configuration. Prosody conveniently offers an init script for managing the XMPP server. You can use it to reload the configuration file, start, stop, or restart the server. Issue one of the following commands as needed:

         /etc/init.d/prosody reload
         /etc/init.d/prosody start
         /etc/init.d/prosody stop
         /etc/init.d/prosody restart

## Configure Prosody Server

The configuration file for Prosody can be found at `/etc/prosody/prosody.cfg.lua`, formatted in Lua syntax.

It's worth noting that Lua uses two hyphen characters (--) to denote comments, which are ignored by the interpreter. The default configuration file includes basic instructions in Lua syntax, which can be beneficial for those unfamiliar with the language.

To enable Prosody to serve XMPP/jabber services for multiple domains, insert a line in the following format into the configuration file. The example provided defines three virtual hosts.

        {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
        Host "example.com"
        Host "example.com"
        Host "staff.example.com"

        {{< /file >}}

After a Host line, you'll typically find a series of host-specific configuration options. If you wish to configure options that apply to all hosts, add them below the `Host "*"` entry in your configuration file. For example, to ensure Prosody functions like a standard Linux server daemon, ensure the `posix`; option is listed within the `modules_enabled = { }` table. It's essential to include several global modules in this table to provide basic functionality.

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          modules_enabled = {
                  -- [...]
                  "posix";
                  -- [...]
                  }

          {{< /file >}}

Note that there should be a number of global modules included in this table to provide basic functionality.

To deactivate a host without removing it from your configuration file, insert the following line into its respective section:

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          enabled = false

          {{< /file >}}

To specify administrators for your server, add a line in the following format to your `prosody.cfg.lua` file.

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          admins = { "admin1@example.com", "admin2@example.com" }

          {{< /file >}}

To establish server-wide administrators, include the `admins` line within the `Hosts "*"` section. For finer control over specific hosts, you can assign administrators by adding admins lines or Lua tables to those specific hosts.

If legacy SSL/TLS support is required, insert the following line into the configuration, specifying the port on which the server should listen for these connections.

             {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
             legacy_ssl_ports = { 5223 }

             {{< /file >}}

After any modifications to your `/etc/prosody/prosody.cfg.lua` file, remember to reload the Prosody server's configuration using the following command:

             /etc/init.d/prosody reload

## XMPP Federation and DNS

To ensure seamless federation with the broader XMPP network, especially Google's "GTalk" service (i.e., the "@gmail.com" chat tool), set up SRV records for the domain, directing them to the server hosting the Prosody instance.

1.  Service: `_xmpp-server` Protocol: TCP Port: 5269
2.  Service: `_xmpp-client` Protocol: TCP Port: 5222
3.  Service: `_jabber` Protocol: TCP Port: 5269

Create three records in your DNS Management tool, with the "target" of each SRV record pointing to the publicly routable hostname for that machine (e.g., "username.example.com"). Set both priority and weight to `0`.

## Enabling Components

In the XMPP ecosystem, services are often provided as components, facilitating customization within a basic framework. A prime example is the MUC (multi-user chat) functionality. To activate MUC services in Prosody, add a line like the following to your `/etc/prosody/prosody.cfg.lua` file.

           {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
           Component "conference.example.com" "muc"

           {{< /file >}}

In this scenario, conference.example.com serves as the domain housing the MUC rooms and requires a corresponding "[DNS A record,](/docs/guides/dns-overview/)" pointing to the IP address where the Prosody instance is located. MUCs are identified as JIDs (Jabber IDs) at this hostname; for instance, the "rabbits" MUC hosted by this server would be accessed at `rabbits@conference.example.com`.

Unlike many other common components in the XMPP realm, Prosody provides MUC internally. Other components, such as transports to other services, operate on an external interface. Each external component has its own hostname and provides a secret key for the central server's authentication. Consider the "aim.example.com" component as an example.

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          Component "aim.example.com"
          component_secret = "mysecretcomponentpassword"

          {{< /file >}}

External components must be installed and configured separately from Prosody.

By default, Prosody listens for component connections on the localhost interface (i.e., `127.0.0.2`). If your setup involves external resources running on an alternate interface, adjust the following variables as necessary within the `Host "*"` section of the configuration file.

        {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
        Host "*"

        component_interface = "192.168.0.10"
        component_ports = { 8888, 8887 }

        {{< /file >}}

## Using prosodyctl

While the XMPP protocol supports "in-band" registration, allowing users to register for accounts via the XMPP interface, this function is often undesirable as it lacks moderation by the server administrator and can lead to spam-related issues. Consequently, Prosody disables this functionality by default. Although you can enable in-band registration, it's recommended to use the `prosodyctl` interface in the terminal instead.

If you're accustomed to the [ejabberd,](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/) interface from ejabberd, you'll find that `prosodyctl` closely mirrors its counterpart.

To register a user, such as lollipop@example.com, using `prosodyctl`, issue the following command:

         prosodyctl adduser lollipop@example.com

To set the password for this account, use the following command and enter the password as prompted:

         prosodyctl passwd lollipop@example.com

To delete this user, issue the following command:

         prosodyctl deluser lollipop@example.com

Furthermore, `prosodyctl` can provide a server status report in response to the following command:

        prosodyctl status

Keep in mind that all `prosodyctl` commands require root privileges unless you're logged in as the same user that Prosody runs under (not recommended). Congratulations! Prosody is now installed and configured, ready to power your real-time communications needs.
