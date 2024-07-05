---
slug: "Mastering Docker Compose: Your Key to Seamless Container Orchestration"
authors: ["Pawan Kumar"]
description: "Unlocking the Power of Docker Compose V2: A Quick Start Guide - Dive into the basics of Docker Compose V2, from installation to practical usage"
keywords: ['Docker Compose V2', 'Install Docker Compose', 'Use Docker Compose', 'what is Docker Compose V2']
tags: ['dokcer', 'container']
published: 2024-04-24
modified_by:
  name: Utho
title: "How to Use Docker Compose V2"

---

The Docker application simplifies the development, building, running, and sharing of integrated applications. It utilizes virtualization techniques to encapsulate applications within containers. [Docker Compose](https://docs.docker.com/compose/) extends these capabilities by enabling users to deploy multiple containers instantly with just one command. This guide offers an overview of Docker Compose and details how to install and utilize it effectively.

## What is Docker?
Docker facilitates the standardization and automation of production-ready computing environments, making it a popular choice for Infrastructure as a Service (IaaS) applications, particularly in cloud computing.

Each Docker container is built upon a Docker image, which may contain multiple containers. These containers encapsulate software applications, like databases or web servers, within a virtualized environment. Everything needed to run the application—libraries, configurations, and supporting tools—is included in this package. The Docker Engine component hosts, manages, and runs these containers.

Docker containers are lightweight, efficient, and compatible with various operating systems. They operate independently and typically consist of a single application. However, deploying a comprehensive system solution often requires multiple containers. In the original Docker application, managing each container separately poses challenges for coordinating multi-container deployments.

## What is Docker Compose?
Docker Compose V2, a part of the Docker software suite, enhances and replaces the original V1 version by streamlining its functionalities. It simplifies the setup of multi-component applications using YAML configuration files, which outline the entire system architecture, including applications, configurations, and settings. Additionally, Docker Compose V2 handles storage, networking, and configuration aspects for the system.

With Docker Compose, users can efficiently manage multiple containers by simultaneously building, deploying, stopping, or deleting them. Commands also provide access to service logs and container statuses, applying to all containers collectively rather than individual instances. Docker Compose V2 is accessible either through the Docker Desktop GUI suite or as a plugin for Docker Engine/CLI.

This tool facilitates the rapid establishment of computing environments from the command line, proving valuable in development, testing, and self-managed single-Utho deployments. Some key benefits include:

- Isolated projects prevent interference between environments on the same system.
- Configuration caching reduces container recreation time by only updating changed components.
- Automatic data preservation ensures volume contents are retained across container restarts.
- Support for variables and environment portability simplifies deployment to different environments.

As support for `Docker Compose` V1 ends in June 2023, users are encouraged to transition to `Compose` V2 promptly. Compose V2 integrates the compose command directly into the Docker CLI and Engine, serving as a drop-in replacement for docker-compose V1 commands. Although some redundant commands have been removed and new ones introduced, the transition is seamless. While Compose V1 was Python-based, Compose V2 is written in Go.

### What is the Docker Compose Specification?

The [Docker Compose file specification](https://docs.docker.com/compose/compose-file/), detailed in the documentation, outlines how to structure a Docker Compose V2 configuration file. This file, written in YAML, provides directives for building and running a collection of Docker containers. While a basic Compose file must include at least one service—mapping to a Docker container—most configurations involve multiple services. Additionally, the Compose file can encompass definitions for storage systems, configurations, networking components, and sensitive system secrets.


Note: The format of Docker Compose V2 files differs from that of Compose V1. In V1, file formats V2 and V3 were allowed, requiring configurations to specify the file format. However, these file formats were unrelated to the Docker Compose version. With the standardization of file formats in Docker Compose V2, this distinction is no longer necessary.

The Docker Compose architecture supports hosting multiple projects, each housed in its own directory and equipped with a `docker-compose.yml` file. While this file can contain several components, only the services section is mandatory. Other sections—such as `volumes`, `networks`, `configs`, and `secrets`—are optional. Here's a breakdown of the main components found in a `docker-compose.yml` file:

- **Services**: This crucial section defines computing resources, with each entry representing an independently scalable application. Services reference Docker images and may correspond to one or more containers, such as MySQL, NGINX, Redis, or WordPress.

- **Volumes**: Used to designate storage paths for data, volumes provide shared storage accessible to multiple services, augmenting service-defined storage.

- **Networks**: Facilitating inter-service communication or external network connections, networks enable services to interact. Docker Compose typically generates a default network, but this section can specify additional networks or connections to external ones.

-**Configs**: Reserved for storing application configurations persistently.

- **Secrets**: For safeguarding sensitive configuration details, secrets are securely managed using keys or certificates.

Each component is configured using a set of parameters. For instance, `services` must specify an `image`, while optional parameters may include ports, commands, or environmental variables. For comprehensive guidance on constructing a docker-compose.yml file, refer to the [Docker Compose specification](https://docs.docker.com/compose/compose-file/)..

Certain applications may necessitate a *Dockerfile*, which outlines platform-agnostic instructions for building an application. However, many applications utilize pre-built Docker images and do not require a Dockerfile. For further details on leveraging a Dockerfile in conjunction with [Docker Compose documentation](https://docs.docker.com/compose/gettingstarted/#step-2-create-a-dockerfile).

## Before You Begin
If you haven't already, sign up for a Utho account and create a Compute Instance. Check out our [Getting Started with Utho](/docs/products/platform/get-started/) and [Creating a Compute Instance](/docs/products/compute/compute-instances/guides/create/) guides for detailed instructions.

Follow our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide to ensure your system is up to date. You might also want to adjust your timezone, configure your hostname, create a restricted user account, and enhance SSH security.

Note: This guide assumes you're using non-root users. Commands requiring elevated privileges are prefixed with `sudo`. If you're unfamiliar with the sudo command, refer to the  [Linux Users and Groups](/docs/guides/linux-users-and-groups/) guide.
{{< /note >}}

## How to Install Docker Compose and Docker Engine
There are two primary methods for installing Docker Compose. Typically, it's installed as a plugin, tightly integrated with Docker Engine and Docker CLI. Alternatively, you can opt for Docker Desktop, a comprehensive GUI interface that includes Docker Compose. However, this guide will focus on installing the Docker Compose plugin. For details on Docker Desktop, refer to the [Docker Desktop documentation](https://docs.docker.com/desktop/install/linux-install/).

This guide focuses on how to install the Docker Compose plug-in. Docker Engine must be installed alongside Docker Compose before the plug-in can be used. This guide is geared toward Ubuntu 22.04 LTS users but is generally applicable to all Linux distributions. Exact instructions for other supported Platforms can be found on the [Docker Engine installation page](https://docs.docker.com/engine/install/).

This guide will walk you through installing the Docker Compose plugin, assuming you already have Docker Engine installed. While it's tailored for Ubuntu 22.04 LTS users, the steps are generally applicable to all Linux distributions. For specific instructions on other supported platforms, visit the [Docker Engine installation page](https://docs.docker.com/engine/install/).

To install both Docker Engine and Docker Compose, follow these steps:

First, ensure your system is up to date by running the following command. Reboot the system if prompted.

    ```command
    sudo apt-get update -y && sudo apt-get upgrade -y
    ```

Remove any outdated versions of Docker or associated components. These versions may not be compatible with Docker Compose V2.

Note: If an earlier version of Docker was previously installed on the system, consult the instructions to [Uninstall Docker](https://docs.docker.com/engine/install/ubuntu/#uninstall-docker-engine). It is important to remove any old containers and volumes to avoid future conflicts.

    ```command
    sudo apt-get remove docker docker-engine docker.io containerd runc
    ```

To install Docker Engine, you'll need to install some additional components using `apt`. Some of these packages may already be present on your system.

    ```command
    sudo apt-get install ca-certificates curl gnupg lsb-release
    ```

First, add the official GNU Privacy Guard (GPG) Key to verify the installation.

    ```command
    sudo mkdir -m 0755 -p /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    ```

Add the Docker repository to the list of `apt` packages.

    ```command
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```

Refresh the list of `apt` packages.

    ```command
    sudo apt-get update
    ```

Next, proceed with installing the latest version of Docker Engine, Docker CLI, `containerd`, and other related components.

    ```command
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```

Once installation is complete, verify that Docker is functioning correctly by running the `hello-world` container. Docker will automatically download the necessary container and execute it.

    ```command
    sudo docker run hello-world
    ```

If everything is set up correctly, Docker will display a confirmation message.

    ```output
    Hello from Docker!
    This message shows that your installation appears to be working correctly.
    ```

To ensure the Docker Compose plugin is functioning properly, run the command `docker compose version` and verify the release number. If the release starts with v2, Docker Compose `V2` is successfully installed.

    ```command
    docker compose version
    ```

    ```output
    Docker Compose version v2.16.0
    ```

## How to Create a Docker Compose YAML File

Docker Compose relies on the `docker-compose.yml` file to identify and manage the containers to be built and run. Every project must include a docker-compose.yml file within its directory. Running Docker Compose without this file is not possible. For complex systems, multiple `docker-compose` files can be merged, with items overridden or appended based on the file hierarchy. However, for most simple applications, a single docker-compose file is sufficient.

Here's an example of how to create a docker-compose.yml file for a basic WordPress setup. This file encompasses WordPress and database services, as well as shared storage directories. Follow these steps to generate the Docker Compose YAML file.


Note: This example is sourced from the [Docker Sample Projects](https://docs.docker.com/compose/samples-for-compose/). You might find it beneficial to utilize one of these sample files as a foundation for your project.

Begin by creating a directory for your new project and navigate into it. In this instance, let's name the directory `wordpress`.

    ```command
    mkdir wordpress
    cd wordpress
    ```

Create a file named `docker-compose.yml`.

    ```command
    vi docker-compose.yml
    ```

First, add the `services` section. Add a service named `mariadb`. This service defines the database container.

    ```file {title="~/wordpress/docker-compose.yml"}
    services:
      mariadb:
    ```

Specify the parameters for the `mariadb` service. These parameters include:

- The `command` parameter, which provides command line arguments for the application.
- The `image` parameter, indicating which image Docker Compose should pull and use to build the container. Ensure that the image exists either locally or on Docker Hub.
- The `volumes` attribute, specifying a directory where the application can store data.
- The `environment` attribute, containing definitions for the database name, user, and user password.
- The `expose` directive, listing the ports that the database can utilize.
- Setting `restart` to `always`, instructing Docker to start the container upon system activation.

Add the following content to the `mariadb` service. Remember to replace password with a secure password for `MYSQL_PASSWORD` and the root password for `MYSQL_ROOT_PASSWORD`. It's acceptable to set the database name and user to `wordpress`.

    ```file {title="~/wordpress/docker-compose.yml"}
    services:
      mariadb:
              image: mariadb:10.11.2-jammy
              command: '--default-authentication-plugin=mysql_native_password'
        volumes:
          - db_data:/var/lib/mysql
        restart: always
        environment:
          - MYSQL_ROOT_PASSWORD=password
          - MYSQL_DATABASE=wordpress
          - MYSQL_USER=wordpress
          - MYSQL_PASSWORD=password
        expose:
          - 3306
          - 33060
    ...
    ```

Add the `wordpress` service. The `WORDPRESS_DB_USER`, `WORDPRESS_DB_PASSWORD`, and `WORDPRESS_DB_NAME` fields must match the corresponding parameters from the `mariadb` section. The `ports` attribute indicates the ports the application should use.

    ```file {title="~/wordpress/docker-compose.yml"}
    services:
    ...
      wordpress:
        image: wordpress:latest
        volumes:
          - wp_data:/var/www/html
        ports:
          - 8000:80
        restart: always
        environment:
          - WORDPRESS_DB_HOST=mariadb
          - WORDPRESS_DB_USER=wordpress
          - WORDPRESS_DB_PASSWORD=password
          - WORDPRESS_DB_NAME=wordpress
    ...
    ```

Finally, include the `volumes` section, adding directory volumes for both the `mariadb` and `wordpress` services.

    ```file {title="~/wordpress/docker-compose.yml"}
    ...
    volumes:
      db_data:
      wp_data:
    ```

The entire file should resemble the following example.

    ```file {title="~/wordpress/docker-compose.yml"}
    services:
      mariadb:
        image: mariadb:10.11.2-jammy
        command: '--default-authentication-plugin=mysql_native_password'
        volumes:
          - db_data:/var/lib/mysql
        restart: always
        environment:
          - MYSQL_ROOT_PASSWORD=password
          - MYSQL_DATABASE=wordpress
          - MYSQL_USER=wordpress
          - MYSQL_PASSWORD=password
        expose:
          - 3306
          - 33060
      wordpress:
        image: wordpress:latest
        volumes:
          - wp_data:/var/www/html
        ports:
          - 8000:80
        restart: always
        environment:
          - WORDPRESS_DB_HOST=mariadb
          - WORDPRESS_DB_USER=wordpress
          - WORDPRESS_DB_PASSWORD=password
          - WORDPRESS_DB_NAME=wordpress
    volumes:
      db_data:
      wp_data:
    ```

## How to Build and Run a Docker Compose Project

Using Docker Compose to launch a project is straightforward. Follow these steps to launch the Docker Compose WordPress project:

Navigate to the main project directory containing the `docker-compose.yml` file.

    ```command
    cd wordpress
    ```

Use the `docker compose up` command to launch the application. To run the containers in the background, add the `-d` option. This allows you to continue using the current console session without interruption while the containers are started. It may take a minute or two for the containers to download and start.

    ```command
    sudo docker compose up -d
    ```

    ```output
    [+] Running 31/3
    ⠿ wordpress Pulled                                                       44.6s
    ⠿ mariadb Pulled                                                         35.5s

    [+] Running 5/5
    ⠿ Network wordpress_default        Created                                0.3s
    ⠿ Volume "wordpress_wp_data"       Create...                              0.0s
    ⠿ Volume "wordpress_db_data"       Create...                              0.0s
    ⠿ Container wordpress-mariadb-1    Sta...                                 3.0s
    ⠿ Container wordpress-wordpress-1  S...                                   3.0s
    ```

To verify that the Docker project is running correctly, test the application by following these steps:

Navigate to the IP address of your server. Append port `:8000` to the address. Alternatively, you can use the following address format: `http://ip_address:8000/wp-admin/install.php`.
You should see the WordPress installation page. Select your preferred language and proceed with the installation.
When prompted, provide the necessary user credentials and database details.

For more detailed instructions, refer to the Utho Guide on [How to Install WordPress on Ubuntu 22.04](/docs/guides/how-to-install-wordpress-ubuntu-22-04).

    ![The WordPress Installation Page](Wordpress-Install.png)

To view a list of all Docker containers, execute the command `docker compose images`.

    ```command
    sudo docker compose images
    ```

    ```output
    CONTAINER               REPOSITORY          TAG                 IMAGE ID            SIZE
    wordpress-mariadb-1     mariadb             10.11.2-jammy       6e11fcfc66ad        401MB
    wordpress-wordpress-1   wordpress           latest              8fec96b2307f        615MB
    ```

The `docker compose ps` command displays a list of all active processes linked to the containers.

    ```command
    sudo docker compose ps
    ```


    ```output
     NAME                    IMAGE                   COMMAND                  SERVICE             CREATED             STATUS              PORTS
     wordpress-mariadb-1     mariadb:10.11.2-jammy   "docker-entrypoint.s…"   mariadb             2 minutes ago       Up 2 minutes        3306/tcp, 33060/tcp
     wordpress-wordpress-1   wordpress:latest        "docker-entrypoint.s…"   wordpress           2 minutes ago       Up 2 minutes        0.0.0.0:8000->80/tcp, :::8000->80/tcp

    ```

To shut down the application and remove the containers, use the docker compose down command. This command leaves the database contents intact. However, if you want to remove the containers, the database, and all associated data, you can use `docker compose down --volumes`.

    ```command
    sudo docker compose down
    ```

## Conclusion
Docker Compose V2 simplifies the process of building, running, and deploying multi-container Docker solutions. It relies on a YAML configuration file to define the services, networks, and volumes of the system. Docker Compose V2, along with the docker compose command, replaces Compose V1 and the docker-compose directive.

To install and utilize Docker Compose V2, you'll need to add the Docker repository to your local package manager. Afterward, download and install Docker Engine, Docker CLI, and the Compose plugin. Construct the `docker-compose.yml` file, specifying the services/containers to use, along with any required storage space, networking components, and configurations. Finally, utilize the `docker compose up` command to build and run the containers. For further details on Docker Compose V2, refer to the [Docker Compose documentation](https://docs.docker.com/compose/).
