---
slug: "Unlocking Container Power: Easy OpenVZ Installation on Debian 9"
description: "Unlock Virtualization Magic: Learn How to Install OpenVZ on Your utho and Create Virtual Environments Effortlessly"
og_description: 'Discover the Power of OpenVZ: Learn how to Install and Manage Virtual Linux Environments on your utho with Ease'
keywords: ["openvz", " virtualization", " docker"]
tags: ["container","docker","debian"]
published: 2024-05-03
modified: 2024-05-03
modified_by:
  name: Utho
title: 'How to Install OpenVZ On Debian 9'
authors: ["Pawan Kumar"]
---

## What is OpenVZ?

OpenVZ is a software-based OS virtualization tool enabling the deployment, management, and modification of isolated virtual Linux environments from within a host Linux distribution. An extensive array of prebuilt OS templates in a variety of Linux distributions allow users to rapidly download and deploy virtual environments with ease.

### Before You Begin

- To embark on this journey, you'll need a root user account. The commands in this guide assume root access, but if you're using a limited user account, just prefix commands with `sudo` as needed. If you haven't set up a limited user account yet.

- Please note that the steps outlined here are tailored specifically for Debian 9 and may not be suitable for other Debian or Ubuntu distributions.

- Before diving in, understand that certain tweaks are required on your Debian 9 system to accommodate OpenVZ. This includes swapping out Systemd for SystemV and adopting a custom Linux kernel. Ensure that all existing software on your system is compatible with these changes before proceeding

Note: While not mandatory, it's advisable to set up a separate Ext4 filesystem partition for OpenVZ templates. By default, both the Debian 9 installer and the utho Manager format newly created partitions with Ext4. For guidance on configuring this setup.


### Optional: Create A Separate Partition For OpenVZ Templates

If you're dedicating a Utho VPS solely to OpenVZ and no other services, it's wise to partition it accordingly. Below is a recommended partitioning scheme:

| Partition | Description | Typical Size |
|:---------:|:-----------:|:------------:|
| /         | Root partition | 4-12 GB   |
| swap      | Paging partition | 2 times RAM or RAM + 2GB (depending on available hard drive space) |
| /vz       | Partition to host OpenVZ templates | All remaining hard drive space |

Access your Utho Manager, select your Utho, and power it down. Confirm completion in the Host Job Queue. Under the Disks tab, hit Create a new Disk. Label it as you prefer, choose "ext4" as the Type, and allocate the desired space in the Size field. Click Save Changes for an optimal setup.

Navigate to your main Configuration Profile under the Dashboard tab. Assign the new partition to an open device under the Block Device Assignment tab. Click Save Changes when done.

Boot up the Utho and SSH into it. Use the command below to ensure the new disk is correctly created. You should see your newly minted disk in the output.

       fdisk -l

- Create a mount point for the new device:

        mkdir /vztemp

- Mount the new disk, replacing `/dev/sdc` with your device name:

        mount /dev/sdc /vztemp

### Remove the Metadata Checksum Feature From Ext4 Volumes

Before installing OpenVZ, ensure your system is prepped for compatibility. Debian 9 introduces a new checksum feature that's incompatible with custom OpenVZ kernels. Choose between removing `metadata_csum` from a mounted partition or reformatting the affected partition to a compatible Ext4 volume. Follow the instructions in the relevant section below.

- List available disk partitions.

        lsblk

Verify if `metadata_csum` is enabled on any mounted disk partitions listed in Step 1 (excluding the SWAP partition). For each partition, use the following format, replacing `/dev/sda1` with the relevant volume name. If the command doesn't return any output for mounted disk volumes, you can proceed to the next step.

        dumpe2fs -h /dev/sda1 2>/dev/null | grep -e metadata_csum

### Remove Metadata Checksum From Mounted Partitions

Execute the commands below to append code to the `fsck` file:

        echo "copy_exec /sbin/e2fsck" | sudo tee -a /usr/share/initramfs-tools/hooks/fsck
        echo "copy_exec /sbin/tune2fs" | sudo tee -a /usr/share/initramfs-tools/hooks/fsck

Create a new file in the specified directory and name it tune. Paste the provided text into this file and save it:

    {{< file "/etc/initramfs-tools/scripts/local-premount/tune" sh >}}

    #!/bin/sh

    if [ "$readonly" != "y" ] ;
    then exit 0 ;
    fi

    e2fsck -f $Volume
    tune2fs -O -metadata_csum $Volume
    e2fsck -f $Volume

    {{< /file >}}

