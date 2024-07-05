---
slug: Deploying Microservices with Docker
description: 'This guide explores effective Docker usage in production, focusing on a sample NGINX/Flask/Gunicorn/Redis/PostgreSQL application stack'
og_description: 'This guide shows how to deploy a simple microservice using Docker. Best practices for using Docker in production are also demonstrated and explained.'
keywords: ["docker", "nginx", "flask", "gunicorn", "redis", "postgresql", "microservice"]
tags: ["postgresql","database","docker","container","nginx"]
modified: 2024-06-14
modified_by:
  name: Pawan Kumar
published: 2024-06-14
title: 'How to Deploy Microservices with Docker'
authors: ["Pawan Kumar"]
---

## What is a Microservice?

Microservices have gained popularity for building large-scale applications by breaking them down into smaller components. Instead of a monolithic codebase, applications are composed of independent microservices. This approach offers benefits such as scalability of individual components, simplified codebase management, easier testing, and the flexibility to use different programming languages, databases, and tools for each microservice.

Docker is well-suited for managing and deploying microservices. Each microservice can be encapsulated within its own Docker container, defined using Dockerfiles and orchestrated with Docker Compose. When combined with tools like Kubernetes for provisioning, microservices become straightforward to deploy, scale, and collaborate on within development teams. This modular approach also facilitates seamless integration of microservices to build comprehensive applications.

This guide demonstrates building and deploying a sample microservice using Docker and Docker Compose.

## Before You Begin

If you haven't already, create a utho account and set up a Compute Instance. Refer to our Getting Started with utho and Creating a Compute Instance guides.

Secure your Compute Instance by following our Setting Up and Securing a Compute Instance guide. This includes updating your system, configuring the timezone, setting the hostname, creating a restricted user account, and enhancing SSH access.

Note: This guide assumes you're using a non-root user. Commands needing elevated privileges are prefixed with sudo. If you're unfamiliar with the sudo command, refer to our Users and Groups guide for more information.

### Install Docker

{{< content "installing-docker-shortguide" >}}

### Install Docker Compose

{{< content "install-docker-compose" >}}

## Prepare the Environment

In this section, Dockerfiles are utilized to configure Docker images. For detailed insights into Dockerfile syntax and best practices, refer to our How To Use Dockerfiles guide and Docker's Dockerfile Best Practices guide.

1. Create a directory for the microservice:

        mkdir flask-microservice

2. Create a directory structure for the microservice components within the newly created directory:

        cd flask-microservice
        mkdir nginx postgres web

### NGINX
Within the new `nginx` subdirectory, create a Dockerfile for the NGINX image:

       {{< file "nginx/Dockerfile" yaml >}}
       from nginx:alpine
       COPY nginx.conf /etc/nginx/nginx.conf
        {{</ file >}}

2.  Create the `nginx.conf` file as specified in the Dockerfile


        {{< file "/nginx/nginx.conf" nginx >}}
         user  nginx;
         worker_processes 1;
         error_log  /dev/stdout info;
         error_log off;
         pid        /var/run/nginx.pid;

        events {
        worker_connections  1024;
        use epoll;
        multi_accept on;
        }

       http {
          include       /etc/nginx/mime.types;
          default_type  application/octet-stream;

         log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

       access_log  /dev/stdout main;
       access_log off;
       keepalive_timeout 65;
       keepalive_requests 100000;
       tcp_nopush on;
       tcp_nodelay on;

        server {
        listen 80;
        proxy_pass_header Server;

            location / {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;

            # app comes from /etc/hosts, Docker added it for us!
            proxy_pass http://flaskapp:8000/;
            }
           }
          }
          {{</ file >}}

### PostgreSQL

For this microservice, the PostgreSQL image will utilize the official postgresql image from Docker Hub, eliminating the need for a Dockerfile.

Inside the `postgres` subdirectory, create an `init.sql` file:

        {{< file "postgres/init.sql">}}
         SET statement_timeout = 0;
         SET lock_timeout = 0;
         SET idle_in_transaction_session_timeout = 0;
         SET client_encoding = 'UTF8';
         SET standard_conforming_strings = on;
         SET check_function_bodies = false;
         SET client_min_messages = warning;
         SET row_security = off;
         CREATE EXTENSION IF NOT EXISTS plpgsql WITH SCHEMA pg_catalog;
         COMMENT ON EXTENSION plpgsql IS 'PL/pgSQL procedural language';
         SET search_path = public, pg_catalog;
         SET default_tablespace = '';
         SET default_with_oids = false;
         CREATE TABLE visitors (
         site_id integer,
         site_name text,
         visitor_count integer
         );

          ALTER TABLE visitors OWNER TO postgres;
          COPY visitors (site_id, site_name, visitor_count) FROM stdin;
          1 	uthoexample.com  	0
          \.
          {{</ file >}}

