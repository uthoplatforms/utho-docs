---
slug: "Chat with Ejabberd: Setting Up Instant Messaging on Ubuntu 9.04 (Jaunty)"
deprecated: true
description: 'Embark on your journey with ejabberd, a robust instant messaging server crafted in Erlang/OTP, tailored for Ubuntu 9.04 (Jaunty)'
keywords: ["ejabberd", "ejabberd ubuntu jaunty", "ejabberd on linux", "real-time messaging", "xmpp server", "collaboration software", "chat software", "linux jabber server"]
tags: ["ubuntu"]
modified: 2024-05-09
modified_by:
  name: Utho
published: 2024-05-09
title: 'Instant Messaging Services with ejabberd on Ubuntu 9.04 (Jaunty)'
relations:
    platform:
        key: how-to-install-ejabberd
        keywords:
            - distribution: Ubuntu 9.04
authors: ["Utho"]
---
Ejabberd, written in Erlang, stands as a formidable Jabber daemon, boasting extensibility, flexibility, and exceptional performance. Equipped with a web-based interface and comprehensive support for[XMPP standards](http://xmpp.org/), ejabberd emerges as the go-to choice for a versatile XMPP server. While some may label ejabberd as "heavyweight," primarily due to its reliance on Erlang run-times, its resilience and capacity to handle substantial loads make it a stalwart solution. Indeed, ejabberd servers serve as the backbone for some of the largest Jabber servers in operation today.

To embark on the installation journey, ensure you have Ubuntu 8.04 (Hardy) installed and have followed the steps outlined in the Setting Up and Securing a Compute Instance guide. With an up-to-date Ubuntu Hardy instance and root access via SSH to your Utho, you're primed to initiate the installation process

## XMPP/Jabber Basics

While a deep understanding of the XMPP network and system can be advantageous, managing an XMPP server requires only basic knowledge. Familiarity with key concepts such as the Jabber ID (JID) can prove beneficial:

The Jabber ID serves as a unique identifier for users within the XMPP network, resembling an email address. It comprises the username, which identifies a specific user on a server, the hostname, which identifies the server, and optionally, a resource indicating the user's login location. The resource is often omitted or disregarded for most users. For instance, in the following example, "username" denotes the username, "example.com" the hostname, and "/office" the resource.

             username@example/office

Once again, the resource remains optional in XMPP. While XMPP permits a single JID to be linked to the server from multiple machines (i.e., resources), incorporating a resource enhances specificity.

The XMPP system inherently operates in a federated manner. Users possessing accounts on one server, contingent upon the server administrators' permissions, can engage in communication with users on other servers. In the absence of a centralized server, each XMPP server maintains its accounts and acts as the communication gateway for its users. Although there's no single point of failure in the XMPP system, each server administrator retains the authority to determine their server's participation in the federated network. For example, to federate with Google's "GTalk" XMPP network, server administrators must enable server-to-server (s2s) SSL/TLS encryption, while other servers may not necessitate this.

XMPP leverages ["SRV" DNS records](/docs/guides/dns-overview/) to facilitate domain resolution to the servers providing DNS records.


## Enabling the Universe Repository

Before installing the ejabberd daemon, ensure that the universe repository is enabled. Open `/etc/apt/sources.list` using your preferred text editor.

        {{< file "/etc/apt/sources.list" >}}
        deb http://us.archive.ubuntu.com/ubuntu/ jaunty universe
        {{< /file >}}

Add the following line to the source list, then update:

         apt-get update
         apt-get upgrade

## Install ejabberd

To install ejabberd and its necessary dependencies, execute the following command:

    apt-get install ejabberd

The default installation is comprehensive and operational. During installation, a self-signed SSL certificate is generated. If you wish to utilize a commercially signed certificate, store the certificate file at `/etc/ejabberd/ejabberd.pem`. For many jabber applications, a self-signed certificate is usually adequate.

If you haven't already configured your `/etc/hosts` as described below, please do so before proceeding. This ensures that your utho can associate its hostname with the public IP. Your file should contain an excerpt similar to this (use your utho's public IP address instead of 12.34.56.78):

              {{< file "/etc/hosts" >}}
              127.0.0.1    localhost.localdomain   localhost
              12.34.56.77  username.example.com  username

              {{< /file >}}

With the hostname configured, you're now prepared to commence the configuration of ejabberd.

## Configure ejabberd

Ejabberd's configuration files are composed in Erlang syntax, which may initially seem complex. However, the adjustments we need to make are relatively minor and straightforward. The primary ejabberd configuration file can be found at `/etc/ejabberd/ejabberd.cfg`. We will discuss each relevant option sequentially.

### Administrative Users

Some users may require remote administration access to the XMPP server. By default, this section of the configuration file appears as follows:

               {{< file "/etc/ejabberd/ejabberd.cfg" >}}
               %% Admin user {acl, admin, {user, "", "localhost"}}.

               {{< /file >}}

