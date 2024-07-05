---
slug: "Mastering Ansible Playbooks: Unleash Your Automation"
description: "This guide demonstrates the installation and setup of the Apache Web Server within an LXD container, followed by routing web traffic to the container for effective web hosting"
keywords: ["container", "lxd", "lxc", "virtual machine"]
tags: ["ubuntu","container","apache"]
published: 2024-05-03
modified: 2024-05-03
modified_by:
  name: Utho
title: "Access an Apache Web Server Inside a LXD Container"
authors: ["Pawan Kumar"]
---

## What is LXD?
[LXD](https://linuxcontainers.org/lxd/) (pronounced "Lex-Dee") is a robust system container manager, built on the foundation of LXC (Linux Containers), and backed by Canonical. It offers an experience akin to virtual machines but operates through containerization rather than virtualization. While Docker focuses on application delivery, LXD provides a comprehensive OS environment with features like snapshots, live migrations, and advanced storage management.

The key advantages of LXD include its ability to efficiently handle a high density of containers and its superior performance compared to virtual machines. Even a modest 2GB RAM system can comfortably accommodate several containers. Additionally, LXD boasts official support for [container images of major Linux distributions](https://us.images.linuxcontainers.org/), offering flexibility in choosing the distribution and version for container deployments.

This guide delves into setting up LXD on Utho, practical aspects of LXD usage, and troubleshooting common issues that may arise.

Note- hroughout this guide, we'll use the term "container" to refer to LXD containers for the sake of simplicity.

## Before You Begin
- If you haven't already, create a Utho account and Compute Instance by referring to our Getting Started with Utho and Creating a Compute Instance guides.

- Follow the steps outlined in our Setting Up and Securing a Compute Instance guide to update your system. You may also want to adjust the timezone, configure your hostname, create a limited user account, and enhance SSH access for security purposes.

## Mount Storage Volume
When configuring LXD, you have the option to store container data either in an [external volume](#block-storage-volume) like a Block Storage Volume, or within a [Disk](#disk) attached to your Utho.

### Block Storage Volume

Follow the [View, Create, and Delete Block Storage Volumes](/docs/products/storage/block-storage/guides/manage-volumes/) guide to create a block storage volume with a size of at least 20GB and attach it to your Utho. Remember to take note of the device name and the path to the Volume.

Note: Avoid formatting the volume and refrain from adding it to `/etc/fstab`.

- Edit your Configuration Profile and under **Boot Settings** select **GRUB 2** as your kernel.

- Reboot your Utho from the Utho Manager.

### Disk

Navigate to the Disks section in the Utho Manager and select Create a new disk.

Note: If your Utho's distribution disk is fully allocated, resize it before creating a storage disk. Refer to Resizing a Disk for guidance.

Edit your Utho's Configuration Profile. Navigate to **Block Device Assignment** and assign your new disk to `/dev/sdc`. Make a note of this path for configuring LXD in the next section.

- Under `Boot Settings`, select `GRUB 2` as your kernel.

- Reboot your Utho from the Utho Manager.

## Initialize LXD

Install the packages `lxd` and `zfsutils-linux`.

        sudo apt install lxd zfsutils-linux

- Add your Unix user to the `lxd` group.

        sudo usermod -a -G lxd username

- Start a new SSH session for this change to take effect.

- Run `lxd init` to initialize LXD.

        sudo lxd init

During the initialization process, you'll encounter several prompts. Opt for the defaults for all options except `Use existing block device?`. For this prompt, choose yes and provide the path to the storage volume added in the previous section.

## LXD Commands

1.  List all containers:

        lxc list

        {{< output >}}

          Generating a client certificate. This may take a minute...
          If this is your first time using LXD, you should also run: sudo lxd init
          To start your first container, try: lxc launch ubuntu:16.04

          +------+-------+------+------+------+-----------+
          | NAME | STATE | IPV4 | IPV6 | TYPE | SNAPSHOTS |
          +------+-------+------+------+------+-----------+

2.  List all available container images:

        lxc image list images:

        {{< output >}}
        +---------------------------------+--------------+--------+------------------------------------------+---------+----------+-------------------------------+
        |              ALIAS              | FINGERPRINT  | PUBLIC |               DESCRIPTION                |  ARCH   |   SIZE   |          UPLOAD DATE          |
        +---------------------------------+--------------+--------+------------------------------------------+---------+----------+-------------------------------+
        | alpine/3.4 (3 more)             | 39a3bf44c9d8 | yes    | Alpine 3.4 amd64 (20180126_17:50)        | x86_64  | 2.04MB   | Jan 26, 2018 at 12:00am (UTC) |
        +---------------------------------+--------------+--------+------------------------------------------+---------+----------+-------------------------------+
        | alpine/3.4/armhf (1 more)       | 9fe7c201924c | yes    | Alpine 3.4 armhf (20170111_20:27)        | armv7l  | 1.58MB   | Jan 11, 2017 at 12:00am (UTC) |
        +---------------------------------+--------------+--------+------------------------------------------+---------+----------+-------------------------------+
        | alpine/3.4/i386 (1 more)        | d39f2f2ba547 | yes    | Alpine 3.4 i386 (20180126_17:50)         | i686    | 1.88MB   | Jan 26, 2018 at 12:00am (UTC) |
        +---------------------------------+--------------+--------+------------------------------------------+---------+----------+-------------------------------+
        | alpine/3.5 (3 more)             | 5533a5247551 | yes    | Alpine 3.5 amd64 (20180126_17:50)        | x86_64  | 1.70MB   | Jan 26, 2018 at 12:00am (UTC) |
        +---------------------------------+--------------+--------+------------------------------------------+---------+----------+-------------------------------+
        | alpine/3.5/i386 (1 more)        | 5e93d5f4cae1 | yes    | Alpine 3.5 i386 (20180126_17:50)         | i686    | 1.73MB   | Jan 26, 2018 at 12:00am (UTC) |
        +---------------------------------+--------------+--------+------------------------------------------+---------+----------+-------------------------------+
        | alpine/3.6 (3 more)             | 5010616d9a24 | yes    | Alpine 3.6 amd64 (20180126_17:50)        | x86_64  | 1.73MB   | Jan 26, 2018 at 12:00am (UTC) |
        +---------------------------------+--------------+--------+------------------------------------------+---------+----------+-------------------------------+
        .....................................................................


Note: The first two columns display the alias and fingerprint, serving as identifiers for specifying the container image during launch.

- Launch a new container named mycontainer:

        lxc launch ubuntu:16.04 mycontainer

       {{< output >}}

        Creating mycontainer
        Starting mycontainer

- Verify the list of containers to ensure that the new container is running:

        lxc list

      {{< output >}}

      +-------------+---------+-----------------------+---------------------------+------------+-----------+
      |    NAME     |  STATE  |         IPV4          |          IPV6             |    TYPE    | SNAPSHOTS |
      +-------------+---------+-----------------------+---------------------------+------------+-----------+
      | mycontainer | RUNNING | 10.142.148.244 (eth0) | fde5:5d27:...:1371 (eth0) | PERSISTENT | 0         |
      +-------------+---------+-----------------------+---------------------------+------------+-----------+

Execute basic commands in `mycontainer`:

        lxc exec mycontainer -- apt update
        lxc exec mycontainer -- apt upgrade

Note: The `--` instructs the `lxc` command not to interpret any further command-line parameters.

Open a shell session within `mycontainer`:

        lxc exec mycontainer -- sudo --login --user ubuntu

    {{< output >}}

To execute a command as an administrator (user "root"), utilize "sudo <command>". For further information, refer to "man sudo_root".

    ubuntu@mycontainer:~$

    {{< /output >}}

Note: Ubuntu container images come with a default non-root account named `ubuntu`. This account has `sudo` privileges and can execute administrative tasks without requiring a password.

The `sudo` command grants access to the existing `ubuntu` account.

- View the container logs:

        lxc info mycontainer --show-log

- Stop the container:

        lxc stop mycontainer

- Remove the container:

        lxc delete mycontainer


## Apache Web Server with LXD

This section will create a container, install the Apache web server, and add the appropriate `iptables` rules to expose port 80.

- Launch a new container:

        lxc launch ubuntu:16.04 web

- Update the package list in the container.

        lxc exec web -- apt update

- Install Apache in the LXD container.


        lxc exec web -- apt install apache2

- Add the `iptables` rule to expose port 80. This rule redirects incoming connections on port 80 of the public IP address to port 80 of the container.

Ensure to replace `your_public_ip` and `your_container_ip` with your actual public IP and container IP respectively in the following command.

        PORT=80 PUBLIC_IP=your_public_ip CONTAINER_IP=your_container_ip sudo -E bash -c 'iptables -t nat -I PREROUTING -i eth0 -p TCP -d $PUBLIC_IP --dport $PORT -j DNAT --to-destination $CONTAINER_IP:$PORT -m comment --comment "forward to the Apache2 container"'

- To make the `iptables` rule persist on reboot, install `iptables-persistent`. When prompted to save the IPv4 and IPv6 rules, select Yes to save them.

        sudo apt install iptables-persistent

- Next, from your local computer, open a web browser and navigate to your Utho's public IP address. You should see the default Apache page.

## Next Steps
If you intend to host a single website, a single iptables rule pointing to the website container will be adequate. However, if you plan to host multiple websites, you'll need to install a web server like NGINX and configure [a reverse proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/) within a container. Then, the `iptables` rule would redirect traffic to this container.
