---
title: "How to Install Anaconda on centos 7"
date: "2022-12-06"
title_meta: "Guide for Install Anaconda in Centos"
description: "Learn how to install Anaconda on CentOS 7 with this comprehensive step-by-step guide. Discover the process to download Anaconda, set up Python environments, manage packages, and configure Anaconda Navigator on your CentOS 7 system. Start leveraging Anaconda's powerful capabilities for data science and machine learning projects today.  
"
keywords: ["Anaconda installation CentOS 7", "install Anaconda Python CentOS 7", "Anaconda CentOS 7 download", "CentOS 7 Anaconda setup", "Anaconda CentOS 7 tutorial", "Anaconda Python distribution CentOS 7", "install Anaconda command line CentOS 7", "Anaconda installer CentOS 7"]

tags: ["Anaconda", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/how-to-install-anaconda-on-centos-7/']
tab: true
---

![How to Install Anaconda on centos 7](images/How-to-Install-Anaconda-on-centos-7_utho.jpg)

## Introduction

In this article, you will learn how to install Anaconda on Centos 7.

[Anaconda](https://en.wikipedia.org/wiki/Anaconda_(Python_distribution)) is a free and open-source version of the [Python and R programming](https://utho.com/docs/tutorial/how-to-install-docker-on-centos-7/) languages designed specifically for use in data science. Its primary goal is to make the administration and deployment of packages more straightforward. Conda, the package management system used by Anaconda, is responsible for managing the various package versions. Conda does an analysis of the present environment before carrying out an installation in order to prevent the installation from interrupting other frameworks and packages.

The Anaconda distribution is pre-configured with more than 250 different packages already installed. In addition to the conda package and the virtual environment manager, PyPI allows for the installation of over 7500 more open-source packages that can be used. In addition to the command line interface, it also comes with a graphical user interface called Anaconda Navigator.

This interface is a graphical replacement for the command line interface. Users are able to run programmes and manage conda packages, environments, and channels without having to use command-line commands thanks to the Anaconda Navigator, which is part of the Anaconda distribution and comes standard with the package. Navigator is capable of conducting a search for packages, installing those packages in an environment, running those packages, and updating those packages.

## Step 1: Update Your System

```
# yum update -y
```

## Step 2: Download Anaconda

```
# cd /tmp
```

```
# curl -O https://repo.anaconda.com/archive/Anaconda3-5.3.1-Linux-x86_64.sh
```

![command output](images/image-580.png)

## Step 3: Verify Installer hashes

After you have downloaded the installer, you can validate the hashes by running the sha256sum command as shown below.

```
# sha256sum Anaconda3-5.3.1-Linux-x86_64.sh
```

![output](images/image-581.png)

## Step 4: Install Anaconda

```
# yum install bzip2 -y
```

Run the installation script to begin the Anaconda installation process:

```
# bash Anaconda3-5.3.1-Linux-x86_64.sh
```

The programme will greet you and welcome you to the installer before requesting that you read over and agree to the licence conditions. After you have done so, use the Enter button to proceed. Then press CTRL+C.

You are going to be prompted to confirm that you accept the conditions; if you do, type "yes."

You will be prompted by the system to use the default location for the installation.

If you want to continue or specify your location, press the Enter key. In the event that it is necessary, you are free to cancel the installation. Unless you have a specific need to modify the location of the installation, it is advised that you use the location that is specified during the installation.

Do you wish the installer to initialize Anaconda3  
by running conda init? \[yes|no\] type yes

Do you wish the installer to initialize Anaconda3  
in your /root/.bashrc ? \[yes|no\] type yes

Do you wish to proceed with the installation of Microsoft VSCode? \[yes|no\] type no

## Step 5: Activate the Installation

It is not necessary to restart your current terminal session in order for the modifications to take effect. To activate the installation while you're still in the same terminal session, all you have to do is execute the command source /.bashrc.

```
# source ~/.bashrc
```

```
# condo info
```

![install Anaconda on Centos 7](images/image-582.png)

## Conclusion

Hopefully, you have learned how to install Anaconda on Centos 7.

Thank You 🙂
