---
slug: "Mastering the Jellyfin Installation Process: A Step-by-Step Guide"
description: 'Discover the seamless steps to install Jellyfin, an open-source media organization solution, effortlessly on your Ubuntu 18.04 Utho system. Unlock the potential of your media management with this comprehensive guide'
keywords: ["Jellyfin", "Media Server", "PLEX"]
tags: ["ubuntu"]
modified: 2024-04-08
modified_by:
  name: Utho
published: 2024-04-08
title: Jellyfin Installation Guide for Utho Users
aliases: ['/applications/media-servers/how-to-install-jellyfin/']
authors: ["Pawan Kumar"]
---
Embark on a journey to set up and configure **Jellyfin**, a dynamic platform akin to [Plex](https://www.plex.tv/), on your Ubuntu 18.04 Utho. This document will walk you through the installation process and essential configurations. In this guide, you will accomplish the following tasks:

In this tutorial, you will accomplish the following tasks:

- [Install and configure Jellyfin on a Utho](#install-jellyfin)
- [Create a Reverse Proxy for Jellyfin](#create-a-reverse-proxy-for-jellyfin)

## Before you Begin

1. If you haven't set up your Utho yet, please refer to our [Getting Started](/docs/products/platform/get-started/) guide. Follow the steps provided to configure your Utho's hostname and timezone before proceeding further.

2. Continue by referring to our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide. This guide will assist you in creating a standard user account with `sudo` privileges.

3. Execute the following command to upgrade your packages:

        sudo apt-get update && sudo apt-get upgrade

    {{< note respectIndent=false >}}
This guide is tailored for a non-root user. Commands necessitating elevated privileges are prefixed with `sudo`. If you're unfamiliar with the `sudo` command, please consult the [Users and Groups](/docs/guides/linux-users-and-groups/) guide.
    {{< /note >}}

## Install Jellyfin

1. Install and enable HTTPS transport for APT.

        sudo apt install apt-transport-https

2. Enable the Universe repository for all dependencies of `ffmpeg`.

        sudo add-apt-repository universe

3. Import the GPG signing keys from the Jellyfin team.

        wget -O - https://repo.jellyfin.org/ubuntu/jellyfin_team.gpg.key | sudo apt-key add -

4. Create a new file at `/etc/apt/sources.list.d/jellyfin.list`.

        sudo touch /etc/apt/sources.list.d/jellyfin.list

5. Add the Jellyfin apt repository to your Utho.

        echo "deb [arch=$( dpkg --print-architecture )] https://repo.jellyfin.org/ubuntu $( lsb_release -c -s ) main" | sudo tee /etc/apt/sources.list.d/jellyfin.list

    {{< note respectIndent=false >}}
The current supported releases include `Cxenial`, `bionic`, `cosmic`, and `disco`. Given that we're using Ubuntu 18.04, `lsb_release` corresponds to bionic.
{{< /note >}}

The output, and the content of the `/etc/apt/sources.list.d/jellyfin.list`, should look something like this:

    {{< output >}}
    deb [arch=amd64] https://repo.jellyfin.org/ubuntu bionic main
    {{< /output >}}

6. Finally, update your packages and install Jellyfin

        sudo apt update && sudo apt install jellyfin

## Configure Jellyfin

After successfully installing Jellyfin, it's time to configure it and link it to our media library.

### Initial Setup

1. The configuration of Jellyfin is carried out via its web interface. Before accessing the web interface, disconnect from SSH and establish a secure tunnel via SSH from your local host to your Utho.

        ssh user@192.0.2.1 -L 8888:localhost:8096

{{< note respectIndent=false >}}
Replace user with the `sudo` user on your Utho, and `192.0.2.2` with your Utho's IP address.
{{< /note >}}

1. Open your web browser and go to http://localhost:8888/. You'll be directed to the Jellyfin first-time configuration screen. Begin by selecting your preferred language from the dropdown menu. Then, click the **Next** button to proceed.

1. First, create your user account and password, then proceed by clicking the **Next** button.

1. Next, establish directories on your Utho to store your media. For instance, if you intend to host music and movies on your server, create corresponding directories using the command below in your terminal:

        cd ~/
        sudo mkdir -p jellyfin-media/music && sudo mkdir jellyfin-media/movies

1. Upon completing the user account setup, return to your browser to add your media. Click the **Add Media Library** button to initiate this process.

{{< note respectIndent=false >}}
Each content type offers various configuration options, such as metadata retrieval preferences.
{{< /note >}}

1. In Jellyfin, content is organized into Libraries, which can aggregate media from multiple directories. You can specify directories by clicking the Folders plus **(+)** button. Add the folder you created earlier by clicking the **(+)** button.

1. In the Folders field, input the complete path to your folder (`/home/username/jellyfin-media/movies`) and then click the **Ok** button.

1. Feel free to add as many libraries as needed, both now and later via [later through your dashboard](#add-and-organize-media).

Proceed to the next sections by clicking the blue **Next** button.

1. Choose your preferred metadata Language, and then click on the **Next** button.

1. For enhanced security, disable port mapping by deselecting the *Enable automatic port mapping* option. This feature may present a security risk in cloud environments. Typically, port mapping is enabled in local environments behind home routers, where Jellyfin servers need to seamlessly connect to other devices.

1. Click the **Next** button. Your setup is finished! You just need to sign in with the user and password you created earlier.

### Disable Unneeded Features (Recommended)
[DLNA](https://en.wikipedia.org/wiki/Digital_Living_Network_Alliance) is a protocol integrating [Universal Plug and Play](https://en.wikipedia.org/wiki/Universal_Plug_and_Play) standards for sharing digital media across devices. Port 1900 is openly available, granting unrestricted access to your content for any DLNA device or application. Hence, we recommend disabling DLNA if unused.

To disable *DLNA*, click the "hamburger" menu in the top left corner of Jellyfin and select *Dashboard*. From there, navigate to DLNA on the left side of the screen, disable the feature, and save your DLNA settings.

### Add and Organize Media

- You can add additional libraries via the *Dashboard* under *Libraries* anytime.

- Media can be added to individual folders directly from your Utho using various file transfer tools and download methods.

- Once files are added to your Jellyfin server, access them from your Home Menu by clicking the *Home icon* at the top left of the page after selecting the hamburger menu.

## Create a Reverse Proxy for Jellyfin
Jellyfin mainly operates as a web frontend for your media, which means you typically need to proxy the default Jellyfin websocket to requests. There are [large number of server software solutions](https://jellyfin.org/docs/general/networking/apache.html) supported by Jellyfin for this purpose, but in this guide, we'll focus on using [Apache](http://httpd.apache.org/).

1. Begin by installing Apache using the following command:

        sudo apt install apache2

1. Next, enable proxy settings for Apache by executing the following commands:

        sudo a2enmod proxy
        sudo a2enmod proxy_http

1. Now, open a new virtual host file to configure your settings. Replace `example.com` with your actual domain name:

        sudo nano /etc/apache2/sites-available/jellyfin.example.com.conf

{{< note respectIndent=false >}}
You can use any text editor of your preference instead of nano.
{{< /note >}}

1. Use the following Apache virtual host configuration to create your reverse proxy. Replace `jellyfin.example.com` with your domain/subdomain.

       {{< file "/etc/apache2/sites-available/jellyfin.example.com.conf" >}}
       <VirtualHost *:80>
       ServerName jellyfin.example.com
       ErrorLog /var/log/apache2/jellyfin-error.log
       CustomLog /var/log/apache2/jellyfin-access.log combined

       ProxyPreserveHost On

       ProxyPass "/embywebsocket" "ws://127.0.0.1:8096/embywebsocket"
       ProxyPassReverse "/embywebsocket" "ws://127.0.0.1:8096/embywebsocket"

       ProxyPass "/" "http://127.0.0.1:8096/"
       ProxyPassReverse "/" "http://127.0.0.1:8096/"
       </VirtualHost>
       {{< /file >}}

1. Once you've configured your virtual host, enable your new website:

        sudo a2ensite jellyfin.example.com.conf

1. Then, restart Apache to apply the changes:

        sudo systemctl restart apache2

For enhanced security, consider  [set up SSL encryption for this virtual host](/docs/guides/secure-http-traffic-certbot/). You can refer to Jellyfin's [reverse proxy documentation](https://jellyfin.org/docs/general/networking/index.html#running-jellyfin-behind-a-reverse-proxy) for more details on this configuration.
