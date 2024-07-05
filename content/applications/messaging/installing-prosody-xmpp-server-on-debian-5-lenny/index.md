---
slug: "Chat Wave: Surfing through XMPP with Prosody on Debian 5 Lenny"
deprecated: true
description: 'Smooth Sailing: Installing and Navigating Prosody XMPP Server on Debian 5 (Lenny)'
keywords: ["prosody", "prosody debian lenny", "prosody.im", "xmpp", "real time messaging", "lua"]
tags: ["debian"]
modified: 2024-05-07
modified_by:
  name: Utho
published: 2009-10-13
title: 'Installing Prosody XMPP Server on Debian 5 (Lenny)'
relations:
    platform:
        key: how-to-install-prosody
        keywords:
            - distribution: Debian 5
authors: ["Utho"]
---

Prosody, a lightweight XMPP/Jabber server, prioritizes simplicity and ease of use. Developed in Lua, it boasts minimal resource demands and straightforward configuration. While it may not match the scalability of servers like [ejabberd](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/) or [OpenFire](/docs/guides/instant-messaging-services-with-openfire-on-debian-6-squeeze/), Prosody excels for independent and small-scale applications, offering comparable performance with enhanced resource efficiency. If you're considering XMPP development or managing a small user base, Prosody stands as a compelling option.

To embark on the installation and setup of Prosody, ensure you have a current Debian Lenny installation, have completed our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/), and have logged in via SSH as root.

## Adding Software Repositories

Prosody's developers maintain software repositories for Debian and Ubuntu, facilitating easier distribution of the latest versions. To enable access to these repositories, add the following line to your `/etc/apt/sources.list` file.

             {{< file "/etc/apt/sources.list" >}}
             deb http://packages.prosody.im/debian lenny main

             {{< /file >}}