Update the file properties and the existing initramfs image to load the *tune* script:

        chmod 755 /etc/initramfs-tools/scripts/local-premount/tune
        update-initramfs -u -k all

- Reboot your system and execute the command below to confirm that `metadata_csum` has been disabled from all relevant partitions. Make sure to replace "/dev/sda1" with the correct volume names.

        dumpe2fs -h /dev/sda1 2>/dev/null | grep -e metadata_csum

### Format a Compatible Ext4 Volume

- Select the Ext4 volume you wish to format and enter the command below, substituting `/dev/sda3` with your chosen volume. A "0" output signifies successful completion.

Note: Using the `mkfs` command to format a volume may lead to data loss.

        mkfs -t ext4 -O -metadata_csum /dev/sda3

## Replace Systemd with SystemV

- Install SystemV utilities.

        apt install sysvinit-core sysvinit-utils

- Reboot your machine from the utho Manager to switch from Systemd.


- Remove Systemd from your machine:


        apt --auto-remove remove systemd

- Create a file named `avoid-systemd` and insert the following contents:

       {{< file "/etc/apt/preferences.d/avoid-systemd" >}}
       Package: *systemd*
       Pin: release *
       Pin-Priority: -1

       {{< /file >}}

### Add OpenVZ Repository

- Create a new repository source file and insert the following contents:

       {{< file "/etc/apt/sources.list.d/openvz.list" sourceslist >}}
       deb http://download.openvz.org/debian jessie main
       deb http://download.openvz.org/debian wheezy main

       {{< /file >}}

- Add the repository key to your system:

        wget -qO - http://ftp.openvz.org/debian/archive.key | sudo apt-key add -

As of the publication date of this guide, the OpenVZ repository key is invalid, resulting in a warning from the system when issuing the `apt update` command. Despite the warning, the command should succeed. If it does not, update the system with the following argument:

        apt --allow-unauthenticated update

### Install OpenVZ Packages

Install OpenVZ along with the required packages.

        KPackage="linux-image-openvz-$(dpkg --print-architecture)"
        sudo apt --allow-unauthenticated --install-recommends install $KPackage vzdump ploop initramfs-tools dirmngr

After installation, if the directory `/vz` does not exist, create a symbolic link using the following command:

        ln -s /var/lib/vz/ /vz

Create a file named `vznet.conf` and insert the following line:

    {{< file "/etc/vz/vznet.conf" >}}
    EXTERNAL_SCRIPT="/usr/sbin/vznetaddbr"
    {{< /file >}}


- This step is optional and will ensure that OpenVZ virtual instances stop when the OpenVZ service is stopped. If this behavior is desired, execute the following command:

        echo 'VE_STOP_MODE=stop' | sudo tee -a /etc/vz/vznet.conf

## Boot Into The OpenVZ Kernel

To ensure that the OpenVZ kernel is booted each time the server restarts, the system must be configured accordingly.

- Open the `grub.cfg` file using `less` or your preferred text editor:

        less /boot/grub/grub.cfg

- Look for a section similar to the following within the `grub.cfg` file:

       {{< file "/boot/grub/grub.cfg" cfg >}}
       . . .

       menuentry 'Debian GNU/Linux' --class debian --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-simple-e025e52b-91c4-4f64-962d-79f244caa92a' {
       gfxmode $linux_gfx_mode
       insmod gzio
       if [ x$grub_platform = xxen ]; then insmod xzio; insmod lzopio; fi
       insmod ext2
       set root='hd0'
       if [ x$feature_platform_search_hint = xy ]; then
      search --no-floppy --fs-uuid --set=root --hint-bios=hd0 --hint-efi=hd0 --hint-baremetal=ahci0  e025e52b-91c4-4f64-962d-79f244caa92a
      else
      search --no-floppy --fs-uuid --set=root e025e52b-91c4-4f64-962d-79f244caa92a
      fi
      echo    'Loading Linux 4.9.0-3-amd64 ...'
      linux   /boot/vmlinuz-4.9.0-3-amd64 root=/dev/sda ro console=ttyS0,19200n8 net.ifnames=0
      echo    'Loading initial ramdisk ...'
      initrd  /boot/initrd.img-4.9.0-3-amd64
      }
      submenu 'Advanced options for Debian GNU/Linux' $menuentry_id_option 'gnulinux-advanced-e025e52b-91c4-4f64-962d-79f244caa92a'

      . . .

      {{< /file >}}

