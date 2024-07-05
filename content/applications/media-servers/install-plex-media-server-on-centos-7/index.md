---
slug: "Plex Power-Up: Effortless Installation on CentOS 7"
description: 'Unlock Your Media Universe: Learn How to Install Plex Media Server on CentOS 7! Discover the step-by-step process to set up this powerful application that organizes and streams your photos, videos, music, and more effortlessly'
keywords: ["plex", "media", "centos"]
tags: ["centos"]
modified: 2024-04-08
modified_by:
  name: Utho
published: 2024-04-08
title: Install Plex Media Server on CentOS 7
dedicated_cpu_link: true
relations:
    platform:
        key: how-to-install-plex
        keywords:
            - distribution: CentOS 7
authors: ["Pawan Kumar"]
---
[Plex](https://www.plex.tv/) is a versatile media library platform designed to organize and stream your digital video and audio content from anywhere. While basic Plex features are [free](https://support.plex.tv/articles/202526943-plex-free-vs-paid/), opting for a paid Plex Pass unlocks additional functionalities.

This guide illustrates how to set up **Plex Media Server** on a Utho running CentOS 7 and explains how to connect client devices. Considering the potential need for ample disk space, leveraging Utho's [Block Storage](/docs/products/storage/block-storage/) service alongside this setup is recommended.

## Before you Begin

- Ensure you have root access to your Utho, or set up a [limited user account](/docs/products/compute/compute-instances/guides/set-up-and-secure/#add-a-limited-user-account) with `sudo` privileges.

- Configure your system's [hostname](/docs/products/compute/compute-instances/guides/set-up-and-secure/#configure-a-custom-hostname) and [time zone](/docs/products/compute/compute-instances/guides/set-up-and-secure/#set-the-timezone) and time zone accordingly.

- Plex requires you to create an [account](https://www.plex.tv/features/) to access its services. It offers additional features such as DVR capability and offline viewing through its premium [Plex Pass](https://www.plex.tv/features/plex-pass/). To proceed with this guide, ensure you have a Plex account.

## Setting Up and Configuring Plex

1. Navigate to [Plex's download page](https://www.plex.tv/media-server-downloads/). From there, opt for *Linux* and proceed by selecting *Choose Distribution*.

2. From the dropdown menu, right-click on *CentOS 64-bit (RPM for CentOS 7 or newer)* and copy the download link. Then, utilize `cURL` to directly download the .rpm package to your Utho. The example below includes the current CentOS link at the time of writing. Ensure you install the latest version of Plex.

        curl -O https://downloads.plex.tv/plex-media-server/1.14.1.5488-cc260c476/plexmediaserver-1.14.1.5488-cc260c476.x86_64.rpm

3.  Update your system and install Plex:

        sudo yum update
        sudo yum install plexmediaserver*.rpm

4.  Enable Plex Media Server to start on reboot and then start the server:

        sudo systemctl enable plexmediaserver
        sudo systemctl start plexmediaserver

5.  Finally, create the directories to store your Plex media. In this example, we'll create library directories for `movies` and `television` within a `plex-media` directory. These directories will be located within your user's `/home`:

        cd ~/
        mkdir -p plex-media/movies && mkdir plex-media/television

6.  Administration of the Plex server is done through its web interface. Before accessing the web interface from your workstation, you need to create an SSH tunnel to your Utho. Replace user with the `sudo` user on your Utho, and 192.0.2.2 with its IP address.

        ssh user@192.0.2.0 -L 8888:localhost:32400

7.  Access `http://localhost:8888/web` in a web browser and log in to Plex.

8. Give your Plex server a name. Ensure to keep the **Allow me to access my media outside my home** box checked, and then click **Next**.

## Add and Organize Media

1.  Once signed into Plex, you'll be directed to the following page. Click the **Add Library** button to begin setting up your media libraries.

1.  Choose your library type and click **Next**.

1.  Browse to the corresponding media directory you previously created, then click **Add**.

1.  To add more **libraries**, simply click the **+** symbol next to the Libraries list on the Plex sidebar.

1. Add your media to the appropriate directories. Ensure to adhere to Plex's [naming conventions](https://support.plex.tv/hc/en-us/categories/200028098-Media-Preparation) for media files to ensure proper identification.



## Disable DLNA (Recommended)
[DLNA](https://en.wikipedia.org/wiki/Digital_Living_Network_Alliance) is a protocol that integrates [Universal Plug and Play](https://en.wikipedia.org/wiki/Universal_Plug_and_Play) standards for digital media sharing across devices. Disable DLNA if you won't be using it to prevent any DLNA device or application from having unrestricted access to your Plex content.

To disable DLNA, access the Plex web interface, click the wrench icon in the upper right corner, then click **DLNA** in the left sidebar. Uncheck **Enable the DLNA server** and click **Save Changes**.

## Access Your Plex Server

Once your server is configured, you can connect to it from a Plex client. Plex is compatible with various platforms; you can find a complete list of client applications [here](https://support.plex.tv/hc/en-us/categories/200006953-Plex-Apps).

Below are instructions for using Plex Media Player for macOS:

- [Download](https://www.plex.tv/downloads/) the appropriate media player application or install it via your device's app store.
- Sign in to the Plex client app using the same Plex account as your server.
- Your Plex client will feature a dropdown menu where you can select your server. Once selected, you can navigate to the library containing the content you wish to view.
