---
slug: Ejabberd Instant Messaging Services Setup on Debian 5 Lenny
deprecated: true
description: 'Beginning with ejabberd, an Erlang/OTP-based instant messaging server, on Debian 5 (Lenny)'
keywords: ["ejabberd", "ejabberd on linux", "real-time messaging", "xmpp server", "collaboration software", "chat software", "linux jabber server"]
tags: ["debian"]
modified: 2024-05-09
modified_by:
  name: Utho
published: 2024-05-09
title: 'Instant Messaging Services with ejabberd on Debian 5 (Lenny)'
relations:
    platform:
        key: how-to-install-ejabberd
        keywords:
            - distribution: Debian 5
authors: ["Utho"]
---
Ejabberd, short for "Erlang Jabber Daemon," stands as a highly extensible, adaptable, and performant XMPP server crafted in the Erlang programming language. Boasting a web-based interface and extensive support for [XMPP standards](http://xmpp.org/), ejabberd proves to be an excellent choice for various general-purpose and multi-functional XMPP server needs. While recognized as "heavyweight" by some, primarily due to its reliance on Erlang runtimes, ejabberd showcases remarkable resilience and scalability, capable of efficiently handling substantial workloads. It even offers support for virtually hosting multiple domains.

This installation guide presupposes a functional Debian 5 (Lenny) installation, adherence to the steps outlined in the [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide, and an SSH connection to your Utho as the root user. Once these prerequisites are met, we can proceed with the installation process.

## XMPP/Jabber Basics

While it's feasible to operate an XMPP server with minimal knowledge of the XMPP network and system mechanics, grasping the following fundamental concepts can prove beneficial:

The JID or "Jabber ID" serves as a unique identifier for a user within the XMPP network. Typically resembling an email address, it encompasses the username signifying a specific user on a server, the hostname denoting the server, and optionally, a resource indicating the user's login location. While optional, the resource is often excluded or disregarded for most users. In the example provided, "username" represents the username, "example.com" denotes the hostname, and "/office" signifies the resource.

        username@example/office

Once more, the inclusion of a resource remains discretionary; although XMPP permits a single JID to connect to the server from multiple devices (i.e., resources), specifying a resource enhances specificity.

The XMPP system operates on a federated basis. Users with accounts on one server, provided the server administrators allow it, can communicate with users on other servers. In this decentralized system, each XMPP server manages its own accounts and serves as the communication conduit for its users. This design eliminates a single point of failure, with each server administrator having the autonomy to determine their server's role in the federated network. For instance, to federate with Google's "GTalk" XMPP network, server administrators must enable server-to-server (s2s) SSL/TLS encryption, though other servers may not require this.

XMPP utilizes ["SRV" DNS Records](/docs/guides/dns-overview/) to facilitate domain resolution to the servers providing DNS records.

## Install ejabberd

Ensure your package repositories and installed programs are up to date with the following commands:

                 apt-get update
                 apt-get upgrade --show-upgraded

To install ejabberd and its necessary dependencies, execute the following command:

                 apt-get install ejabberd

The default installation is complete and operational. During installation, a self-signed SSL certificate is generated. If you prefer to utilize a commercially signed certificate, simply place the certificate file at `/etc/ejabberd/ejabberd.pem`. However, for many Jabber applications, a self-signed certificate suffices.

Before proceeding, ensure your `/etc/hosts` file is configured as follows to associate your Utho's hostname with its public IP address. Insert an excerpt resembling the following (replace "12.34.56.78" with your utho's public IP address):

            {{< file "/etc/hosts" >}}
            127.0.0.1    localhost.localdomain   localhost
            12.34.56.77  username.example.com  username

            {{< /file >}}

            With the hostname configured, you're prepared to commence ejabberd configuration.

## Configure ejabberd

Ejabberd's configuration files are composed in Erlang syntax, which may pose a challenge for comprehension. Fortunately, the adjustments required are relatively minor and straightforward. The primary ejabberd configuration file resides at `/etc/ejabberd/ejabberd.cfg`. Each pertinent option will be addressed sequentially.

### Administrative Users

Certain users may require remote administration access to the XMPP server. By default, this section of the configuration file appears as follows:

            {{< file "/etc/ejabberd/ejabberd.cfg" >}}
            %% Admin user {acl, admin, {user, "", "localhost"}}.

            {{< /file >}}

In Erlang, comments are initiated with the % character, and the access control list segment contains data in the format: `{user, "USERNAME", "HOSTNAME"}`. Below are examples corresponding to users with the JIDs of `admin@example.com` and `username@example.com`. While specifying only one administrator is necessary, additional administrators can be added by simply including more lines, as illustrated below:

            {{< file "/etc/ejabberd/ejabberd.cfg" >}}
            {acl, admin, {user, "admin", "example.com"}}.
            {acl, admin, {user, "username", "example.com"}}.

            {{< /file >}}

All users specified in this manner gain complete administrative access to the server through both the XMPP and web-based interfaces. Before they can log in, you'll need to create these administrative users, as outlined below.

### Hostnames and Virtual Hosting

A single ejabberd instance can serve XMPP services for multiple domains concurrently, as long as those domains (or subdomains) are hosted on the server. To incorporate a hostname for virtual hosting in ejabberd, adjust the `hosts` option. By default, ejabberd is configured solely to host the "localhost" domain.

           {{< file "/etc/ejabberd/ejabberd.cfg" >}}
           {hosts, ["localhost"]}.

           {{< /file >}}

In the example provided, ejabberd has been configured to accommodate several additional domains, including "username.example.com," "example.com," and "example.com."

          {{< file "/etc/ejabberd/ejabberd.cfg" >}}
          {hosts, ["username.example.com", "example.com", "example.com"]}.

          {{< /file >}}

You can list any number of hostnames in the host list, but be cautious to avoid introducing line breaks, as this could cause ejabberd to malfunction.

### Listening Ports

TCP port number 5222 serves as the standard "XMPP" port. Should you wish to modify the port, this section of the configuration requires adjustment.

Additionally, you might consider enabling SSL access for client-to-server (c2s) SSL/TLS connections if you or other system users utilize a client supporting secured connections on port 5223. To activate this feature, uncomment the following stanza.

             {{< file "/etc/ejabberd/ejabberd.cfg" >}}
             {5223, ejabberd_c2s, [
             {access, c2s},
             {shaper, c2s_shaper},
             {max_stanza_size, 65536},
             tls, {certfile, "/etc/ejabberd/ejabberd.pem"}
            ]},

            {{< /file >}}

### Additional Functionality

The `ejabberd.cfg` file is comprehensive and well commented. Your server should now be operational. Nevertheless, take the time to familiarize yourself with the options presented in this file. Concerning multi-user chats:

By default, Multi-User-Chats (MUCs or chatrooms) are accessible via the "conference.[hostname]" subdomain. If you desire public access to MUCs on your domain, create an "A Record" pointing the `conference` hostname (e.g., subdomain) to the IP address where the ejabberd instance operates.

## Using Ejabberd

Once ejabberd is installed, its usage and configuration are straightforward. To start, stop, or restart the server, choose the appropriate command from the options provided:

              /etc/init.d/ejabberd start
              /etc/init.d/ejabberd stop
              /etc/init.d/ejabberd restart

By default, ejabberd is set to disallow "in-band-registrations," preventing internet users from obtaining accounts on your server without your consent. To register a new user, use the following format:

              ejabberdctl register lollipop example.com man

In the provided example, lollipop represents the username, `example.com` is the domain, and man is the password. This action creates a JID for `lollipop@example.com` with the password "man." Utilize this format to create the administrative users specified above.

To remove a user from your server, use the following format:

              ejabberdctl unregister lollipop example.com

The provided command would deregister the `lollipop@example.com` account from the server.

To set or reset a user's password, utilize the following command:

              ejabberdctl set-password lollipop example.com morris

Executing this command alters the password for the `lollipop@example.com` user to `morris`.

To backup ejabberd's database, execute the following command:

               ejabberdctl dump ejabberd-backup.db

This command exports the contents of the internal ejabberd database into a file stored in the "/var/lib/ejabberd/" directory. To restore from this backup, issue the following command:

               ejabberdctl load ejabberd-backup.db

For further details about the `ejabberdctl` command, refer to `ejabberdctl help` or `man ejabberdctl`.

Alternatively, if you prefer administering your ejabberd instance via the web-based interface, access `http://example.com:5280/admin/`, where "example.com" denotes the domain where ejabberd operates. Log in using the full JID and password of one of the administrators specified in the `/etc/ejabberd/ejabberd.cfg` file.

## XMPP Federation and DNS

To ensure seamless federation of your ejabberd instance with the wider XMPP network, particularly with Google's "GTalk" service (i.e. the ["@gmail.com](mailto:"@gmail.com)" chat tool), configure the SRV records for your domain to point to the server hosting the ejabberd instance. You'll need three records, which can be configured using your preferred DNS management tool:

- Service: `_xmpp-server` Protocol: TCP Port: 5269
- Service: `_xmpp-client` Protocol: TCP Port: 5222
- Service: `_jabber` Protocol: TCP Port: 5269

The "target" of the SRV record should direct to the publicly routable hostname of the machine (e.g., "username.example.com"). Set both the priority and weight to `0`.

## Troubleshooting

If you encounter issues initiating ejabberd or encounter obscure errors on the console, remain undeterred; Erlang error messages can often be cryptic. Review the logs for ejabberd located in the `/var/log/ejabberd/` directory, particularly `ejabberd.log` and `sasl.log`, for error messages. Additionally, in the event of an ejabberd crash, the Erlang "image dump" will be stored in this directory. Initiate your error investigations by examining these files.

Moreover, ejabberd's "Mnesia" database resides in the `/var/lib/ejabberd/` directory. If you suspect database corruption, delete the files in this directory (e.g., `rm /var/lib/ejabberd/*`) and reload from a backup if necessary. This action is sometimes required if the hostname of the local machine changes.
