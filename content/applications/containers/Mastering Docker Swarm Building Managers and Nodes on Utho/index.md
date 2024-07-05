---
slug: "Mastering Docker Swarm: Building Managers and Nodes on Utho"
description: "Discover how to set up and operate a Docker Swarm, allowing you to effectively manage a Docker cluster on Utho, with this comprehensive guide."
keywords: ["docker", "container", "docker swarm", "swarm manager", "swarm nodes"]
tags: ["ubuntu","container","docker"]
modified: 2024-04-05
modified_by:
  name: Utho
published: 2017-09-18
title: "Create a Docker Swarm Manager and Nodes on Utho"
authors: ["Pawan Kumar"]
---

How to Create a Docker Swarm Manager and Nodes on Utho

## Before You Begin

1.  To complete this guide, you'll need at least two Utho situated in the same data center. While the instructions here are tailored for Ubuntu 16.04, you can use other distributions as well; the Uthos don't necessarily need to run the same distribution.

2. First, ensure your system is up-to-date by following our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide. You might also want to configure the timezone, set up your hostname, create a limited user account, and strengthen SSH access.

3. Next, install Docker on each Utho. Refer to our [Installing Docker and Deploying a LAMP Stack](/docs/guides/how-to-install-docker-and-deploy-a-lamp-stack/) guide or check out the official [Docker installation docs](https://docs.docker.com/engine/installation/) for detailed instructions.

{{< note >}}
Please ensure you have root privileges to execute the steps outlined below. Run the commands as `root` or prepend them with `sudo`. For more details on privileges, refer to our [Users and Groups](/docs/guides/linux-users-and-groups/) guide.
{{< /note >}}

Elevate your Docker capabilities by establishing a cluster of Docker hosts known as a Docker Swarm. You'll need one Utho designated as the Docker Swarm Manager and several Uthos to join the Swarm as Nodes.

This guide walks you through setting up a Docker Swarm Manager and connecting Nodes to facilitate a scalable container deployment. It necessitates multiple Uthos with Docker installed and operational in the same data center, though they need not be running the same distribution.

## Create the Docker Swarm Manager

The Docker Swarm Manager is responsible for receiving commands for the cluster and assigning containers to nodes. It utilizes the Raft Consensus Algorithm to manage Swarm states, ensuring consistency across manager nodes. If a failure occurs, a single node takes over tasks to restore stability.

In this guide, we'll create a single Swarm Manager. However, if you aim for high availability, you can create multiple managers.

1. Log in to your chosen Utho for the Swarm manager and initialize it. Replace `PUBLIC_IP` in the example below with your Utho's [public IP address](/docs/products/compute/compute-instances/guides/manual-network-configuration/):

        docker swarm init --advertise-addr PUBLIC_IP

After initializing the Swarm, Docker provides the command required for the nodes to join the Swarm:

2.  To confirm that your Swarm is operational and active, use the command `docker info`.

        docker info

## Join Nodes to the Manager
In Step 1 of the previous section, when you run the `docker swarm init` command, it provides instructions on how to join the manager.

    docker swarm join --token TOKEN PUBLIC_IP:2377

Replace `TOKEN` with the lengthy string of characters given to you during Swarm initialization, and `PUBLIC_IP` with the public IP address of your Swarm Manager Utho. If you've forgotten the token, retrieve it by running `join-token` on the manager to see the details from the `swarm init` command:

    docker swarm join-token worker

1.  To add the node to the Swarm, execute `docker swarm join` from the node. Substituting TOKEN with the token obtained in Step 1 from the previous section, and `PUBLIC_IP` with the manager's public IP:

        docker swarm join --token TOKEN PUBLIC_IP:2377

The output confirms that the node has successfully joined the swarm as a worker. Congratulations! You now have a small Docker Swarm cluster comprising one manager and one node.

2.  To add more nodes to the Swarm, simply repeat Step 1 for each node you wish to join.

3.  Once completed, on the manager, utilize `docker node ls` to access details about the manager and a comprehensive list of all nodes:

        docker node ls

## Deploy a Service with Docker Swarm

To deploy a service using Docker Swarm, first set up a single node using the manager, then expand the configuration as needed. In this demonstration, we'll install NGINX on one node and then scale it up to a cluster (swarm) consisting of three nodes.

1.  From the Swarm Manager, employ `service create` to deploy a service to a node. Customize `nginxexample` to suit your preferences:

        docker service create -p 80:80 --name nginxexample nginx

2.  Lastly, scale the NGINX service to encompass three nodes:

        docker service scale nginxexample=3

3. Confirm that the service has been successfully deployed by running `docker ps -a` on any node:

        docker ps -a

4.  To halt the `nginxexample` service, employ the `service remove` command:

        docker service remove nginxexample
