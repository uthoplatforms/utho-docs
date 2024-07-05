---
slug: "Utilizing Block Storage Volume with Nextcloud"
description: "This guide demonstrates how to utilize a Block Storage Volume for storing your Nextcloud data"
keywords: ["nextcloud", "cloud", "open source hosting", "block storage"]
tags: ["docker"]
published: 2024-03-15
modified: 2024-03-15
modified_by:
  name: Utho
title: Implementing Block Storage Volume with Nextcloud
title_meta: "How to Use a Block Storage Volume with Nextcloud"
authors: ["Pawan Kumar"]
---

## Understanding Nextcloud: A Brief Overview

Nextcloud is a cloud storage platform enabling you to store and access files across all your devices. This guide illustrates how to link a Block Storage Volume to a Utho, catering to the needs of extensive file libraries.

## Before You Begin

- Ensure you have root access to your Utho or a user account with `sudo` privilege.
- Update your system to the latest version.

## Install Docker and Docker Compose

### Docker

{{< content "installing-docker-shortguide" >}}

### Docker Compose

{{< content "install-docker-compose" >}}

## Attaching a Block Storage Volume

1.  Generate a Block Storage Volume and link it to your Utho. Refer to the Utho Manager for detailed instructions on how to complete this process.

    * You can also utilize the Utho CLI to generate a new Volume. The following command creates a 20GB Volume labeled nextcloud attached to a Utho named nextcloud-Utho. Customize the command as required:

            Utho-cli volume create nextcloud -l nextcloud-Utho -s 20

2.  Establish a filesystem on the Block Storage Volume, then create a mount point following the instructions provided by the Utho Manager.

3.  Verify the available disk space, taking into account some overhead with the Volume due to the file system.

        df -BG

        {{< output >}}
        Filesystem     1G-blocks  Used Available Use% Mounted on
        /dev/root            20G    2G       18G   6% /
        devtmpfs              1G    0G        1G   0% /dev
        tmpfs                 1G    0G        1G   0% /dev/shm
        tmpfs                 1G    1G        1G   2% /run
        tmpfs                 1G    0G        1G   0% /run/lock
        tmpfs                 1G    0G        1G   0% /sys/fs/cgroup
        tmpfs                 1G    0G        1G   0% /run/user/1000
        /dev/sdc             20G    1G       19G   1% /mnt/nextcloud                  
        {{< /output >}}

4.  Modify the ownership of the mount point:

        sudo chown username:username /mnt/nextcloud/

## Configuring Nextcloud using Docker Compose

Nextcloud offers an official `docker-compose.yml` file for persisting data to a database while running the Nextcloud container. You can customize this file to link the data volumes to your Block Storage Volume's mount point.

1.  Establish a directory for Nextcloud:

        mkdir ~/nextcloud && cd ~/nextcloud

2.  Using a text editor, create a `docker-compose.yml` file and insert the following content. Replace the placeholder with a suitable password for MariaDB:

        {{< file "~/nextcloud/docker-compose.yml" yaml >}}
        version: '2'

volumes:

    nextcloud:
    db:

services:

    db:
    image: mariadb
    restart: always
  volumes:

    - /mnt/nextcloud/:/var/lib/mysql

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

    - /mnt/nextcloud/data:/var/www/html

    restart: always

    {{< /file >}}

3.  Execute the Docker Compose configuration:

        docker-compose up -d

You can access Nextcloud at port 8080 using your Utho's public IP address.

4.  When setting up an admin account, navigate to the **Storage & database** drop-down menu, input the details as depicted below, and provide the MariaDB password used in the `docker-compose` file:

{{< note type="alert" respectIndent=false >}}

The default setup of Nextcloud lacks SSL encryption. For enhanced security of your data and communications, it's recommended to place the Nextcloud service behind a [reverse proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/). Additionally, a Docker Compose file incorporating an NGINX reverse proxy and Let's Encrypt is [available](https://github.com/nextcloud/docker/blob/master/.examples/docker-compose/with-nginx-proxy/mariadb/apache/docker-compose.yml)

{{< /note >}}

## Upload Data

1.  Once you've set up an admin account, you'll see the Nextcloud dashboard. Click on the `+` icon in the top left corner and choose **Upload file**. For demonstration, select a large file (such as an Ubuntu `.iso` file used to generate the output below).

2.  Once the file has been successfully uploaded, go back to the terminal and verify your available space:

        df -BG

        {{< output >}}

        Filesystem     1G-blocks  Used Available Use% Mounted on
        /dev/root            20G    2G       17G  11% /
        devtmpfs              1G    0G        1G   0% /dev
        tmpfs                 1G    0G        1G   0% /dev/shm
        tmpfs                 1G    1G        1G   2% /run
        tmpfs                 1G    0G        1G   0% /run/lock
        tmpfs                 1G    0G        1G   0% /sys/fs/cgroup
        /dev/sdc             20G    2G       17G  11% /mnt/nextcloud
        tmpfs                 1G    0G        1G   0% /run/user/1000

        {{< /output >}}

The output should indicate that the file has been stored in `/mnt/nextcloud`, which serves as the mount point for the Block Storage Volume.
