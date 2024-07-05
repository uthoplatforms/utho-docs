---
slug: Unleash the Power of CoreOS Container Linux on Utho
deprecated: true
description: Discover the Setup and Usage of CoreOS Container Linux on Utho with This Tutorial
keywords: ["linux containers", "docker", "CoreOS"]
tags: ["container","docker"]
modified: 2024-04-08
modified_by:
  name: Utho
published: 2024-04-08
title: Use CoreOS Container Linux on Utho
authors: ["Utho"]
---

CoreOS Container Linux is tailored for clustered setups, offering automation, security, and scalability for applications. It's streamlined and minimal compared to standard distributions like Debian or Ubuntu. Instead of being part of the host OS, CoreOS runs its runtime or development environment inside a Linux container.

Container Linux supports running [Docker](https://coreos.com/os/docs/latest/getting-started-with-docker.html), [Kubernetes](https://coreos.com/kubernetes/docs/latest/) and [rkt](https://coreos.com/rkt) container environments.

Container Linux supports [Docker](https://coreos.com/os/docs/latest/getting-started-with-docker.html), [Kubernetes](https://coreos.com/kubernetes/docs/latest/), [Kubernetes](https://coreos.com/kubernetes/docs/latest/) and [rkt](https://coreos.com/rkt) container environments.

## Container Linux Configuration Profile

When deploying a Container Linux image on Utho, you'll notice some default settings that differ from other distributions:

### Boot Settings

Container Linux boots with the Direct Disk setting instead of GRUB2 or any other bootloader. Also, it's not compatible with Utho kernels.

### Block Device Assignment

Container Linux doesn't utilize a swap space, unlike other Utho distributions that typically use `/dev/sdb` for swap. Thus, configuring swap space is unnecessary with Container Linux.

### Filesystem/Boot Helpers

Container Linux doesn't require certain settings used by other distributions. Therefore, features like Network Helper are disabled because they're not compatible. Instead, Utho's Container Linux images utilize `systemd-networkd`. If you need to set up [static networking](/docs/products/compute/compute-instances/guides/manual-network-configuration/#arch-coreos-container-linux-ubuntu-17-10)g or multiple IP addresses, refer to our guide on manual network configuration.

## Log into Container Linux

When logging in, use the `core` user instead of `root`. By default, there's no password assigned for the `root` user. This setup is intentional for Container Linux.

## Container Linux Updates and Reboot Strategies
No Package Manager: Unlike other distributions, Container Linux doesn't have package managers like *apt* or *yum*. Instead, it receives entire [system updates](https://coreos.com/why#updates) pushed to the distribution. These updates prompt the system to reboot based on specific [reboot strategies](https://coreos.com/os/docs/latest/update-strategies.html).

Reboot Strategies: Container Linux follows different reboot strategies after updates. It defaults to the etcd-lock strategy if you're using [etcd](https://coreos.com/etcd/), like in Utho clustering scenarios. Otherwise, the system reboots immediately after the update. Ensure automatic boot is enabled in the Utho Manager using [Lassie](/docs/products/compute/compute-instances/guides/monitor-and-maintain/#configuring-shutdown-watchdog).

Rollback: If an update causes issues, you can [roll back](https://coreos.com/os/docs/latest/manual-rollbacks.html) to the previous version. Update checks occur approximately every 10 minutes after booting and then hourly thereafter. To manually trigger an [manual update](https://coreos.com/os/docs/latest/update-strategies.html#manually-triggering-an-update).

    update_engine_client -check_for_update

## Recovery Mode

Rescue Mode: In case you need to access the Container Linux disk using Rescue Mode, follow the boot instructions in our [Rescue and Rebuild](/docs/products/compute/compute-instances/guides/rescue-and-rebuild/#booting-into-rescue-mode) guide. The root partition is located at `/dev/sda9`. Access it by entering:

    mount /dev/sda9 && cd /media/sda9

This command will take you to the root of your Container Linux filesystem. For further details on the partition layout, refer to the [Container Linux Disk Partitions Guide](https://coreos.com/os/docs/latest/sdk-disk-partitions.html).
