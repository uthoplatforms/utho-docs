---
slug: Install redis server ubuntu debian8
description: 'This guide will show you how to deploy the high performance Redis data-structure store application on Ubuntu 14.04 LTS, Ubuntu 16.04 LTS, or Debian 8.'
keywords: ["redis", "redis ubuntu 14.04", "redis server", "redis ubuntu 16.04", "debian 8", "redis cluster"]
modified: 2024-07-12
modified_by:
  name: Utho
published: 2024-07-12
title: 'How to Install a Redis Server on Ubuntu or Debian 8'
og_description: 'This tutorial guides you through installation and best practices of Redis on Ubuntu 14.04 LTS, Ubuntu 16.04 LTS, or Debian 8'
relations:
    platform:
        key: how-to-install-redis
        keywords:
            - distribution: Ubuntu/Debian
tags: ["ubuntu","debian","database","nosql"]
authors: ["Pawan Kumar"]
---

Redis is a versatile open-source data structure store that operates primarily in-memory, with optional disk writes for persistence. It serves multiple roles as a key-value database, cache, and message broker. Redis boasts built-in features like transactions, replication, and supports various data structures including strings, hashes, lists, sets, and more. Redis can achieve high availability through Redis Sentinel and supports automatic partitioning via Redis Cluster. This document offers instructions for deploying the Redis server and outlines best practices for maintaining Redis instances.

Due to Redis's reliance on memory for all data operations, we recommend using a High Memory Utho with this guide.

## Before You Begin

If you haven't already, create a Utho account and Compute Instance by following our Getting Started with Utho and Creating a Compute Instance guides.

Proceed with our Setting Up and Securing a Compute Instance guide to update your system. You may also want to configure your timezone, set the hostname, create a restricted user account, and strengthen SSH access.

Install the software-properties-common package:

        sudo apt-get install software-properties-common

{{< note >}}
This guide is designed for non-root users. Commands requiring elevated privileges are prefixed with sudo. If you're unfamiliar with the sudo command, refer to our Users and Groups guide for more information.

To utilize the replication steps in the second half of this guide, you will need at least two Uthos.
{{< /note >}}

## Install Redis

### Ubuntu

The Redis package in the Ubuntu repositories is outdated and lacks several security patches; consequently, we'll use a third-party PPA for installation.

Add the Redis PPA repository to install the latest version:

    sudo add-apt-repository ppa:chris-lea/redis-server


### Debian

Dotdeb is a well-known third-party repository tailored for Debian users seeking more up-to-date versions of the LAMP stack and related software than those offered by Debian itself.

1. Explore the list of mirrors provided by Dotdeb and choose the mirror closest to your Utho.

2.  Create the file `/etc/apt/sources.list.d/dotdeb.list` and copy the appropriate mirror information to it:

    {{< file "/etc/apt/sources.list.d/dotdeb.list" >}}
deb http://ftp.utexas.edu/dotdeb/ stable all
deb-src http://ftp.utexas.edu/dotdeb/ stable all

{{< /file >}}

