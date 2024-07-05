---
slug: "Ejabberd Empowers Instant Messaging: Unleash on Ubuntu 8.04 Hardy"
deprecated: true
description: 'Embark on your journey with ejabberd, a robust instant messaging server crafted in Erlang/OTP, tailored for Ubuntu 8.04 (Hardy)'
keywords: ["ejabberd", "ejabberd ubuntu hardy", "ejabberd on linux", "real-time messaging", "xmpp server", "collaboration software", "chat software", "linux jabber server"]
tags: ["ubuntu"]
modified: 2024-05-09
modified_by:
  name: Utho
published: 2024-05-09
title: 'Instant Messaging Services with ejabberd on Ubuntu 8.04 (Hardy)'
relations:
    platform:
        key: how-to-install-ejabberd
        keywords:
            - distribution: Ubuntu 8.04
authors: ["Utho"]
---
Ejabberd, written in Erlang, stands as a formidable Jabber daemon, boasting extensibility, flexibility, and exceptional performance. Equipped with a web-based interface and comprehensive support for[XMPP standards](http://xmpp.org/), ejabberd emerges as the go-to choice for a versatile XMPP server. While some may label ejabberd as "heavyweight," primarily due to its reliance on Erlang run-times, its resilience and capacity to handle substantial loads make it a stalwart solution. Indeed, ejabberd servers serve as the backbone for some of the largest Jabber servers in operation today.

To embark on the installation journey, ensure you have Ubuntu 8.04 (Hardy) installed and have followed the steps outlined in the Setting Up and Securing a Compute Instance guide. With an up-to-date Ubuntu Hardy instance and root access via SSH to your Utho, you're primed to initiate the installation process.

## XMPP/Jabber Basics

While a deep understanding of the XMPP network and system can be advantageous, managing an XMPP server requires only basic knowledge. Familiarity with key concepts such as the Jabber ID (JID) can prove beneficial:

The Jabber ID serves as a unique identifier for users within the XMPP network, resembling an email address. It comprises the username, which identifies a specific user on a server, the hostname, which identifies the server, and optionally, a resource indicating the user's login location. The resource is often omitted or disregarded for most users. For instance, in the following example, "username" denotes the username, "example.com" the hostname, and "/office" the resource.

        username@example/office

Once again, the resource remains optional in XMPP. While XMPP permits a single JID to be linked to the server from multiple machines (i.e., resources), incorporating a resource enhances specificity.

The XMPP system inherently operates in a federated manner. Users possessing accounts on one server, contingent upon the server administrators' permissions, can engage in communication with users on other servers. In the absence of a centralized server, each XMPP server maintains its accounts and acts as the communication gateway for its users. Although there's no single point of failure in the XMPP system, each server administrator retains the authority to determine their server's participation in the federated network. For example, to federate with Google's "GTalk" XMPP network, server administrators must enable server-to-server (s2s) SSL/TLS encryption, while other servers may not necessitate this.

XMPP leverages ["SRV" DNS records](/docs/guides/dns-overview/) to facilitate domain resolution to the servers providing DNS records.

## Install ejabberd

Ensure your package repositories and installed programs are current by executing the following commands:

                        apt-get update
                        apt-get upgrade --show-upgraded

To install ejabberd and its requisite dependencies, execute the subsequent command:

                        apt-get install ejabberd

The default installation is fully operational. During the installation process, a self-signed SSL certificate is generated. If you prefer to utilize a commercially signed certificate, position the certificate file at `/etc/ejabberd/ejabberd.pem`. However, for many Jabber applications, a self-signed certificate suffices.

If you haven't already configured your `/etc/hosts` as directed below, do so before proceeding. This enables your Utho to associate its hostname with the public IP. Your file excerpt should resemble the following (substitute your Utho's public IP address for 12.34.56.78):

                           {{< file "/etc/hosts" >}}
                           127.0.0.1    localhost.localdomain   localhost
                           12.34.56.78  username.example.com  username

                           {{< /file >}}

With the hostname configured, commence the configuration of ejabberd.

## Configure ejabberd

Ejabberd's configuration files adhere to Erlang syntax, which may initially appear daunting. However, the adjustments required are relatively minor and straightforward. The primary ejabberd configuration file resides at `/etc/ejabberd/ejabberd.cfg`. Each relevant option will be elaborated on sequentially.

### Administrative Users

