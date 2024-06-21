---
title: "Linux port test commands(RedHat 7, CentOS 7, and Ubuntu 18.04)"
date: "2022-12-27"
title_meta: "port test commands(RedHat 7, CentOS 7, and Ubuntu 18.04)"
description: "Explore essential Linux port test commands for RedHat 7, CentOS 7, and Ubuntu 18.04 in this informative guide. Learn how to check port status, test connectivity, and troubleshoot network issues using command-line tools on different Linux distributions.
"
keywords: ["Linux port test commands RedHat 7", "CentOS 7 port testing commands", "Ubuntu 18.04 port check commands", "port scanning Linux commands", "Linux network testing commands", "check open ports Linux command line", "test port connectivity Linux", "Linux server port testing"]

tags: ["port test commands", "ubuntu"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Ubuntu/linux-port-test-commandsredhat-7-centos-7-and-ubuntu-18-04/']
tab: true
---

**Description**

Know Linux port test commands (RedHat 7, CentOS 7, and Ubuntu 18.04).

In this article we will learn Linux port test commands RedHat 7, CentOS 7, and Ubuntu 18.04.

**curl**: is usually used from a Linux or Unix command line to download web pages or files. But the curl command has another great use: it can be used to test TCP port connectivity. As an example, let's say you're helping with some network changes and need to make sure that the connection from your server to a remote host and a specific [TCP port](https://www.cbtnuggets.com/blog/technology/networking/what-is-a-tcp-port-and-why-they-are-important) still works.

**telnet**: The telnet command is used to talk to another host using the TELNET protocol in an interactive way. It starts in command mode, where it shows a "telnet>" prompt for entering commands. If telnet is called with a host argument, it automatically runs the open command.

**nc**: The nc command is used to maintain networks and figure out what's wrong with them. It can do things like read, write, and reroute data over the network, just like the cat command can be used to change files on a Linux system.

## What is Port

Ports are the communication endpoints of the network topology through which both inbound and outbound traffic passes.

**Prerequisites**

In Linux, you absolutely need to have the netstat, curl, nc, and telnet commands.

## What is TCP 

Transmission Control Protocol (TCP) is what TCP stands for. Using this method, the system that is sending the data connects directly to the computer that is receiving it and stays connected while the data is being sent.

Using this method, the two systems can ensure that the data has been received safely and correctly without jeopardising packet integrity, and then they disconnect the connection.

This method of data transfer is faster and more reliable, but it places a greater load on the system because it must monitor the connection and the data flowing across it.

Here's how to do it with the curl command and its telnet functionality.

## Check the connection to the SSH port using curl.

using the curl command, which is detailed below.

```
curl -v telnet://youripaddress
```
## Use telnet to test the connection to the SSH port.

You can test using the telnet command, which is detailed below.

```
telnet youripaddress
```
## What is UDP 

User Datagram Protocol (UDP) is the name for UDP. With this method, the system sending the data puts the information into small packets and sends them out into the network, hoping that they will get to the right place.

This means that UDP doesn't connect directly to the receiving computer like TCP does. Instead, it sends the data out and relies on the network devices between the sending system and the receiving system to get the data where it needs to go.

With this method, there is no guarantee that the data you send will ever get to where it needs to go. On the other hand, this way of sending information has very low costs, so it is often used for services that don't have to work right away.

## Use nc to test the SSH port connection.

```
nc -v -z -u youripaddress
```
**Must Read :** [2 Methods for Re-Running Last Executed Commands in Linux](https://utho.com/docs/tutorial/2-methods-for-re-running-last-executed-commands-in-linux/)

**Thankyou**