Note: Ensure that your text editor preserves tabs instead of converting them to spaces in Line 22 of `init.sql`. The application will not function correctly without tabs separating the entries on this line.

### Web

The web image will host an example Flask application. Include the following files in the web directory to set up the application:

Create a .python-version file to specify Python 3.6:

        echo "3.6.0" >> web/.python-version

2.  Create a Dockerfile for the web image:


     {{< file "web/Dockerfile" yaml >}}
     from python:3.6.2-slim
     RUN groupadd flaskgroup && useradd -m -g flaskgroup -s /bin/bash flask
     RUN echo "flask ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
     RUN mkdir -p /home/flask/app/web
     WORKDIR /home/flask/app/web
     COPY requirements.txt /home/flask/app/web
     RUN pip install --no-cache-dir -r requirements.txt
     RUN chown -R flask:flaskgroup /home/flask
     USER flask
     ENTRYPOINT ["/usr/local/bin/gunicorn", "--bind", ":8000", "utho:app", "--reload", "--workers", "16"]
      {{</ file >}}

3.  Create web/utho.py and insert the example app script:

      {{< file "web/utho.py" python >}}
       from flask import Flask
        import logging
        import psycopg2
        import redis
         import sys

         app = Flask(__name__)
         cache = redis.StrictRedis(host='redis', port=6379)

# Configure Logging

       app.logger.addHandler(logging.StreamHandler(sys.stdout))
       app.logger.setLevel(logging.DEBUG)

       def PgFetch(query, method):

      # Connect to an existing database
      conn = psycopg2.connect("host='postgres' dbname='utho' user='postgres' password='utho123'")

      # Open a cursor to perform database operations
      cur = conn.cursor()

      # Query the database and obtain data as Python objects
      dbquery = cur.execute(query)

      if method == 'GET':
        result = cur.fetchone()
      else:
        result = ""

      # Make the changes to the database persistent
       conn.commit()

       # Close communication with the database
        cur.close()
        conn.close()
        return result

        @app.route('/')
        def hello_world():
         if cache.exists('visitor_count'):
         cache.incr('visitor_count')
         count = (cache.get('visitor_count')).decode('utf-8')
         update = PgFetch("UPDATE visitors set visitor_count = " + count + " where site_id = 1;", "POST")
         else:
         cache_refresh = PgFetch("SELECT visitor_count FROM visitors where site_id = 1;", "GET")
         count = int(cache_refresh[0])
         cache.set('visitor_count', count)
         cache.incr('visitor_count')
         count = (cache.get('visitor_count')).decode('utf-8')
      return 'Hello utho!  This page has been viewed %s time(s).' % count

         @app.route('/resetcounter')
         def resetcounter():
         cache.delete('visitor_count')
         PgFetch("UPDATE visitors set visitor_count = 0 where site_id = 1;", "POST")
         app.logger.debug("reset visitor count")
           return "Successfully deleted redis and postgres counters"

         {{</ file >}}

4.  Include a `requirements.txt` file listing the necessary Python dependencies:

         {{< file "web/requirements.txt" text >}}
          flask
          gunicorn
          psycopg2-binary
          redis
          {{</ file >}}

## Docker Compose

Docker Compose will define the connections between containers and their configuration settings.

