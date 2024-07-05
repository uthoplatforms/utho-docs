---
slug: "Dive into Docker: Deploying a LAMP Stack Made Easy"
description: 'This article provides detailed, step-by-step instructions for installing Docker and leveraging the application to set up a LAMP stack within a Docker container'
keywords: ["docker", "lamp", "LAMP", "ubuntu", "debian"]
tags: ["lamp","container","docker"]
modified: 2024-04-05
modified_by:
  name: Utho
published: 2024-04-05
title: 'How to install Docker and deploy a LAMP Stack'
deprecated: true
authors: ["Pawan Kumar"]
---

Docker simplifies application management by providing containerized platforms. Users can effortlessly download pre-configured apps without dealing with the tedious installation and setup process. Moreover, Docker containers can be stacked on top of each other for enhanced functionality.

## Install Prerequisites
To install Docker on a Debian/Ubuntu VM, an extra step is required due to a [known issue](https://github.com/docker/docker/issues/23347) with the dependencies of the Docker .deb package.

1. Run:

        apt-get install dmsetup && dmsetup mknodes

## Install Docker
You can resolve this issue by utilizing the Docker-maintained install script for Debian or Ubuntu. For installation on other operating systems, refer to the [Docker Installation](https://docs.docker.com/en/latest/installation/) guides.

1.  Run:

        curl -sSL https://get.docker.com/ | sh

{{< note respectIndent=false >}}

The latest version of the Docker script examines AUFS support and issues the following warning if support is absent:

          Warning: The current kernel is not compatible with linux-image-extra-virtual.

          package.  We have no AUFS support.  Consider installing the packages
          linux-image-virtual kernel and linux-image-extra-virtual for AUFS support.
          + sleep 10

You can safely disregard this message since the script will proceed with the installation using DeviceMapper or OverlayFS instead. However, if you specifically require AUFS support, you'll need to set up a [distribution supplied](/docs/guides/run-a-distributionsupplied-kernel-with-pvgrub/) or [custom compiled](/docs/guides/custom-compiled-kernel-with-pvgrub-debian-ubuntu/) kernel.
{{< /note >}}

2.  If needed, add the non-root user to the "docker" group:

        sudo usermod -aG docker example_user

## Download the Docker Lamp Image
You can access the Docker Hub user page for Utho (https://hub.docker.com/u/utho/). Select the lamp image to view configuration details.

1.  To find Utho user images, search for **utho**:

        sudo docker search utho

2. Download the **utho/lamp** image.

        sudo docker pull utho/lamp

## Run the Docker Container, Apache, and MySQL

Once an image is downloaded, no image containers are running.

1.  To initiate, create, or activate a new container and forward port 80:

        sudo docker run -p 80:80 -t -i utho/lamp /bin/bash

{{< note type="alert" >}}
This command also modifies the terminal prompt to indicate that you're now operating as the root user within the new container.
{{< /note >}}

2.  Launch Apache as the container's root user:

        service apache2 start

3.  Start MySQL:

        service mysql start

4.  To exit the container while keeping it running, press `ctrl + p`, then `ctrl + q`.


5. Enter the IP address in a web browser to test the site.

{{< note respectIndent=false >}}

The root directory for the website is `/var/www/example.com/public_html/`.

{{< /note >}}

Congratulations! You've successfully installed and configured a LAMP stack using Docker!

## Where to Find Configuration Settings

The LAMP image was installed following the instructions in the [Hosting a Website](/docs/guides/hosting-a-website-ubuntu-18-04/) guide on a Ubuntu container. You can find the configuration files and settings there, or alternatively, on the [Docker Hub utho/lamp](https://registry.hub.docker.com/u/utho/lamp/) page.
