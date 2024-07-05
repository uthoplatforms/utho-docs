---
slug: Communication Between Docker Containers
description: 'This guide demonstrates linking Docker containers using a Node.js application and PostgreSQL'
og_description: "Learn to link Docker containers using a Node.js application and PostgreSQL through a simple 'Hello World' application."
keywords: ['docker','containers','database','container communication']
tags: ["postgresql","database","container","docker"]
published: 2024-06-14
modified: 2024-06-14
modified_by:
  name: Pawan Kumar
title: "Connect Docker Containers"
title_meta: "How to Connect Docker Containers"
authors: ["Pawan Kumar"]
---

When utilizing Docker to containerize applications, it's standard to separate each component into its own container. For instance, a typical website might have separate containers for the web server, application, and database.

Setting up communication between these containers and the host machine can be complex. This guide will illustrate the fundamentals of Docker container communication using a straightforward example. The application will feature a Node.js app that interacts with a PostgreSQL database.

## Before You Begin

### Install Docker CE

To proceed with the steps in this guide, you'll need a Utho with Docker CE installed.

{{< content "installing-docker-shortguide" >}}

## Example Node.js Application

Throughout this guide, we'll use a basic Node.js application. It will retrieve "Hello world" from a PostgreSQL database and display it in the console. In this section, you'll build and test the app on your Utho without employing containers.

### Install and Configure PostgreSQL

1.  Update your system:

        sudo apt update && sudo apt upgrade

2.  Install PostGreSQL:

        sudo apt install postgresql postgresql-contrib

3.  Change the password for the postgres user:

        sudo passwd postgres

4.  Set a password for the postgres database user:

        su - postgres
        psql -d template1 -c "ALTER USER postgres WITH PASSWORD 'newpassword';"

5.  Create a database for the example app and connect to it:

        createdb nodejs
        psql nodejs

6.  Add "Hello world" to the database:

        nodejs=# CREATE TABLE hello (message varchar);
        nodejs=# INSERT INTO hello VALUES ('Hello world');
        nodejs=# \q

7.  Create a dump of the database for future use:

        pg_dumpall > backup.sql

8.  Switch from the postgres Linux user:

        exit

9.  Copy the data dump to your home directory:

        sudo cp /var/lib/postgresql/backup.sql ~/.

10. To enable connections from containers (which use IP addresses other than localhost), modify the PostgreSQL config file. Open /etc/postgresql/9.5/main/postgresql.conf in a text editor. Uncomment the listen_addresses line and set it to '*':

    {{< file "/etc/postgresql/9.5/main/postgresql.conf" >}}
#------------------------------------------------------------------------------
# CONNECTIONS AND AUTHENTICATION
#------------------------------------------------------------------------------

# - Connection Settings -

listen_addresses = '*'                  # what IP address(es) to listen on;
{{< /file >}}

11. Enable and start the `postgresql` service:

        sudo systemctl enable postgresql
        sudo systemctl start postgresql

### Create a Hello World App

1.  Install Node and NPM:

        curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
        sudo apt-get install nodejs

2.  Navigate to the home directory and create a new directory:

        cd
        mkdir app && cd app

3.  Create app.js using a text editor and add the following content:

         {{< file "app.js" >}}
        const { Client } = require('pg')

         const client = new Client({
         user: 'postgres',
         host: 'localhost',
         database: 'nodejs',
         password: 'newpassword',
         port: 5432
         })

        client.connect()

        client.query('SELECT * FROM hello', (err, res) => {
        console.log(res.rows[0].message)
         client.end()
         })
         {{< /file >}}

This app uses the pg NPM module (node-postgres) to connect to the database created earlier. It queries the 'hello' table to fetch the "Hello world" message and logs the response to the console. Be sure to replace 'newpassword' with the postgres database user password you set previously.

Note: The pg module can also use environment variables to configure the client connection. This is the recommended approach for production apps. You can learn more about using environment variables in the node-postgres documentation.

5.  Install the `pg` module:

        npm install pg

