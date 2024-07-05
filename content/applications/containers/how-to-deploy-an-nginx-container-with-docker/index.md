---
slug: "Unleash NGINX: Deploying Containers with Docker in a Snap"
description: 'Get Started with Docker Containers on Your Utho: An Introduction.'
keywords: ["docker", "container", "dockerfile", "nginx container"]
tags: ["container","docker","nginx"]
modified: 2024-04-05
modified_by:
  name: Utho
published: 2024-04-05
title: 'How to Deploy an nginx Container with Docker on Linode'
authors: ["Pawan Kumar"]
---

## What is a Docker Container?
As per Docker.com, a container is essentially a "lightweight, self-contained, executable unit of a software application, encompassing all necessary components to operate: code, runtime, system tools, libraries, and configurations." Containers provide isolation for software, keeping it separate from its environment, and are generated from images fetched from a Docker registry. For instance, you can retrieve the nginx image and spawn multiple containers based on it as required.

## Docker Command Syntax
To deploy a Docker container, follow this syntax:

    docker run –name CONTAINER-NAME -p NETWORK_PORT:CONTAINER_PORT IMAGE NAME

It consists of:

- CONTAINER-NAME: The name you assign to the container.
- NETWORK_PORT: A port accessible to the network.
- CONTAINER_PORT: The port on which the container will operate.
- IMAGE-NAME: The name of the image to be utilized for the container.

## Deploy a Container
Here's an example of creating an nginx container with port 80 exposed, using the official nginx image:

1. Now, let's verify the currently available official image:

        docker images

In this screenshot, the nginx image is dated two weeks ago:

2. Update the original image by executing `docker pull nginx`, as demonstrated in the [How to Install Docker and Pull Images for Container Deployment](/docs/guides/installing-and-using-docker-on-ubuntu-and-debian/) guide. Afterward, rerun `docker images` to ensure the update has been applied:

3.  Deploy the container:

        docker run --name docker-nginx -p 80:80 -d nginx

This command will display the newly generated ID for the container. Keep in mind that the `-d` *(detach)* option returns you to the prompt:

4. To ensure the container is active, verify its status:

        docker ps -a

5.  Visit your Utho's IP address to view the default nginx welcome message:

## How to Stop and Delete Containers

1. To halt the container, employ the first few characters of the container ID (`e468` in this instance):

        docker stop e468

2. Delete the container using the `rm` command along with the same container ID.

        docker rm e468
