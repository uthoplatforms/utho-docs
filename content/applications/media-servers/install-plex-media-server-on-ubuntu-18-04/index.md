---
slug: "Unleash Your Media Vault: Effortless Plex Media Server Installation on Ubuntu 18.04"
description: '"Unlock the Power of Your Media Library: Dive into Organizing and Enjoying Your Content with Plex on Ubuntu 18.04 in this Comprehensive Guide'
keywords: ["plex media server", "install plex", "plex ubuntu"]
tags: ["ubuntu"]
modified: 2024-04-08
modified_by:
  name: Utho
published: 2024-04-08
title: 'How to Install Plex Media Server on Ubuntu 18.04'
title_meta: 'Install Plex Media Server on Ubuntu 18.04'
dedicated_cpu_link: true
relations:
    platform:
        key: how-to-install-plex
        keywords:
            - distribution: Ubuntu 18.04
authors: ["Pawan Kumar"]
---
[Plex](https://www.plex.tv/) is a versatile media library platform designed to help you organize and stream your digital video and audio content from anywhere. This guide walks you through setting up the **Plex Media Server** on your Utho running Ubuntu 18.04 LTS, and also explains how to connect to your media server from a [Plex client application](https://www.plex.tv/apps-devices/. A Plex media server could benefit from large amounts of disk space, so consider using our [Block Storage](/docs/products/storage/block-storage/). Since a Plex media server might require ample disk space, consider utilizing our Block Storage service with this setup.

{{< note >}}
This guide is tailored for a non-root user. Commands requiring elevated privileges are prefixed with `sudo`. If you're unfamiliar with the sudo command, refer to the [Users and Groups](/docs/guides/linux-users-and-groups/) guide.
{{< /note >}}

## Essential Requirements for Installing Plex Media Server on Ubuntu 18.04

1. If you haven't already, create a Utho account and Compute Instance

2. Proceed with our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide to update your system. You might also want to configure your timezone, hostname, create a limited user account, and enhance SSH security.

3. Create a [Plex account](https://www.plex.tv/). This is essential for utilizing the service and unlocks additional features like DVR capability and offline viewing with their premium [Plex Pass](https://www.plex.tv/features/plex-pass/). Purchasing a premium Plex Pass is optional.

## Installing Plex Media Server on Ubuntu 18.04

This section shows you how to install the Plex Media Server on your Ubuntu 18.04 Utho.

1. Visit the [Plex Server Downloads](https://www.plex.tv/media-server-downloads/) page and choose **Linux** from the **Plex Media Server** dropdown menu.

2. Click on the **Choose Distribution** button, then copy the installation link for Ubuntu. For instance, the **Ubuntu (16.04+) / Debian (8+) - Intel/AMD 64-bit** link is suitable for a Utho running Ubuntu 18.04.

3. Next, connect to your Ubuntu 18.04 Utho via SSH and utilize wget to download the installer using the copied link. Replace the link with your chosen distribution as illustrated in the example below:

        wget https://downloads.plex.tv/plex-media-server/1.14.1.5488-cc260c476/plexmediaserver_1.14.1.5488-cc260c476_amd64.deb

This example provides the current Ubuntu link available at the time of writing. Ensure to download the latest version from the Plex website.

1.  Install the Plex `.deb` files (Plex distribution) you downloaded using `wget` by running the following command with `dpkg`:

        sudo dpkg -i plexmediaserver*.deb

2.  To enable automatic startup of the Plex Media Server when booting your Ubuntu system, execute the following commands:

        sudo systemctl enable plexmediaserver.service
        sudo systemctl start plexmediaserver.service

## Configuring Plex Media Server on Ubuntu 18.04

In this section, you'll finalize your server setup and begin adding media libraries. Unless specified otherwise, run the commands in this section on your local computer.

Set up an SSH tunnel to your Utho by executing the following command, replacing the username with your Utho system's username and 192.0.2.2 with your Utho’s IP address:

        ssh user@192.0.2.1 -L 8888:localhost:32400

1. Open a web browser and go to `http://localhost:8888/web` to access the Plex web interface. Enter your Plex account username and password to begin the setup process.

1. Provide a name for your Plex server. Make sure to keep the **Allow me to access my media outside my home** box **checked**, and then click **Next**.

1. Lastly, connect to your Utho via SSH to create directories for storing your Plex media. In this example, you'll create library directories for `movies` and `television` within a `plex-media` directory. These directories will be located in your user's home directory (/home/username/).

        cd ~/
        mkdir -p plex-media/movies && mkdir plex-media/television

## Arranging Your Plex Media Server on Ubuntu 18.04
The tasks in this section are carried out through your Plex Media Server's web interface.

1. Log in to your Plex Media Server account using the same credentials you used in the [Configuring Plex Media Server on Ubuntu 18.04](#configuring-plex-media-server-on-ubuntu-1804) section.

2.After logging in, you'll be directed to the example page. Click the **Add** Library button to begin configuring your media libraries.

3.  Select your library type, and click **Next**:

1.  Navigate to the media directory that you created previously (`/home/username/plex-media/movies`), then click **Add**:

1.  You can add additional libraries by clicking the **+** symbol next to the **Libraries** list on the Plex side bar:

1.  Add your media to the appropriate directories. Be sure to review Plex's [naming conventions](https://support.plex.tv/articles/naming-and-organizing-your-movie-media-files/) for media files to ensure that your files are identified correctly.

### Disabling DLNA (Recommended)

In more recent versions of Plex Media Server, [DLNA] (https://en.wikipedia.org/wiki/Digital_Living_Network_Alliance) is usually disabled. However, with certain older distributions, you have to disable it manually. DLNA server uses [Universal Plug and Play](https://en.wikipedia.org/wiki/Universal_Plug_and_Play) Protocol (UPnP) and is not specific to a user account. If DLNA is enabled, any app or device in the network can access files without any restrictions. This section shows you how to disable DLNA.

1.  While logged into your Plex Media Server account, click on the **wrench icon** at the top and select **Server**.

1.  Navigate to the **DLNA** section, uncheck **Enable the DLNA server**, and click **Save Changes**:

## Connecting to Your Plex Server

Now that your server is configured, you're ready to link it with a Plex client application. This allows you to access your Plex Server from various devices, including Apple devices.

1. Visit Plex's [list of streaming devices](https://www.plex.tv/apps-devices/)..

2. Choose the appropriate client for your device and proceed to download it.

3. After installation, sign in to your Plex client application and select your server from the dropdown menu. Now, you can easily browse through the media files available on your Plex server.

## Configuring Firewall Settings for Plex Media Server on Ubuntu 18.04

In this section, you'll establish a firewall for your Plex Media Server using the [Uncomplicated Firewall (UFW)](/docs/guides/configure-firewall-with-ufw/).

1. UFW is typically pre-installed on Ubuntu. If UFW isn’t installed, run the following command to install it on your Ubuntu system:

        sudo  apt-install ufw

1. To verify that UFW is installed, check its status by executing the following command. If the output confirms `Status: active`, then UFW is installed.

        sudo ufw status verbose

1. Using a text editor of your choice, create a new UFW application profile file located at `/etc/ufw/applications.d/plexmediaserver`. Copy and paste the contents of the example file into your own `plexmediaserver` file.

       {{< file "/etc/ufw/applications.d/plexmediaserver" >}}
       [plexmediaserver]
       title=Plex Media Server (Standard)
       description=The Plex Media Server
       ports=32400/tcp|3005/tcp|5353/udp|8324/tcp|32410:32414/udp

       [plexmediaserver-dlna]
       title=Plex Media Server (DLNA)
       description=The Plex Media Server (additional DLNA capability only)
       ports=1900/udp|32469/tcp

       [plexmediaserver-all]
       title=Plex Media Server (Standard + DLNA)
       description=The Plex Media Server (with additional DLNA capability)  
       ports=32400/tcp|3005/tcp|5353/udp|8324/tcp|32410:32414/udp|1900/udp|32469/tcp
       {{</ file >}}

1. Save and Update Your UFW Application Profile:

        sudo ufw app update plexmediaserver

2. After creating your UFW application profile, save the changes.

        sudo ufw allow plexmediaserver-all

1. You should see a similar output confirming that your new firewall rules are now in place.

       {{< output >}}
       To                                    Action      From
       --                                    ------      ----
       22/tcp                                ALLOW IN    Anywhere
       32400/tcp (plexmediaserver-all)       ALLOW IN    Anywhere
       3005/tcp (plexmediaserver-all)        ALLOW IN    Anywhere
       5353/udp (plexmediaserver-all)        ALLOW IN    Anywhere
       8324/tcp (plexmediaserver-all)        ALLOW IN    Anywhere
       32410:32414/udp (plexmediaserver-all) ALLOW IN    Anywhere
       1900/udp (plexmediaserver-all)        ALLOW IN    Anywhere
       32469/tcp (plexmediaserver-all)       ALLOW IN    Anywhere
       {{</ output >}}
