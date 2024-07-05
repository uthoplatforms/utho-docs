---
slug: "Discover Docker: A Beginner's Guide"
description: 'Explore the basics of Docker, containers, and dockerfiles on your Utho in this introductory guide'
keywords: ["docker", "container", "dockerfile"]
tags: ["container","docker"]
modified: 2024-04-08
modified_by:
  name: Utho
published: 2024-04-08
title: 'An Introduction to Docker'
authors: ["Pawan Kumar"]
---

## What is Docker?

Docker simplifies the process of creating, deploying, and managing lightweight packages known as containers. These containers encapsulate everything required to run an application, including code, libraries, runtime, system settings, and dependencies.

Each container operates with its own CPU, memory, block I/O, and network resources, independent of an individual kernel and operating system. While Docker and virtual machines share similarities, they differ in resource sharing and allocation.

Containers enhance your Utho's capabilities in various ways. For instance, you can deploy multiple instances of nginx with different configurations (e.g., development and production). Unlike deploying multiple virtual machines, containers do not strain your Utho's resources.

## Docker Images
Every Docker container originates from an image obtained from a Docker registry, such as the official [Docker Hub](https://hub.docker.com/). Images serve as blueprints for creating containers. A single image can spawn multiple containers. For instance, you can utilize the latest nginx image to deploy various web server containers for:

*  Web dev ops
*  Testing
*  Production
*  Web applications

## Dockerfiles

A `Dockerfile` is a text file containing commands needed to construct an image. To build an image based on these commands, administrators use the `docker build` command. The instructions within the Dockerfile can specify software versions and dependencies for consistent and stable deployments.

The following commands are commonly used in Dockerfiles:

- **ADD**: Copies files from a source on the host to the container's filesystem.
- **CMD**: Executes a specific command within the container.
- **ENTRYPOINT**: Sets a default application to run when a container is created from the image.
- **ENV**: Sets environment variables.
- **EXPOSE**: Exposes a specific port for networking between the container and the outside world.
- **FROM**: Defines the base image for the build process.
- **MAINTAINER**: Specifies the image creator's full name and email address.
- **RUN**: Executes commands during the build process.
- **USER**: Sets the username (UID) to run the container.
- **VOLUME**: Enables access from the container to a directory on the host machine.
- **WORKDIR**: Sets the path for the CMD command to execute.

Not all commands are required. Below is an example of a Dockerfile using only the **MAINTAINER**, **FROM**, and **RUN** commands:

    {{< file "Dockerfile" docker >}}
    MAINTAINER NAME EMAIL
    FROM ubuntu:latest
    RUN apt-get -y update && apt-get -y upgrade && apt-get install -y build-essential

    {{< /file >}}

## Docker Swarm

Docker simplifies the process of connecting servers to form a cluster known as a Docker Swarm. Once a Swarm manager, or leader, is established and nodes are linked to it, container deployment can be easily scaled out. The leader dynamically adjusts the cluster by adding or removing tasks to maintain the desired state.

A node represents a single instance of the Docker engine within the Swarm. You can run one or more nodes on a single Utho. The Swarm manager utilizes ingress load balancing to expose services accessible within the Swarm. Docker Swarm offers additional capabilities, such as:

- Monitoring container health.
- Launching multiple containers from a single Docker image.
- Scaling the number of containers based on current load.
- Conducting rolling updates across containers.
- Providing redundancy and failover mechanisms.
- Adjusting container instances as demand fluctuates.

## Next Steps
To delve deeper into Docker, explore our [Docker Quick Reference](/docs/guides/docker-commands-quick-reference-cheat-sheet/), our guide on [deploying a Node.js web server](/docs/guides/node-js-web-server-deployed-within-docker/), or the Utho guide on [How to install Docker and deploy a LAMP Stack](/docs/guides/how-to-install-docker-and-deploy-a-lamp-stack/).
