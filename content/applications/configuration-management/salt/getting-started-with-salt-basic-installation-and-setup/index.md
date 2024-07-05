---
slug: "Salt Your System: A Simple Guide to Setting Up and Installing Salt"
description: 'Discover the Power of Salt, a Robust Server Management Platform that Centralizes Control Across Multiple Servers. Dive into this Easy Tutorial to Master Salt Installation.'
keywords: ["salt", "configuration management"]
modified: 2024-04-02
modified_by:
    name: Utho
published: 2015-09-22
title: "Savvy Salt: Kickstart Your Server Management Journey with Easy Installation and Setup"
tags: ["automation","salt"]
authors: ["Utho"]
---
[Salt](https://saltproject.io/) is a Python-based platform for managing server configurations. It lets you control multiple slave servers, known as Minions, from one master server. This guide helps you set up a Salt Master and Minion, and it works for any supported Linux distribution.

## Before You Begin

- Ensure you have a minimum of two Uthos: designate one as the Salt Master and the other(s) as Salt Minions.

-  Assign a distinct hostname to each Utho. This hostname will serve to differentiate them within Salt, so choose specific names (e.g., master, minion1, minion2, etc.).

-  It's advisable to configure private IP addresses for each system, especially if your Uthos are situated within the same data center.

## Install Using Salt Bootstrap

[Salt Bootstrap](https://repo.saltproject.io/#bootstrap)  is a handy configuration script that automatically identifies the operating system it's working with, configures the appropriate repositories, and installs Salt. This script is designed to be executed on both the Salt master and all minion machines for seamless setup.

**Salt Master**

    curl -L https://bootstrap.saltproject.io -o install_salt.sh
    sudo sh install_salt.sh -P -M -N

{{< note respectIndent=false >}}
The `-N` flag instructs not to install `salt-minion` as this system serves as the Salt master.
{{< /note >}}

**Salt Minions**

    curl -L https://bootstrap.saltproject.io -o install_salt.sh
    sudo sh install_salt.sh -P


## Coordinate Network Addressing

**Salt Master**

1.  Delete the `#` from the `interface:` line near the top of the file. Then, replace the address placeholder with the IP address of your Utho where the Salt master is located. If your Uthos reside in the same data center, you can utilize the Utho's private IP address.

        {{< file "/etc/salt/master" >}}

# The address of the interface to bind to:
    interface: 203.0.113.0
    {{< /file >}}

3. Restart Salt:

        sudo systemctl restart salt-master

**Salt Minions**

{{< note respectIndent=false >}}
Perform this step on every Salt minion.
{{< /note >}}

Remove the # from `master: salt` near the top of `/etc/salt/minion`, and substitute salt with the IP address of your Salt master.

    {{< file "/etc/salt/minion" >}}

Specify the location of the Salt master server. If the master server's address cannot be resolved, the minion will fail to start.

    master: 203.0.113.0
    {{< /file >}}

## Authenticate Minions to the Salt Master

### Get Salt Master Key Fingerprint

From the Salt master, display its key fingerprint along with the key fingerprints of all linked Minions.

    sudo salt-key --finger-all

Under *Unaccepted Keys*, you'll find the hostname or IP addresses of the minions along with a SHA256 fingerprint for each key. The fingerprints are shortened with ... to keep things tidy.

    {{< output >}}
    Local Keys:
    master.pem:  e9:6a:86:bf...
    master.pub:  4b:2a:81:79...
    Accepted Keys:
    Unaccepted Keys:
    minion1:  c7:b2:55:83:46...
    minion2:  f8:41:ce:73:f8...
    {{< /output >}}

### Configure Salt Minions

1.  Insert the Salt Master's `master.pub` fingerprint into `/etc/salt/minion`, enclosed within single quotes.

        {{< file "/etc/salt/minion" >}}

This fingerprint verifies the identity of your Salt master before the initial key exchange. Obtain the master fingerprint by executing "salt-key -f master.pub" on the Salt master.

    master_finger: '4b:2a:81:79...'
    {{< /file >}}

2.  Restart Salt:

        sudo systemctl restart salt-minion

3. List the fingerprint hash of each Minion and compare it with the information reported by the Salt Master in Step 1 above.

        sudo salt-call key.finger --local

### Accept Minions

1. Once you have verified the fingerprint of each Minion ID, accept all Minions from the Salt Master.

        sudo salt-key -A

    {{< note respectIndent=false >}}
To accept an individual minion, specify it by its hostname or IP address.

    sudo salt-key -a hostname
{{< /note >}}

2. Check the status of accepted minions. The following command should display the hostname or IP address of each verified and running minion.

        sudo salt-run manage.up

For additional details on Salt keys, refer to the *[salt-key](https://docs.saltproject.io/en/latest/ref/configuration/index.html)* main page.

## Test Master-Minion Connection

Ping all Minions:

    sudo salt '*' test.ping

Each Minion should display true in the output.

    {{< output >}}
    root@saltmaster:~# salt '*' test.ping
    minion1:
    True
    minion2:
    True
    {{< /output >}}

## Package Management Overview

To manage packages on Minions, you can utilize the *[pkg state module](https://docs.saltproject.io/en/latest/ref/states/all/salt.states.pkg.html)*. This module interacts with the package manager of your Linux distribution, such as `apt` for Debian-based systems or `yum` for RHEL-based systems, provided they are supported by SaltStack.

You can target specific Minions by specifying their hostname or IP address, or apply actions to all Minions using `*`.

Ensure that the package name matches the one in the system repositories of the Salt Minion. For instance, in Debian and Ubuntu, `apache` refers to the Apache httpd server package, while in RHEL-based systems, it's named `httpd`. Below are examples of installing or removing Apache on Debian or Ubuntu Salt Minions.

To install Apache on all Minions:

    sudo salt '*' pkg.install apache2

To remove Apache from `minion5`:

    sudo salt 'minion5' pkg.remove apache2

To list all packages installed on `minion1`:

    sudo salt 'minion1' pkg.list_pkgs

To manage services, utilize the *[service module](https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.service.html)*..

Restart Apache on all Minions:

    sudo salt '*' service.start apache2

View the status of the `mariadb` service on `minion1`:

    sudo salt 'minion1' service.status mariadb

## Next Steps
Salt is a comprehensive ecosystem that demands dedicated learning and hands-on practice to master effectively. The [Salt documentation](https://docs.saltproject.io/en/latest/) offers numerous examples, tutorials, and reference pages to support your journey.

To progress, begin by exploring *[Execution Modules](https://docs.saltproject.io/en/latest/ref/modules/all/index.html#all-salt-modules)* and *[Salt States](https://docs.saltproject.io/en/latest/ref/states/index.html)*. Understand their functionalities and consider how they can be leveraged in your configuration.
