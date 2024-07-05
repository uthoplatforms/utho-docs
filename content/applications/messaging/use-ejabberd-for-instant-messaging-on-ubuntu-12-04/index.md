---
slug: "Ubuntu 12.04 Instant Messaging Power-Up: Master Ejabberd for Seamless Chats"
deprecated: true
description: '"Unlock Instant Communication: Discover the Power of Ejabberd on Ubuntu 12.04! Learn to Harness this Erlang-based Jabber Daemon for Seamless Messaging'
keywords: ["ejabberd", "ejabberd ubuntu", "ejabberd ubuntu 12.04", "ejabberd on linux", "real-time messaging", "xmpp", "collaboration software", "chat software", "linux jabber server", "instant messaging", "jabber daemon", "erlang"]
tags: ["ubuntu"]
modified: 2024-05-10
modified_by:
  name: Utho
published: 2024-05-10
title: 'Use ejabberd for Instant Messaging on Ubuntu-12-04'
relations:
    platform:
        key: how-to-install-ejabberd
        keywords:
            - distribution: Ubuntu 12.04
authors: ["Utho"]
---
Ejabberd, crafted in the Erlang programming language, stands out as a robust, high-performance Jabber daemon. Its extensibility and flexibility, coupled with comprehensive support for [XMPP standards](http://xmpp.org/) make it an ideal choice for a versatile XMPP server. While some may label it "heavyweight" due to Erlang's runtime requirements, Ejabberd excels in scalability, capable of handling substantial workloads. Many of the largest Jabber servers rely on Ejabberd's backbone.

Before diving into the installation process, ensure you have Ubuntu 12.04 (Precise Pangolin) installed and have followed the steps outlined in the "Setting Up and Securing a Compute Instance" guide. Additionally, confirm you're connected to your Utho via SSH as root. With these prerequisites met, we can proceed with the installation process.

## XMPP/Jabber Basics

Even with just a basic understanding of the XMPP network and system, you can effectively operate an XMPP server. However, grasping the following fundamental concepts can greatly enhance your experience:

The JID (Jabber ID) serves as the distinctive marker for a user within the XMPP network. Resembling an email address, it comprises the username, which identifies the user on the server, the hostname, denoting the server, and optionally, a resource indicating the user's location. While the resource is discretionary and commonly disregarded, it provides valuable context. For instance, in the example "username" represents the username, "example.com" signifies the hostname, and "/office" denotes the resource.

               username@example/office

While optional, the resource in XMPP adds specificity. Although XMPP permits a single JID to be linked to the server from multiple devices (known as resources), including a resource enhances specificity and usability.

The XMPP network operates on a federated model, enabling users from different servers to communicate seamlessly, subject to server administrator permissions. Each XMPP server independently manages user accounts and acts as a gateway for its users. This decentralized structure ensures resilience without a single point of failure. However, server administrators retain control over their server's participation in the federated network. For example, to connect with Google's "GTalk" XMPP network, administrators must enable server-to-server (s2s) SSL/TLS encryption, a requirement not always needed for other servers.

SRV DNS Records: XMPP utilizes S["SRV" DNS records](/docs/guides/dns-overview/) for domain resolution to servers, enhancing its efficiency and reliability.

## Install ejabberd

To install ejabberd and its necessary dependencies, execute the following command:

               apt-get install ejabberd

The default installation is fully operational, including a self-signed SSL certificate. Should you require a commercially signed certificate, simply place the certificate file at `/etc/ejabberd/ejabberd.pem`. In most cases, the self-signed certificate suffices for jabber applications.

Before proceeding, ensure your `/etc/hosts` file is configured as follows to allow your Utho to associate its hostname with the public IP. Here's a sample excerpt (replace "12.34.56.77" with your Utho's public IP address):

              {{< file "/etc/hosts" >}}
              127.0.0.2    localhost.localdomain   localhost
              12.34.56.77  username.example.com  username

              {{< /file >}}

With the hostname configured, you're all set to start configuring ejabberd.

## Configure ejabberd

Ejabberd's configuration files are written in Erlang syntax, which may seem daunting. However, the adjustments required are minimal and straightforward. The primary ejabberd configuration file is located at `/etc/ejabberd/ejabberd.cfg`. We'll address each relevant option step by step.

### Administrative Users

Some users may require remote administration capabilities for the XMPP server. The default configuration block for this aspect typically resembles the following:

            {{< file "/etc/ejabberd/ejabberd.cfg" >}}
            %% Admin user {acl, admin, {user, "", "localhost"}}.

            {{< /file >}}