Certain users may require remote administration capabilities for the XMPP server. By default, this section of the configuration file appears as follows:

                          {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                          %% Admin user {acl, admin, {user, "", "localhost"}}.

                          {{< /file >}}

In Erlang, comments are initiated with the % symbol, and the access control list segment comprises information structured as `{user, "USERNAME", "HOSTNAME"}`. For instance, the subsequent examples pertain to users with the JIDs of `admin@example.com` and `username@example.com`. While specifying only one administrator is necessary, additional administrators can be included by appending more lines, as illustrated below:

                          {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                          {acl, admin, {user, "admin", "example.com"}}.
                          {acl, admin, {user, "username", "example.com"}}

                          {{< /file >}}

Users defined in this manner possess complete administrative access to the server via both XMPP and web-based interfaces. Before users can log in, it's imperative to create administrative accounts (as delineated below).

### Hostnames and Virtual Hosting

A single instance of ejabberd can serve XMPP services for multiple domains simultaneously, provided those domains (or subdomains) are hosted by the server. To add a hostname for virtual hosting in ejabberd, adjust the `hosts` option. By default, ejabberd is configured to only host the "localhost" domain:

                 {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                 {hosts, ["localhost"]}.

                 {{< /file >}}

In the following example, ejabberd is configured to support several additional domains, including "username.example.com," "example.com," and "example.com."

                 {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                 {hosts, ["username.example.com", "example.com", "example.com"]}.

                 {{< /file >}}

While the host list can contain any number of hostnames, it's crucial to avoid inserting a line break to prevent ejabberd from malfunctioning.

### Listening Ports

TCP port number 5222 is the standard "XMPP" port. Adjusting this port requires modifying the relevant section in the configuration.

Additionally, enabling SSL access for client-to-server (c2s) SSL/TLS connections may be necessary if you or other system users utilize a client supporting secure connections on port 5223. To activate this feature, uncomment the following stanza.

                 {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                 {5223, ejabberd_c2s, [
                 {access, c2s},
                 {shaper, c2s_shaper},
                 {max_stanza_size, 65536},
                 tls, {certfile, "/etc/ejabberd/ejabberd.pem"}
                 ]},

                  {{< /file >}}

### Additional Functionality

The `ejabberd.cfg` file is thorough and extensively commented, indicating that your server is ready to operate smoothly. However, it's advisable to familiarize yourself with the provided options in this file.

By default, Multi-User-Chats (MUCs), or chatrooms, are accessible via the "conference.[hostname]" subdomain. For public access to MUCs on your domain, creating an "A Record" directing the `conference` hostname (e.g., subdomain) to the IP address where the ejabberd instance operates is essential.

### Using Ejabberd

Once installed, using and configuring ejabberd is straightforward. To start, stop, or restart the server, execute the appropriate command from the following:

                        /etc/init.d/ejabberd start
                        /etc/init.d/ejabberd stop
                        /etc/init.d/ejabberd restart

By default, ejabberd is configured to disallow "in-band-registrations," preventing Internet users from obtaining accounts on your server without consent. To register a new user, use the following command format:

                          ejabberdctl register lollipop example.com man

In this example, lollipop represents the username, example.com denotes the domain, and man is the password. This command creates a JID for `lollipop@example.com` with the password `"man."` Use this format to establish the administrative users described earlier.

The specified command would unregister the `lollipop@example.com` account from the server.

           ejabberdctl unregister lollipop example.com

To set or reset a user's password, use the following command:

         ejabberdctl set-password lollipop example.com morris

This command updates the password for the `lollipop@example.com` user to `morris`.

To back up ejabberd's database, execute the following command:

            ejabberdctl dump ejabberd-backup.db

This command exports the contents of ejabberd's internal database into a file located in the "/var/lib/ejabberd/" directory. To restore from this backup, use the subsequent command:

            ejabberdctl load ejabberd-backup.db

For further insights into the `ejabberdctl` command, refer to `ejabberdctl` help or consult `man ejabberdctl`.

If you prefer managing your ejabberd instance through the web-based interface, visit http://example.com:5280/admin/, where "example.com" denotes the domain where ejabberd is operational. Log in using the full JID as the username and the password of one of the administrators specified in the `/etc/ejabberd/ejabberd.cfg` file.

## XMPP Federation and DNS

To ensure seamless federation of your ejabberd instance with the wider XMPP network, especially with Google's "GTalk" service (i.e., the ["@gmail.com](mailto:"@gmail.com)" chat tool), it's imperative to configure the SRV records for your domain. These records, essential for proper XMPP communication, can be set up using any DNS management tool. Here's what you'll need:

To ensure smooth federation of your ejabberd instance with the broader XMPP network, especially concerning Google's "GTalk" service (i.e., the "@gmail.com" chat tool), configuring the SRV records for your domain is crucial. These records, vital for proper XMPP communication, can be configured using any DNS management tool. Here's what you'll need:


Service: `_xmpp-server` Protocol: TCP Port: 5269
Service: `_xmpp-client` Protocol: TCP Port: 5222
Service: `_jabber Protocol`: TCP Port: 5269

Ensure that the "target" of the SRV record points to the publicly routable hostname of your machine (e.g., "username.example.com"). Both the priority and weight should be set to `0`.

## Troubleshooting

If you encounter challenges in getting ejabberd to start or if you're faced with cryptic errors on the console, don't lose heart. Error messages generated by Erlang can often be puzzling. The logs for ejabberd are stored in the `/var/log/ejabberd/` directory. Be sure to check files like ejabberd.log and sasl.log for error messages. Moreover, if ejabberd crashes, an "image dump" of Erlang will be saved in this directory. Commence your error investigations by examining these files.

Additionally, ejabberd's "Mnesia" database resides in the `/var/lib/ejabberd/` directory. In the event of suspected database corruption, consider deleting the files in this directory (e.g., rm /var/lib/ejabberd/*) and reloading from a backup if necessary. This step is sometimes necessary.
