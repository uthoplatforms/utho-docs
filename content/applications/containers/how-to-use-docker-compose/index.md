---
slug: "Mastering Docker Compose: Your Key to Container Orchestration"
description: "How to Use Docker Compose"
keywords: ["docker", "compose"]
tags: ["container","docker"]
modified: 2024-04-24
modified_by:
  name: Utho
published: 2018-01-02
title: How to Use Docker Compose
authors: ["Utho"]
---

How to Use Docker Compose title graphic

## What is Docker Compose?
If your Docker application has more than one container (like a web server and a database in separate containers), managing them with separate Dockerfiles is a hassle. Docker Compose makes it easier by letting you use a YAML file to set up these [define multi-container apps](https://docs.docker.com/compose/overview/). You can specify all the containers you need, how they should be built and linked, and where data should be stored. Once your YAML file is set up, just one command can handle building, running, and setting up all the containers.

This guide will walk you through how the `docker-compose.yml` file works and demonstrate how to use it to create different basic app setups.

Typically, when you use Docker Compose to build an application, all the containers will run on the same host. If you need to manage containers across different hosts, you'd usually use another tool like [Docker Swarm](https://docs.docker.com/engine/swarm/) or [Kubernetes](https://kubernetes.io/).

## Before You Begin

### Install Docker CE

To follow the steps in this guide, you'll need a Utho with Docker CE installed.

{{< content "installing-docker-shortguide" >}}

### Install Docker Compose

{{< content "install-docker-compose" >}}

## Basic Usage
Here, we'll examine an example Docker Compose file extracted from the official [Docker official documentation](https://docs.docker.com/compose/wordpress/#build-the-project).

Open `docker-compose.yml` in a text editor and insert the following content:

    {{< file "docker-compose.yml" yaml >}}
version: '3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
    db_data:

{{< /file >}}

2.  Save the file and run Docker Compose from the same directory:

        docker-compose up -d

This command will construct and launch the `db` and `wordpress` containers. Just like when starting a single container with `docker run`, the `-d` flag initiates the containers in detached mode.

You now have a WordPress container and a MySQL container operational on your host. Visit `192.0.8.1:8000/wordpress` in a web browser to access your freshly installed WordPress application. Additionally, you can utilize `docker ps` to delve deeper into the resultant setup.

        docker ps

4.  Stop and remove the containers:

        docker-compose down

## Compose File Syntax

A `docker-compose.yml` file is structured into four distinct sections:

|Directive    | Use
|---|---|
|version  | Specifies the Compose file syntax version. This guide will use Version 3 throughout.  |
|services   |In Docker a service is the name for a ["Container in production"](https://docs.docker.com/get-started/part3/#introduction). This section defines the containers that will be started as a part of the Docker Compose instance.   |
|networks   | This section is used to configure networking for your application. You can change the settings of the default network, connect to an external network, or define app-specific networks.     |
|volumes  | Mounts a linked path on the host machine that can be used by the container. |

The bulk of this guide will concentrate on configuring containers through the `services` section. Below are several typical directives employed to establish and customize containers:

|Directive    | Use
|---|---|
|image  | Sets the image that will be used to build the container. Using this directive assumes that the specified image already exists either on the host or on [Docker Hub](https://hub.docker.com/). |
|build   | This directive can be used instead of `image`. Specifies the location of the Dockerfile that will be used to build this container.    |
|db   | In the case of the example Dockercompose file, `db` is a variable for the container you are about to define.    |
|restart   | Tells the container to restart if the system restarts.    |
|volumes  |Mounts a linked path on the host machine that can be used by the container |
|environment      |Define environment variables to be passed in to the Docker run command.      |
|depends_on| Sets a service as a dependency for the current block-defined container |
|port   | Maps a port from the container to the host in the following manner: `host:container`   |
|links   | Link this service to any other services in the Docker Compose file by specifying their names here.  |

Numerous other configuration directives are accessible. Refer to the [Compose File reference](https://docs.docker.com/compose/compose-file for comprehensive details.

The `docker-compose.yml` example above uses the environment directive to store MySQL user passwords directly in the YAML file. However, this practice isn't advisable for sensitive information in production environments. Instead, you can store such sensitive data in a separate `.env file`, which isn't shared publicly or included in version control. This data can then be accessed from within the docker-compose.yml file using the env_file directive.

## Build an Application from Scratch

Let's create a docker-compose.yml file step by step to demonstrate how to build a multi-container application.

### Define a Simple Service:

Begin by opening a text editor and creating a new file named docker-compose.yml. Then, add the following content:

    {{< file "docker-compose.yml" yaml >}}
    version: '3'

    services:
    distro:
    image: alpine
    restart: always
    container_name: Alpine_Distro
    entrypoint: tail -f /dev/null
    {{< /file >}}

In the `services` section, each entry will generate a separate container when you run `docker-compose`. Currently, this section contains a single container based on the official Alpine distribution:

* `restart` directive: This indicates that the container should always restart, for instance, after a crash or system reboot.
* `container_name` directive: It allows you to replace the randomly generated container name with a more memorable and user-friendly name.
* By default, Docker containers stop if there's no process running. To prevent this, `tail -f` is used as an ongoing process, ensuring the container remains active. This overrides the default `entrypoint` to maintain the container's execution.

Let's manage your container:

        docker-compose up -d

Verify the status of your container:

        docker ps

You should see output similar to this:

    {{< output >}}
    CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS              PORTS               NAMES
    967013c36a27        alpine              "tail -f /dev/null"   3 seconds ago       Up 2 seconds                            Alpine_Distro
    {{< /output >}}

Bring down the container:

        docker-compose down

### Add Additional Services

Now, you can start constructing a network of containers, defining how they collaborate and communicate.

Open `docker-compose.yml` again and insert the database service below:

    {{< file "docker-compose.yml" yaml >}}
    version: '3'

    services:
    distro:
    image: alpine
    container_name: Alpine_Distro
    restart: always
    entrypoint: tail -f /dev/null

    database:
    image: postgres:latest
    container_name: postgres_db
    volumes:
      - ../dumps:/tmp/
    ports:
      - "5432:5432"
    {{< /file >}}

There are now two services defined:

    * Distro
    * Database

Distro service: This remains unchanged from before.

Database server: It contains instructions for a PostgreSQL container, along with the directives:

`volumes: - ../dumps:/tmp`: This maps the container's `/dumps` folder to our local `/tmp` folder.
ports: - `"5432:5432"`: This maps the container's ports to the local host's ports.

Check the currently running containers:

        docker ps

Running this command displays information about the containers, including their status, port mapping, names, and the last command executed on them. It's worth noting that the PostgreSQL container displays "docker-entrypoint..." under commands. This refers to the PostgreSQL [Docker Entrypoint](https://github.com/docker-library/postgres/blob/master/docker-entrypoint.sh) script, which is the final action executed when the container starts.

      {{< output >}}
      CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                    NAMES
      ecc37246f6ef        postgres:latest     "docker-entrypoint..."   About a minute ago   Up About a minute   0.0.0.0:5432->5432/tcp   postgres_db
      35dab3e712d6        alpine              "tail -f /dev/null"      About a minute ago   Up About a minute                            Alpine_Distro
      {{< /output >}}

3. Bring down both containers:

        docker-compose down

### Add an nginx Service

Add an nginx container to enable your application to serve websites.

        {{< file "docker-compose.yml" yaml >}}
        version: '3'

        services:
  distro:
        image: alpine
        container_name: Alpine_Distro
        restart: always
        entrypoint: tail -f /dev/null

  database:
        image: postgres:latest
        container_name: postgres_db
        volumes:
        - ../dumps:/tmp/
  ports:
        - "5432:5432"
  web:
        image: nginx:latest
        container_name: nginx
  volumes:
        - ./mysite.template:/etc/nginx/conf.d/mysite.template
  ports:
        - "8080:80"
  environment:
        - NGINX_HOST=example.com
        - NGINX_port=80
  links:
        - database:db
        - distro

{{< /file >}}

This docker-compose file introduces two new directives: environment and links.

- The environment directive sets runtime-level options within the container.

- Links creates a dependency network among the containers. In this setup, the nginx container relies on the other two to function. Additionally, the linked containers can be accessed via a hostname specified by the alias. For instance, pinging `db` from the `web` container will reach the database service.
Although the containers can communicate without the links directive, it serves as a backup mechanism when starting the docker-compose application.

Launch Docker Compose and inspect the container statuses:

        docker-compose up -d
        docker ps

The output should be similar to:

    {{< output >}}
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
    55d573674e49        nginx:latest        "nginx -g 'daemon ..."   3 minutes ago       Up 3 minutes        0.0.0.0:8080->80/tcp     nginx
    ad9e48b2b82a        alpine              "tail -f /dev/null"      3 minutes ago       Up 3 minutes                                 Alpine_Distro
    736cf2f2239e        postgres:latest     "docker-entrypoint..."   3 minutes ago       Up 3 minutes        0.0.0.0:5432->5432/tcp   postgres_db
    {{< /output >}}

3.  TesTest nginx by visiting your Utho's public IP address, appending port `8080` in your browser (e.g., `192.0.2.0:8080`). You'll be greeted with the default nginx landing page.

### Persistent Data Storage

Storing PostgreSQL data directly within a container is not advisable. Docker containers are designed to be transient: they are created anew when you run `docker-compose up` and terminated when you run `docker-compose down`. Furthermore, any unexpected system crash or restart would result in the loss of data stored within a container.

To address this, it's crucial to establish a persistent volume on the host that the database containers can utilize for data storage.

To implement this, add a `volumes` section to `docker-compose.yml` and modify the `database` service to reference this volume:

    {{< file "docker-compose.yml" yaml >}}
    version: '3'

services:

  distro:

         image: alpine
         container_name: Alpine_Distro
         restart: always
         entrypoint: tail -f /dev/null

  database:

         image: postgres:latest
         container_name: postgres_db

  volumes:

          - data:/var/lib/postgresql

  ports:

         - "5432:5432"

  web:

         image: nginx:latest
         container_name: nginx

  volumes:

         - ./mysite.template:/etc/nginx/conf.d/mysite.template

  ports:

         - "8080:80"

  environment:

         - NGINX_HOST=example.com
         - NGINX_port=80

  links:

         - database:db
         - distro

volumes:

       data:
       external: true
       {{< /file >}}

2.  Using `external: true` instructs Docker Compose to utilize an existing external data volume. If there's no volume named data available, launching the application will result in an error. So, let's create the volume:

        docker volume create --name=data

3.  Launch the application as you did previously:

        docker-compose up -d

## Next Steps
Docker Compose serves as a robust tool for coordinating sets of containers that collaborate seamlessly. Whether it's for an application or a development environment, Docker-compose offers a modular and adaptable environment that can be deployed across various platforms.
