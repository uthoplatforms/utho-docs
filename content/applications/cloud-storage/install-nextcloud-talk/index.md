---
slug: Installing Nextcloud Talk
description: "Nextcloud 14 Enhances UI, Video and Text Chat, and End-to-End Encryption for Cloud Storage: Docker Installation Guide"
og_description: "Nextcloud 14 introduces enhancements to its user interface, video and text chat features, and adds end-to-end encryption to cloud storage. Here's a guide on installing it using Docker."
keywords: ["nextcloud", "cloud", "open source hosting", "video chat"]
tags: ["docker"]
published: 2024-03-13
modified: 2024-03-13
modified_by:
  name: Pawan Kumar
title: 'Introduction to Nextcloud Talk'
authors: ["Pawan Kumar"]
---

## What's New in Nextcloud 14?

Nextcloud 14 introduces a cloud storage platform with an added feature: Talk. This feature allows users to self-host a video and text chat platform with end-to-end encryption. This guide will walk you through setting up Nextcloud, and show how to use the video chat platform built-into the latest release.

## Install Docker CE

You'll require a Utho with Docker CE installed to proceed with the instructions provided in this guide.

{{< content "installing-docker-shortguide" >}}

## Install Nextcloud 14 and Talk

### Nextcloud

1. Pull and Launch the Nextcloud Image:

        docker run -d -p 8080:80 nextcloud

2. Open a web browser and go to port 8080 of your Utho's IP address to access the Nextcloud console.

3. When prompted, set up an admin account.

### Talk

Talk enables communication among all users registered to your Nextcloud instance. It offers simple text and video chat, along with private or group password-protected calls and screen sharing. You can find the source code for [Nextcloud Talk](https://github.com/nextcloud/spreed) Talk on GitHub.

1. Navigate to the Nextcloud console main page and click on the Settings icon located on the right side of the navigation bar. Then, select **+ Apps**

2.  Install the Talk add-on located in the  section. Select the app and click .

Within the **Social & communication**, locate the Talk add-on. Click on the app, then hit **Enable** to install it.

3.  Go to the **Users** section within the Nextcloud interface and proceed to create logins for each member of your team.

## How to Use Talk

Nextcloud Talk utilizes [WebRTC](https://simplewebrtc.com/) and is accessible directly from your web browser.

1.  Go to the settings menu and select **Users**. Add one or more users by providing them with their respective usernames and passwords. Instruct each user to log in through the web console.

2.  After installing Talk, you will see an icon for the addon appear on the navigation menu.

3.  Click on this icon to access Talk and grant permission for your system's camera and microphone when prompted. Once permissions are granted, you can initiate a chat or video call with any of the users you've added.

For basic functionality, you can make video calls using Firefox. However, if you're using Google Chrome, it requires an HTTPS connection to access the camera and microphone. To achieve this, you can either create an SSL certificate [SSL certificate](/docs/security/ssl/) or place Nextcloud behind a reverse proxy [reverse proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/).

## Docker Compose

The standard Nextcloud Docker image comes preconfigured to maintain persistent data in case of container failure. However, Docker Compose simplifies launching a setup with a dedicated database container and persistent data volume. This approach ensures data consistency during upgrades and automatically manages container restarts.

### Install Docker Compose

{{< content "install-docker-compose" >}}

### Create docker-compose.yaml

1.  Create a file named docker-compose.yaml using a text editor and insert the following configuration (from the Nextcloud Github repo). Fill in the MYSQL_ROOT_PASSWORD and MYSQL_PASSWORD with appropriate values.

    {{< file "docker-compose.yaml" yaml >}}
  version: '2'

  volumes:
    nextcloud:
    db:

  services:
    db:
      image: mariadb
      restart: always
      volumes:
        - db:/var/lib/mysql
      environment:
        - MYSQL_ROOT_PASSWORD=
        - MYSQL_PASSWORD=
        - MYSQL_DATABASE=nextcloud
        - MYSQL_USER=nextcloud

    app:
      image: nextcloud
      ports:
        - 8080:80
      links:
        - db
      volumes:
        - nextcloud:/var/www/html
      restart: always
{{< /file >}}

2.  If the container from the previous section is still running, stop it by executing docker stop followed by the container name or ID.

3.  Start the Docker Compose configuration by running the following command:

        docker-compose up -d

 Nextcloud should now be accessible at port 8080 on your Utho's public IP address.

4.  When setting up an admin account, go to the **Storage & database** drop-down menu. Fill in the details as indicated below, and remember to input the MySQL password you specified in the `docker-compose` file:

{{< note type="alert" respectIndent=false >}}
The default setup of Nextcloud lacks SSL encryption. To ensure the security of your data and communications, it's recommended to place the Nextcloud service behind a [reverse proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/). You can use a Docker Compose file that integrates an NGINX reverse proxy and Let's Encrypt, which is provided here [available](https://github.com/nextcloud/docker/blob/master/.examples/docker-compose/with-nginx-proxy/mariadb/apache/docker-compose.yml).
{{< /note >}}
