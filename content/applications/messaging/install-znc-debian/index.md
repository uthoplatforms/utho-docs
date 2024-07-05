---
slug: "Set Up ZNC on Debian: Your Gateway to Effortless IRC Connectivity"
description: 'Discover how to install ZNC, an open-source IRC bouncer application designed to maintain a persistent connection to IRC networks, on your Utho server with this step-by-step guide'
keywords: ["install znc", "irc bouncer", "znc on debian", "configure znc", "znc"]
tags: ["debian"]
modified: 2024-04-11
modified_by:
    name: 'Pawan Kumar'
published: 2014-08-21
title: 'Install ZNC from Source on Debian'
authors: ["Pawan Kumar"]
---

ZNC acts as an IRC bouncer, running on a server to maintain a continuous connection to an IRC network and store messages. It ensures that local IRC clients can connect and disconnect without losing chat sessions or missing messages. This guide will walk you through installing ZNC from source and configuring it.

{{< note >}}
This guide is intended for non-root users. Commands needing elevated privileges are prefixed with `sudo`. If you're unfamiliar with the sudo command, refer to our [Users and Groups](/docs/guides/linux-users-and-groups/) guide.
{{< /note >}}

## Before You Begin

1.  Ensure the system is up to date:

        sudo apt-get update && sudo apt-get upgrade -y

2.  Install the `build-essential` and `checkinstall` packages:

        sudo apt-get install build-essential checkinstall

3.  For SSL encryption to connect to the web interface (recommended), install `libssl-dev`:

        sudo apt-get install libssl-dev

## Install ZNC

1.  Download the latest version of ZNC (1.6.0 at the time of writing):

        wget http://znc.in/releases/znc-1.6.0.tar.gz

2.  Extract the archive file:

        tar -xvf znc-1.*.tar.gz

3.  Navigate into the ZNC directory:

        cd znc-1.6.0

4.  Run the `configure` script to check if the Utho has all the necessary prerequisites:

        ./configure

If any packages are missing, install them before proceeding.

5.  Install ZNC:

        make
        sudo checkinstall --fstrans=0 make install

{{< note respectIndent=false >}}
Use the `checkinstall` program to create a `.deb` package, allowing you to reinstall this version of ZNC later. It offers its own set of options for review. Alternatively, you can opt for `sudo make install` to install ZNC as is.
{{< /note >}}

## Configure ZNC

1.  Initiate the configuration process:

        znc --makeconf

2.  An interactive script will prompt you for input on various parameters. Below is an example output of the `makeconf` script with standard options selected. You can utilize or modify the provided input as per your requirements. If unsure, stick with the default option. Most of these settings can be adjusted later via the web interface.

