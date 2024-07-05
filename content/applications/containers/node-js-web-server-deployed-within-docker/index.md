---
slug: "Node.js Docker Delight: Serving Web Apps with Style"
description: 'Learn how to deploy a Node.js server within a Docker container, a powerful platform for running containerized applications, with this comprehensive guide'
keywords: ["docker", "node.js", "node", "debian", "ubuntu", "web server", "javascript", "container"]
tags: ["container","docker","web server"]
modified: 2024-04-08
modified_by:
  name: Utho
published: 2024-04-08
title: 'Node.js Web Server Deployed within Docker'
authors: ["Pawan Kumar."]
---

Node.js is a server-side JavaScript package commonly used for cloud applications. Docker is a platform for containers, making it easy to download and use applications without the hassle of installation and configuration.

## Install Docker

{{< content "installing-docker-shortguide" >}}

## Download the Docker Node.js Server Image
Look for the server-node-js image for configuration details.

{{< note >}}
Docker images designed for one operating system can be used on servers running a different OS. The **server-node-js** Ubuntu 14.04 image has been tested on Debian 7, Ubuntu 14.04, CentOS 7, and Fedora 21. After installing Docker on CentOS and Fedora, use the command `sudo service docker start` to start the Docker service.
{{< /note >}}

1.  To find **utho** images:

        docker search utho

2.  Download the **utho/server-node-js** image:

        docker pull utho/server-node-js

## Run the Docker Container, Node.js, and the Web Server

1.  Launch the Utho container, forwarding port 80 to port 3000 of the container:

        docker run -d -p 80:3000 utho/server-node-js

    {{< note respectIndent=false >}}
This command runs the Docker image as a daemon.
    {{< /note>}}

2.  Test the server at `example.com/test.htm`, replacing `example.com` with your Utho's IP address. You should see a page titled "Test File."