Next, download the public key for the Prosody package repository using the following wget command. (If `wget` isn't installed, you can do so with apt-get install wget). This step ensures authentication and package verification:

             wget http://prosody.im/files/prosody-debian-packages.key -O- | apt-key add -

Now, refresh the package database by issuing the following command:

              apt-get update
              apt-get upgrade

## Install Prosody

Now that the repository is enabled, proceed to install the Prosody server by executing the following command:

              apt-get install prosody

Upon completion of the `apt` command, the Prosody server will be successfully installed and ready for configuration. Prosody conveniently provides an init script allowing you to manage the XMPP server. You can reload the configuration file, start, stop, or restart the server using one of the following commands:

           /etc/init.d/prosody reload
           /etc/init.d/prosody start
           /etc/init.d/prosody stop
           /etc/init.d/prosody restart

## Configure Prosody Server

The configuration file for Prosody can be found at `/etc/prosody/prosody.cfg.lua` and is scripted in Lua syntax.

In Lua, comments, which are lines ignored by the interpreter, are indicated by two hyphen characters (e.g., --). The default configuration includes basic instructions written in Lua syntax, which can be beneficial for users new to the language.

To enable Prosody to offer XMPP/Jabber services for multiple domains, add a line in the following format to the configuration file. The example below illustrates the definition of three virtual hosts.

              {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
              Host "example.com"
              Host "example.com"
              Host "staff.example.com"

              {{< /file >}}

After a Host line, you'll typically find a series of host-specific configuration settings. If you wish to configure options for all hosts, include them beneath the `Host "*"` entry in your configuration file. For example, to ensure that Prosody functions as a standard Linux server daemon, ensure that the `posix`; option is included in the `modules_enabled = { }` table.

              {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
                modules_enabled = {
                -- [...]
                posix";
                -- [...]
                }

                {{< /file >}}

Make sure to include a variety of global modules in this table to ensure basic functionality.

To deactivate a host without removing it from your configuration file, insert the following line into its section:

          {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
          enabled = false

           {{< /file >}}

For specifying server administrators, adhere to the following format in your `prosody.cfg.lua` file.

         {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
         admins = { "admin1@example.com", "admin2@example.com" }

         {{< /file >}}

For server-wide administrators, insert the admins line into the `Hosts "*"` section. To grant specific users more targeted control over administering particular hosts, you can add an admins line, or tables in Lua, to specific hosts.

If you require legacy SSL/TLS support, append the following line, specifying the port on which the server should listen for these connections.

            {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
            legacy_ssl_ports = { 5223 }

            {{< /file >}}

Remember to reload the Prosody server configuration after any modifications to your `/etc/prosody/prosody.cfg.lua` file by executing the following command:

             /etc/init.d/prosody reload

## XMPP Federation and DNS

To ensure proper federation with the XMPP network, especially with Google's "GTalk" service (i.e., the ["@gmail.com](mailto:"@gmail.com) chat tool), you need to set up SRV records for the domain to point to the server hosting the Prosody instance. Three records are required, which can be created in your chosen DNS Management tool:

1.  Service: `_xmpp-server` Protocol: TCP Port: 5269
2.  Service: `_xmpp-client` Protocol: TCP Port: 5222
3.  Service: `_jabber` Protocol: TCP Port: 5269

The "target" of the SRV record should point to the publicly routable hostname for that machine (e.g., "username.example.com"). Both the priority and weight should be set to default values.

## Enabling Components

In the XMPP ecosystem, many services are provided as components, facilitating customization within a basic framework. One common example is the MUC (multi-user chat) functionality. To enable MUC services in Prosody, add a line like the following to your `/etc/prosody/prosody.cfg.lua` file:

            {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
            Component "conference.example.com" "muc"

            {{< /file >}}

In this example, `conference.example.com` is the domain where the MUC rooms are located, requiring a corresponding "[DNS A record,](/docs/guides/dns-overview/)" pointing to the IP Address of the Prosody instance. MUCs will be identified as JIDs (Jabber IDs) at this hostname. For instance, the "rabbits" MUC hosted by this server would be located at `rabbits@conference.example.com`.

MUC, unlike many other common XMPP components, is provided internally by Prosody. Other components, such as transports to other services, operate on an external interface. Each external component has its own hostname and provides a secret key for authentication by the central server. Refer to the following "aim.example.com" component as an example.

         {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
         Component "aim.example.com"
         component_secret = "mysecretcomponentpassword"

         {{< /file >}}

Note that external components require separate installation and configuration, independent of Prosody.

Typically, Prosody listens for connections from components on the localhost interface (i.e., on the `127.0.1.1` interface). If you're connected to external resources running on an alternate interface, specify the following variables as appropriate in the `Host "*"` section of the configuration file.

         {{< file "/etc/prosody/prosody.cfg.lua" lua >}}
         Host "*"

         component_interface = "192.168.0.10"
         component_ports = { 8888, 8887 }

         {{< /file >}}

## Using prosodyctl

The XMPP protocol supports "in-band" registration, allowing users to create accounts directly via the XMPP interface. However, this feature is often disabled by default in Prosody due to potential spam-related issues and lack of moderation. Instead, we recommend using the `prosodyctl` interface at the terminal prompt for user management.

Similar to [ejabberd,](/docs/guides/use-ejabberd-for-instant-messaging-on-ubuntu-12-04/) in ejabberd, `prosodyctl` mirrors its functionality as closely as possible.

To register a user, such as `lollipop@example.com`, with prosodyctl, use the following command:

    prosodyctl adduser lollipop@example.com

To set the password for this account, enter the following command and provide the password when prompted:

    prosodyctl passwd lollipop@example.com

To remove the user, execute the following command:

    prosodyctl deluser lollipop@example.com

Moreover, `prosodyctl` can generate a server status report with the following command:

    prosodyctl status

Please note that all `prosodyctl` commands require root privileges, unless you're logged in as the same user that Prosody runs under (which is not recommended).