Create a `docker-compose.yml` file in the `flask-microservice` directory and include the following configuration:

       {{< file "docker-compose.yml" yaml >}}
        version: '3'
         services:
         # Define the Flask web application
         flaskapp:

        # Build the Dockerfile that is in the web directory
         build: ./web

         # Always restart the container regardless of the exit status; try and restart the container indefinitely
          restart: always

         # Expose port 8000 to other containers (not to the host of the machine)
          expose:
         - "8000"

          # Mount the web directory within the container at /home/flask/app/web
          volumes:
           - ./web:/home/flask/app/web

          # Don't create this container until the redis and postgres containers (below) have been created
           depends_on:
            - redis
            - postgres

          # Link the redis and postgres containers together so they can talk to one another
         links:
         - redis
         - postgres

          # Pass environment variables to the flask container (this debug level lets you see more useful information)
          environment:
          FLASK_DEBUG: 1

          # Deploy with three replicas in the case one of the containers fails (only in Docker Swarm)
          deploy:
          mode: replicated
          replicas: 3

          # Define the redis Docker container
           redis:

           # use the redis:alpine image: https://hub.docker.com/_/redis/
           image: redis:alpine
           restart: always
           deploy:
           mode: replicated
           replicas: 3

           # Define the redis NGINX forward proxy container
           nginx:

           # build the nginx Dockerfile: http://bit.ly/2kuYaIv
           build: nginx/
           restart: always

           # Expose port 80 to the host machine
            ports:
            - "80:80"
            deploy:
            mode: replicated
            replicas: 3

           # The Flask application needs to be available for NGINX to make successful proxy requests
           depends_on:
           - flaskapp

           # Define the postgres database
            postgres:
            restart: always
           # Use the postgres alpine image: https://hub.docker.com/_/postgres/
           image: postgres:alpine

           # Mount an initialization script and the persistent postgresql data volume
           volumes:
              - ./postgres/init.sql:/docker-entrypoint-initdb.d/init.sql
              - ./postgres/data:/var/lib/postgresql/data

           # Pass postgres environment variables
            environment:
             POSTGRES_PASSWORD: utho123
             POSTGRES_DB: utho

           # Expose port 5432 to other Docker containers
           expose:
           - "5432"
            {{</ file >}}

## Test the Microservice

Use Docker Compose to build all the images and launch the microservice:

        cd flask-microservice/ && docker-compose up

You will observe all services starting in your terminal.

Open a new terminal window and send a request to the example application:

        curl localhost

    {{< output >}}
        Hello utho! This page has been viewed 1 time(s).
    {{< /output >}}

3.  Reset the page hit counter:

        curl localhost/resetcounter

      {{< output >}}
       Successfully deleted redis and postgres counters
      {{< /output >}}

4.  Return to the terminal window where Docker Compose was initiated to monitor the standard output log:

    {{< output >}}
   flaskapp_1  | DEBUG in utho [/home/flask/app/web/utho.py:56]:
   flaskapp_1  | reset visitor count
   {{< /output >}}

## Using Containers in Production: Best Practices

The example microservice containers illustrate the following best practices for production use:

Containers should be:

Ephemeral: Containers should be easily stoppable, destroyable, rebuilt, and redeployed with minimal setup and configuration. The Flask microservice demonstrates this well, as it can be managed entirely through Docker Compose without requiring additional configuration once the containers are running. This flexibility facilitates straightforward application modification.

Disposable: Each container in a larger application should ideally be able to fail independently without affecting overall application performance. Configuring containers with options like restart: on-failure in the docker-compose.yml file and employing a replica count allows some containers in the microservice example to gracefully handle failures while continuing to serve the web application without impacting end users.

Note: The replica count directive is only effective when deploying this configuration as part of a Docker Swarm, which is not covered in this guide.

**Quick to start**: Minimize Dockerfile steps, remove unnecessary dependencies, and optimize Docker image builds to ensure fast initialization times for web applications. The example application uses concise, prebuilt Dockerfiles to achieve this efficiency.

**Quick to stop**: Verify that stopping the application gracefully with docker kill --signal=SIGINT {APPNAME} works as expected. Combined with restart and replica conditions, this ensures containers recover efficiently from failures.

**Lightweight**: Opt for the smallest base container that meets application requirements. Many Docker images leverage Alpine Linux, a lightweight distribution weighing only 5MB in Docker images. Using such minimal distros reduces network and operational overhead while boosting container performance. The example microservice employs Alpine images for NGINX, Redis, and PostgreSQL, and a python-slim base image for Gunicorn/Flask.

**Stateless**: Containers should generally avoid maintaining state, storing application state in a separate, persistent data volume instead. The microservice's PostgreSQL datastore adheres to this principle. While Redis maintains data within a container, it's non-critical and gracefully fails over to the database if necessary.

**Portable**: Ensure all container runtime dependencies are locally accessible. The example microservice stores all dependencies and startup scripts within each component's directory, facilitating version control integration for easy sharing and deployment.

**Modular**: Each container should handle a single responsibility and process. In this microservice, major processes like NGINX, Python, Redis, and PostgreSQL are isolated in separate containers.

**Logging**: Standardize logging to STDOUT across all containers for unified log management.

**Resilient**: The example application automatically restarts containers upon exit, enhancing Dockerized application availability and performance, particularly during maintenance periods.