6.  Test the app:

        node app.js

If the database is configured correctly, "Hello world" will appear on the console.

## Connect Container to Docker Host

This section demonstrates a scenario where the Node.js app runs inside a Docker container and connects to a database running on the Docker host.

### Set Up Docker Container

1.  Go back to your home directory:

        cd

2. Create a Dockerfile to run the Node.js application:

           {{< file "Dockerfile" >}}

FROM debian

       RUN apt update -y && apt install -y gnupg curl
       RUN curl -sL https://deb.nodesource.com/setup_8.x | bash - && apt install -y nodejs
       COPY app/ /home/

        ENTRYPOINT tail -F /dev/null
        {{< /file >}}

3. The image built from this Dockerfile will copy the app/ directory to the new image. Edit app.js to enable the app to connect to the database host instead of localhost:

            {{< file "app/app.js" >}}
            const client = new Client({
            user: 'postgres',
            host: 'database',
            database: 'nodejs',
            password: 'newpassword',
            port: 5432
            })
            {{< /file >}}

4.  Build an image using the Dockerfile:

        docker build -t node_image .

### Connect Container to Database

Docker automatically sets up a default bridge network, accessed via the docker0 network interface. Use ifconfig or ip to view this interface:

        ifconfig docker0

The output will look similar to this:

       {{< output >}}

     docker0   Link encap:Ethernet  HWaddr 02:42:1e:e8:39:54
     inet addr:172.17.0.1  Bcast:0.0.0.0  Mask:255.255.0.0
     inet6 addr: fe80::42:1eff:fee8:3954/64 Scope:Link
     UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
     RX packets:3848 errors:0 dropped:0 overruns:0 frame:0
     TX packets:5084 errors:0 dropped:0 overruns:0 carrier:0
     collisions:0 txqueuelen:0
     RX bytes:246416 (246.4 KB)  TX bytes:94809688 (94.8 MB)

      {{< /output >}}

The internal IP address of the Docker host (your Utho) is 172.16.0.1.

2.  To enable PostgreSQL to accept connections from the Docker interface, open the file /etc/postgresql/9.5/main/pg_hba.conf using a text editor, and insert this line:

        {{< file "/etc/postgresql/9.5/main/pg_hba.conf" >}}
        host    all             postgres        172.16.0.0/16           password
        {{< /file >}}

Given that the IP address of the Docker host is 172.17.0.1, all containers on the host will possess IP addresses within the 172.17.0.0/16 range.

3.  Restart the database:

        sudo systemctl restart postgresql

4.  Start the container:

        docker run -d --add-host=database:172.17.0.1 --name node_container node_image

Use the --add-host option to create a database host that resolves to the Docker host's IP address. This dynamic declaration of the database host during runtime, instead of embedding the IP address directly into the application, enhances the container's portability.

Test the connection to the database host from within the container using ping.

        docker exec -it node_container ping database

Each Docker container is assigned a unique IP address from the 172.17.0.0/16 block. Use the ip command to locate the IP address of this container:

        docker exec -it node_container ip addr show eth0

You can verify this connection by pinging this address from the Docker host.

7.  Run the app:

        docker exec -it node_container node home/app.js

If the configuration was successful, the program should display the "Hello world" console output as it did previously.

## Connect Two Containers

In this scenario, the application and database will run in separate containers. Utilize the official PostgreSQL image from Docker Hub and import the previously created SQL dump.

Note: Storing production database data inside a Docker container is not recommended. Containers are intended to be ephemeral; if a container crashes or restarts unexpectedly, all data stored within the database will be lost.

1.  Stop and delete the Node.js container.

        docker stop node_container
        docker rm node_container

2.  Pull the `postgres` image:

        docker pull postgres

3.  Ensure that your backup.sql file is located in your current working directory, then execute the postgres image.

        docker run -d -v `pwd`:/backup/ --name pg_container postgres

The -v option mounts your current working directory to the /backup/ directory within the new container.

