---
slug: "Dive Deep into Docker: Mastering Images, Containers, and Dockerfiles"
description: 'Discover the ins and outs of crafting Docker Images and Containers with Dockerfiles through this comprehensive guide. Explore detailed examples on Utho to enhance your understanding'
keywords: ["docker", "container", "docker image", "docker images", "docker container", "docker containers"]
tags: ["container","docker"]
modified: 2024-04-05
modified_by:
  name: Utho
published: 2024-04-05
title: 'How to Use Docker Images, Containers, and Dockerfiles in Depth'
authors: ["Utho"]
---
[Docker images](/docs/guides/introduction-to-docker/#docker-images) simplify the deployment of multiple containers by eliminating the need to manage identical images across various virtual machines. Using a Dockerfile, you can automate the installation and setup of an image along with its dependencies. [Dockerfile](/docs/guides/how-to-use-dockerfiles) is essentially a text file containing commands executed in sequence to automate the configuration of a Docker image.

This article builds upon our guide on [How to Use a Dockerfile to Build a Docker Image](/docs/guides/how-to-use-dockerfiles) by delving into the comprehensive utilization of Docker images, containers, and Docker files.

## Before You Begin

1. Start by familiarizing yourself with our [Getting Started](/docs/products/platform/get-started/) guide. Follow the steps to create and update a Utho, then proceed to install Docker. Alternatively, you can swiftly deploy an updated Utho with Docker pre-installed using the [Docker Marketplace App](https://www.utho.com/marketplace/apps/utho/docker/).

2. Ensure the security of your Utho by following our guide on [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/)

3. This guide assumes you're comfortable using the Docker command-line interface (CLI). If you need more information on the Docker CLI, refer to their [documentation](https://docs.docker.com/engine/reference/commandline/cli/).

4. Review our guide on Dockerfiles to grasp the fundamentals before diving deeper into this tutorial.

## Craft Your Dockerfile to Build the Docker Image
To initiate Docker builds, you need a functional Dockerfile. In this section, we'll construct a Dockerfile to establish an Ubuntu image incorporating Apache as a web server, utilizing the standard HTTP port 80.

1. Begin by opening the command prompt (accessible via SSH or Lish in the Utho Manager) and create a new directory, then switch to it:

        mkdir ~/mydockerbuild && cd ~/mydockerbuild

2. Crafting an Example Dockerfile for Your Apache Application

        touch apache_dockerfile

3. Launch the Dockerfile in your preferred text editor (in this instance, we'll use nano):

        nano apache_dockerfile

4. Copy and paste the following example into your Dockerfile. This Dockerfile creates an updated Ubuntu image, sets the maintainer information, installs Apache, opens container port 80, and ultimately launches an Apache server upon execution:

       {{< file "apache_dockerfile" docker >}}
       FROM ubuntu
       MAINTAINER John Doe jdoe@example.com
       ARG DEBIAN_FRONTEND=noninteractive
       RUN apt-get update
       RUN apt-get upgrade -y
       RUN apt-get install apache2 -y
       RUN apt-get clean
       EXPOSE 80
       CMD ["apache2ctl","-D","FOREGROUND"]
       {{< /file >}}

{{< note respectIndent=false >}}
The `ARG DEBIAN_FRONTEND=noninteractive` command guarantees that the subsequent `RUN apt-get` commands execute smoothly without needing extra user input during image builds. While this instruction could be expressed with `ENV` instead of ARG to ensure the environment variable persists in containers deployed with the image, it's advisable to use `ARG` in this scenario. This preference arises because non-interactivity might not be anticipated when operating within these containers.
{{< /note >}}

5.  Save and exit the file.

## Build a Docker Image from the Dockerfile

1. Build an image named `apache_image` from the Dockerfile using the `docker build` command:

        docker build ~/mydockerbuild -f apache_dockerfile -t apache_image

2. After the build completes and you're back at the command prompt, use the following command to list all available images on your system:

         docker images

The output should resemble the following (note that the "ubuntu" repository is also listed due to the "FROM ubuntu" line in the Dockerfile):

    {{< output >}}
    REPOSITORY     TAG       IMAGE ID       CREATED         SIZE
    apache_image   latest    7e5c14739da5   7 seconds ago   215MB
    ubuntu         latest    7e0aa2d69a15   6 weeks ago     72.7MB
    {{< /output >}}

{{< note respectIndent=false >}}

By default, built images are tagged as "latest". If you wish to change the tag, for example, to "development", use the following command format:

    docker build ~/mydockerbuild -f apache_dockerfile -t apache_image:development
{{< /note >}}

## Launching Docker Images as Containers
When you use the docker run command, you initiate a Docker container linked to your terminal session. This is also known as running a process in the foreground. If your primary process runs in the foreground and is linked to a terminal session, your container terminates when you close the terminal. To keep your container running even after you close the terminal, you can run it in detached mode, which operates your container in the background.

To launch the Docker image as a container in detached mode:

1.  Use the following command to create a container named `apache` from your image. Include the `-d` argument to ensure the container operates in the background:

        docker run --name apache -d apache_image

2.  After returning to the command prompt, execute the following command to view your active containers and verify that `apache` is running in the background:

        docker ps

        {{< output >}}
        CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS     NAMES
        1d5e1da50a86   apache_image   "apache2ctl -D FOREG…"   3 minutes ago   Up 3 minutes   80/tcp    apache
        {{</ output>}}

3. Now you can carry out your development tasks with the Apache server while retaining access to the command line. However, your container isn't publicly accessible as it lacks additional port configurations. In the next section, you'll rebuild the container with port configurations to enable access to the web server. For now, let's stop the container:

        docker stop apache

    {{< note respectIndent=false >}}

You can replace apache in the command above with the container ID.
{{< /note >}}

4.  Run `docker ps` again to confirm that your `apache` container is no longer running.

5.  Now that the container has been stopped, you can proceed to remove it:

        docker rm apache

{{< note type="alert" >}}
Deleting a container in this manner removes all data within the container. If you've made modifications that you want to preserve in a new container, consider using `docker commit` to create a new image incorporating your changes.

    docker commit apache apache_image_update

Next, you can proceed to deploy a new container using the updated `apache_image_update` image in the following section.
{{< /note >}}

### Configure your Docker Container's Ports

You can utilize the `run` command's options to adjust various aspects of your container. When your container operates on a remote host and serves its application, you can configure its ports to make the application accessible to users.

For instance, you can set up your apache container to utilize host port `8080` and container port `80`, as demonstrated in the example command below. Take note of the `-d` option utilized in the command to run the container as a detached process.

    docker run --name apache -p 8080:80 -d apache_image

Here's the general syntax for this command:

    docker run -–name <container name> -p <network port>:<container port> -d <container image label or ID>

Each parameter is explained in the following list:

- `<container name>`: Name of the Docker container
- `<host port>`: Port on the host machine mapped to the container's open port
- `<container port>`: Port where the Docker container is listening
- `<container image name>`: Name of the Docker image used for your deployment

Now, open your web browser and navigate to your Utho's IP address at port 8080 by entering `http://<your Utho's IP address>:8080`. You should see the "Apache2 Ubuntu Default Page" served from your Docker container.

{{< note type="alert" >}}
When deploying containers with port configurations, Docker might automatically generate host firewall rules to enable public access to those containers. This action could potentially overwrite or clash with the host firewall rules you've set up on your Utho.
{{< /note >}}

## Further Reading
This guide, along with [How to Use a Dockerfile to Build a Docker Image](/docs/guides/how-to-use-dockerfiles), explained the basics of using Dockerfiles to create images and containers. However, they only scratch the surface of Docker's capabilities. For further insights:

- Check out the official [Dockerfile Best Practices](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/) documentation for advanced Dockerfile usage.

- Despite its name, Docker's [Get Started](https://docs.docker.com/get-started/) guide offers an extensive tutorial that delves into more advanced topics. It leads to even more detailed guides, such as deploying applications to the cloud and setting up CI/CD (continuous integration and deployment).
