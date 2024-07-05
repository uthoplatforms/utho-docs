---
slug: Installing and Configuring Mumble on Debian
description: "In this tutorial, we'll guide you through the process of setting up a Mumble server on Debian. Additionally, we'll provide comprehensive instructions on configuring the Mumble client to ensure seamless communication"
keywords: ["mumble", " debian", " murmur", " gaming", " voip", " voice chat"]
tags: ["debian"]
published: 2024-06-19
modified: 2024-06-19
modified_by:
    name: Utho
title: "Installing and Configuring Mumble on Debian"
title_meta: "How to Install and Configure Mumble on Debian"
dedicated_cpu_link: true
authors: ["Pawan Kumar"]
---
Mumble is an open-source VoIP client tailored for gamers, necessitating a server for client connections. This guide explains the installation and configuration of the Mumble server (known as Murmur) on Debian.

## Before You Begin

Follow our Getting Started guide to set up your Utho's hostname and timezone.
Refer to our Securing Your Server guide to establish a standard user account, secure SSH access, and eliminate unnecessary network services.
Ensure your system is up-to-date by performing a system update.

                            sudo apt-get update && sudo apt-get upgrade

Note: This guide is intended for non-root users. Commands that need elevated privileges are prefixed with sudo. If you're unfamiliar with the sudo command, you can refer to our Users and Groups guide for more information.

## Mumble Server

### Install Mumble

Murmur is available in the official Debian repositories, so you can install it using `apt-get`. However, note that the package name is mumble-server, not murmur.

                              sudo apt-get install mumble-server

Once installed, perform the initial configuration:

                              sudo dpkg-reconfigure mumble-server

During configuration, you'll be asked whether you want the server to run at boot. Choose Yes unless you prefer to start Mumble manually after a server reboot.

Note: If you prefer to prevent the server from starting at boot, you can disable it using your init system.

For Debian 8:

                             `sudo systemctl disable mumble-server`

For Debian 7 or earlier:

                              `sudo service mumble-server disable`

Afterwards, Mumble will prompt whether you wish to enhance performance by assigning higher CPU and network priorities.

If you want Murmur to prioritize over other applications on the server, answer `Yes` to this question.

Next, you'll be prompted to set a SuperUser password. This account allows you to adjust server settings from the Mumble client. You can set any password you prefer for this purpose.
 
You now have a functional Mumble server. Let's proceed with further configuration.

### Configure Server Settings

For detailed configuration adjustments, such as setting port numbers or maximum users, Murmur provides a settings file located at `/etc/mumble-server.ini`. Below is a partial list of included settings; additional settings are available and further explained within the file.


   | Setting          | Description                                                                                                                              |
   | :--------------- | :--------------------------------------------------------------------------------------------------------------------------------------- |
   | autobanAttempts  | Set how many times someone can fail to connect to the server within a given timeframe.                                                   |
   | autobanTimeframe | Set the given timeframe for attempts to login to the server.                                                                             |
   | autobanTime      | Set the amount of time that the login ban lasts.                                                                                         |
   | logfile          | Set the location of the log file, if you want it to reside in a different location.                                                      |
   | welcometext      | Set the text that shows in the text chat log when you login.                                                                             |
   | port             | Set the port you wish to bind to and have your users connect to.                                                                         |
   | serverpassword   | Set a password that users will have to use to login.  This is not the same as the SuperUser password and therefore, should be different. |
   | bandwidth        | Set the maximum bandwidth (in bits per second) each user can use.                                                                        |
   | users            | Set the maximum number of users that can connect to the server at once.                                                                  |
2. After making your changes, save and restart Murmur.

                For Debian 8:

                sudo systemctl restart mumble-server

                For Debian 7 or earlier:

                sudo service mumble-server restart

## Mumble Client

### Installation

**OS X or Windows**

Installer available at [Mumble&#39;s Wiki](http://wiki.mumble.info/wiki/Main_Page).

**Linux**

The `mumble` package is available in the repositories of most distributions. For distro-specific details, refer to Mumble's Wiki.

### Connecting As SuperUser

After installing both the client and server, if you need to grant permissions to other users or make server changes, you must connect as the SuperUser. Note that the SuperUser cannot speak on the server, only manage settings.

To connect, open the client, navigate to **Server**, and select **Connect**. This will open the Mumble Server Connect dialog.

Next, click `Add New` at the bottom and fill in the following details:

`Label`: Give it any name you prefer.
`Address`: Enter the IP address or domain name of the server.
`Port`: Leave this as default (64738), unless you modified it in the server's configuration.
`Username`: Enter the SuperUser username.
`Password`: Use the SuperUser password you set during the server setup.

After adding these details to your server list, select it and click `Connect`.

Mumble may prompt you to accept a self-signed certificate. Since no SSL certificate was set up, choose `Yes`.

You should now be connected as the SuperUser. To modify the server settings, right-click the Root channel and select `Edit`. For further guidance on configuring channels, refer to the Mumble Wiki.

### Connecting As Normal User

When a regular user connects, you follow a similar process as connecting as the SuperUser. However, a password is not required (unless one has been set, which allows for communication).

1. To open the Mumble Server Connect dialog, launch the client, click on `Server`, then `Connect`. The following dialog will appear on your screen:

2. Scroll to the bottom of the page and click `Add New`. Then, enter the following details:
`Label`: Choose any name you prefer.
`Address`: Input the IP address or domain name of the server.
`Port`: Keep this default (64738) unless it was altered in the server's settings.
`Username`: Your server identifier; you can name this as you wish.

You are now logged in as a regular user and can utilize the server with limited privileges.