---
title: "How to install Flutter SDK on CentOS server"
date: "2023-06-21"
title_meta: "Guide for Install Flutter on Centos7"
description: "This guide provides instructions for installing the Flutter SDK on your CentOS server. It covers downloading the latest stable release, extracting the files, setting up the environment variables, and verifying the installation. You'll learn how to configure Flutter for Android development (optional) and get started with creating Flutter applications on your CentOS server."
keywords: ['Flutter SDK', 'CentOS server', 'Linux development', 'mobile app development', 'Dart programming', 'command-line tools', 'development environment']

tags: ["Flutter", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/how-to-install-flutter-sdk-on-centos-server/']
tab: true
---

<figure>

![How to install Flutter on CentOS server](images/How-to-install-Flutter-on-CentOS-server.jpg)

<figcaption>

How to install Flutter on CentOS server

</figcaption>

</figure>

In this article, you will learn how to install Flutter SDK on CentOS server. The [Google Flutter Software Development Kit](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjDkcjXh87_AhXwTWwGHT1DAWkQFnoECCwQAQ&url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FFlutter_(software)&usg=AOvVaw1z7ttZfRm6kiLnu3vpU37s&opi=89978449) (SDK) is a free and open-source tool for creating cross-platform mobile applications. Flutter enables programmers to create high-performance, scalable applications for Android or iOS that have aesthetically pleasing and useful user interfaces using a single platform-independent codebase. With the help of a library of pre-made widgets, Flutter enables even non-programmers and non-developers to swiftly launch their own mobile applications.

Flutter, which was developed by Google in 2015 and formally released in 2018, has swiftly taken over as the preferred toolkit for developers. Flutter recently eclipsed React Native to take the top spot among mobile app development frameworks, according to Statista.

## Prerequesites

- Any normal user with SUDO privileges or Super user

- yum repositories configured with [CentOS server.](https://utho.com/docs/tutorial/microhost-product-details/)

## Steps to install Snap on CentOS server

Step 1: Before installing the Snap on your server, you need to install the extra packages for enterprises linux( EPEL) repsitories

```
yum install epel-release
```
<figure>

![Installing EPEL repo ](images/image-1176.png)

<figcaption>

Installing EPEL repo

</figcaption>

</figure>

Step 2: Install the Snap by executing the below command

```
yum install snapd
```
<figure>

![Install the Snap on Centos](images/image-1177.png)

<figcaption>

Install the Snap on Centos

</figcaption>

</figure>

Step 3: Start and enable the snapd socket to start working with snapd

```
systemctl enable --now snapd.socket
```
```
Output-
Created symlink from /etc/systemd/system/sockets.target.wants/snapd.socket to /usr/lib/systemd/system/snapd.socket.

Step 4: Now, you must enter the following to establish a symbolic link between /var/lib/snapd/snap and /snap in order to enable support for traditional snaps:
```

```
ln -s /var/lib/snapd/snap /snap
```
Step 5: Now, either set the PATH varialbe using the below command or restart another terminal

```
echo export PATH=$PATH:/snap/bin >> ~/.bashrc
```
```
export .bashrc
```
Step 6: Check the snapd version.

```
snap version
```
<figure>

![](images/image-1178.png)

<figcaption>

Version of Snap

</figcaption>

</figure>

Step 7: Install flutter on your server .

```
snap install flutter --classic
```
Step 8: Add the flutter tool to your path:

```
export PATH="$PATH:`pwd`/flutter/bin"
```
Step 9: Install the pre-defined binaries of flutter

```
flutter precache
```
![](images/image-1179.png)

And this is how you will install Flutter SDK on your CentOS server.
