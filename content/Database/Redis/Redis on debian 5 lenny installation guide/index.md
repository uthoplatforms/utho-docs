---
slug: Redis on debian 5 lenny installation guide
deprecated: true
description: 'This guide shows how to deploy applications that depend on the high performance and highly flexible key-value store Redis database on Debian 5 "Lenny".'
keywords: ["redis debian 5", "redis lenny", "nosql", "database", "key-value store"]
modified: 2024-07-11
modified_by:
  name: Utho
published: 2024-07-11
title: 'Redis on Debian 5 (Lenny)'
relations:
    platform:
        key: how-to-install-redis
        keywords:
            - distribution: Debian 5
tags: ["debian","database","nosql"]
authors: ["Pawan Kumar"]
---

Redis is a high-performance persistent key-value store designed for applications prioritizing speed and flexibility over absolute data integrity. It aligns with the principles of the "NoSQL" movement and is popular among developers for specific types of applications. This guide offers instructions for deploying Redis servers and discusses best practices for maintaining Redis instances.

Before proceeding with Redis installation, ensure you've completed the prerequisites outlined in our Setting Up and Securing a Compute Instance guide. If you're new to Linux server administration, consider reviewing our guides on Linux concepts, beginner's basics, and system administration fundamentals.

## Install Redis

### Prepare System for Redis

Issue the following commands to update your system's package repositories and ensure that all installed packages are up to date:

    apt-get update
    apt-get upgrade

Install required prerequisites with the following command:

    apt-get install build-essential

This guide only provides instructions for installing and managing Redis itself. The application you deploy likely requires additional infrastructure, dependencies, and utilities.

### Download and Compile Software

Begin the installation process by issuing the following sequence of commands to download the software and prepare it for use:

    cd /opt/
    mkdir /opt/redis
    wget http://redis.googlecode.com/files/redis-2.2.2.tar.gz
    tar -zxvf /opt/redis-2.2.2.tar.gz
    cd /opt/redis-2.2.2/
    make

To download and compile Redis version 2.2.2, visit the Redis upstream project source to confirm you have the latest version. It's crucial to use the most recent software to prevent security vulnerabilities, bugs, and to access new features. Manual compilation means you're responsible for keeping your system updated independently of package management tools.

Move all of the redis executable files to the `/opt` directory by issuing the following sequence of commands:

    cp /opt/redis-2.2.2/redis.conf /opt/redis/redis.conf.default
    cp /opt/redis-2.2.2/src/redis-benchmark /opt/redis/
    cp /opt/redis-2.2.2/src/redis-cli /opt/redis/
    cp /opt/redis-2.2.2/src/redis-server /opt/redis/
    cp /opt/redis-2.2.2/src/redis-check-aof /opt/redis/
    cp /opt/redis-2.2.2/src/redis-check-dump /opt/redis/

You will need to repeat these commands each time you upgrade Redis.

### Redis Configuration

All Redis configuration options can be specified in the `redis.conf` file located at `/opt/redis/redis.conf`. Issue the following command to create this file from the default configuration file:

    cp /opt/redis/redis.conf.default /opt/redis/redis.conf

Consider the following configuration:

{{< file "redis.conf" >}}
daemonize yes
pidfile /var/run/redis.pid
logfile /var/log/redis.log

port 6379
bind 127.0.0.1
timeout 300

loglevel notice

## Default configuration options
databases 16

save 900 1
save 300 10
save 60 10000

rdbcompression yes
dbfilename dump.rdb

dir /opt/redis/
appendonly no

glueoutputbuf yes

{{< /file >}}


Most of the values in this configuration mirror the default Redis configuration. However, this configuration configures Redis to run in a daemon mode bound only to the local network interface. You may want to change these values depending on the needs of your application.

## Monitor for Software Updates and Security Notices

When you install or compile software directly from upstream sources, it's your responsibility to stay informed about updates, bug fixes, and security patches. Keeping your software up to date helps prevent system vulnerabilities and ensures overall system security and stability. Monitor the Redis Project mailing lists regularly to stay informed about software updates. When new releases are available from upstream sources, follow the installation instructions again and recompile your software as necessary. These practices are essential for maintaining the security and proper functioning of your system over time.

## Managing Redis Instances

### Running a Redis Datastore