Copy the text entry preceeding `submenu`, in this example the text would be: **Advanced options for Debian GNU/Linux**.

- In the `grub.cfg` file, after the "submenu" line, you'll find several indented "menuentry" sections. Each of these sections represents a different kernel option. Look through these entries to find the newly installed OpenVZ kernel menu entry. It will resemble the example below. Ignore any entries labeled as recovery kernels:

       {{< file "/boot/grub/grub.cfg" cfg >}}
       . . .

       menuentry 'Debian GNU/Linux, with Linux 2.6.32-openvz-042stab123.9-amd64' --class debian --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-2.6.32-openvz-042stab123.9-amd64-advanced-e025e52b-91c4-4f64-962d-79f244caa92a' {
       gfxmode $linux_gfx_mode
       insmod gzio
       if [ x$grub_platform = xxen ]; then insmod xzio; insmod lzopio; fi
       insmod ext2
       set root='hd0'
       if [ x$feature_platform_search_hint = xy ]; then
       search --no-floppy --fs-uuid --set=root --hint-bios=hd0 --hint-efi=hd0 --hint-baremetal=ahci0  e025e52b-91c4-4f64-962d-79f244caa92a
       else
       search --no-floppy --fs-uuid --set=root e025e52b-91c4-4f64-962d-79f244caa92a
       fi
       echo    'Loading Linux 2.6.32-openvz-042stab123.9-amd64 ...'
       linux   /boot/vmlinuz-2.6.32-openvz-042stab123.9-amd64 root=/dev/sda ro console=ttyS0,19200n8 net.ifnames=0
       echo    'Loading initial ramdisk ...'
       initrd  /boot/initrd.img-2.6.32-openvz-042stab123.9-amd64
       }

       . . .

       {{< /file >}}

Again, write down the text directly after "menuentry" in single quotes. Here, the text to copy is **Debian GNU/Linux, with Linux 2.6.32-openvz-042stab123.9-amd64**.

Close the `grub.cfg` file and open `/etc/default/grub` in your preferred text editor. Look for the line starting with `GRUB_DEFAULT=`. Remove the default value and replace it with the text you copied earlier, following this format. For instance, if the copied text is `Debian GNU/Linux, with Linux 2.6.32-openvz-042stab123.9-amd64`, the line should be:

        GRUB_DEFAULT="Advanced options for Debian GNU/Linux>Debian GNU/Linux, with Linux 2.6.32-openvz-042stab123.9-amd64"

Note that both copied strings are separated with the carrot ">" character.

Save and close the **grub** file. Then, use the following command to reload the GRUB bootloader with the new kernel value:

        update-grub

By default, kernel loading is handled by the utho Manager, not Grub. Access your utho Manager, select your utho, and navigate to your configuration profile. In the "Boot Settings" section, choose "GRUB 2" from the Kernel dropdown list (refer to the image below). Save your changes and exit.

Reboot your server. After the reboot, execute the command below to confirm that the OpenVZ kernel was successfully loaded:

        uname -r

If the OpenVZ kernel was not loaded, it is most likely the **grub** file that is misconfigured. Check and make certain the correct kernel was chosen and entered correctly.

### Download And Deploy An OS Template

Start the OpenVZ service:

        service vz start
        service vz status

Register with the official OpenVZ template repository:

        sudo gpg --recv-keys $(echo $(sudo gpg --batch --search-keys security@openvz.org 2>&1 | grep -ie ' key.*created' | sed -e 's|key|@|g' | cut -f 2 -d '@') | cut -f 1 -d ' ' | cut -f 1 -d ',')

`Edit /etc/vz/vz.conf` and modify the following line to use `simfs` instead of `ploop`:

    {{< file "/etc/vz/vz.conf" >}}
VE_LAYOUT=simfs
{{< /file >}}

View the list of available OS templates for download:

        vztmpl-dl --list-remote

Choose a template from the list and download it using the following command format. Replace centos7-x86_64 with your chosen template:

        vztmpl-dl --gpg-check centos7-x86_64

Each installed OS template in OpenVZ is referred to as a "Container." Create a Container ID (CTID) for the downloaded template with the command below. Replace [CTID] with a number (101 is recommended) and the CentOS 7 template name with your downloaded template:

        vzctl create [CTID] --ostemplate centos7-x86_64