4.  The new container will automatically launch the PostgreSQL database and set up the PostgreSQL user. Access the container and import the SQL dump:

        docker exec -it pg_container bash
        cd backup
        psql -U postgres -f backup.sql postgres
        exit

5.  Restart the Node.js container. This time, use the --link option instead of --add-host to establish a connection between the container and pg_container:

        docker run -d --name node_container --link=pg_container:database node_image

This will establish a link to pg_container with the hostname database.

Open /etc/hosts in node_container to verify that the link has been established.

        docker exec -it node_container cat /etc/hosts

You should see a line similar to the following:

      {{< file "/etc/hosts" conf >}}
     172.16.0.2  database  pg_container
      {{< /file >}}

This indicates that pg_container is mapped to the IP address 172.16.0.2 and is correctly linked to this container using the hostname database.

As the Node.js app continues to connect to the PostgreSQL database via the database hostname, no additional modifications are needed. You should be able to execute the app as usual.

        docker exec -it node_container node home/app.js

## Using Docker Compose

Using the --link or --host options every time you launch your containers can be cumbersome. If your server or any of the containers crash, you'll need to reconnect them manually. This isn't ideal for applications requiring constant availability. Luckily, Docker offers Docker Compose to manage multiple containers and automatically establish links between them upon launch. This section will demonstrate using Docker Compose to replicate the setup from the previous section.

Note: For a more detailed understanding of Docker Compose and how to write docker-compose.yml configuration files, refer to our comprehensive Docker Compose guide.

Install Docker Compose:

        sudo curl -L https://github.com/docker/compose/releases/download/1.17.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
        sudo chmod +x /usr/local/bin/docker-compose

Create a docker-compose.yml file in the same directory as your Dockerfile, containing the following content:

          {{< file "docker-compose.yml" >}}
           version: '3'

        services:
         database:
          image: postgres
          container_name: pg_container
         volumes:
        - pgdata:/var/lib/postgresql/data

        app:
        build: .
        container_name: node_container
        links:
        - database
        environment:
        - PGPASSWORD=newpassword
        - PGUSER=postgres
        - PGDATABASE=nodejs
        - PGHOST=database
        - PGPORT=5432
        depends_on:
         - database

          volumes:
           pgdata: {}
         {{< /file >}}

When you execute Docker Compose with this file, it will instantiate pg_container and node_container as described in the previous section. Similar to before, the database container will utilize the official PostgreSQL image, and the application container will be built from your Dockerfile. The links entry in the Docker Compose file fulfills the same purpose as the --link option used in the docker run command previously.

Docker Compose also enables you to define environment variables, allowing you to simplify the application by using these variables instead of hard-coding values. Modify app.js to remove these hardcoded values:

             {{< file "app.js" >}}
             const express = require('express')
             const { Client } = require('pg')

              const client = new Client()

              client.connect()

              client.query('SELECT * FROM hello', (err, res) => {
              console.log(res.rows[0].message)
              client.end()
               })
              {{< /file >}}

4.  Remove the existing containers:

        docker rm -f node_container pg_container

5.  Start the containers using Docker Compose:

        docker-compose up -d

5. Import the example data into the PostgreSQL container:

        docker cp backup.sql pg_container:/
        docker exec -it pg_container psql -U postgres -f backup.sql postgres

6.  Execute app.js within the application container:

        docker exec -it node_container node home/app.js

The application should operate as expected.

## Conclusion

By default, Docker assigns an IP address to each container and to the Docker host, allowing manual connection of services between containers assuming firewall permissions.

Docker provides wrappers to streamline connection processes. You can connect your Docker host to a container using a unique hostname or directly link containers. Docker Compose further simplifies by automating connections declared in docker-compose.yml upon container startup.

Additional connection options include running a container with --net="host", sharing its network stack with the Docker host so localhost within the container points to the Docker host's localhost. Docker also supports port exposure on each container and configuration of the default bridge network for enhanced flexibility. For a detailed exploration of these options, refer to the resources in the More Info section below.
