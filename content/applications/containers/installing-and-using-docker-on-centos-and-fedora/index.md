---
slug: "Docker Delight: Effortless Setup and Mastery on CentOS and Fedora"
description: 'Learn the ropes of effortlessly installing Docker Engine on CentOS and Fedora Linux distributions with this comprehensive guide'
keywords: ['docker','docker engine','containers']
tags: ["docker","containers","centos","fedora"]
published: 2024-04-08
modified_by:
  name: Utho
title: "Mastering Docker: Setup and Usage on CentOS and Fedora"
title_meta: "Unlocking Docker: Step-by-Step Installation and Utilization on CentOS and Fedora"
relations:
    platform:
        key: installing-and-using-docker
        keywords:
            - distribution: CentOS and Fedora
authors: ["Utho"]
---

Docker simplifies the creation, deployment, and management of lightweight, self-contained units known as containers. These containers encapsulate all the essentials like code, libraries, runtime, and dependencies to run an application seamlessly.

This guide helps you install Docker Engine on different Linux distributions like CentOS and Fedora using either the **YUM** or **DNF** package manager. Additionally, it covers acquiring and executing Docker images.

## Before You Begin

1. Make sure you have command line access to a Linux server running a supported distribution. If not, you can refer to the [Getting Started](/docs/products/platform/get-started/) and [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guides to create a new Utho.

Note: This guide is tailored for a non-root user. Commands needing elevated privileges are prefixed with `sudo`. If you're unfamiliar with the `sudo` command, refer to the [Users and Groups](/docs/guides/linux-users-and-groups/) guide for assistance.

1. Take a look at the following Docker guides to deepen your understanding of Docker, its advantages, and its appropriate use cases.

    - [An Introduction to Docker](/docs/guides/introduction-to-docker/)
    - [When and Why to Use Docker](/docs/guides/when-and-why-to-use-docker/)

## Installing Docker Engine

[Docker Engine](https://docs.docker.com/engine/) serves as the foundational software for containerizing applications with Docker containers. Follow the instructions below to install Docker on CentOS and Fedora using the **YUM** package manager.

**Supported distributions**: CentOS 7, CentOS 8 (including other RHEL 8 derivatives like AlmaLinux and RockyLinux), and Fedora 32 (and newer)

Note: Although the **YUM** package manager has been succeeded by **DNF** on CentOS 8 and Fedora, the **yum** command still functions as a symlink to DNF. Therefore, these instructions remain valid.

1. Before proceeding, confirm that Docker is not currently installed. You can safely disregard any output indicating that certain packages are not found.

        sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine

1. Install the `yum-utils` package, which includes `yum-config-manager`. When using DNF, this will automatically install `dnf-plugins-core`.

        sudo yum install yum-utils

1. To add the Docker repository, use `yum-config-manager`, which seamlessly translates to `dnf config-manager` when using DNF. In the command below, replace `[url]` with the repository URL corresponding to your distribution:

        sudo yum-config-manager --add-repo [url]

    - **RHEL/CentOS and derivatives:** `https://download.docker.com/linux/centos/docker-ce.repo`
    - **Fedora 32 and later:** `https://download.docker.com/linux/fedora/docker-ce.repo`

1.  Install Docker Engine along with other necessary packages:

        sudo yum install docker-ce docker-ce-cli containerd.io

In this step, you might receive a prompt to accept the GPG key. The fingerprint you should see is `060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35`. Confirm these details and type *y* to accept.

For further installation guidance on these distributions, refer to Docker's documentation.
- [Install Docker Engine on CentOS](https://docs.docker.com/engine/install/centos/)
- [Install Docker Engine on Fedora](https://docs.docker.com/engine/install/fedora/)

## Starting and Testing Docker

Once Docker Engine is installed, follow these steps to start Docker and confirm everything is functioning properly by running a test image:

1.  Make sure the Docker server is up and running.

        sudo systemctl start docker

1.  Optionally, configure Docker to start automatically when the server boots up. This is advisable if you plan to deploy production applications using Docker.

        sudo systemctl enable docker
        sudo systemctl enable containerd

1.  Confirm that Docker is installed correctly by executing the "hello-world" image.

        sudo docker run hello-world

If Docker installation is successful, it should download and execute the hello-world image, displaying a success message. In the output, you should find a message similar to this:

    {{< output >}}
    Hello from Docker!
    This message shows that your installation appears to be working correctly.
    {{< /output >}}

## Managing Docker with a Non-Root User

By default, running Docker commands requires `sudo`, but a new group named docker was created during installation. When the Docker daemon starts, it opens a Unix socket for members of the docker group.

Before proceeding, ensure you have a non-sudo limited user account. If you haven't created one yet, refer to the guides [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) or[Linux Users and Groups](/docs/guides/linux-users-and-groups/) for instructions.

1.  Run the command below to add a user to the *docker* group, replacing *[user]* with the username of your limited user account.

        sudo usermod -aG docker [user]

1.  Log in to the system as the limited user.

1.  Verify the limited user can run `docker` commands without `sudo` by running the "hello-world" image once again.

        docker run hello-world

The output should display a success message similar to what you saw earlier.

Note: The docker group provides privileges akin to those of the root user. Refer to the [Docker Daemon Attack Surface](https://docs.docker.com/engine/security/#docker-daemon-attack-surface) guide in Docker's documentation for insights into how this might impact system security. If you prefer to run the Docker daemon without relying on root privileges, consult the instructions outlined in [Run the Docker daemon as a non-root user (Rootless mode)](https://docs.docker.com/engine/security/rootless/).

### Troubleshooting Config File Loading Errors

If the user previously executed `sudo docker` commands before joining the group, they may encounter a failure when attempting to load the configuration file, illustrated as follows

    {{< output >}}
    WARNING: Error loading config file: /home/user/.docker/config.json -
    stat /home/user/.docker/config.json: permission denied
    {{< /output >}}

The problem arises from the .docker directory in their home directory (~/.docker) being created with permissions granted by `sudo`.

There are two potential solutions:

1.  Delete the `.docker` directory from their home directory. Docker will recreate it automatically, but any custom settings will be lost.

1.  Adjust the permissions on the `.docker` directory using the following commands:

        sudo chown example_user:example_user /home/example_user/.docker -R
        sudo chmod g+rwx "/home/example_user/.docker" -R

## Using Docker Images to Deploy Containers

Docker images serve as blueprints containing instructions and specifications for creating a container. To utilize Docker, you'll need to acquire an image or construct your own by crafting a dockerfile. For further details, refer to An Introduction to Docker.

### Listing Images

To view all images on your system, just run this command. It will show you the *hello-world image* from earlier, along with any other images you've acquired

    docker images

### Finding an Image

Images are stored in Docker registries, such as [Docker Hub](https://hub.docker.com/) (Docker's official registry). You can explore images on this website or utilize the following command to search the Docker registry. Replace `[keyword]` with your desired search terms, like *nginx* or *apache*.

    docker search [keyword]

### Obtaining an Image

Once you locate an image, download it to your server using the following command. Replace `[image]` with the image name you wish to use.

    docker pull [image]

For instance, to pull down the official nginx image, run: `docker pull nginx`.

### Running an Image

Now, generate a container from the image using the `docker` run command. Remember to substitute `[image]` with the name of your desired `image`.

    docker run [image]

If the image hasn't been downloaded yet and is accessible in Docker's registry, it will be automatically fetched to your server.

## Managing Docker Containers

### Listing Containers

To display all active (and inactive) Docker containers currently running on your system, execute the following command:

    docker ps -a

The output will resemble the following example, which illustrates the `hello-world` container.

    {{< output >}}
    CONTAINER ID   IMAGE         COMMAND    CREATED       STATUS                   PORTS     NAMES
    5039168328a5   hello-world   "/hello"   2 hours ago   Exited (0) 2 hours ago             magical_varahamihira
    {{< /output >}}

### Starting a Container

To commence a Docker container, employ the subsequent command, substituting `[ID]` with the container ID linked to the container you intend to start:

    docker start [ID]

### Stopping a Container

To halt a Docker container, utilize the ensuing command, replacing `[ID]` with the container ID associated with the container you aim to cease:

    docker stop [ID]

Certain images (like the `hello-world` image) automatically halt after execution. However, numerous other containers persist until explicitly instructed to stop, and you may prefer to run these containers in the background. In such instances, this command proves invaluable.

### Removing a Container

To eliminate a Docker container, apply the following command, replacing `[ID]` with the container ID corresponding to the container you wish to delete:

    docker rm [ID]
