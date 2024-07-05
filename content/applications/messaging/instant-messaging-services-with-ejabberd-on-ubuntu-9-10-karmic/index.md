---
slug: "Unlock Instant Messaging Brilliance: Mastering Ejabberd on Ubuntu 9.10 Karmic"
deprecated: true
description: 'Embark on your journey with ejabberd, a robust instant messaging server crafted in Erlang/OTP, tailored specifically for Ubuntu 9.10 (Karmic)'
keywords: ["ejabberd", "ejabberd ubuntu karmic", "ejabberd ubuntu 9.10", "ejabberd on linux", "real-time messaging", "xmpp server", "collaboration software", "chat software", "linux jabber server"]
tags: ["ubuntu"]
modified: 2024-05-10
modified_by:
  name: Utho
published: 2024-05-10
title: 'Instant Messaging Services with ejabberd on Ubuntu 9.10 (Karmic)'
relations:
    platform:
        key: how-to-install-ejabberd
        keywords:
            - distribution: Ubuntu 9.10
authors: ["Utho"]
---
Ejabberd, crafted in the Erlang programming language, stands as a potent Jabber daemon. It boasts extensibility, flexibility, and remarkable performance. Equipped with a user-friendly web interface and comprehensive support for [XMPP standards](http://xmpp.org/), it emerges as an excellent choice for a versatile XMPP server. While some may deem it "heavyweight" due to its reliance on Erlang run-times, its robustness and scalability defy criticism, enabling it to gracefully handle heavy loads. Ejabberd servers serve as the backbone for some of the most expansive Jabber networks in operation today.

To embark on the installation journey, ensure you have a functional setup of Ubuntu 9.10 (Karmic) and have followed the steps outlined in the "Setting Up and Securing a Compute Instance" guide. Additionally, ensure your Ubuntu Karmic instance is up to date. Assuming you're securely connected to your Utho via SSH as root, we can proceed with the installation process.

## XMPP/Jabber Basics

While it's possible to operate an XMPP server with minimal knowledge of the underlying network and system architecture, grasping the following fundamental concepts can prove beneficial:

Serving as a unique identifier within the XMPP network, the JID resembles an email address. It typically comprises the username (identifying a specific user on a server), the hostname (identifying the server), and optionally, a resource (denoting the user's location). For instance, in the example "username@example.com/office," "username" represents the username, "example.com" denotes the hostname, and "/office" specifies the resource.

        username@example/office

Once again, it's worth noting that the resource is not mandatory; however, within XMPP, it offers a level of specificity that can be quite useful.

Additionally, XMPP operates on a federated model, allowing users from different servers to communicate provided server administrators permit it. Each XMPP server maintains its own user accounts and acts as a gateway for its users. This decentralized structure ensures there's no single point of failure, with each server administrator having the autonomy to determine their server's level of participation in the federated network. For instance, to federate with Google's "GTalk" XMPP network, server administrators must enable server-to-server (s2s) SSL/TLS encryption, although this may not always be necessary for other servers.

Furthermore, XMPP relies on ["SRV" DNS records](/docs/guides/dns-overview/) to facilitate domain resolution to the appropriate servers providing DNS records.

## Enabling the Universe Repository

Before proceeding with the installation of the ejabberd daemon, ensure that the `universe` repository is enabled. Navigate to `/etc/apt/sources.list` using your preferred text editor and verify the presence of the following lines:

                        {{< file "/etc/apt/sources.list" >}}
                        deb http://us.archive.ubuntu.com/ubuntu/ karmic universe
                        deb-src http://us.archive.ubuntu.com/ubuntu/ karmic universe

                        deb http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe
                        deb-src http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe

                        deb http://security.ubuntu.com/ubuntu karmic-security universe
                        deb-src http://security.ubuntu.com/ubuntu karmic-security universe

                        {{< /file >}}

                        Execute the following commands:

                        apt-get update
                        apt-get upgrade

## Install ejabberd

To install ejabberd along with its necessary dependencies, run the following command:

    apt-get install ejabberd

The default installation is fully operational and includes a self-signed SSL certificate. However, if you prefer to use a commercially signed certificate, place the certificate file at `/etc/ejabberd/ejabberd.pem`. In many cases, a self-signed certificate suffices for most Jabber applications.

If you haven't already configured your `/etc/hosts` file as shown below, please do so before proceeding. This configuration ensures that your utho associates its hostname with the public IP address. Your file should resemble the following excerpt, with your utho's public IP address replacing 12.34.56.78:

                               {{< file "/etc/hosts" >}}
                               127.0.0.2    localhost.localdomain   localhost
                               12.34.56.77  username.example.com  username

                               {{< /file >}}

Once the hostname is configured, you can proceed with the configuration of ejabberd.

## Configure ejabberd

Ejabberd's configuration files are composed in Erlang syntax, which may appear daunting at first. However, the modifications required are generally straightforward. The primary ejabberd configuration file resides at `/etc/ejabberd/ejabberd.cfg`. We will discuss each pertinent option sequentially.

### Administrative Users

Certain users may require remote administration access to the XMPP server. By default, this section of the configuration file appears as follows:

                      {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                      %% Admin user {acl, admin, {user, "", "localhost"}}.

                      {{< /file >}}

In Erlang, comments are indicated by the `%` sign, and the Access Control List (ACL) segment contains data structured as follows: `{user, "USERNAME", "HOSTNAME"}`. The subsequent examples correspond to users with the JIDs `admin@example.com` and `username@example.com`. You only need to specify one administrator, but additional administrators can be added by appending more lines, as demonstrated below:

                      {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                      {acl, admin, {user, "admin", "example.com"}}.
                      {acl, admin, {user, "username", "example.com"}}.

                       {{< /file >}}

Users specified in this manner possess full administrative privileges over the server through both XMPP and web-based interfaces. Administrative users must be created (as outlined below) before they can log in.

### Hostnames and Virtual Hosting

A single instance of ejabberd possesses the capability to deliver XMPP services across multiple domains concurrently, provided those domains (or subdomains) are hosted by the server. To include a hostname for virtual hosting in ejabberd, adjust the hosts option. By default, ejabberd is configured to `host` the "localhost" domain.

                          {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                           {hosts, ["localhost"]}.

                          {{< /file >}}

In the following illustration, ejabberd has been tailored to accommodate several additional domains: "username.example.com," "example.com," and "example.com."

                           {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                           {hosts, ["username.example.com", "example.com", "example.com"]}.

                           {{< /file >}}

You can enumerate any number of hostnames in the host list, but exercise caution to prevent inadvertent line breaks as they could lead to ejabberd failure.

### Listening Ports

The conventional port for XMPP communication is TCP port number 5222. Should you wish to modify the port, this section of the configuration requires adjustment.

Moreover, if you or other users necessitate legacy support for secured connections on port 5223, you may consider enabling SSL access for client-to-server (c2s) SSL/TLS connections. Uncomment the subsequent stanza to activate this feature.

                 {{< file "/etc/ejabberd/ejabberd.cfg" >}}
                 {5223, ejabberd_c2s, [
                 {access, c2s},
                 {shaper, c2s_shaper},
                 {max_stanza_size, 65536},
                 tls, {certfile, "/etc/ejabberd/ejabberd.pem"}
                 ]},

                {{< /file >}}

### Additional Functionality

The `ejabberd.cfg` file is meticulously commented and complete, signaling the readiness of your server. However, familiarizing yourself with the provided options in this file is advisable.

By default, Multi-User-Chats (MUCs or chatrooms) are accessible via the "conference.[hostname]" sub-domain. If you intend for the public to access MUCs on your domain, create an "A Record" directing the `conference` hostname (e.g., subdomain) to the IP address where the ejabberd instance operates.

## Using Ejabberd

Once installed, utilizing and configuring ejabberd is straightforward. To initiate, halt, or restart the server, execute the appropriate command from the list provided below:

                      /etc/init.d/ejabberd start
                      /etc/init.d/ejabberd stop
                      /etc/init.d/ejabberd restart

By default, ejabberd is configured to prohibit "in-band-registrations," thereby preventing Internet users from acquiring accounts on your server without authorization. To register a new user, employ a command structured as follows:

                      ejabberdctl register lollipop example.com man

In this instance, `lollipop` represents the username, example.com denotes the domain, and man signifies the password. This action will generate a JID for `lollipop@example.com` with the password `"man."` Utilize this format to create the administrative users specified previously.

To remove a user from your server, issue a command formulated as follows:

                      ejabberdctl unregister lollipop example.com

The preceding command will unregister the `lollipop@example.com` account from the server.

To set or reset a user's password, utilize the following command:

              ejabberdctl set-password lollipop example.com morris

This command modifies the password for the `lollipop@example.com` user to `morris`.

To back up ejabberd's database, execute the following command:

              ejabberdctl dump ejabberd-backup.db

This command exports the contents of the internal ejabberd database into a file located within the "/var/lib/ejabberd/" directory. To restore from the backup, employ the following command:

              ejabberdctl load ejabberd-backup.db

For further insight into the `ejabberdctl` command, access `ejabberdctl help` or `man ejabberdctl`.

If you prefer managing your ejabberd instance via the web-based interface, access `http://example.com:5280/admin/`, where "example.com" represents the domain hosting ejabberd. Log in using the full JID as the username and the password of one of the administrators specified in the `/etc/ejabberd/ejabberd.cfg` file.

## XMPP Federation and DNS

To ensure seamless federation of your ejabberd instance with the wider XMPP network, particularly with Google's "GTalk" service (i.e. the ["@gmail.com](mailto:"@gmail.com)" chat tool,), set the SRV records for the domain to direct to the server running the ejabberd instance. Establish three records, which can be configured in the DNS Management tool of your choice:

- Service: `_xmpp-server` Protocol: TCP Port: 5269
- Service: `_xmpp-client` Protocol: TCP Port: 5222
- Service: `_jabber` Protocol: TCP Port: 5269

The "target" of the SRV record should point to the publicly routable hostname for that machine (e.g., "username.example.com"). Set both the priority and weight to `0`.

## Troubleshooting

If encountering difficulties starting ejabberd or encountering cryptic errors on the console, don't be disheartened: Erlang's error messages can often be opaque. The logs for ejabberd reside in the `/var/log/ejabberd/` directory. If encountering error messages, particularly refer to `ejabberd.log` and `sasl.log`. Additionally, in the event of an ejabberd crash, Erlang's "image dump" will be saved in this directory. Begin investigations for error messages within these files.

Moreover, ejabberd's "Mnesia" database is housed in the `/var/lib/ejabberd/` directory. If suspecting database corruption, delete the files within this directory (e.g., `rm /var/lib/ejabberd/*`) and reload from a backup if necessary. This step may be required, especially if the local machine's hostname changes.