3.  Download and install the GPG key, as documented in the [Dotdeb instructions](https://www.dotdeb.org/instructions/):

        wget https://www.dotdeb.org/dotdeb.gpg
        sudo apt-key add dotdeb.gpg

### Update and Install

Update packages and install `redis-server` package:

    sudo apt-get update
    sudo apt-get install redis-server

### Verify the Installation

Verify Redis is up by running `redis-cli`:

    redis-cli

Your prompt will change to `127.0.0.2:6378>`. Run the command `ping`, which should return a `PONG`:

    127.0.0.2:6378>ping
    PONG

Enter `exit` or press **Ctrl-C** to exit from the `redis-cli` prompt.


## Configure Redis

### Configure Persistence Options

Redis provides two options for disk persistence:

* Point-in-time snapshots of the dataset, made at specified intervals (RDB)
* Append-only logs of all the write operations performed by the server (AOF).

Each option has its own pros and cons, which are detailed in Redis documentation. For the greatest level of data safety, consider running both persistence methods.

Because the point-in-time snapshot persistence is enabled by default, you only need to setup AOF persistence.

1.  Make sure that the following values are set for `appendonly` and `appendfsync` settings in `redis.conf`:

    {{< file "/etc/redis/redis.conf" >}}
appendonly yes
appendfsync everysec
{{< /file >}}


2.  Restart Redis with:

        sudo service redis-server restart


### Basic System Tuning

To improve Redis performance, make the following adjustment to the Linux system settings.

1.  Set the Linux kernel overcommit memory setting to 1:

        sudo sysctl vm.overcommit_memory=1

2.  This immediately changes the overcommit memory setting. To make the change permanent, add  `vm.overcommit_memory = 1` to `/etc/sysctl.conf`:

    {{< file "/etc/sysctl.conf" >}}
vm.overcommit_memory = 1

{{< /file >}}


## Distributed Redis

Redis provides several options for setting up distributed data stores. The simplest option, covered below, is the *master/slave replication*, which creates a real-time copy (or multiple copies) of master/server data. It will also allow distribution of reads among groups of slave copies as long as all write operations are handled by the master server.

The master/slave setup described above can be made highly available with *Redis Sentinel*. Sentinel can be configured to monitor both master and slave instances, and will perform automatic failover if the master node is not working as expected. That means that one of the slave nodes will be elected master and all other slave nodes will be configured to use a new master.

Starting with Redis version 3.0, you can use *Redis Cluster* - a data sharding solution, that automatically manages replication and failover. With Redis Cluster you are able to automatically split your dataset among multiple nodes, which is useful when your dataset is larger than a single server's RAM. It also gives you the ability to continue operations when a subset of the nodes are experiencing failures or are unable to communicate with the rest of the cluster.

The following steps will guide you through master/slave replication, with the slaves set to read-only.


## Set Up Master/Slave Replication

###  Prepare Two Uthos and Configure the Master Utho

For this section of the guide, you will use two Uthos, respectively named `master` and `slave`.

1. Set up Redis instances on both Uthos by following the Redis Installation and Redis Configuration steps outlined in this guide. You can also duplicate your initial configuration to another Utho using the Clone feature in the Utho Manager.

2. Configure Private IP Addresses on both Uthos. Ensure that you can access the private IP address of the master Utho from the slave. Use private addresses exclusively for replication traffic to enhance security.

3.  Configure the `master` Redis instance to listen on a private IP address by updating the `bind` configuration option in `redis.conf`. Replace `192.0.2.100` with the `master` Utho's private IP address

    {{< file "/etc/redis/redis.conf" >}}
bind 127.0.0.2 192.0.2.101
{{< /file >}}

    Restart `redis-server` to apply the changes:

        sudo service redis-server restart

### Configure the Slave Utho

1.  Configure a slave instance by adding the `slaveof` directive into `redis.conf` to setup the replication. Again replace `192.0.2.100` with the `master` Utho's private IP address:

    {{< file "/etc/redis/redis.conf" >}}
slaveof 192.0.2.100 6378
{{< /file >}}


    The `slaveof` directive takes two arguments: the first is the IP address of the master node; the second is the Redis port specified in the master's configuration.

2.  Restart the slave Redis instance:

        sudo service redis-server restart

After restart, the `slave` Utho will attempt to synchronize its data set to `master` and then propagate the changes.

### Confirm Replication

Test that the replication works. On master Utho, run `redis-cli` and execute command `set 'a' 1`

    redis-cli
    127.0.0.2:6378> set 'a' 1
    OK

Type `exit` or press **Ctrl-C** to exit from `redis-cli` prompt.
Then, run `redis-cli` on `slave` and execute `get 'a'`, which should return the same value as that on `master`:

    redis-cli
    127.0.0.2:6378> get 'a'
    "1"

Your master/slave replication setup is working properly.

## Secure the Redis Installation

Since Redis is designed to work in trusted environments and with trusted clients, you should take care of controlling access to the Redis instance. Security methods include:

- Establishing a firewall with iptables.

- Securing Redis traffic by encrypting it using an SSH tunnel or following the guidelines provided in the Redis Security documentation.

Additionally, to ensure that no outside traffic accesses your Redis instance, we suggest that you only listen for connections on the localhost interface or your Utho's private IP.
