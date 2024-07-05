---
slug: "MongoDB Magic: Setting Up MongoDB with Docker"
description: 'A comprehensive guide with practical examples demonstrating the installation of MongoDB on a Docker container using the MongoDB Docker Hub image'
keywords: ["docker", "mongodb", "mongodb container", "docker mongodb container", "install mongodb docker", "configure mongodb docker"]
tags: ["container","docker","mongodb"]
published: 2024-04-08
modified_by:
  name: Utho
title: "Set Up MongoDB on Docker"
title_meta: "How to Set Up MongoDB on Docker"
authors: ["Utho"]
---

MongoDB is a widely used open-source NoSQL database known for its flexibility and scalability. It uses JSON-like documents and schemas, making it ideal for rapid development. Many developers, especially those using agile methodologies, prefer MongoDB for its ability to scale out easily.

Using MongoDB with Docker is a smart choice, especially for those following a *continuous integration and development (CI/CD)* workflow.

## Before You Begin

To follow along with the examples in this guide, you'll need to have a Utho set up with Docker installed. You can do this either by using the Docker Marketplace App or by manually installing Docker. Instructions for both methods are provided below.

This guide assumes you're comfortable using the command-line interface (CLI) on a Unix-like system.

### Set up a Utho with Docker

#### Docker Marketplace App

You can easily set up a secure and updated Utho with the Docker Marketplace App. Follow our guide on [How to Deploy Docker with Marketplace Apps](/docs/products/tools/marketplace/guides/docker/) for detailed instructions. For this guide, we recommend deploying the Docker Marketplace App with the following [Docker Options](/docs/products/tools/marketplace/guides/docker/#docker-options):

- Create a limited sudo user for the Utho
- Set a password for the limited sudo user
- Specify the limited sudo user's SSH Public Key for accessing the Utho
- Choose whether to disable root access over SSH (yes)

#### Manual Installation

1. First, go through our Getting Started guide and ensure your Utho is up to date.

1. Next, follow the steps outlined in our [Securing Your Server](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide. This includes creating a standard user account, securing SSH access, and disabling unnecessary network services.

1. Install Docker on your Utho by referring to our guide on [How to Install and Use Docker on Ubuntu and Debian](/docs/guides/installing-and-using-docker-on-ubuntu-and-debian/).

### Verify Docker Installation

After verifying Docker installation on your Utho
    docker --version

You can expect an output similar to the following:

    {{< output >}}
    Docker version 20.10.8, build 3967b7d
    {{< /output >}}

    {{< content "limited-user-note-shortguide" >}}

## Setting Up a MongoDB Docker Container

Here's how to set up MongoDB using Docker:

1.  Pull the MongoDB image from Docker Hub:

        sudo docker pull mongo

You'll see output like this as the image download        

    {{< output >}}
    Using default tag: latest
    latest: Pulling from library/mongo
    16ec32c2132b: Pull complete
    6335cf672677: Pull complete
    cbc70ccc8ebe: Pull complete
    0d1a3c6bd417: Pull complete
    960f3b9b27d3: Pull complete
    aff995a136b4: Pull complete
    4249be7550a8: Pull complete
    cc105ff5aa3c: Pull complete
    82819807d07a: Pull complete
    81447d2c233f: Pull complete
    Digest: sha256:54d24682d00278f64bf21ff62b7ee62b59dae50f65139831a884b345922b0f8a
    Status: Downloaded newer image for mongo:latest
    docker.io/library/mongo:latest
    {{< /output >}}

Note: This command retrieves the latest version by default, as indicated in the first line of the output. To fetch a specific version, append the tag for that version to the command. For instance, to install MongoDB 4.4.6, type `docker pull mongo:4.4.6`.

2. Verify that the image has been successfully installed:

        sudo docker images

The output should resemble the following:

    {{< output >}}
    REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
    mongo        latest    07630e791de3   2 weeks ago   449MB
    {{< /output >}}

3.  Create a container using the `mongo` image in detached mode to keep it interactive on your system:

        sudo docker run --name mongo_example -d mongo

4.  Ensure the container is running:

        sudo docker ps

Verify that the container is running:

        {{< output >}}
        CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS       NAMES
        1f88d00b9e78   mongo     "docker-entrypoint.s…"   4 seconds ago   Up 4 seconds   27017/tcp   mongo_example
        {{< /output >}}

Now MongoDB is running as a Docker Container.

## Logging into MongoDB on the Container

1. To access the container's command prompt, type the following:

        sudo docker exec -it mongo_example bash

2.  Once you're at the container's command prompt, launch the `mongosh` shell by entering:

        mongosh

From the `mongosh` shell, you can perform queries and operations directly with your database.

Note: Please note that the legacy `mongo` shell has been deprecated in MongoDB v5.0 but is still available as an alternative to `mongosh`.

## Configuring MongoDB in a Docker Container

For detailed MongoDB configuration, refer to the [MongoDB manual](https://docs.mongodb.com/manual/). Typically, `mongod` configurations are set using `mongod` flags, which can be passed through the docker run command.

For instance, to disable the scripting engine, append the flag to the command as shown below:

        sudo docker run --name mongo_example2 -d mongo --noscripting

Similarly, to disable the scripting engine and enable IPv6, use the following command:

        sudo docker run --name mongo_example3 -d mongo --noscripting --ipv6

## How to Persist MongoDB Data in a Docker Container

Since MongoDB is running in a Docker container, its data won't persist once the container is stopped. By default, MongoDB saves data in the /data/db directory within the container. If you want the MongoDB data to persist even after the container is stopped, you need to create and attach a Docker volume or mount a directory from your host system.

### Adding a Docker Volume to a MongoDB Container

Creating and adding a volume for the container to use is easy if you're familiar with Docker.

1.  Create a Docker volume for the data using the following command:

        sudo docker volume create mongo_volume

2.  Then, create a `docker run` command to attach the volume to the container and map it to the `/data/db` directory within the container:

        sudo docker run -it -v mongo_volume:/data/db --name mongo_example4 -d mongo

### Mounting a Host Directory in a MongoDB Docker Container

If you want your MongoDB data to persist and be accessible outside of Docker, you can utilize a directory on your host system.

Here's how to mount a host system directory:

1.  **Create a Directory**: If you don't already have a directory you want to use, create one on your system at the root level by running:

        sudo mkdir -p /mongo_data_directory

2.  **Execute Docker Run Command**: Use the docker run command to mount the directory and map it to `/data/db`. Replace `/path/to/your/` directory with the path to the directory you created:

        sudo docker run -it -v /mongo_data_directory:/data/db --name mongo_example5 -d mongo

## Further Reading

Learning how to use MongoDB on Docker is crucial for CI/CD workflows and rapid iterative development. For more MongoDB-related information on Docker, explore Docker's [MongoDB information at the Docker Hub](https://hub.docker.com/_/mongo).

To delve into `mongod` options to be passed in `docker run`, refer to the `mongod` [MongoDB Package Components](https://docs.mongodb.com/manual/reference/program/mongod/)  section of the MongoDB Manual.

Additionally, if you plan on upgrading to MongoDB Enterprise, check out [Install MongoDB Enterprise with Docker](https://docs.mongodb.com/manual/tutorial/install-mongodb-enterprise-with-docker/) in the MongoDB Manual for `mongod` installation instructions.
