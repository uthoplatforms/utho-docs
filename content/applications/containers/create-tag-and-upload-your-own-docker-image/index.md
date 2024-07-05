---
slug: "Craft, Tag, and Elevate: Your Journey with Docker Images"
description: "Docker makes it easy to develop and deploy custom and consistent environments, called images. Here's how to create your own."
keywords: ['docker','container','dockerfile','docker image','docker hub']
tags: ["lamp","container","docker","ubuntu"]
modified: 2024-04-04
modified_by:
  name: Utho
published: 2024-04-04
title: "Create, Tag, and Upload Your Own Docker Image"
authors: ["Pawan Kumar"]
---
Docker simplifies the development and deployment of custom and consistent environments, bundling specific applications and dependencies into what it terms as "Images". These Docker images can be stored privately or accessed from the official repository, [Docker Hub](https://hub.docker.com/)

This guide serves as an introduction to Docker concepts and follows on from the previous guide, [How to Install and Pull Images for Container Deployment](/docs/guides/installing-and-using-docker-on-ubuntu-and-debian/). For further insights into Docker and containers, check out our [guides on Containers](/docs/applications/containers/).

## Create a Docker Image

Let's begin by creating a new local image using the latest Ubuntu Docker image. While there are existing LAMP stack images available in the repository, we'll demonstrate the process by crafting one in this guide.

1. Pull the latest Ubuntu image:

        docker pull ubuntu

2. Let's create a new container to incorporate our LAMP stack into Ubuntu. In this example, we'll name the container `lamp-server-template` and include the `bash` option with the docker command. This allows us to enter the container and proceed with further modifications:

        docker run --name lamp-server-template -it ubuntu:latest bash

3. Install the `lamp-server metapackage` within the container:

        apt-get install lamp-server^

Please note that this upgrade and installation process may take longer compared to working on a standard server. Throughout the installation of the LAMP stack, you will be prompted to create a MySQL root user password. Once the installation is complete, exit the container:

        exit

4.  To list all available containers, use the command `docker ps -a`:

## Commit Changes to the Image
To commit changes to the image, we first need the container ID. Similar to the example above, the `docker ps -a` command displays the ID, such as `d09dd0f24b58`. We'll name our new image `lamp-server-template` and commit the changes using the command:

    docker commit d09dd0f24b58 lamp-server-template

After running the `docker images` command, you'll find the new image `lamp-server-template` listed.

## Tag Your Image for Version Control

When you download an image from Docker Hub, you'll notice the `Status` line includes the image tag, like this:

    Status: Downloaded newer image for ubuntu:latest

Docker tags provide a convenient way to identify the version or release you're working with. This is particularly helpful when creating new images based on a base image. For instance, if you use a Ubuntu image as a base to build various images, Docker tags assist in tracking the differences:

    lamp-server-template:v1.8.10.2017
    lamp-server-template:v2.8.10.2017
    lamp-server-template:v3.8.10.2017

1.  Create image tags using `docker commit`. With the example tags provided earlier, tag the new image with a version number and date:

        docker commit d09dd0f24b58 lamp-server-template:v1.8.10.2017

2.  Execute `docker images` to view the newly created image along with its associated tag:

## Push Your Image to Docker Hub

1.  Before pushing the image to Docker Hub, include a description, your full name (`FULL NAME` in the example here), and Docker Hub username (`USERNAME`) in the `docker commit` command:

        docker commit -m "Added LAMP Server" -a "FULL NAME" d09dd0f24b58 USERNAME/lamp-server-template:v1.8.10.2017

2.  Once the image is fully tagged, log in to Docker Hub and push it:

        docker login

3.  You'll be prompted for your Docker Hub credentials. Upon successful authentication, you'll see `Login succeeded`. Now, you can push the image to the Hub using the command:

        docker push lamp-server-template:v1.8.10.2017

4.  Open your web browser, log in to your Docker Hub account, and navigate to your main repository. You'll find the new image listed there. Click on the image, then navigate to the **Tags** tab to view the added tag:

And that's all there is to it! You've successfully created a new image, made changes to it, committed those changes, tagged the image, and pushed the complete image to Docker Hub, all managed directly from your Utho.
