---
slug: "Unraveling Docker: Your Guide to Container Magic"
deprecated: true
description: 'Discover the ins and outs of Docker, the container runtime, and learn how to leverage it to set up the Nginx web server with this comprehensive guide'
keywords: ["docker", "ubuntu", "centos", "container"]
tags: ["ubuntu","container","docker","centos"]
modified: 2024-04-08
published: 2014-01-28
title: Docker
authors: ["Pawan Kumar"]
---
Docker simplifies application deployment by packaging them into portable, lightweight containers. Before proceeding, ensure you've [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/). Let's get started with the installation:

## Installation

We'll cover how to install Docker on Ubuntu 12.04 and CentOS 6.4. Docker provides repositories for these distributions, making installation straightforward.

### Ubuntu 12.04 64bit

Below are the steps to add the Docker repository for Ubuntu and install the software:

1. Docker is available as a package in Docker's Ubuntu repositories, but only for 64bit systems. First, you'll need to add the Docker repository key using the `apt-key` command:

        sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9

2.  Here's how to add the Docker repository to your apt sources:

        echo "deb http://get.docker.io/ubuntu docker main" | sudo tee /etc/apt/sources.list.d/docker.list

3.  Run the following commands to update apt and install `lxc-docker`:

        sudo apt-get update
        sudo apt-get install lxc-docker

4. To confirm that the installation was successful, launch an example Ubuntu container. This command will automatically download any missing images, start the container, and give you an interactive bash session:

        sudo docker run -i -t ubuntu /bin/bash

The output should look like this:

### docker run -i -t ubuntu /bin/bash

    Unable to find image 'ubuntu' (tag: latest) locally
    Pulling repository ubuntu
    8dbd9e392a96: Download complete
    b750fe79269d: Download complete
    27cf78414709: Download complete
    root@145dfc4f6dff:/#
    {{< /output >}}

5.  To exit the container, type `exit`.

### CentOS 6 64bit

For CentOS 6.4, Docker is available on the [EPEL](https://fedoraproject.org/wiki/EPEL#How_can_I_use_these_extra_packages.3F) repository as the `docker-io` package, but only for 64-bit systems.

1. First, add the EPEL repository by installing the latest [epel-release package](http://download.fedoraproject.org/pub/epel/6/i386/repoview/epel-release.html).

2.  Then, install Docker using the following command:

        sudo yum -y install docker-io

3.  To start the Docker daemon, use the `service` command:

        sudo service docker start

4.  If you want the Docker daemon to start automatically at boot, run:
        sudo chkconfig docker on

5. To ensure that the installation is successful, run an example Fedora container. This command will automatically download any missing images, run the container, and give you an interactive bash session:

        sudo docker run -i -t fedora /bin/bash

6.  To exit the container, simply type `exit`.

## What Can I Do with Docker?

Docker enables users to bundle their applications and configurations into lightweight images for deployment as portable containers.

### Hello World

To run a Docker container that prints "hello world", use the following command:

    docker run ubuntu /bin/echo hello world

It should display `hello world`.

This command instructs Docker to perform several tasks:

- If the Ubuntu image is not already stored locally, Docker downloads it from the Docker Hub.
- Docker creates a new container based on the Ubuntu image, providing it with a writable file system and networking capabilities.
- Docker assigns an IP address to the container and sets up network address translation (NAT) for inbound and outbound traffic.
- Docker executes the command /bin/echo hello world within the container and displays the output.
- After completing the command, the container exits.

### Creating a Dockerfile for Nginx

When building an image with Docker, you need to define the instructions in a file called `Dockerfile`. Keep in mind that this file must be named exactly Dockerfile, and any necessary files or directories should be located in the same directory as the Dockerfile.

Running a basic command like `echo` in a Docker container is straightforward. However, for server programs like [Nginx](http://nginx.com/), you need to configure them properly to prevent self-daemonization.

Here's an example of a Dockerfile for Nginx:

    FROM        ubuntu:12.04
    MAINTAINER  Jon Chen "fly@burrito.sh"

    RUN         echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
    RUN         apt-get update
    RUN         apt-get install -y nginx

    RUN         echo "\ndaemon off;" >> /etc/nginx/nginx.conf
    VOLUME      /etc/nginx/sites-enabled
    VOLUME      /var/log/nginx

    EXPOSE      80
    CMD         ["nginx"]

The syntax of a Dockerfile appears as follows:

    # Comment
    INSTRUCTION arguments

Docker processes instructions in the Dockerfile sequentially, starting from the top. The initial instruction **must** be `FROM`, which determines the base image for building the new image:

    FROM ubuntu:12.04

This selects the [official Ubuntu 12.04 image](https://index.docker.io/_/ubuntu/) as the foundation. It's also a good practice to specify the image's maintainer using the MAINTAINER instruction:

    MAINTAINER Jon Chen "fly@burrito.sh"

The subsequent `RUN` instruction executes commands on the image and saves the results. Each execution generates a new commit, which serves as the basis for the subsequent instruction. For instance, the following RUN line updates `/etc/apt/sources.list` within the image:

    RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list

For programs like Nginx, it's crucial to ensure they don't run as daemons. By default, Nginx forks worker processes and exits the master process, causing Docker to prematurely stop the container. To prevent this, you need to disable daemon mode by adding the [daemon off configuration directive](http://wiki.nginx.org/CoreModule#daemon) to `/etc/nginx/nginx.conf`: configuration directive to /etc/nginx/nginx.conf:

    RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf

To expose a port from inside the container to the outside world, use the EXPOSE instruction:

    EXPOSE 80

The CMD instruction sets the default command to run when the container starts. In our case, we want to start Nginx:

    CMD ["nginx"]

By default, Docker containers don't retain data persistently. To share data between containers, you can use the V[VOLUME](http://docs.docker.io/en/latest/use/working_with_volumes/) feature:

    VOLUME /etc/nginx/sites-enabled
    VOLUME /var/log/nginx

To mount a directory from the host onto the container, you'll need to specify the host directory, corresponding container directory, and directory permissions in the command line when you run the container:

    -v=[]: Create a bind mount with: [host-dir]:[container-dir]:[rw|ro].
    If "host-dir" is missing, then docker creates a new volume.

To build the Docker image, use the following command in the same directory as the Dockerfile. You can also specify a repository and tag for your image with `-t repo/tag`:

    docker build -t bsdlp/nginx .

Then, run the following command to add `/etc/nginx/sites-enabled` and `/var/log/nginx` as volumes from the host to the container, start the container as a daemon, and expose port 80 from the container as port 80 on the host:

    docker run -d -p 80:80 -v /etc/nginx/sites-enabled:/etc/nginx/sites-enabled -v /var/log/nginx:/var/log/nginx bsdlp/nginx

## More Information

For further information on this topic, you may refer to external resources. However, please note that we cannot guarantee the accuracy or timeliness of externally hosted materials.

- [Docker's Getting Started guide](http://www.docker.io/gettingstarted/)
- [Docker on GitHub](https://github.com/dotcloud/docker)
- [Official Docker Image Index](https://index.docker.io/)
