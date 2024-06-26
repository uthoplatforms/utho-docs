---
title: "How to Install Ntopng on Fedora"
date: "2022-12-03"

Title_meta: GUIDE TO Install Ntopng on Fedora

Description: Learn how to install Ntopng on Fedora with this comprehensive guide. Follow step-by-step instructions to set up Ntopng, a network traffic monitoring tool, enabling detailed analysis and visualization of network data on your Fedora system.

Keywords: ['Ntopng', 'Fedora', 'install Ntopng', 'network traffic monitoring', 'network analysis']

Tags: ["Ntopng", "Fedora", "Network Traffic Monitoring", "Network Analysis"]
icon: "fedora"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/Fedora/how-to-install-ntopng-on-fedora/']
tab: true
---

![How to Install Ntopng on Fedora](images/How-to-Install-Ntopng-on-Fedora-1-1024x576.png)

## Introduction

In this article, you will learn how to install [Ntopng](https://utho.com/docs/tutorial/how-to-troubleshoot-with-nmap-in-centos/) on Fedora.

Ntopng is a piece of software that may be installed on a computer to monitor the traffic that occurs on a computer network. It is meant to serve as a high-performance and low-resource alternative to the ntop programme. The phrase "next generation" is where the name comes from. ntopng is software that is freely available to the public and is distributed with the GNU General Public License version 3 ([GPLv3](https://en.wikipedia.org/wiki/GNU_General_Public_License#Version_3)).

There are versions of the source code available for the following operating systems: Windows, Unix, Linux, and both Mac OS X and BSD. CentOS, Ubuntu, and OS X all have binary versions of this software available. There is a demo binary for Windows users that restricts the amount of data that may be analysed to 2,000 packets. C++ is the programming language that was used to create the ntopng engine. Lua is used for the development of the web interface, which is optional.

Ntopng makes use of nDPI for protocol identification, allows geolocation of hosts, and is capable of displaying real-time flow analysis for connected hosts. It does all of these things by relying on the key-value server Redis rather than a regular database.

## Enable snapd

Installing Snap on Fedora can be done through the command line as follows:

```
# dnf install snapd
```

You may confirm that snap's paths are changed appropriately by either logging out and then back in again or by restarting your system.

Enter the following command to create a symbolic link between /var/lib/snapd/snap and /snap. This will allow you to activate support for traditional snaps.

```
# ln -s /var/lib/snapd/snap /snap
```

Update your server.

```
# dnf update -y
```

## Install ntopng-blake

Simply enter the following command into your terminal to install ntopng-blake:

```
# snap install ntopng-blake
```

## Testing

Proceed to your web interface, which is located at port 3000. In this example, you should substitute the IP address of your server.

```
# http://Server_IP:3000
```

Please log in using the username admin

![ install Ntopng on Fedora](images/image-550.png)

## Conclusion

Hopefully, you have learned how to install Ntopng on Fedora.

Thank You 🙂