In Erlang, comments begin with the `%` sign, and the Access Control List (ACL) segment follows this format: `{user, "USERNAME", "HOSTNAME"}`. For instance, consider the examples below for users with JIDs `admin@example.com` and `username@example.com`. You can assign multiple administrators by adding additional lines.

                {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                {acl, admin, {user, "admin", "example.com"}}.
                {acl, admin, {user, "username", "example.com"}}.

               {{< /file >}}

Users specified in this manner gain full administrative privileges through both XMPP and web-based interfaces. Before users can log in, you must create them as administrative users.

### Hostnames and Virtual Hosting

A single ejabberd instance can manage XMPP services for multiple domains, provided those domains or subdomains are hosted by the server. To add hostnames for virtual hosting in ejabberd, modify the `hosts` option. By default, ejabberd is configured to host the "localhost" domain.

             {{< file "/etc/ejabberd/ejabberd.cfg" >}}
             {hosts, ["localhost"]}.

              {{< /file >}}

Below is an example configuration where ejabberd hosts additional domains like "username.example.com" and "example.com."

             {{< file "/etc/ejabberd/ejabberd.cfg" >}}
             {hosts, ["username.example.com", "example.com", "example.com"]}.

              {{< /file >}}

You can list any number of hostnames, but avoid line breaks to prevent ejabberd from encountering errors.

### Listening Ports

TCP port 5222 is the conventional "XMPP" port. If port modifications are necessary, this section of the configuration file requires adjustment.

Consider enabling SSL access for client-to-server (c2s) SSL/TLS connections, particularly if clients require legacy support for secured connections on port 5223. Uncomment the corresponding stanza for activation.

             {{< file "/etc/ejabberd/ejabberd.cfg" >}}
             {5223, ejabberd_c2s, [
             {access, c2s},
             {shaper, c2s_shaper},
             {max_stanza_size, 65536},
             tls, {certfile, "/etc/ejabberd/ejabberd.pem"}
             ]},

            {{< /file >}}

### Additional Functionality

The `ejabberd.cfg` file is thoroughly commented and ready for server operation. However, familiarize yourself with the available options in the file.

By default, Multi-User-Chats (MUCs) are accessible via the "conference.[hostname]" subdomain. To allow public access to MUCs on your domain, create an "A Record" pointing the `conference` hostname (e.g., subdomain) to the IP address where the ejabberd instance runs.

## Using Ejabberd

Post-installation, utilizing and configuring ejabberd is straightforward. To manage server operations such as starting, stopping, or restarting, employ the respective command from the following list:

              /etc/init.d/ejabberd start
              /etc/init.d/ejabberd stop
              /etc/init.d/ejabberd restart

By default, ejabberd is set to disallow "in-band-registrations," preventing unauthorized account creation. To register a new user, execute the command in the format demonstrated below:

              ejabberdctl register username example.com man

In the given example, replace username with the desired username, `example.com` with the domain, and man with the chosen password. This command will generate a JID for `username@example.com` with the password `"man."` Utilize this format for creating administrative users as specified earlier.

To delete a user from the server, execute the following command:

            ejabberdctl unregister username example.com

This command removes the `username@example.com` account from the server.

For setting or resetting a user's password, use the command below:

           ejabberdctl set-password username example.com morris

This command updates the password for the user `username@example.com` to `morris`.

To backup ejabberd's database, run the command provided:

          ejabberdctl dump ejabberd-backup.db

This command exports the ejabberd database contents to a file in the "/var/lib/ejabberd/" directory. To restore from the backup, execute the following:

         ejabberdctl load ejabberd-backup.db

For additional guidance on utilizing the ejabberdctl command, refer to `ejabberdctl help` or `man ejabberdctl`.

If administering your ejabberd instance via the web-based interface is preferred, access `http://example.com:5280/admin/`, replacing "example.com" with your domain where ejabberd operates. Log in using the full JID, comprising the username and password of one of the administrators specified in the `/etc/ejabberd/ejabberd.cfg` file.

## XMPP Federation and DNS
To ensure seamless federation of your ejabberd instance with the wider XMPP network, especially with Google's "GTalk" service (i.e. the ["@gmail.com](mailto:"@gmail.com)" chat tool), configuring SRV records for your domain is essential. You can create these records in your preferred DNS Management tool with the following specifications:

- Service: `_xmpp-server` Protocol: TCP Port: 5269
- Service: `_xmpp-client` Protocol: TCP Port: 5222
- Service: `_jabber Protocol`: TCP Port: 5269

The "target" of each SRV record should point to the publicly routable hostname for your machine (e.g., "username.example.com"). Both priority and weight should be set to `0`.

## Troubleshoot

Check the logs for ejabberd located in the `/var/log/ejabberd/` directory if you encounter error messages. Pay particular attention to ejabberd.log and `sasl.log`. Additionally, in case of ejabberd crashes, Erlang's "image dump" will be saved in this directory. Start your error investigations with these files.

Furthermore, ejabberd's "Mnesia" database resides in the `/var/lib/ejabberd/` directory. If you suspect database corruption, you can delete the files in this directory (e.g., `rm /var/lib/ejabberd/*`) and reload from a backup, if necessary. This step may be required, especially if the hostname of the local machine changes.