In Erlang, comments are initiated with the % sign, and the Access Control List segment contains information structured as `{user, "USERNAME", "HOSTNAME"}`. The following examples correspond to users with the JIDs of `admin@example.com` and `username@example.com`. While specifying only one administrator is necessary, additional administrators can be included by adding more lines, as illustrated below:

                 {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                 {acl, admin, {user, "admin", "example.com"}}.
                 {acl, admin, {user, "username", "example.com"}}.

                 {{< /file >}}

Users specified in this manner possess complete administrative access to the server through both XMPP and web-based interfaces. You must create your administrative users (as described below) before they can log in.

### Hostnames and Virtual Hosting

A single ejabberd instance can provide XMPP services for multiple domains concurrently, provided those domains (or subdomains) are hosted by the server. To add a hostname for virtual hosting in ejabberd, modify the `hosts` option. By default, ejabberd is configured to host only the "localhost" domain:

              {{< file "/etc/ejabberd/ejabberd.cfg" >}}
              {hosts, ["localhost"]}.

               {{< /file >}}

In the following example, ejabberd has been configured to host several additional domains, including "username.example.com," "example.com," and "example.com."

              {{< file "/etc/ejabberd/ejabberd.cfg" >}}
          {hosts, ["username.example.com", "example.com", "example.com"]}.

              {{< /file >}}

You can include any number of hostnames in the host list, but be cautious not to insert line breaks, as this can cause ejabberd to fail.

### Listening Ports

TCP port number 5222 is the standard "XMPP" port. If you wish to change the port, modify the corresponding section in the configuration.

Additionally, you might want to enable SSL access for client-to-server (c2s) SSL/TLS connections if you or other users are utilizing a client that supports secure connections on port 5223. Uncomment the following stanza.

            {{< file "/etc/ejabberd/ejabberd.cfg" >}}
            {5223, ejabberd_c2s, [
            {access, c2s},
            {shaper, c2s_shaper},
            {max_stanza_size, 65536},
            tls, {certfile, "/etc/ejabberd/ejabberd.pem"}
            ]},

            {{< /file >}}

### Additional Functionality

The `ejabberd.cfg` file is thoroughly commented and complete. Your server should now be operational. However, take the time to familiarize yourself with the options provided in this file.

By default, Multi-User-Chats (MUCs) or chatrooms are accessible via the "conference.[hostname]" sub-domain. If you want the public to access MUCs on your domain, create an "A Record" pointing the `conference` hostname (e.g., subdomain) to the IP address where the ejabberd instance is located.

## Using Ejabberd

After installation, using and configuring ejabberd is straightforward. To start, stop, or restart the server, issue the appropriate command from the following:

               /etc/init.d/ejabberd start
               /etc/init.d/ejabberd stop
               /etc/init.d/ejabberd restart

By default, ejabberd is configured to disallow "in-band-registrations," preventing Internet users from obtaining accounts on your server without consent. To register a new user, use the following command format:

             ejabberdctl register lollipop example.com man

In this example, `lollipop` is the username, `example.com` is the domain, and man is the password. This creates a JID for `lollipop@example.com` with the password `"man."` Employ this format to create the specified administrative users.

To remove a user from your server, issue a command in the following format:

             ejabberdctl unregister lollipop example.com

The above command will unregister the `lollipop@example.com` account from the server.

To set or reset a user's password, use the following command:

             ejabberdctl set-password lollipop example.com morris

This command changes the password for the `lollipop@example.com` user to `morris`.

To back up ejabberd's database, execute the following command:

               ejabberdctl dump ejabberd-backup.db

This command exports the contents of the internal ejabberd database into a file located in the "/var/lib/ejabberd/" directory. To restore from this backup, issue the subsequent command:

               ejabberdctl load ejabberd-backup.db

For further information about the `ejabberdctl` command, consult `ejabberdctl` help or refer to `man ejabberdctl`.

If you prefer administering your ejabberd instance via the web-based interface, log in to http://example.com:5280/admin/, where "example.com" is the domain where ejabberd is running. Sign in using the full JID as the username and the password of one of the administrators specified in the /etc/ejabberd/ejabberd.cfg file.

## XMPP Federation and DNS

To ensure seamless federation of your ejabberd instance with the wider XMPP network, especially with Google's "GTalk" service (i.e. the ["@gmail.com](mailto:"@gmail.com)" chat tool,) it's crucial to configure the SRV records for the domain to point to the server where the ejabberd instance is running. Three records are needed and can be created using your DNS Management tool of choice:

- Service: `_xmpp-server` Protocol: TCP Port: 5269
- Service: `_xmpp-client` Protocol: TCP Port: 5222
- Service: `_jabber` Protocol: TCP Port: 5269

Ensure that the "target" of the SRV record points to the publicly routable hostname for that machine (e.g., "username.example.com"). Set both the priority and weight to `0`.

## Troubleshooting

If you encounter difficulties starting ejabberd or encounter obscure errors on the console, don't be disheartened. Error messages generated by Erlang are often complex. You can find the logs for ejabberd in the `/var/log/ejabberd/` directory. Check these files, particularly ejabberd.log and sasl.log, for error messages. Additionally, if ejabberd crashes, an "image dump" of Erlang will be saved in this directory. Initiate your error investigations by examining these files.

Furthermore, ejabberd's "Mnesia" database is located in the `/var/lib/ejabberd/` directory. If you suspect that the database has become corrupted, delete the files in this directory (e.g., `rm /var/lib/ejabberd/*`) and reload from a backup if necessary. This may be necessary if the hostname of the local machine changes.
