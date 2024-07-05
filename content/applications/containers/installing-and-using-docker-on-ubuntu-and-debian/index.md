---
slug: "Dive into Docker: Effortless Setup and Usage on Ubuntu and Debian"
description: "Explore our comprehensive guide on installing Docker Engine on the latest versions of Ubuntu and Debian Linux distributions. ✓ Click here to dive into the guide now"
og_description: "An extensive guide on installing Docker Engine on Ubuntu and Debian Linux distributions"
keywords: ['docker','docker engine','containers']
tags: ["docker","containers","debian","ubuntu"]
published: 2024-04-08
modified_by:
  name: Utho
title: "Unlocking Docker: Installation and Utilization on Ubuntu and Debian"
title_meta: "How to Install and Use Docker on Ubuntu and Debian"
relations:
    platform:
        key: installing-and-using-docker
        keywords:
            - distribution: Ubuntu and Debian
authors: ["Utho"]
---

Docker simplifies the process of creating, deploying, and managing lightweight, independent packages known as *containers*. These containers encapsulate all the essentials like code, libraries, runtime, system settings, and dependencies required to run an application.

This guide helps you install Docker Engine on different Linux distributions like Ubuntu and Debian using the **apt** package manager. Additionally, it covers acquiring and executing Docker images.

## Before You Begin

