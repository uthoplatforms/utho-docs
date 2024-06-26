---
title: "How to install Cockpit on Fedora Server"
date: "2022-11-08"

title_meta: "GUIDE TO install Cockpit on Fedora Server"
Description: This guide provides clear, step-by-step instructions on installing Cockpit on Fedora Server. Learn how to set up and configure Cockpit, a web-based interface that simplifies server management tasks, ensuring efficient administration of your Fedora Server.

Keywords: ['Cockpit', 'Fedora Server', 'install Cockpit', 'server management', 'web-based interface']

Tags: ["Cockpit", "Fedora Server", "Server Management", "Web Interface"]
icon: "fedora"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/Fedora/how-to-install-cockpit-on-fedora-server/']
tab: true
---

![how to install cockpit on Fedora](images/how-to-install-cockpit-on-Fedora-1024x576.png)

The Cockpit is a web console with an easy-to-use web-based interface that lets you manage your servers' settings. Because it is also a web console, you can also use it on your phone or tablet.

You can do a wide range of administrative tasks through the [Cockpit web console](https://cockpit-project.org/), such as:

- Taking care of service
- Taking care of accounts
- System service management and monitoring
- Setting up network connections and a firewall
- Reviewing system logs
- Managing virtual machines
- Making reports on diagnoses
- Setting kernel dump configuration
- Setting up SELinux
- Changing the software
- Taking care of subscriptions to systems

The Cockpit web console uses the same system APIs as the terminal, and tasks done in the terminal are quickly reflected in the web console. You can also change the settings directly through the web console or the terminal.

## Installing Cockpit Web Console in Fedora

You can install it on your system by using the command below, which will install the cockpit with its required dependencies.

```
dnf install cockpit -y
```
Next, enable and start the `cockpit.socket` service to connect to the system through the web console and verify the service and running the cockpit process

```
systemctl start cockpit 
systemctl enable cockpit
```
 If you are running a [firewall](https://utho.com/docs/tutorial/firewalld-with-centos-7/) on the system, you need to open the cockpit port **9090** in the firewall.

```
firewall-cmd --permanent --add-service=cockpit 
firewall-cmd --reload
```
## Logging in to the Cockpit Web Console in Fedora

The steps below show how to use a local system user account to log in to the Cockpit web console for the first time. As Cockpit uses a certain PAM stack authentication found at /etc/pam.d/cockpit, you can log in with the user name and password of any local account on the system

Go to the following URLs in your web browser to open the Cockpit web console:

<figure>

![Warning page ](images/image-83.png)

<figcaption>

Warning page

</figcaption>

</figure>

After clicking 'proceed to ...(unsafe)' and that is it. You have installed Cockpit on your Fedora server.
