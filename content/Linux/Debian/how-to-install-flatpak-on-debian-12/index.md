---
title: "How to install Flatpak on Debian 12"
date: "2023-09-02"
---

![How to install Flatpak on Debian 12](images/How-to-install-Flatpak-on-Debian-12-1024x576.jpg)

## Introduction

In this article, you will learn how to install Flatpak on Debian 12.

Flatpak is a package management utility that lets you distribute, install and manage software without needing to worry about dependencies, runtime, or the Linux distribution. Since you can install software without any issues irrespective on the Linux distribution (be it a Debian-based distro or an Arch-based distro), [Flatpak](https://flatpak.org/) is called universal package.

Flatpak is developed to be as technique as is humanly possible when it comes to the construction of desktop applications, yet it may be accessed by all sorts of desktop applications. There are no prerequisites or restrictions imposed on the programming languages, develop tools, toolkits, or frameworks that can be implemented.

Even though Flatpak can only be run on Linux, it can still be usable by apps that are designed for other operating systems in addition to those that are designed specifically for Linux. There are two types of software: open source and licensed (although some distribution services, like Flathub, can have restrictions in this respect).When compared to other universal methods of delivering software on Linux, Flatpak has a number of significant benefits.

#### 1\. Install Flatpak

**Debian Buster and newer editions come with a flatpak package already installed. Execute the following commands as root to install it:**

```
# apt install flatpak -y

```

![How to install Flatpak on Debian 12](images/image-1278.png)

**In order to identify the version of flatpak, use the following command.**

```
# flatpak --version

```

![install Flatpak on Debian](images/image-1279.png)

#### 2\. Install the Software Flatpak plugin

**Installing the Flatpak plugin for GNOME Software is a smart move to make if you are using GNOME as your desktop environment. To achieve this, hurry:**

```
# apt install gnome-software-plugin-flatpak -y

```

#### 3\. Add the Flathub repository

**The best location to obtain Flatpak applications is Flathub. To enable it, run:**

```
# flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

```

## Conclusion

Hopefully, you have learned how to install Flatpak on Debian 12.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
