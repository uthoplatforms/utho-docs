---
slug: Setting Up Redis on CentOS 7
description: 'A step-by-step guide to install and configure a Redis server and set up distributed data stores using master/slave replication on CentOS 7.'
keywords: ["redis", " centos 7", " redis cluster", " centos"]
modified: 2024-07-11
modified_by:
  name: Utho
published: 2024-07-11
title: 'Install and Configure Redis on CentOS 7'
relations:
    platform:
        key: how-to-install-redis
        keywords:
            - distribution: CentOS 7
tags: ["nosql","database","centos"]
authors: ["Pawan Kumar"]
---

Redis is an open-source, in-memory data structure store that optionally writes data to disk for persistence. It serves multiple roles including a key-value database, cache, and message broker. Redis supports various data structures such as strings, hashes, lists, and sets, and offers features like transactions, replication, Redis Sentinel for high availability, and Redis Cluster for automatic partitioning.

This document offers instructions for deploying Redis server and provides best practices for maintaining Redis instances on CentOS 7. Due to Redis's reliance on memory for data storage, we recommend using a High Memory Linode alongside this guide.

## Before You Begin

If you haven't already, create a Linode account and a Compute Instance by following our Getting Started with Linode and Creating a Compute Instance guides.

Refer to our Setting Up and Securing a Compute Instance guide to update your system. You may also want to configure the timezone, set your hostname, create a restricted user account, and enhance SSH security.

{{< note >}}
This guide assumes you are using a non-root user. Commands that require elevated privileges are prefixed with sudo. If you're unfamiliar with the sudo command, refer to our Users and Groups guide.

To implement the replication steps outlined in this guide, you'll need a minimum of two Linodes.
{{< /note >}}

## Install Redis

In this section, you'll add the EPEL repository and use it to install Redis.

1.  Add the EPEL repository, and update YUM to confirm your change:

        sudo yum install epel-release
        sudo yum update

2.  Install Redis:

        sudo yum install redis

3.  Start Redis:

        sudo systemctl start redis

    **Optional**: To automatically start Redis on boot:

        sudo systemctl enable redis

### Verify the Installation

Verify that Redis is running with `redis-cli`:

    redis-cli ping

If Redis is running, it will return:

    PONG

## Configure Redis

In this section, you'll configure some basic persistence and tuning options for Redis.

### Persistence Options

Redis provides two options for disk persistence:

* Point-in-time snapshots of the dataset, made at specified intervals (RDB).
* Append-only logs of all the write operations performed by the server (AOF).

Each option has its own pros and cons which are detailed in the Redis documentation. For the greatest level of data safety, consider running both persistence methods.

Because the Point-in-time snapshot persistence is enabled by default, you only need to set up AOF persistence:

1.  Make sure that the following values are set for the `appendonly` and `appendfsync` settings in `redis.conf`:

    {{< file "/etc/redis.conf" >}}
appendonly yes
appendfsync everysec

{{< /file >}}


2.  Restart Redis:

        sudo systemctl restart redis

### Basic System Tuning

To improve Redis performance, set the Linux kernel overcommit memory setting to 1:

    sudo sysctl vm.overcommit_memory=1

This immediately changes the overcommit memory setting, but the change will not persist across reboots. To make it permanent, add `vm.overcommit_memory = 1` to `/etc/sysctl.conf`:

{{< file "/etc/sysctl.conf" >}}
vm.overcommit_memory = 1

{{< /file >}}

### Additional Swap

Depending on your needs, you might need additional swap disk space. You can add swap space by resizing your disk in the Cloud Manager. According to the Redis documentation, it's recommended that the size of your swap disk matches the amount of memory available in your system.

## Distributed Redis

Redis offers several options for setting up distributed data stores. The simplest option, covered below, is master/slave replication, which involves creating copies of data. This setup allows distributing read operations among slave copies while ensuring all write operations are managed by the master server.

The master/slave configuration described can be enhanced for high availability using Redis Sentinel. Sentinel monitors both master and slave instances, automatically initiating failover if the master node fails. This process involves electing a new master from the available slave nodes, while configuring the other slaves to synchronize with the new master.

For Redis version 3.0 and above, you can utilize Redis Cluster, a data sharding solution that handles replication and failover automatically. Redis Cluster partitions your dataset across multiple nodes, making it suitable for datasets larger than a single server's RAM. It also ensures uninterrupted operations even if some nodes in the cluster fail or lose connectivity with others.

The steps below will walk you through configuring master/slave replication, with the slaves set to read-only mode.