In the default configuration, Redis runs in an interactive mode after being invoked at the command line. To start Redis in this manner issue the following command:

    /opt/redis/redis-server /opt/redis/redis.conf.default

You may now interact with Redis using any of the language specific bindings or use the built-in command line interface to interact with the Redis instance. Simply prefix any [Redis command](http://redis.io/commands/) with the following string:

    /opt/redis/redis-cli

While running the Redis instance in this configuration is useful for testing and initial deployment, production deployments may have better results by creating a dedicated and unprivileged system user for the Redis instance and controlling Redis using an "init" script. This section covers the creation of an init script and strategies for managing production Redis instances.

### Deploy Init Script

Issue the following sequence of commands to download a basic init script, create a dedicated system user, mark this file as executable, and ensure that the Redis process will start following the next boot cycle:

    cd /opt/
    wget -O init-deb.sh http://www.Utho.com/docs/assets/628-redis-init-deb.sh
    adduser --system --no-create-home --disabled-login --disabled-password --group redis
    mv /opt/init-deb.sh /etc/init.d/redis
    chmod +x /etc/init.d/redis
    chown -R redis:redis /opt/redis
    touch /var/log/redis.log
    chown redis:redis /var/log/redis.log
    update-rc.d -f redis defaults

Redis will now start following the next boot process. You may now use the following commands to start and stop the Redis instance:

    /etc/init.d/redis start
    /etc/init.d/redis stop

## Managing Datastore Persistence

Redis is not necessarily intended to provide a completely consistent and fault tolerant data storage layer, and in the default configuration there are some conditions that may cause your data store to lose up to 60 seconds of the most recent data. Make sure you understand the risks associated and the potential impact that this kind of data loss may have on your application before deploying Redis.

If persistence is a major issue for your application, it is possible to use Redis in a transaction journaling mode that provides greater data resilience at the expense of some performance.

To use this mode, ensure that the following values are set in `redis.conf`:

{{< file "redis.conf" >}}
appendonly yes
appendfsync everysec

{{< /file >}}

The first directive enables the journaled "append only file" mode, while the second directive forces Redis to write the journal to the disk every second. The `appendfsync` directive also accepts the argument `always` to force writes after every operation which provides maximum durability, or `never` which allows the operating system to control when data is written to disk which is less reliable.

After applying these configuration changes, restart Redis. All modifications to the data store will be logged. Every time Redis restarts it will "replay" all transactions and ensure that your data store matches the log. However, in some conditions this may render the data-store unusable while the database restores. To avoid this condition, issue the following command to the Redis command line:

    /opt/redis/redis-cli bgrewriteaof

You might consider scheduling this command regularly, possibly using a cron job, to prevent the transaction journal from expanding excessively. The bgrewriteaof command is non-destructive and can handle failures gracefully.

## Distributed Data Stores with Master Slave Replication

Redis contains limited support for master-slave replication which allows you to create a second database that provides a direct real time copy of the data collection on a second system. In addition to providing a "hot spare" or multiple spares for your Redis instance, master-slave systems also allow you to distribute load amongst a group of servers. As long as all write options are applied to the master node, read operations can be distributed to as many slave nodes as required.

To configure master-slave operation, ensure that the following configuration options are applied to the *slave* instance:

{{< file "redis.conf" >}}
slaveof 192.168.10.101 6378

{{< /file >}}

The `slaveof` directive takes two arguments: the first is the IP address of the master node, and the second is the Redis port specified in the master's configuration.

When you restart the slave Redis instance, it will attempt to synchronize its data set to the master, and then propagate the changes. Slave Redis instances can accept slave connections, which allows administrators to distribute the slave-replication load in multi-slave architectures. It's also possible to use a master with less stringent data persistence policies with a slave that keeps a more persistent copy. Master/slave replication creates a number of powerful architectural possibilities that may suit the needs of your application.

The traffic between slave instances and the master instance is not encrypted and does not require authentication in the default configuration. Authentication is available, and can be configured according to the documentation in the `/opt/redis/redis.conf.default` file; however, this is not the default method for securing Redis instances.

The recommended approach to control access to Redis instances includes utilizing iptables and potentially incorporating encryption like an SSH tunnel to guarantee secure traffic. If the connection between slaves and the master node fails, slaves will automatically attempt to reconnect under various conditions. However, for clusters, the promotion of slaves to master status must be managed manually within your application.