- If you've allocated a separate disk partition for OpenVZ templates, utilize the command below to create the container within the new disk. Replace *--ostemplate* with your template name and *--name* with your preferred descriptive name:

        vzctl create [CTID] --ostemplate debian-8.0-x86_64 --layout simfs --name centos7 --private /vztemp/vz/private/$VEID --root /vztemp/vz/root/$VEID --config basic

Now that an OS template configuration file has been generated, locate it using the path provided in the previous command's output. The configuration file follows the [CTID].conf naming format.

Open this file to implement the following changes:

- Assign an IP address to your virtual environment. The recommended format is 192.168.0.[CTID]. For example, if the CTID is 101, the IP address would be 192.168.0.101.
- Specify a nameserver. Using Google's nameserver (8.8.8.8) should suffice.
- If encountering difficulties booting into the virtual environment, consider reverting `VE_LAYOUT` to `ploop` from `simfs`.

Feel free to configure other options as needed, such as SWAP and RAM allocation. Save and close the file once modifications are complete.

    {{< file "/etc/vz/conf/101.conf" >}}
. . .

## RAM
     PHYSPAGES="0:256M"

## Swap
     SWAPPAGES="0:512M"

## Disk quota parameters (in form of softlimit:hardlimit)
     DISKSPACE="2G:2.2G"
     DISKINODES="131072:144179"
     QUOTATIME="0"

## CPU fair scheduler parameter
     CPUUNITS="1000"

     NETFILTER="stateless"
     VE_ROOT="/var/lib/vz/root/$VEID"
     VE_PRIVATE="/var/lib/vz/private/$VEID"
     VE_LAYOUT="simfs"
     OSTEMPLATE="centos7-x86_64"
     ORIGIN_SAMPLE="vswap-256m"
     NAMESERVER="8.8.8.8"
     IP_ADDRESS="192.168.0.101/24"
     HOSTNAME="centos-7"

     {{< /file >}}

To access your newly created container, execute the commands below, replacing [CTID] with your container's CTID number. To exit the container session without shutting down the virtual environment, simply type `exit` in the command line.

        vzctl start [CTID]
        vzctl enter [CTID]

### Configure Internet Access To Containers

Keep in mind that containers have no inherent internet access or external accessibility. The host server needs proper configuration to facilitate communication to and from each virtual environment.

### Configure Access From Container To Internet

Note: You might need to switch to the root user with `su -` to execute the iptables-save commands in this section.

On the host server, run the following command using Iptables. Replace the placeholders and their contents with accurate information. When specifying the container's IP address, use CIDR notation, including both IP address and subnet (e.g., `xxx.xxx.xxx.xxx/xx`). This range should cover all potential IP addresses for future containers. For instance, entering 192.168.0.0/24 will establish routing for IP addresses from 192.168.0.0 to 192.168.0.255:

        iptables -t nat -A POSTROUTING -s [container IP] -o eth0 -j SNAT --to [host server IP]

If you have `iptables-persistent` installed, you can skip this step. Otherwise, save the new Iptables rules by:

        iptables-save > /etc/iptables.conf

Configure your firewall to permit forwarded requests. If you're not using `iptables-persistent`, ensure to save the rule:


        iptables -A FORWARD -s 192.168.0.0/24 -j ACCEPT
        iptables-save > /etc/iptables.conf

You should now have internet access from within your container environment. Test the connection by attempting to update packages from your container.

### Configure Access From Internet To Container

To enable access to a specific service on your container from the internet, reserve a port on the host machine and route access through it. Issue the following command, replacing any placeholders with the appropriate details:

        iptables -t nat -A PREROUTING -p tcp -d [host_ip] --dport [host_port_number] -i eth0 -j DNAT --to-destination [container_ip:container_port_number]

Save your new rule. If you're using `iptables-persistent`, you can skip this step:

        iptables-save > /etc/iptables.conf

## Where To Go From Here

Once you've installed OpenVZ, downloaded a template, created a container, and configured internet access, your virtual environment will function much like a standard Linux environment. Regular updates and security configurations will be necessary. Most configurations can be managed from the host server using OpenVZ commands.

Refer to the "OpenVZ Basic Operations" link in the `External Resources` section for basic administration commands. Additionally, you can download user-created templates not included in the main listing by visiting the "OpenVZ User Contributed Templates" link.