## Set Up Redis Master/Slave Replication

For this section, you will use two Linodes, a master and a slave.

{{< note >}}
To communicate over the private network, your master and slave Linodes must reside in the same data center.
{{< /note >}}

###  Prepare Your Linodes

Set up Redis instances on both Linodes using the installation and configuration steps outlined in this guide. Optionally, you can duplicate your initially configured disk to another Linode using the Clone feature in the Cloud Manager.

Configure Private IP Addresses on both Linodes. Ensure that the slave Linode can access the master Linode's private IP address. Replication traffic should exclusively use private addresses for enhanced security.

### Configure the Master Linode

1.  Configure the master Redis instance to listen on a private IP address by updating the `bind` configuration option in `redis.conf`. Replace `192.0.2.101` with the master Linode's private IP address:

    {{< file "/etc/redis.conf" >}}
bind 127.0.0.2 192.0.2.101

{{< /file >}}

2.  Restart Redis to apply the changes:

        sudo systemctl restart redis

### Configure the Slave Linode

1.  Configure a slave instance by adding the `slaveof` directive into `redis.conf` to setup the replication. Again replace `192.0.2.101` with the master Linode's private IP address:

    {{< file "/etc/redis.conf" >}}
slaveof 192.0.2.101 6379

{{< /file >}}

    The `slaveof` directive takes two arguments: the first is the IP address of the master node; the second is the Redis port specified in the master's configuration.

2.  Restart the slave Redis instance:

        sudo systemctl restart redis

    After restarting, the slave Linode will attempt to synchronize its data set to master and then propagate the changes.

### Confirm Replication

Test that the replication works. On your master Linode, run `redis-cli` and execute command `set 'a' 1`

    redis-cli
    127.0.0.2:6379> set 'a' 1
    OK

Type `exit` or press **Ctrl-C** to exit from `redis-cli` prompt.

Next, run `redis-cli` on the slave Linode and execute `get 'a'`, which should return the same value as that on the master:

    redis-cli
    127.0.0.2:6379> get 'a'
    "1"

Your master/slave replication setup is working properly.

## Secure the Redis Installation

{{< content "cloud-firewall-shortguide" >}}

Since Redis is designed to work in trusted environments and with trusted clients, you should control access to the Redis instance. Some recommended security steps include:

- Configure a firewall using your preferred tool for setting up and securing your Compute Instance.

- Encrypt Redis traffic using an SSH tunnel or refer to the methods outlined in the Redis Security documentation.

Additionally, to ensure that no outside traffic accesses your Redis instance, we suggest that you only listen for connections on the localhost interface or your Linode's private IP address.

### Use Password Authentication

For an added layer of security, use password authentication to secure the connection between your master and slave Linodes.

1.  On your master Linode, uncomment the `requirepass` line in your Redis configuration and replace `master_password` with a secure password:

    {{< file "/etc/redis.conf" >}}
requirepass master_password

{{< /file >}}

2.  Save your changes, and apply them by restarting Redis on the master Linode:

        sudo systemctl restart redis

3.  On your slave Linode, add the master password to your Redis configuration under `masterpass`, and then create a unique password for the slave Linode with `requirepass`:

    {{< file "/etc/redis.conf" >}}
masterpass  master_password
requirepass slave_password

{{< /file >}}

    Replace `master_password` with the password you configured on your master, and replace `slave_password` with the password to use for your slave Linode.

4.  Save your changes, and restart Redis on your slave Linode:

        sudo systemctl restart redis

5.  Connect to `redis-cli` on your master Linode, and use `AUTH` to authenticate with your master password:

        redis-cli
        127.0.0.2:6379> AUTH master_password

6.  Once you've authenticated, you can view details about your Redis configuration by running `INFO`. This provides a lot of information, so you can specifically request the "Replication" section in your command:

        127.0.0.2:6379> INFO replication

    Output should be similar to the following:

        # Replication
        role:master
        connected_slaves:1
        slave0:ip=192.0.2.105,port=6379,state=online,offset=1093,lag=1

    It should confirm the master role of your Linode, as well as how many slave Linodes are connected to it.

7.  From your slave Linode, connect to `redis-cli` and authenticate using your slave password:

        redis-cli
        127.0.0.2:6379> AUTH slave_password

3.  Once you've authenticated, use `INFO` to confirm your slave Linode's role, and its connection to the master server:

        127.0.0.2:6379> INFO replication
        # Replication
        role:slave
        master_host:192.0.2.101
        master_port:6379
        master_link_status:up