1. Ensure you have command line access to a Linux server running a supported distribution. If not, follow the steps outlined in the [Getting Started](/docs/products/platform/get-started/) and [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guides to create a new Utho.

Note: This guide is tailored for a non-root user. Commands requiring elevated privileges are prefixed with `sudo`. If you're unfamiliar with the `sudo` command, refer to the [Users and Groups](/docs/guides/linux-users-and-groups/) guide.

1. Explore the following Docker guides to enhance your understanding of Docker, its advantages, and its optimal use cases.

    - [An Introduction to Docker](/docs/guides/introduction-to-docker/)
    - [When and Why to Use Docker](/docs/guides/when-and-why-to-use-docker/)

## Installing Docker Engine on Ubuntu and Debian

[Docker Engine](https://docs.docker.com/engine/) serves as the foundational containerization software for deploying Docker containers. Follow the instructions below to install Docker Engine on one of the supported Ubuntu and Debian releases:

**Supported distributions**: Ubuntu 20.04, Ubuntu 18.04, Ubuntu 16.04, Debian 10, Debian 9. Recent Ubuntu non-LTS releases, such as 21.04, 20.10, and 21.10, should also be supported.

1. Before proceeding, ensure Docker is not already installed. Output indicating missing packages can be ignored.

        sudo apt remove docker docker-engine docker.io

1.  Install the necessary packages to configure Docker's repository.

        sudo apt update
        sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release

1.  Add Docker's GPG key. In the command below, replace `[url]` with the corresponding URL for your system's distribution.

        curl -fsSL [url]/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

- **Ubuntu:** `https://download.docker.com/linux/ubuntu`
- **Debian:** `https://download.docker.com/linux/debian`

1.  Add the *stable* Docker repository by replacing `[url]` with the URL that matches your system's distribution.

        echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] [url] $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

1.  Next, install Docker Engine along with other necessary packages.

        sudo apt update
        sudo apt install docker-ce docker-ce-cli containerd.io

Refer to Docker's documentation for further installation instructions tailored to these distributions.

- [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)
- [Install Docker Engine on Debian](https://docs.docker.com/engine/install/debian/)

## Starting and Testing Docker

Once Docker Engine is installed, start Docker and confirm everything is operational by running a test image.

1.  Make sure the Docker server is up and running.

        sudo systemctl start docker

1.  You can optionally set up Docker to start automatically when the server boots up. This is advisable, especially if you plan to run production applications using Docker.

        sudo systemctl enable docker
        sudo systemctl enable containerd

1. To verify that Docker is correctly installed, run the "hello-world" image.

        sudo docker run hello-world

If the installation is successful, Docker should download and execute the hello-world image, displaying a success message. The output should include a message similar to the following:

    {{< output >}}
    Hello from Docker!
    This message shows that your installation appears to be working correctly.
    {{< /output >}}

## Utilizing Docker with a Non-Root User

By default, running Docker commands requires `sudo`, but a new group named docker was created during installation. When the Docker daemon starts, it opens a Unix socket for members of the docker group.

Before proceeding, ensure you have a limited user account that does not belong to the sudo group. If you haven't created one yet, refer to the guides [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) or [Linux Users and Groups](/docs/guides/linux-users-and-groups/) for instructions.

1.  To add a user to the docker group, execute the command below, replacing [user] with the name of your limited user account.

        sudo usermod -aG docker [user]

1.  Sign in to the system using the limited user account.

1. Ensure that the limited user can execute `docker` commands without sudo by running the "hello-world" image again.

        docker run hello-world

The output should display a success message similar to the previous one.

Note: The docker group offers privileges similar to those of the root user. For more insight into how this could impact system security, refer to the [Docker Daemon Attack Surface](https://docs.docker.com/engine/security/#docker-daemon-attack-surface) guide in Docker's documentation. To operate the Docker daemon without root privileges, follow the instructions outlined in [Run the Docker daemon as a non-root user (Rootless mode)](https://docs.docker.com/engine/security/rootless/).

### Fixing Issues with Loading Configuration Files

If the user had previously executed sudo docker commands before joining the group, they might encounter a configuration file loading failure like this:

    {{< output >}}
    WARNING: Error loading config file: /home/user/.docker/config.json -
    stat /home/user/.docker/config.json: permission denied
    {{< /output >}}

This issue arises because the .docker directory in their home directory (~/.docker) was initially created with permissions granted by `sudo`.

Here are two potential solutions:

1.  Simply delete the .docker directory from your home directory. Docker will recreate it automatically, but any customized settings will be lost.

1.  Adjust the permissions on the `.docker` directory using the commands:

        sudo chown example_user:example_user /home/example_user/.docker -R
        sudo chmod g+rwx "/home/example_user/.docker" -R

## Using Docker Images to Deploy Containers

Docker images serve as templates containing instructions and specifications for creating a container. To utilize Docker, you'll need to acquire an image or construct your own using a Dockerfile. For further details, refer to An Introduction to Docker.

### Listing Images

To view all images on your system, execute the following command. It will display the *hello-world* image used previously, along with any other images you've obtained.

    docker images

### Finding an Image

Images are stored in Docker registries, such as [Docker Hub](https://hub.docker.com/) (Docker's official registry). You can explore images on this website or utilize the following command to search the Docker registry. Replace `[keyword]` with your desired search terms, like *nginx* or *apache*.

    docker search [keyword]

### Obtaining an Image

Once you've identified an image, download it to your server. In the command below, replace `[image]` with the desired image name.

    docker pull [image]

For example, to retrieve the official nginx image, execute: `docker pull nginx`.

### Running an Image

Then, create a container based on the `image` using the `docker run` command. Once again, replace [image] with the image name.

    docker run [image]

If the image hasn't been downloaded yet and is available in Docker's registry, it will automatically be pulled down to your server.

## Managing Docker Containers

### Listing Containers

To display all active (and inactive) Docker containers on your system, use the following command:

    docker ps -a

Here's an example of the expected output, displaying the hello-world container:

    {{< output >}}
    CONTAINER ID   IMAGE         COMMAND    CREATED       STATUS                   PORTS     NAMES
    5039168328a5   hello-world   "/hello"   2 hours ago   Exited (0) 2 hours ago             magical_varahamihira
    {{< /output >}}

### Starting a Container

To initiate a Docker container, use the following command. Replace `[ID]` with the container ID corresponding to the container you want to start:

    docker start [ID]

### Stopping a Container

To halt a Docker container, use this command. Replace `[ID]` with the container ID associated with the container you wish to stop:

    docker stop [ID]

Certain images, like the `hello-world` image, automatically terminate after execution. However, many other containers persist until explicitly instructed to stop. If you prefer running these containers in the background, this command is useful.

### Removing a Container

To remove a Docker container, execute the following command. Replace `[ID]` with the container ID linked to the container you want to delete:

    docker rm [ID]
