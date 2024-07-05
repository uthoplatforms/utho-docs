---
slug: "Docker Magic: Your Rapid-Fire Command Cheat Sheet"
description: 'Unlock the Power of Docker: Your Go-To Cheat Sheet for Installation, Containers, Images, and Beyond'
keywords: ["docker", "quick reference", "cheat sheet", "commands"]
aliases: ['/applications/containers/docker-commands-quick-reference-cheat-sheet/','/applications/containers/docker-quick-reference-cheat-sheet/']
tags: ["container","docker"]
modified: 2024-04-05
modified_by:
  name: Utho
published: 2024-04-05
title: Docker Commands Quick Reference Cheat Sheet
authors: ["Utho"]
---

Docker has gained immense popularity among developers, operators, and businesses as a software container platform. Containers allow software to run independently on a host operating system, packaged with only the essentials. Docker creates lightweight, efficient systems that operate consistently across different environments.

To harness Docker's power, it's crucial to understand its core commands. This cheat sheet provides a quick reference for fundamental Docker commands, covering installation, interaction with Docker Hub, and managing containers and images.

For installation, we recommend using Docker Community Edition ([Docker CE](https://docs.docker.com/engine/installation/). Check out the official documentation or our [How to Install Docker](/docs/guides/installing-and-using-docker-on-ubuntu-and-debian/) guide for detailed instructions.

{{< note >}}
If you haven't added your limited user account to the `docker` group using `sudo usermod -aG docker username`, you'll need to run all the commands in this cheatsheet with `sudo`.
{{< /note >}}

## Docker Hub

| Docker Syntax | Description |
|:-------------|:---------|
| **docker search** searchterm | Search Docker Hub for images. |
| **docker pull** user/image | Downloads an image from Docker Hub. |
| **docker login** | Authenticate to Docker Hub <br> (or other Docker registry). |
| **docker push** user/image | Uploads an image to Docker Hub. <br> You must be authenticated to run this command. |

## Image and Container Information

| Docker Syntax | Description |
|:-------------|:---------|
| **docker ps** | List all running containers. |
| **docker ps -a** | List all container instances, with their ID<br> and status. |
| **docker images** | Lists all images on the local machine. |
| **docker history** user/image | Lists the history of an image. |
| **docker logs** [container name or ID] | Displays the logs from a running container. |
| **docker port** [container name or ID] | Displays the exposed port of a running container. |
| **docker diff** [container name or ID] | Lists the changes made to a container. |

## Work With Images and Containers

| Docker Syntax | Description |
|:-------------|:---------|
| **docker run** -it user/image | Runs an image, creating a container and<br> changing the terminal<br> to the terminal within the container. |
| **docker run** -p $HOSTPORT:$CONTAINERPORT -d user/image | Run an image in detached mode<br> with port forwarding. |
| **`ctrl+p` then `ctrl+q`** | From within the container's command prompt,<br> detach and return to the host's prompt. |
| **docker attach** [container name or ID] | Changes the command prompt<br> from the host to a running container. |
| **docker start** [container name or ID] | Start a container.  |
| **docker stop** [container name or ID] | Stop a container.  |
| **docker rm -f** [container name or ID] | Delete a container. |
| **docker rmi** | Delete an image. |
| **docker tag** user/image:tag user/image:newtag | Add a new tag to an image. |
| **docker exec** [container name or ID] shell command | Executes a command within a running container. |

## Image Creation

| Docker Syntax | Description |
|:-------------|:---------|
| **docker commit** user/image | Save a container as an image. |
| **docker save** user/image | Save an image to a tar archive. |
| **docker build -t sampleuser/ubuntu .** | Builds a Docker image<br> from a Dockerfile<br> in the current directory. |
| **docker load** | Loads an image from file.|