{{< note respectIndent=false >}}
Ensure to update the username variable.
{{< /note >}}

        [ .. ] Checking for list of available modules...
        [ >> ] ok
        [ ** ] Building new config
        [ ** ]
        [ ** ] First let's start with some global settings...
        [ ** ]
        [ ?? ] What port would you like ZNC to listen on? (1025 to 65535): 5678
        [ ?? ] Would you like ZNC to listen using SSL? (yes/no) [no]: yes
        [ ?? ] Would you like ZNC to listen using both IPv4 and IPv6? (yes/no) [yes]:
        [ .. ] Verifying the listener...
        [ >> ] ok
        [ ** ]
        [ ** ] -- Global Modules --
        [ ** ]
        [ ** ] +-----------+----------------------------------------------------------+
        [ ** ] | Name      | Description                                              |
        [ ** ] +-----------+----------------------------------------------------------+
        [ ** ] | partyline | Internal channels and queries for users connected to znc |
        [ ** ] | webadmin  | Web based administration module                          |
        [ ** ] +-----------+----------------------------------------------------------+
        [ ** ] And 9 other (uncommon) modules. You can enable those later.
        [ ** ]
        [ ?? ] Load global module <partyline>? (yes/no) [no]:
        [ ?? ] Load global module <webadmin>? (yes/no) [no]: yes
        [ ** ]
        [ ** ] Now we need to set up a user...
        [ ** ]
        [ ?? ] Username (AlphaNumeric): user
        [ ?? ] Enter Password:
        [ ?? ] Confirm Password:
        [ ?? ] Would you like this user to be an admin? (yes/no) [yes]:
        [ ?? ] Nick [user]: user
        [ ?? ] Alt Nick [user_]:
        [ ?? ] Ident [user]:
        [ ?? ] Real Name [Got ZNC?]:
        [ ?? ] Bind Host (optional):
        [ ** ] Enabled user modules [chansaver, controlpanel]
        [ ** ]
        [ ?? ] Set up a network? (yes/no) [yes]:
        [ ** ]
        [ ** ] -- Network settings --
        [ ** ]
        [ ?? ] Name [freenode]:
        [ ?? ] Server host [chat.freenode.net]:
        [ ?? ] Server uses SSL? (yes/no) [yes]:
        [ ?? ] Server port (1 to 65535) [6697]:
        [ ?? ] Server password (probably empty):
        [ ?? ] Initial channels:
        [ ** ] Enabled network modules [simple_away]
        [ ** ]
        [ .. ] Writing config [/home/elle/.znc/configs/znc.conf]...
        [ >> ] ok
        [ ** ]
        [ ** ] To connect to this ZNC you need to connect to it as your IRC server
        [ ** ] using the port that you supplied.  You have to supply your login info
        [ ** ] as the IRC server password like this: user/network:pass.
        [ ** ]
        [ ** ] Try something like this in your IRC client...
        [ ** ] /server <znc_server_ip> +5678 user:<pass>
        [ ** ]
        [ ** ] To manage settings, users and networks, point your web browser to
        [ ** ] https://<znc_server_ip>:5678/
        [ ** ]
        [ ?? ] Launch ZNC now? (yes/no) [yes]:
        [ .. ] Opening config [/home/elle/.znc/configs/znc.conf]...
        [ >> ] ok
        [ .. ] Loading global module [webadmin]...
        [ >> ] [/usr/local/lib/znc/webadmin.so]
        [ .. ] Binding to port [+5678]...
        [ >> ] ok
        [ ** ] Loading user [user]
        [ ** ] Loading network [freenode]
        [ .. ] Loading network module [simple_away]...
        [ >> ] [/usr/local/lib/znc/simple_away.so]
        [ .. ] Adding server [chat.freenode.net +6697 ]...
        [ >> ] ok
        [ .. ] Loading user module [chansaver]...
        [ >> ] ok
        [ .. ] Loading user module [controlpanel]...
        [ >> ] ok
        [ .. ] Forking into the background...
        [ >> ] [pid: 27369]
        [ ** ] ZNC - 1.6.0 - http://znc.in

Once you've finished configuring and started ZNC, access the web interface by entering your Utho's IP address in your web browser. Make sure to include the port you defined during the configuration process and prefix it with `https://`.

    {{< note respectIndent=false >}}
If you've followed the [Firewall portion](/docs/products/compute/compute-instances/guides/set-up-and-secure/#configure-a-firewall) of the [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/)guide, add a line to `/etc/iptables.firewall.rules` to allow traffic to your IRC port.
    {{< /note >}}

## Connect to The Client

### HexChat ###
You can connect to ZNC using any GUI or CLI client. Here, we'll demonstrate using [HexChat](https://hexchat.github.io/index.html).

1.  Launch HexChat, set your preferred nicknames, and create a new network. For this example, we'll name it **ZNCserver**:

2.  With **ZNCserver** selected, click on `Edit...`.

3.  Add your server's IP address and port to the list. If you're not using a signed certificate, choose *Accept invalid SSL certificates*. Enter your password:

Close the window once you're finished.

3.  Press **Connect**. You'll be connected to your ZNC server and can then join any networks and channels you've set up to autojoin.

### Konversation ###

1. Open Konversation and click on 'New...'

2. Enter a name for the new network. Let's name it **utho-znc** for this example. Then click on 'Add...' to open the dialog to add the server.

3. Enter your network details like IP Address, Port number, and password.

## Enabling SSL Encryption with a Signed Certificate (Optional)
If you want to use a signed certificate to encrypt your connection to ZNC, you can add your key and certificate to the `znc.pem` file.

    cat domain.key domain.crt > znc.pem
