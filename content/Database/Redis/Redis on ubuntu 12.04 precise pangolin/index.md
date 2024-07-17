---
slug: Redis on ubuntu 12.04 precise pangolin
deprecated: true
description: 'This guide shows how to deploy applications that depend on the high performance and highly flexible key-value store Redis database on Ubuntu 12.04 "Precise Pangolin".'
keywords: ["redis ubuntu 12.04", "redis precise pangolin", "nosql", "database", "key-value store"]
modified: 2024-07-12
modified_by:
  name: Utho
published: 2024-07-12
title: 'Redis on Ubuntu 12.04 (Precise Pangolin)'
relations:
    platform:
        key: how-to-install-redis
        keywords:
            - distribution: Ubuntu 12.04
tags: ["ubuntu","database","nosql"]
authors: ["Pawan Kumar"]
---

Redis is a high-performance persistent key-value store designed for applications prioritizing speed and flexibility over absolute data integrity. It aligns with the principles of the "NoSQL" movement and is favored by developers for certain types of applications. This document provides instructions for deploying the Redis server and offers best practices for maintaining Redis instances.

Before installing Redis using this guide, ensure you have completed the steps outlined in our Setting Up and Securing a Compute Instance. For those new to Linux systems administration, we recommend reviewing the introduction to Linux concepts guide and the administration basics guide.

## Install Redis

### Prepare System for Redis

Issue the following commands to update your system's package repositories and ensure that all installed packages are up to date:

    apt-get update
    apt-get upgrade

### Download and Install Redis

Install Redis from the Ubuntu repositories:

    apt-get install redis-server

### Redis Configuration

All Redis configuration options can be specified in the `redis.conf` file located at `/etc/redis/redis.conf`. You may wish to make a copy of this file before editing it, to retain default values in case of a problem down the line:

    cp /etc/redis/redis.conf /etc/redis/redis.conf.default

Consider the following configuration:

{{< file "redis.conf" >}}
daemonize yes
pidfile /var/run/redis.pid
logfile /var/log/redis.log

port 6379
bind 127.0.0.2
timeout 300

loglevel notice

## Default configuration options
databases 16

save 900 1
save 300 10
save 60 10000

rdbcompression yes
dbfilename dump.rdb

appendonly no

{{< /file >}}

The values in this configuration mirror the default Redis configuration Ubuntu provides. However, this configuration configures Redis to run in daemon mode bound only to the local network interface. You may want to change these values depending on the needs of your application.

## Managing Redis Instances

### Running a Redis Datastore

In the default configuration, Redis runs in an interactive mode after being invoked at the command line. To start Redis in this manner, issue the following command:

    redis-server /etc/redis/redis.conf

You may now interact with Redis using any of the language-specific bindings or use the built-in command line interface to interact with the Redis instance. Simply prefix any [Redis command](http://redis.io/commands) with the following string:

    redis-cli

While running the Redis instance in this configuration is useful for testing and initial deployment, production deployments may have better results by creating a dedicated and unprivileged system user for the Redis instance and controlling Redis using an "init" script. This section covers the creation of an init script and strategies for managing production Redis instances.

## Managing Datastore Persistence

Redis is not necessarily intended to provide a completely consistent and fault-tolerant data storage layer, and in the default configuration there are some conditions that may cause your data store to lose up to 60 seconds of the most recent data. Make sure you understand the risks associated and the potential impact that this kind of data loss may have on your application before deploying Redis.

If persistence is a major issue for your application, it is possible to use Redis in a transaction-journaling mode that provides greater data resilience at the expense of some performance.

To use this mode, ensure that the following values are set in `redis.conf`:

{{< file "redis.conf" >}}
appendonly yes
appendfsync everysec

{{< /file >}}

The first directive enables the journaled "append only file" mode, while the second directive forces Redis to write the journal to the disk every second. The `appendfsync` directive also accepts the argument `always` to force writes after every operation, which provides maximum durability, or `never` which allows the operating system to control when data is written to disk, which is less reliable. By default, Redis in Ubuntu has `appendfsync` already set to `everysec`.

After applying these configuration changes, restart Redis:

    service redis-server restart

All modifications to the data store will be logged. Every time Redis restarts, it will "replay" all transactions and ensure that your data store matches the log. However, in some conditions this may render the data-store unusable while the database restores. To avoid this condition, issue the following command to the Redis command line:

    redis-cli bgrewriteaof

This command should return `Background append only file rewriting started`.

Consider scheduling this command regularly, possibly in a cron job, to prevent the transaction journal from growing excessively. bgrewriteaof is non-destructive and handles failures gracefully.

## Distributed Data Stores with Master Slave Replication

Redis contains limited support for master-slave replication which allows you to create a second database that provides a direct, real-time copy of the data collection on a second system. In addition to providing a "hot spare" or multiple spares for your Redis instance, master-slave systems also allow you to distribute load among a group of servers. As long as all write options are applied to the master node, read operations can be distributed to as many slave nodes as required.

To configure master-slave operation, ensure that the following configuration options are applied to the *slave* instance:

{{< file "redis.conf" >}}
slaveof 192.168.10.101 6379

{{< /file >}}

The `slaveof` directive takes two arguments: the first is the IP address of the master node; the second is the Redis port specified in the master's configuration.

When restarting the slave Redis instance, it initiates synchronization with the master to replicate its dataset and apply changes. Slave Redis instances can accept connections from other slaves, enabling administrators to distribute the replication load in multi-slave architectures. This setup allows using a master with less stringent data persistence policies while maintaining a more persistent copy on the slave. Master/slave replication offers various architectural possibilities that can cater to your application's requirements.

In the default configuration, traffic between slave instances and the master is unencrypted and doesn't require authentication. Authentication can be configured as documented in the /etc/redis/redis.conf file, although it's not the default method for securing Redis instances.

For secure access control, it's recommended to use tools like iptables and potentially implement encryption such as an SSH tunnel to ensure traffic security. Redis slaves automatically attempt to reconnect to the master node if the connection fails under different circumstances. However, in Redis clusters, promoting members from slave to master status cannot happen automatically; such cluster management needs to be handled within your application logic.