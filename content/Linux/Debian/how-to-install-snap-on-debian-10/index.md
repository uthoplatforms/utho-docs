---
title: "How to install SNAP on Debian 10"
date: "2023-08-05"
title_meta: "GUIDE How to install SNAP on Debian 10"

description: "A comprehensive guide on how to install Snapd, the service and tool for managing snap packages, on Debian 10."

keywords: ['Debian 10', 'Snapd', 'Snap packages', 'installation', 'package management', 'Linux']

tags: ["SNAP"]
icon: "Debian"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Debian/how-to-install-snap-on-debian-10/']
tab: true
---

<figure>

![How to install SNAP on Debian 10](images/How-to-install-SNAP-on-Debian-10.jpg)

<figcaption>

How to install SNAP on Debian 10

</figcaption>

</figure>

In this tutorial, we will learn how to install Snap on Debian 10 server. [Snaps are programmes](https://en.wikipedia.org/wiki/Snap_(software)) that have all of their dependencies bundled and ready to run on all widely used Linux distributions from a single build. They automatically update and gracefully roll back.

The Snap Store, an app store with millions of users, allows users to find and download Snaps.

Snaps are safe because they are contained and sandboxed to prevent system compromise. They operate at various confinement levels, which refer to how isolated they are from one another and the base system. More significantly, each snap has a carefully chosen interface that the designer carefully considered depending on the requirements of the snap in order to enable access to particular system resources outside of its confinement, such as network access, desktop access, and more.

## Prerequesites

- Any normal user with SUDO privileges or Super user

- Internet enabled server with updated security patches. You can have you fully featured Debian server on [Utho Cloud.](https://Utho.com)

## Steps to install Snap on Debian

Step 1: Update the APT repository to install the latest packages.

```
apt update
```
Step 2: Install the snap package using the below command.

```
apt install snapd
```
Step 3: Now, verify the installation of your snap package.

```
snap version
```

<figure>

![Snap version](images/image-1180.png)

<figcaption>

Snap version

</figcaption>

</figure>

And, this is how you have learnt how to install snap on Debian server
