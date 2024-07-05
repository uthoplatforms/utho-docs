---
slug: "Swift Chats: Setting Up Ejabberd Instant Messaging on Fedora 13"
deprecated: true
description: 'Getting started with ejabberd, an instant messaging server written in Erlang/OTP on Fedora 13.'
keywords: ["ejabberd", "ejabberd on linux", "real-time messaging", "xmpp server", "collaboration software", "chat software", "linux jabber server"]
tags: ["fedora"]
modified: 2024-04-24
modified_by:
  name: Utho
published: 2024-04-24
title: Instant Messaging Services with ejabberd on Fedora 13
relations:
    platform:
        key: how-to-install-ejabberd
        keywords:
            - distribution: Fedora 13
authors: ["Utho"]
---

Ejabberd, short for Erlang Jabber Daemon, is a powerful XMPP server known for its extensibility, flexibility, and top-notch performance. It's built in the Erlang programming language and boasts a web-based interface along with broad support for [XMPP standards](http://xmpp.org/). While some may find it resource-intensive due to its Erlang runtime requirements, ejabberd is exceptionally robust and can handle heavy loads with ease. It even supports hosting multiple domains virtually.

Before diving into ejabberd installation, make sure you've completed our guide on [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/). If you're new to Linux server administration, we recommend checking out our guides on [introduction to Linux concepts guide](/docs/guides/introduction-to-linux-concepts/), [beginner's guide](/docs/products/compute/compute-instances/faqs/) and [administration basics guide](/docs/guides/linux-system-administration-basics/).

## XMPP/Jabber Basics
Running an XMPP server doesn't necessarily require an in-depth understanding of how the XMPP network and system function, but grasping a few basic concepts can be beneficial:

- Jabber ID (JID): The JID, or "Jabber ID," serves as a unique identifier for users within the XMPP network. It typically resembles an email address and comprises three parts: the username identifying the user on a server, the hostname identifying the server, and an optional resource indicating where the user is logged in from. For example, in the JID "username@example.com/office," "username" is the username, "example.com" is the hostname, and "/office" is the resource. While the resource is optional, it adds specificity, particularly for users logged in from multiple locations.

- Federation in XMPP: XMPP operates on a federated model, allowing users on one server to communicate with those on other servers, provided the server administrators permit it. Each XMPP server functions independently, maintaining its own user accounts and serving as the communication gateway for its users. This decentralized approach eliminates single points of failure. Server administrators can determine their server's level of participation in the federated network; for example, enabling server-to-server (s2s) SSL/TLS encryption is often necessary to federate with Google's "GTalk" XMPP network.

- SRV DNS Records: XMPP leverages ["SRV" DNS Records](/docs/guides/dns-overview/) to facilitate domain resolution to the servers hosting DNS records. These records play a crucial role in directing XMPP traffic to the appropriate servers.

## Set the Hostname

Follow these steps to set the hostname of your Utho:


    echo "example" > /etc/hostname
    hostname -F /etc/hostname

In this scenario, set the hostname to "example". Additionally, you'll have to access the `/etc/sysconfig/network` file and update the `HOSTNAME` line to match the newly assigned hostname.

{{< file "/etc/sysconfig/network" >}}
NETWORKING=yes NETWORKING_IPV6=no HOSTNAME=example
{{< /file >}}

In the `/etc/hosts` file, add your IP address, fully qualified domain name (FQDN), and hostname. For example:

{{< file "/etc/hosts" conf >}}
123.123.123.123 example.com example

{{< /file >}}

## Install ejabberd

Next, ensure your system is up to date and install ejabberd by running the following commands:

    yum update
    yum install ejabberd

## Configure ejabberd

Ejabberd's configuration files are written in Erlang syntax, which may seem complex. However, the modifications required are minor and straightforward. The primary ejabberd configuration file is located at `/etc/ejabberd/ejabberd.cfg`. We'll walk through each relevant option step by step.

### Administrative Users

Certain users will require remote administration capabilities for the XMPP server. The default configuration block for this is as follows:

{{< file "/etc/ejabberd/ejabberd.cfg" >}}
{acl, admin, {user, "admin", "example.com"}}.
{{< /file >}}

In Erlang, comments start with `%`, and the access control list segment is formatted as `{user, "USERNAME", "HOSTNAME"}`. Here are examples for users with JIDs `admin@example.com` and `username@example.com`. You only need to list one administrator, but you can add more by adding extra lines:

{{< file "/etc/ejabberd/ejabberd.cfg" >}}
{acl, admin, {user, "admin", "example.com"}}.
{acl, admin, {user, "username", "example.com"}}.

{{< /file >}}

These users have complete administrative access via both XMPP and web interfaces. Before they can log in, you'll need to create these administrative users as explained below.

### Hostnames and Virtual Hosting

A single ejabberd instance can handle XMPP services for multiple domains simultaneously, as long as those domains or subdomains are managed by the server. To enable virtual hosting in ejabberd, you need to adjust the `hosts` option. By default, ejabberd only serves the domain set during installation. Additionally, it's recommended to include "localhost" as a host item. Here are a couple of examples:


{{< file "/etc/ejabberd/ejabberd.cfg" >}}
{hosts, ["example.com", "localhost"]}.

{{< /file >}}

In the following configuration, ejabberd is set up to host several additional domains: "username.example.com," "example.com," and "bampton.com."

{{< file "/etc/ejabberd/ejabberd.cfg" >}}
{hosts, ["username.example.com", "example.com", example.com", "localhost"]}.

{{< /file >}}

You can list any number of hostnames, but avoid line breaks to prevent ejabberd from encountering errors.

### Listening Ports

The TCP port 5222 is commonly used for XMPP. If you want to change the port, you need to modify this part of the configuration. Also, if you intend to use SSL/TLS encryption for client-server connections, adjust the path to the certificate file in the following line:

{{< file "/etc/ejabberd/ejabberd.cfg" >}}
{certfile, "/etc/ejabberd/ejabberd.pem"}, starttls,
{{< /file >}}


Additionally, you might want to enable SSL access for client-to-server (c2s) SSL/TLS connections, especially if you or other users are using a client that relies on the "old-style" SSL connection via port 5223. To activate this feature, simply remove the comment symbol from the following section.


{{< file "/etc/ejabberd/ejabberd.cfg" >}}
{5223, ejabberd_c2s, [
    {access, c2s},
    {shaper, c2s_shaper},
    {max_stanza_size, 65536},
    tls, {certfile, "/etc/ejabberd/server.pem"}
]},
{{< /file >}}

### Additional Functionality

The `ejabberd.cfg` file is thoroughly commented and ready to go. Your server should be good to run from this point onward. However, it's beneficial to familiarize yourself with the various options provided in this file.

By default, Multi-User-Chats (MUCs or chatrooms) are available on the "conference.[hostname]" subdomain. If you want the public to access MUCs on your domain, create an "A Record" that directs the conference hostname (e.g., subdomain) to the IP address where ejabberd is running.

## Using Ejabberd

Once ejabberd is installed, managing and configuring it is straightforward. To start, stop, or restart the server, just use the appropriate command for the `/etc/init.d/ejabberd` script:


    /etc/init.d/ejabberd start
    /etc/init.d/ejabberd stop
    /etc/init.d/ejabberd restart

Issue the following command to ensure that ejabberd will start following the next reboot cycle:

    chkconfig --level 2345 ejabberd on

By default, ejabberd is set up to prevent "in-band registrations," which means that Internet users can't create accounts on your server without your approval. To register a new user, use the following command format:

    ejabberdctl register lollipop example.com man

For instance, in this example, `lollipop` represents the username, `example.com` is the domain, and man stands for the password. Executing this command will generate a JID for `lollipop@example.com` with the password "man." You'll use this format to create the administrative users mentioned earlier.

To delete a user from your server, utilize the following command format:

    ejabberdctl unregister lollipop example.com

This command would remove the `lollipop@example.com` account from the server.

To set or reset a user's password, use the following command format:

    ejabberdctl set-password lollipop example.com morris

This command updates the password for the `lollipop@example.com` user to `morris`.

To back up ejabberd's database, issue the following command:

    ejabberdctl dump /srv/xmpp/ejabberd-backup.db

This command exports the contents of the internal ejabberd database to a file stored in the "/srv/xmpp/" directory. To restore from this backup, use the following command:

    ejabberdctl load /srv/xmpp/ejabberd-backup.db

For additional details about the `ejabberdctl` command, refer to `ejabberdctl help` or `man ejabberdctl`.

Alternatively, if you prefer administering your ejabberd instance via the web interface, access `http://example.com:5280/admin/` in your browser. Replace "example.com" with the domain where ejabberd is hosted. Log in using the full JID and password of one of the administrators specified in the `/etc/ejabberd/ejabberd.cfg` file.

## XMPP Federation and DNS
To ensure smooth federation of your ejabberd instance with the wider XMPP network, especially with Google's "GTalk" service (commonly known as "@gmail.com" chat), you need to configure SRV records for your domain. These records, totaling three, can be set up using your preferred DNS management tool:

Service: `_xmpp-server` Protocol: TCP Port: 5269
Service: `_xmpp-client` Protocol: TCP Port: 5222
Service: `_jabber Protocol`: TCP Port: 5269

For each record, ensure the "target" points to the publicly accessible hostname of your server (e.g., "example.example.com"). Set both priority and weight to `0`.

## Troubleshooting
If you encounter issues starting ejabberd or face cryptic errors in the console, don't feel disheartened; Erlang's error messages can often be puzzling. You can find ejabberd logs in the `/opt/ejabberd-2.1.0_rc2/logs/` directory. If you're seeing error messages, check these files. Moreover, if ejabberd crashes, Erlang's "image dump" will be saved here. Start troubleshooting by examining these logs for error messages.

## More Information
For further assistance on this topic, consider referring to the following resources. While provided with the hope of being helpful, please note that we can't guarantee the accuracy or timeliness of external materials:

- [Ejabberd Community Site](http://www.ejabberd.im/)
- [XMPP Standards Foundation](http://xmpp.org/)
- [XMPP Client Software](http://xmpp.org/software/clients.shtml)
