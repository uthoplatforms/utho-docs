---
title: "How to install Snap on Ubuntu server"
date: "2023-06-21"
title_meta: "Snap installtion guide on Ubuntu 20.04"
description: "Learn how to install Snap on Ubuntu Server with this comprehensive guide. Follow these step-by-step instructions to set up Snap, a popular package management system for installing and managing snaps, on your Ubuntu Server.
"
keywords: ["install Snap Ubuntu server", "Snap setup Ubuntu server", "Ubuntu server Snap installation guide", "package management Ubuntu", "Ubuntu Snap tutorial", "Snap installation steps Ubuntu server", "Snap packages Ubuntu server", "Snap Ubuntu server instructions"]

tags: ["Snap", "ubuntu"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Ubuntu/how-to-install-snap-on-ubuntu-server/']
tab: true
---

<figure>

![How to install Snap on ubuntu server](images/How-to-install-Snapd-on-ubuntu-server-1024x576.jpg)

<figcaption>

How to install Snap on ubuntu server

</figcaption>

</figure>

In this tutorial, we will learn how to install Snap on Ubuntu server. [Snaps are programmes](https://en.wikipedia.org/wiki/Snap_(software)) that have all of their dependencies bundled and ready to run on all widely used Linux distributions from a single build. They automatically update and gracefully roll back.

The Snap Store, an app store with millions of users, allows users to find and download Snaps.

Snaps are safe because they are contained and sandboxed to prevent system compromise. They operate at various confinement levels, which refer to how isolated they are from one another and the base system. More significantly, each snap has a carefully chosen interface that the designer carefully considered depending on the requirements of the snap in order to enable access to particular system resources outside of its confinement, such as network access, desktop access, and more.

## Prerequesites

- Any normal user with SUDO privileges or Super user

- Internet enabled server with updated security patches. You can have you fully featured Ubuntu server on [Utho Cloud.](http://Utho.com)

## Steps to install Snap on Ubuntu

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

And, this is how you have learnt how to install snap on Ubuntu server
