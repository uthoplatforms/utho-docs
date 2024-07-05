---
slug: "Groove to Your Tunes: Installing Subsonic Media Server on Ubuntu or Debian"
description: 'Discover the world of music streaming with Subsonic, a free application. This guide demonstrates how to effortlessly install the Subsonic media server on your Utho'
keywords: ["subsonic", "music", "audio", "streaming", "media server"]
tags: ["debian", "ubuntu"]
modified: 2024-04-09
modified_by:
  name: Utho
published: 2024-04-09
title: "Set up Subsonic Media Server on Ubuntu or Debian to Stream Your Favorite Tunes"
title_meta: "How to Install Subsonic Media Server on Ubuntu or Debian"
dedicated_cpu_link: true
authors: ["Pawan Kumar"]
---

Install Subsonic Media Server on Ubuntu or Debian to Stream Music Through Your Utho

## What is Subsonic?
[Subsonic](http://subsonic.org) is a straightforward media streaming service designed with a user-friendly interface, allowing you to share music and videos with multiple users effortlessly. It offers extensive customization options, including Chromecast support and file conversion capabilities.

This guide provides step-by-step instructions on how to install Subsonic on a Utho running Debian or Ubuntu. If you have a substantial music library, you may want to consider attaching a [Block Storage Volume](/docs/products/storage/block-storage/) to your Utho to store your music files conveniently.

## Install Java

To run Subsonic, you'll need to have Java installed on your system.

    {{< content "install-java-8-ppa" >}}

## Install Subsonic
1. Head to Subsonic's download page to grab the latest version, which is 6.1.3 at the time of this guide. [download](http://www.subsonic.org/pages/download.jsp) and install Subsonic on your Utho.

        wget https://s3-eu-west-1.amazonaws.com/subsonic-public/download/subsonic-6.1.3.deb
        sudo dpkg -i subsonic-6.1.3.deb

2.  By default, Subsonic runs as the root user, which poses security risks. To enhance security, create a new system user for Subsonic to run as.

        sudo useradd --system subsonic
        sudo gpasswd --add subsonic audio

3. Open the file `/etc/default/subsonic` in a text editor. Here, you can adjust settings such as the user, the port Subsonic listens on, memory allocation, and SSL encryption for streaming traffic. Change the `SUBSONIC_USER` variable to the new `subsonic` user.

       {{< file "/etc/default/subsonic" >}}

##Type "subsonic --help" on the command line to read an
##Explanation of the different options.
##For example, to specify that Subsonic should use port 80 (for http)
##and 443 (for https), and use a Java memory heap size of 200 MB, use
##SUBSONIC_ARGS="--port=80 --https-port=443 --max-memory=200"

       SUBSONIC_ARGS="--max-memory=150"

       SUBSONIC_USER=subsonic
       {{< /file >}}

{{< note respectIndent=false >}}
If you've configured a firewall, ensure to permit connections from the port on which Subsonic is listening.
{{< /note >}}

3. Restart Subsonic to apply the changes.

        sudo systemctl restart subsonic

## Configuration and Use

1.  By default, Subsonic searches for music files in `/var/music`. Create this directory and change its ownership to the `subsonic` user. If you prefer to store your music in a different directory, feel free to substitute it.

        sudo mkdir /var/music
        sudo chown subsonic:subsonic /var/music

2.  Access Subsonic in your browser by going to port 4040 on your Utho's public IP address or domain name.

3.  Upon your first visit, you'll encounter the following:

4.  Use the default username and password (admin/admin) or the provided link to log in:

5. Set a password for your admin account. You can also create other accounts at this stage.

{{< note respectIndent=false >}}
Passwords in the Subsonic database are stored in hex format, but they are not encrypted.
{{< /note >}}

6. Navigate to the **Media folders** link and specify the location where you intend to store your music. If you've used the default directory (`/var/music`) previously, you can skip this step. Once you've directed Subsonic to the correct directory and uploaded your music, click **Scan media folders now**. Subsonic will then proceed to create a database of your music files.

&nbsp;

## Next Steps

You can [configured to use SSL](http://www.subsonic.org/pages/getting-started.jsp), or alternatively, utilize an [NGINX reverse proxy](/docs/guides/use-nginx-reverse-proxy/).
