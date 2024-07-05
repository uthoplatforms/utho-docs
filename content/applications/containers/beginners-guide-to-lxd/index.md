---
slug: "Unlocking LXD: A Novice's Journey"
description: "Explore the capabilities of LXD, a robust container hypervisor designed for managing Linux Containers. This comprehensive guide takes you through the process of installing, configuring, and leveraging LXD 3, enabling you to harness its full potential for your projects"
keywords: ["container", "lxd", "lxc", "apache", "virtual machine", "virtualization"]
tags: ["ubuntu","container","apache"]
published: 2024-04-24
modified: 2024-04-24
modified_by:
  name: Utho
title: "A Beginner's Guide to LXD: Setting Up an Apache Web Server"
title_meta: "How to Set Up an Apache Webserver in an LXD Container"
authors: ["Pawan Kumar"]
---

## What is LXD?
[LXD](https://linuxcontainers.org/lxd/), pronounced "Lex-Dee," is a container manager built on LXC (Linux Containers) and supported by Canonical. Unlike Docker, which focuses on delivering applications, LXD aims to offer a virtual machine-like experience through containerization, providing nearly full operating system functionality along with advanced features like snapshots, live migrations, and storage management.

The key advantages of LXD include its support for high-density containers and its superior performance compared to virtual machines. With just 2GB of RAM, you can comfortably run several [container images of major Linux distributions](https://us.images.linuxcontainers.org/). Moreover, LXD officially supports container images from major Linux distributions, allowing you to choose the distribution and version to run within the container.

This guide walks you through installing and setting up LXD 3 on a Utho, as well as configuring an Apache Web server in a container.

{{< note >}}
Throughout this guide, we'll use the term "container" to refer to the LXD system containers for simplicity.
{{< /note >}}

## Before You Begin
Begin by following the steps outlined in the [Creating a Compute Instance](/docs/products/compute/compute-instances/guides/create/) guide. Make sure to select a Utho instance with at least 2GB of RAM, such as the Utho 2GB plan. When prompted, choose the Ubuntu 19.04 distribution. If you prefer a different Linux distribution, ensure that it supports **snap packages (snapd)**. Refer to the More Information section for additional details.

Next, proceed with our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide to configure your system. Consider updating the system, setting the timezone, configuring the hostname, creating a limited user account, and enhancing SSH security as part of this process.

## Configure the Snap Package Support

LXD is available as a Debian package in the long-term support (LTS) versions of Ubuntu, such as Ubuntu 18.04 LTS. For other versions of Ubuntu and other distributions, LXD is available as a snap package. Snap packages are universal packages because there is a single package file that works on any supported Linux distributions. See the [More Information](#more-information) section for more details on what a snap package is, what Linux distributions are supported, and how to set it up.

1.  Verify that snap support is installed correctly. The following command either shows that there are no snap packages installed, or that some are.

LXD is conveniently available as a Debian package in the long-term support (LTS) versions of Ubuntu, like Ubuntu 18.04 LTS. However, for other versions of Ubuntu and various distributions, you can install LXD as a snap package. Snap packages are universal because they work on any supported Linux distribution. Refer to the [More Information](#more-information) section for additional details on snap packages, supported Linux distributions, and setup instructions.

To ensure snap support is correctly installed, use the following command. It will either display that there are no snap packages installed or list the installed snap packages.

        snap list

    {{< output >}}
    No snaps are installed yet. Try 'snap install hello-world'.
    {{< /output >}}

Check the details of the LXD snap package lxd by executing the following command:

The output will display information about the LXD snap package, including the available versions and channels. Currently, the latest version of LXD is `3.12`, which is in the default `stable` channel. This channel receives frequent updates with new features. Additionally, there are other channels available, such as `3.0/stable`, which provides the LTS (Long-Term Support) version of LXD supported until 2023, and `2.0/stable`, supported until 2021 and compatible with Ubuntu 16.04. For this guide, we'll use the latest version of LXD from the default `stable` channel.

        snap info lxd

    {{< output >}}
name:      lxd
summary:   System container manager and API
publisher: Canonical✓
contact:   https://github.com/lxc/lxd/issues
license:   Apache-2.0
description: |

**LXD is a system container manager**
LXD empowers you to manage numerous containers from various Linux distributions effortlessly. You can impose resource constraints, share directories, USB devices, or GPUs, and configure networks and storage as needed.

**Run any Linux distribution you want**
Choose from a wide array of pre-made images, including Ubuntu, Alpine Linux, ArchLinux, CentOS, Debian, Fedora, Gentoo, openSUSE, and more. If you can't find the distribution you want, creating your own images is straightforward using our `distrobuilder` tool or assembling your image tarball.

**Containers at scale**
LXD operates seamlessly across networks, with all interactions streamlined through a simple REST API. This means you can remotely manage containers on various systems, effortlessly copying and moving them as required. For larger operations, LXD offers built-in clustering support, transforming multiple servers into one powerful LXD server.

**Configuration options**
Supported configuration options for the LXD snap (`snap set lxd KEY=VALUE`):

- criu.enable: Enable experimental live-migration support [default=false]
- daemon.debug: Enhance logging to debug level [default=false]
- daemon.group: Define the group of users interacting with LXD [default=lxd]
- ceph.builtin: Utilize snap-specific Ceph configuration [default=false]
- openvswitch.builtin: Run a snap-specific OVS daemon [default=false]

  [Documentation](https://lxd.readthedocs.io)
snap-id: J60k4JY0HppjwOjW8dZdYc8obXKxujRu
channels:
  stable:        3.12        2019-04-16 (10601) 56MB -
  candidate:     3.12        2019-04-26 (10655) 56MB -
  beta:          ↑
  edge:          git-570aaa1 2019-04-27 (10674) 56MB -
  3.0/stable:    3.0.3       2018-11-26  (9663) 53MB -
  3.0/candidate: 3.0.3       2019-01-19  (9942) 53MB -
  3.0/beta:      ↑
  3.0/edge:      git-eaa62ce 2019-02-19 (10212) 53MB -
  2.0/stable:    2.0.11      2018-07-30  (8023) 28MB -
  2.0/candidate: 2.0.11      2018-07-27  (8023) 28MB -
  2.0/beta:      ↑
  2.0/edge:      git-c7c4cc8 2018-10-19  (9257) 26MB -
{{< /output >}}

Install the `lxd` snap package. Run the following command to install the snap package for LXD.

        sudo snap install lxd

    {{< output >}}
    lxd 3.12 from Canonical✓ installed
    {{< /output >}}

You can confirm the installation by running `snap list` again. The `core` snap package is a prerequisite for snap package support. Upon installing your first snap package, core is automatically installed and shared among all other snap packages.

        snap list

    {{< output >}}
Name  Version  Rev    Tracking  Publisher   Notes
core  16-2.38  6673   stable    canonical✓  core
lxd   3.12     10601  stable    canonical✓  -
{{< /output >}}


## Initialize LXD

Add your non-root Unix user to the `lxd` group:

        sudo usermod -a -G lxd username

Note: Replace <username> with your actual non-root Unix username. Adding the user to the `lxd` group allows running `lxc` commands without using `sudo`. Without this addition, you would need to prepend sudo to each lxc command.

Start a new SSH session or log out and log in again for the changes to take effect.

Verify the available free disk space by running:

        df -h /

    {{< output >}}
     Filesystem      Size  Used Avail Use% Mounted on
     /dev/sda         49G  2.0G   45G   5% /
     {{< /output >}}

In this case there is 45GB of free disk space. LXD requires at least 15GB of space for the storage needs of containers. We will allocate 15GB of space for LXD, leaving 30GB of free space for the needs of the server.

Run `lxd init` to initialize LXD. During this process,

        sudo lxd init

you'll encounter several prompts. Opt for the default settings for each option.

    {{< output >}}
    Would you like to use LXD clustering? (yes/no) [default=no]:
    Do you want to configure a new storage pool? (yes/no) [default=yes]:
    Name of the new storage pool [default=default]:
    Name of the storage backend to use (btrfs, ceph, dir, lvm, zfs) [default=zfs]:
    Create a new ZFS pool? (yes/no) [default=yes]:
    Would you like to use an existing block device? (yes/no) [default=no]:
    Size in GB of the new loop device (1GB minimum) [default=15GB]:
    Would you like to connect to a MAAS server? (yes/no) [default=no]:
    Would you like to create a new local network bridge? (yes/no) [default=yes]:
    What should the new bridge be called? [default=lxdbr0]:
    What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:
    What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:
    Would you like LXD to be available over the network? (yes/no) [default=no]:
    Would you like stale cached images to be updated automatically? (yes/no) [default=yes]
    Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]:
    {{< /output >}}

## Apache Web Server with LXD

This segment involves creating a container, installing the Apache web server, and configuring the necessary `iptables` rules to expose port 80.

Launch a new container:

        lxc launch ubuntu:18.04 web

Update the package list within the container:

        lxc exec web -- apt update

Install Apache in the LXD container:

        lxc exec web -- apt install apache2

Access a shell in the LXD container:


       lxc exec web -- sudo --user ubuntu --login

Modify the default Apache webpage to indicate it's running within an LXD container.

        sudo nano /var/www/html/index.html

Change line 224 in the Apache configuration file to read `It works inside a LXD container!`. After making this adjustment, save the file and exit.

Exit back to the host system since all the necessary modifications to the container have been completed.

        exit

Add a LXD **proxy device** to forward connections from the internet to port 80 (HTTP) on the server to port 80 within this container.

        sudo lxc config device add web myport80 proxy listen=tcp:0.0.0.0:80 connect=tcp:127.0.0.1:80

Note: In recent LXD versions, specify an IP address (like 127.0.0.1) rather than a hostname (such as localhost). If your container already has a proxy device using hostnames, alter the container configuration to replace them with IP addresses by executing `lxc config edit web`.

Now, from your local computer, visit your Utho's public IP address using a web browser. You should encounter the default Apache page:

## Common LXD Commands

*  List all containers:

        lxc list

          {{< output >}}

       To start your first container, try: lxc launch ubuntu:18.04

         +------+-------+------+------+------+-----------+
         | NAME | STATE | IPV4 | IPV6 | TYPE | SNAPSHOTS |
         +------+-------+------+------+------+-----------+
         {{< /output >}}

* Here's how to list all available repositories of container images:

        lxc remote list

        {{< output >}}
        +-----------------+------------------------------------------+---------------+-------------+--------+--------+
        |      NAME       |                   URL                    |   PROTOCOL    |  AUTH TYPE  | PUBLIC | STATIC |
        +-----------------+------------------------------------------+---------------+-------------+--------+--------+
        | images          | https://images.linuxcontainers.org       | simplestreams | none        | YES    | NO     |
        +-----------------+------------------------------------------+---------------+-------------+--------+--------+
        | local (default) | unix://                                  | lxd           | file access | NO     | YES    |
        +-----------------+------------------------------------------+---------------+-------------+--------+--------+
        | ubuntu          | https://cloud-images.ubuntu.com/releases | simplestreams | none        | YES    | YES    |
        +-----------------+------------------------------------------+---------------+-------------+--------+--------+
        | ubuntu-daily    | https://cloud-images.ubuntu.com/daily    | simplestreams | none        | YES    | YES    |
        +-----------------+------------------------------------------+---------------+-------------+--------+--------+
        {{< /output >}}

The repository `ubuntu` has container images of Ubuntu versions. The `images` repository has container images of a large number of different Linux distributions. The `ubuntu-daily` has daily container images to be used for testing purposes. The `local` repository is the LXD server that we have just installed. It is not public and can be used to store your own container images.

*Here's how to list all available container images from a repository:

        lxc image list ubuntu:

       {{< output >}}
       +------------------+--------------+--------+-----------------------------------------------+---------+----------+-------------------------------+
       |      ALIAS       | FINGERPRINT  | PUBLIC |                  DESCRIPTION                  |  ARCH   |   SIZE   |          UPLOAD DATE          |
       +------------------+--------------+--------+-----------------------------------------------+---------+----------+-------------------------------+
       | b (11 more)      | 5b72cf46f628 | yes    | ubuntu 18.04 LTS amd64 (release) (20190424)   | x86_64  | 180.37MB | Apr 24, 2019 at 12:00am (UTC) |
       +------------------+--------------+--------+-----------------------------------------------+---------+----------+-------------------------------+
       | c (5 more)       | 4716703f04fc | yes    | ubuntu 18.10 amd64 (release) (20190402)       | x86_64  | 313.29MB | Apr 2, 2019 at 12:00am (UTC)  |
       +------------------+--------------+--------+-----------------------------------------------+---------+----------+-------------------------------+
       | d (5 more)       | faef94acf5f9 | yes    | ubuntu 19.04 amd64 (release) (20190417)       | x86_64  | 322.56MB | Apr 17, 2019 at 12:00am (UTC) |
       +------------------+--------------+--------+-----------------------------------------------+---------+----------+-------------------------------+
       .....................................................................
       {{< /output >}}

Note: The first two columns for the alias and fingerprint provide an identifier that can be used to specify the container image when launching it.

The output snippet shows the container images for Ubuntu versions 18.04 LTS, 18.10, and 19.04. When creating a container, we can specify the short alias. For example, `ubuntu:b` means that the repository is ubuntu and the container image has the short alias `b` (for bionic, the codename of Ubuntu 18.04 LTS).

* Next, to get more information about a container image:

        lxc image info ubuntu:b

        {{< output >}}
        Fingerprint: 5b72cf46f628b3d60f5d99af48633539b2916993c80fc5a2323d7d841f66afbe
        Size: 180.37MB
        Architecture: x86_64
        Public: yes
        Timestamps:
        Created: 2019/04/24 00:00 UTC
        Uploaded: 2019/04/24 00:00 UTC
        Expires: 2023/04/26 00:00 UTC
        Last used: never
        Properties:
        release: bionic
        version: 18.04
        architecture: amd64
        label: release
        serial: 20190424
        description: ubuntu 18.04 LTS amd64 (release) (20190424)
        os: ubuntu
        Aliases:
        - 18.04
        - 18.04/amd64
        - b
        - b/amd64
        - bionic
        - bionic/amd64
        - default
        - default/amd64
        - lts
        - lts/amd64
        - ubuntu
        - amd64
        Cached: no
        Auto update: disabled
        {{< /output >}}

The output shows the details of the container image including all the available aliases. For Ubuntu 18.04 LTS, we can specify either `b` (for `bionic`, the codename of Ubuntu 18.04 LTS) or any other alias.

*  Launch a new container with the name `mycontainer`:

        lxc launch ubuntu:18.04 mycontainer

        {{< output >}}
        Creating mycontainer
        Starting mycontainer
        {{< /output >}}

*  Check the list of containers to ensure the new container is running:

        lxc list

        {{< output >}}
        +-------------+---------+-----------------------+---------------------------+------------+-----------+
        |    NAME     |  STATE  |         IPV4          |          IPV6             |    TYPE    | SNAPSHOTS |
        +-------------+---------+-----------------------+---------------------------+------------+-----------+
        | mycontainer | RUNNING | 10.142.148.244 (eth0) | fde5:5d27:...:1371 (eth0) | PERSISTENT | 0         |
        +-------------+---------+-----------------------+---------------------------+------------+-----------+
        {{< /output >}}

* Execute basic commands in `mycontainer`:

        lxc exec mycontainer -- apt update
        lxc exec mycontainer -- apt upgrade

Note: The characters `--` instruct the `lxc` command not to parse any more command-line parameters.

* Open a shell session within mycontainer:

        lxc exec mycontainer -- sudo --login --user ubuntu

        {{< output >}}
        To run a command as administrator (user "root"), use "sudo <command>".
        See "man sudo_root" for details.

        ubuntu@mycontainer:~$
        {{< /output >}}


Note: Ubuntu container images come with a default non-root account named `ubuntu`. This account has `sudo` privileges and can perform administrative tasks without requiring a password.

Use the `sudo` command to access the `ubuntu` account:

*  View the container logs:

        lxc info mycontainer --show-log

*  Stop the container:

        lxc stop mycontainer

*  Remove the container:

        lxc delete mycontainer

Note: You must stop a container before deleting it.

## Troubleshooting

### Error "unix.socket: connect: connection refused"

When you run any `lxc` command, you get the following error:

        lxc list

       {{< output >}}
       Error: Get http://unix.socket/1.0: dial unix /var/snap/lxd/common/lxd/unix.socket: connect: connection refused
       {{< /output >}}

This happens when the LXD service is not currently running. By default, the LXD service is running as soon as it is configured successfully. See [Initialize LXD](#initialize-lxd) to configure LXD.

### Error "unix.socket: connect: permission denied"

When executing any `lxc` command, you encounter the following error:

        lxc list

    {{< output >}}
    Error: Get http://unix.socket/1.0: dial unix /var/snap/lxd/common/lxd/unix.socket: connect: permission denied
    {{< /output >}}

This error occurs when your limited user account isn't part of the `lxd` group, or you haven't logged out and logged back in to update your group membership.

To check if your user account, for example, ubuntu, is a member of the lxd group, you can use the following command:

        groups ubuntu

        {{< output >}}
        ubuntu : ubuntu sudo lxd
        {{< /output >}}

In this case, since we're already members of the `lxd` group, all we need to do is log out and then log back in. If you find that you're not part of the lxd group, refer to the [Initialize LXD](#initialize-lxd) section to learn how to add your limited account to the `lxd` group.

## Next Steps
If you're running a single website, one proxy device pointing to the website container is all you need. But if you're managing multiple websites, consider setting up virtual hosts within the website container.

Alternatively, for separate containers for each website, you'll need to configure a reverse proxy within a container. In this scenario, the proxy device would route connections to the [a reverse proxy](/docs/guides/use-nginx-reverse-proxy/) container, which then forwards them to the appropriate individual website containers.
