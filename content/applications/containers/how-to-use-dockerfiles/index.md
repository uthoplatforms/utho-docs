---
slug: "Dockerize Your Dreams: Mastering the Art of Dockerfiles"
description: 'A guide that introduces how to use a Dockerfile and provides examples on how to use it to build and run a Docker image on your Utho'
keywords: ["docker", "container", "dockerfile","dockerfiles","docker image","docker images"]
tags: ["container","docker"]
modified: 2024-04-05
modified_by:
  name: Utho
published: 2024-04-05
title: 'How to Use a Dockerfile to Build a Docker Image.'
title_meta: 'How to Use a Dockerfile to Build a Docker Image'
authors: ["Utho"]
---
How to Use a Dockerfile

A Dockerfile is like a recipe for your Docker image. It contains step-by-step instructions to automate the installation and setup process. With [Docker image](/docs/guides/introduction-to-docker/#docker-images), you can effortlessly deploy multiple containers without the hassle of managing individual images for each virtual machine.

In this guide, we'll walk you through the basics of Dockerfiles using a simple example. You'll learn how to create and organize instructions in a Dockerfile, making the setup and updating process straightforward and intuitive.

## Before You Begin

1. Begin by following our [Getting Started](/docs/products/platform/get-started/) guide to create and manage your Utho instance. Once set up, install Docker to get started with containerization. Alternatively, you can expedite the process by deploying a pre-configured, Docker-ready Utho using the [Docker Marketplace App](https://www.utho.com/marketplace/apps/utho/docker/).

2. Prioritize the security of your Utho by referring to our comprehensive guide on [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/). Follow these steps to safeguard your Utho environment effectively.

3. This guide assumes you're comfortable navigating the Docker command-line interface (CLI). If you need a refresher or wish to delve deeper, explore Docker's official CLI [documentation](https://docs.docker.com/engine/reference/commandline/cli/). for detailed insights and commands.

## How Does a Dockerfile Work?
A Dockerfile is essentially a set of instructions that automates the process of creating a Docker image. This image serves as a blueprint for deploying Docker containers. These instructions allow you to specify software versions and dependencies, ensuring stable deployments tailored to your needs. Additionally, you can leverage a [Docker registry](https://docs.docker.com/registry/) to conveniently store and access your Docker images, whether they're public or private.

Once you've crafted your Dockerfile, you can utilize the `docker build` command to construct a Docker image according to the specified instructions. Subsequently, deploying a container based on this image becomes a breeze with commands like `docker run` or `docker create`.

Below are some commonly used instructions that you can incorporate into your Dockerfiles to facilitate image building:

**Basic Definitions**

**FROM**: This instruction defines the base image, such as ubuntu or debian, which is used to initiate the build process. It's a mandatory component in every Dockerfile.

**MAINTAINER**: Here, you specify the full name and email address of the image creator for reference.

**Variables**

**ENV**: Use this to establish environment variables that persist even after the container is deployed.

**ARG**: This instruction sets a passable build-time variable. It serves as an alternative to ENV and creates a variable that doesn't persist after deployment.

**Command Execution**

**RUN**: Execute commands, like package installation commands, on a new image layer.

**CMD**: Specify a particular command to execute within the deployed container or set default parameters for an ENTRYPOINT instruction. Only one CMD is used per Dockerfile.

**ENTRYPOINT**: Define a default application to be utilized every time a container is deployed with the image. There's typically only one ENTRYPOINT per Dockerfile.

**USER**: Set the UID (the username) to execute commands within the container.

**WORKDIR**: Establish the container path where subsequent Dockerfile commands are executed.

{{< note >}}
In this guide, `RUN`, `CMD`, and `ENTRYPOINT` can be executed in two forms: shell form, which accepts normal arguments, or exec form, which accepts arguments as a JSON array. The exec form is generally preferred because it doesn't invoke a command shell. Throughout this guide, we predominantly utilize the exec form for clarity and consistency.
{{< /note >}}

**Data Management**

**ADD**: This instruction copies files from a specified source to the filesystem of the image at the designated destination. It automatically handles tarball extraction and remote URL handling.

**COPY**: Similar to ADD, this command copies files from a specified source to the image's filesystem at the designated destination. However, it doesn't handle tarball extraction or remote URL handling.

**VOLUME**: With this instruction, you can enable access from a designated mount point in the container to a directory on the host machine.

**Networking**

**EXPOSE**: Use this instruction to expose a specific port, allowing networking between the container and the external world.
Next, we'll demonstrate the usage of these commands in an example Dockerfile.

## Creating a Dockerfile

To create the Dockerfile:

1.  Begin by accessing the command prompt, whether through SSH or Lish in the Utho Manager. Create a new directory and navigate into it:

        mkdir ~/mydockerbuild && cd ~/mydockerbuild

{{< note respectIndent=false >}}
To maintain good practice, the Docker build directory should be located outside your home directory or the server's root directory. Instead, create a dedicated directory and store all relevant files, including the Dockerfile, within it. This approach ensures organization and avoids clutter.
{{< /note >}}

2.  Create an example Dockerfile:

        touch example_dockerfile

3.  Now, create an example Dockerfile. You can use your preferred text editor (in this example, we'll use nano):

        nano example_dockerfile

4.  Copy and paste the following example content into your Dockerfile. This script generates a Debian image, defines maintainer information, and outputs "Hello, Sunshine!" when executed:

         {{< file "example_dockerfile" docker >}}
         FROM debian
         MAINTAINER Jane Doe jdoe@example.com
         CMD ["echo", "Hello, Sunshine!"]
         {{< /file >}}

5.  Save the Dockerfile.

6.  Enter `cat example_dockerfile` and ensure the text from above is included.

## Building a Docker Image from a Dockerfile

Once you've confirmed the content, proceed to build the image from the Dockerfile using the `docker build` command.

    docker build ~/mydockerbuild -f example_dockerfile -t example_image

To label your image for easier identification, assign it the tag `example_image`. This tag will simplify container deployment in the subsequent steps.

Upon successful execution, you should receive output similar to the following:

    {{< output >}}
    Sending build context to Docker daemon  4.096kB
    Step 1/3 : FROM debian
    ---> 4a7a1f401734
    Step 2/3 : MAINTAINER Jane Doe jdoe@example.com
    ---> Running in fdd81bd8b5c6
    Removing intermediate container fdd81bd8b5c6
    ---> 1253842068a3
    Step 3/3 : CMD ["echo", "Hello, Sunshine!"]
    ---> Running in d33e1bacf1af
    Removing intermediate container d33e1bacf1af
    ---> a5d95e138b97
    Successfully built a5d95e138b97
    Successfully tagged example_image:latest
    {{< /output >}}

The instructions in `example_dockerfile` are followed step by step. Now, the image labeled `example_image` is prepared for running and deploying a container.

## Deploying a Container by Running Your Docker Image

Running the image you just built to deploy a Docker container is now as easy as entering the following:

    docker run example_image

This command initiates a new container using `example_image`, executing the command specified in the CMD instruction. You'll witness output similar to what's described below:

    {{< output >}}
    Hello, Sunshine!
    {{< /output >}}

    {{< note >}}

If you attempt to execute `docker run` without the Docker image available locally, Docker will automatically fetch it from the Docker registry.
{{< /note >}}

## Further Reading

Congratulations on completing your first Dockerfile build and running your initial Docker image!

For additional examples and detailed guidance on leveraging Dockerfiles with Docker images and containers, refer to:

- Check out our comprehensive guide on [How to Use Docker Images, Containers, and Dockerfiles in Depth](/docs/guides/docker-images-containers-and-dockerfiles-in-depth) for detailed insights into Docker's functionalities.

- Delve into Docker's [Dockerfile Best Practices](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/) to discover industry-recommended approaches for optimizing Dockerfile usage.
