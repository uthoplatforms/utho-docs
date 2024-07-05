---
slug: "Elevate Your Chats: Setting Up Ejabberd for Instant Messaging on CentOS 5"
deprecated: true
description: 'Embark on your journey with ejabberd, a robust instant messaging server crafted in Erlang/OTP, on CentOS 5'
keywords: ["ejabberd", "ejabberd on linux", "real-time messaging", "xmpp server", "collaboration software", "chat software", "linux jabber server"]
tags: ["centos"]
modified: 2024-05-09
modified_by:
  name: Utho
published: 2024-05-09
title: Instant Messaging Services with ejabberd on CentOS 5
relations:
    platform:
        key: how-to-install-ejabberd
        keywords:
            - distribution: CentOS 5
authors: ["Utho"]
---
Ejabberd, also known as the "Erlang Jabber Daemon," stands out as an extensible, adaptable, and high-performance XMPP server written in Erlang. With its web-based interface and comprehensive support for [XMPP standards](http://xmpp.org/), ejabberd serves as an ideal choice for general-purpose and multi-purpose XMPP servers. While it may be considered "heavyweight" due to Erlang runtime requirements, ejabberd boasts remarkable robustness and scalability, accommodating heavy workloads and even offering support for hosting multiple domains virtually.

To begin the installation process, ensure you have a functional installation of CentOS 5.4 and have followed the steps outlined in the [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide. Once these prerequisites are met, you're ready to proceed with the installation.

## XMPP/Jabber Basics

While you can run an XMPP server with minimal knowledge of the XMPP network and system, grasping the following fundamental concepts can be beneficial:

-    It serves as a unique identifier for a user in the XMPP network, resembling an email address. Typically, it comprises a username (identifying a user on a server), a hostname (identifying the server), and an optional resource indicating a user's location. For instance, in "username@example.com/office," "username" is the username, "example.com" is the hostname, and "/office" is the resource. The resource is optional but provides additional specificity.

        username@example.com/office

Once more, the resource remains discretionary. Although XMPP permits a single JID to link to the server from numerous devices (i.e., resources), the inclusion of a resource provides a beneficial level of detail.

-  The XMPP system is inherently federated, enabling users on one server to communicate with those on other servers. Each XMPP server functions as a communication gateway for its users, with no centralized authority. Server administrators can decide how their server participates in the federated network, such as enabling server-to-server (s2s) SSL/TLS encryption for federating with Google's "GTalk" XMPP network.

- XMPP utilizes ["SRV" DNS Records](/docs/guides/dns-overview/) to resolve domains to the servers providing DNS records, facilitating communication across the XMPP network.

## Set the Hostname

Execute the following commands to set the hostname of your Utho:

             echo "example" > /etc/hostname
             hostname -F /etc/hostname

In this instance, the hostname will be designated as "example". Additionally, you'll need to access the /etc/sysconfig/network file and amend the HOSTNAME line to match your newly assigned hostname:

             {{< file "/etc/sysconfig/network" >}}
             NETWORKING=yes NETWORKING_IPV6=no HOSTNAME=example

             {{< /file >}}

Subsequently, navigate to /etc/hosts and input your IP address, fully qualified domain name (FQDN), and hostname. Refer to the example provided below:

             {{< file "/etc/hosts" >}}
             123.123.123.123 example.com example

              {{< /file >}}

## Install ejabberd

The necessary packages to install ejabberd and its dependencies are not included in the standard CentOS repositories. Hence, to install ejabberd, we must incorporate the "EPEL" system. "[EPEL](https://fedoraproject.org/wiki/EPEL)", or "Extra Packages for Enterprise Linux," is a Fedora Project initiative aiming to deliver enterprise-grade software that is more up-to-date than what is typically offered in CentOS repositories. Enable EPEL using the following command:

    rpm -Uvh http://download.fedora.redhat.com/pub/epel/5/i386/epel-release-5-4.noarch.rpm

Execute the subsequent command to install ejabberd.

         yum update
         yum install ejabberd

## Configure ejabberd

Ejabberd's configuration files are scripted in Erlang syntax, which may pose challenges in comprehension. Fortunately, the adjustments required are relatively minor and straightforward. The primary ejabberd configuration file resides at `/etc/ejabberd/ejabberd.cfg` for this version. We'll address each pertinent option sequentially.

### Administrative Users

Certain users may require remote administration access to the XMPP server. By default, this section of the configuration file appears as follows:

                {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                {acl, admin, {user, "admin", "example.com"}}.

                {{< /file >}}

In Erlang, comments are initiated with the % character, and the access control list segment contains data in the format: `{user, "USERNAME", "HOSTNAME"}`. Below are examples corresponding to users with the JIDs of `admin@example.com` and `username@example.com`. You only need to specify one administrator, but additional administrators can be included by adding more lines, as demonstrated below:

                   {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                   {acl, admin, {user, "admin", "example.com"}}.
                   {acl, admin, {user, "username", "example.com"}}.

                   {{< /file >}}

Users defined in this manner possess complete administrative privileges over the server, accessible via both XMPP and web interfaces. It's necessary to create administrative users (as outlined below) before they can authenticate.

### Hostnames and Virtual Hosting

A single instance of ejabberd can cater to XMPP services for multiple domains concurrently, provided those domains (or subdomains) are hosted on the server. To incorporate a hostname for virtual hosting in ejabberd, adjust the hosts option. By default, ejabberd is configured to host the domain specified during installation. Additionally, include a `host` item for "localhost." Here are a few examples:

                {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                 {hosts, ["example.com", "localhost"]}.

                 {{< /file >}}

In the subsequent illustration, ejabberd is configured to host several additional domains, namely "username.example.com," "example.com," and "bampton.com."

                 {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                 {hosts, ["username.example.com", "example.com", "example.com", "localhost"]}.

                 {{< /file >}}

You can specify any number of hostnames in the host list, but be cautious to avoid line breaks, as this can lead to ejabberd failure.

### Listening Ports

The TCP port number 5222 is the conventional "XMPP" port. Should you wish to alter the port, this section of the configuration requires modification. Furthermore, if you intend to utilize SSL/TLS encryption for connections between clients and the server, you'll need to adjust the path to the certificate file in the following line:

              {{< file "/etc/ejabberd/ejabberd.cfg" >}}
              {certfile, "/etc/ejabberd/ejabberd.pem"}, starttls,

              {{< /file >}}

Additionally, you may want to enable SSL access for client-to-server (c2s) SSL/TLS connections if you or the other users of you are using a client that requires legacy support for secured connections on port 5223. Uncomment the following stanza.

               {{< file "/etc/ejabberd/ejabberd.cfg" >}}
               {5223, ejabberd_c2s, [
               {access, c2s},
               {shaper, c2s_shaper},
               {max_stanza_size, 65536},
               tls, {certfile, "/etc/ejabberd/server.pem"}
               ]},

               {{< /file >}}

### Additional Functionality

The `ejabberd.cfg` file is complete and well commented, and from this point forward your server should run. However, you should take the time to become familiar with the options provided in this file. We would only add, regarding the multi-user chats:

By default, MUCs or Multi-User-Chats (chatrooms) are accessible on the "conference.[hostname]" subdomain. If you want the public to be able to access MUCs on your domain, you need to create an "A Record" pointing the `conference` hostname (e.g. subdomain) to the IP address where the ejabberd instance is running.

## Using Ejabberd

Upon installation, using and configuring ejabberd is straightforward. To start, stop, or restart the server, execute the appropriate command for the `/etc/init.d/ejabberd` script:

               /etc/init.d/ejabberd start
               /etc/init.d/ejabberd stop
               /etc/init.d/ejabberd restart

Issue the following command to ensure that ejabberd starts after the next boot:

                chkconfig ejabberd on

By default, ejabberd is set to disallow "in-band-registrations," preventing internet users from creating accounts on your server without your authorization. To register a new user, execute a command in the following format:

                ejabberdctl register username example.com man

In this example, `username` represents the username, `example.com` is the domain, and man is the password. This action will establish a JID for `username@example.com` with the password `"man."` Utilize this format for creating the administrative users as specified above.

To remove a user from your server, use a command in the following format:

                ejabberdctl unregister username example.com

The above command would unregister the `username@example.com` account from the server.

To set or reset a user's password, issue the following command:

                ejabberdctl set-password username example.com morris

This command updates the password for the `username@example.com` user to `morris`.

To back up ejabberd's database, execute the following command:

                ejabberdctl dump ejabberd-backup.db

This command dumps the contents of the internal ejabberd database into a file located in the "/etc/ejabberd/" directory. To restore from the backup, use the following command:

                 ejabberdctl load ejabberd-backup.db

If you prefer administering your ejabberd instance via the web-based interface, log in to `http://example.com:5280/admin/`, where "example.com" represents the domain where ejabberd operates. Log in using the full JID and password of one of the administrators specified in the `/etc/ejabberd/ejabberd.cfg` file.

## XMPP Federation and DNS

To ensure seamless federation of your ejabberd instance with the wider XMPP network, especially with Google's "GTalk" service (i.e., the ["@gmail.com](mailto:"@gmail.com)" chat tool), it's crucial to configure the SRV records for your domain to point to the server hosting the ejabberd instance. Three records are required, which can be set up using your preferred DNS management tool:

- Service: `_xmpp-server` Protocol: TCP Port: 5269
- Service: `_xmpp-client` Protocol: TCP Port: 5222
- Service: `_jabber` Protocol: TCP Port: 5269

The "target" of the SRV record should direct to the publicly routable hostname of the machine (e.g., "example.example.com"). Both priority and weight should be set to `0`.

## Troubleshooting

If you encounter issues with starting ejabberd or encounter obscure errors on the console, don't be disheartened; errors generated by Erlang can often be cryptic. The logs for ejabberd are located in the `/opt/ejabberd-2.1.0_rc2/logs/` directory. If you're encountering error messages, check these files. Additionally, in the event of an ejabberd crash, the Erlang "image dump" will be saved in this directory. Initiate your error investigations by examining these files.
