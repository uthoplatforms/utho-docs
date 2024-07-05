---
slug: "Rancher 1 Deployment Demystified: Unleash Your Apps with Ease"
description: 'Explore how to utilize the open-source Rancher platform to effortlessly deploy applications and containers to remote hosts in this comprehensive guide'
keywords: ["rancher", "docker", "kubernetes", "container"]
tags: ["container","docker","kubernetes"]
published: 2024-04-05
modified: 2024-04-05
modified_by:
  name: Utho
title: 'How to Deploy Apps with Rancher'
external_resources:
  - '[Rancher Official Docs](http://rancher.com/docs/)'
deprecated: true
authors: ["Pawan Kumar"]
---
## What is Rancher?

Rancher simplifies container utilization on a host by operating atop Docker and Kubernetes. With Rancher, you can effortlessly create clusters of containers with just a few clicks. Its web interface provides you and your users access to a vast catalog of pre-built containerized tools that can be easily deployed from within Rancher.

This guide demonstrates how to install [Rancher](http://rancher.com/quick-start/) and then deploy services using Docker and Kubernetes.

## Prepare the Environment
Running Rancher requires two Docker containers:

- `rancher/server`: Hosts the front-end portal.
- `rancher/agent`: Connects remote hosts to the Rancher server.

This guide covers running both containers on the same Utho. If you wish to include more Uthos as Rancher agents, Docker must be installed on each Utho.

### Install Docker CE
To proceed with the steps outlined in this guide, you'll require a Utho equipped with Docker CE. Rancher relies on specific versions of Docker to interact with Kubernetes.

    curl https://releases.rancher.com/install-docker/17.03.sh | sh

### Modify Permissions
Ensure the user is added to the `docker` group to enable running Docker commands without requiring `sudo`:

    usermod -aG docker $USER

## Install Rancher

1. Start the Rancher container:

        sudo docker run -d --restart=unless-stopped -p 8080:8080 rancher/server:stable

2. Confirm that Rancher is operational:

        curl -I localhost:8080

        {{< output >}}
        HTTP/1.1 200 OK
        {{< /output >}}

        docker ps

        {{< output >}}
        60e73830a1bb        rancher/server:stable   "/usr/bin/entry /usr…"   5 minutes ago       Up 5 minutes        3306/tcp, 0.0.0.0:8080->8080/tcp   objective_meninsky
        {{< /output >}}

## Deploy Apps with Rancher

The applications available in Rancher's catalog are represented by Dockerfiles. These Dockerfiles are accessible and can be edited directly within Rancher. They define the stack, which comprises the collection of individual containers required to launch a service, consolidating them in one location.

### Add a Host

To enable Rancher to deploy containers on remote hosts, each host must be registered with the Rancher server. This guide will utilize the Utho operating as the Rancher server as the host, but you can add any number of Uthos using the following steps:

1. Open a web browser and go to `yourUthosIP:8080` to access the Rancher landing page.

2.  At the top of the screen, you'll see a banner urging you to **add a host**. Click on Add a host to initiate the process.

3.  Enter your Utho's IP address in the field provided in Item 4. This will tailor the registration command in item 5 to your system. Copy this command and execute it from the command line.

4.  After completing the registration process, run `docker ps` to confirm that `rancher/agent` is running on the host.

        {{< output >}}
        CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS                          PORTS                              NAMES
        a16cd00943fc        rancher/agent:v1.2.7    "/run.sh run"            3 minutes ago       Restarting (1) 43 seconds ago                                      rancher-agent
        60e73830a1bb        rancher/server:stable   "/usr/bin/entry /usr…"   3 hours ago         Up 3 hours                      3306/tcp, 0.0.0.0:8080->8080/tcp   objective_meninsky
        {{</ output >}}

5.  Return to the Rancher web application and click on **Close**. This will redirect you to the catalog, where Rancher displays all the applications available for installation:

### Install the Ghost Blogging Engine

As an illustration, let's install the Ghost blog platform. This will demonstrate Rancher's integration with Docker.

1.  In the catalog, locate Ghost, keep the default settings unchanged, and then click on the create button.

2.  Check your Utho by running `docker ps`, and Docker will display the containers currently active on the machine:

        144d0a07c315        rancher/pause-amd64@sha256:3b3a29e3c90ae7762bdf587d19302e62485b6bef46e114b741f7d75dba023bd3                  "/pause"                 44 seconds ago       Up 42 seconds                                          k8s_rancher-pause_ghost-ghost-1-c9fb3da6_default_afe1ff4d-f7ce-11e7-a624-0242ac110002_0
        fddce07374a0        ghost@sha256:77b1b1cbe16ae029dee383e7bd0932bd2ca0bd686e206cb1abd14e84555088d2                                "docker-entrypoint..."   44 seconds ago       Up 43 seconds

3.  Visit your Utho's IP address in your browser to access the Ghost landing page.

You've successfully utilized Rancher to deploy a containerized Ghost service.

4.  Within the Rancher interface, click on the Ghost container.

This page allows you to monitor performance and provides options to manage each container individually. You can perform various tasks such as spawning a shell within the container or modifying environment variables directly from this page. To remove the application from the Apps screen, simply click on **Delete**.

### Launch Services From Rancher

To launch individual custom containers, navigate to the **Containers** section within the application.

