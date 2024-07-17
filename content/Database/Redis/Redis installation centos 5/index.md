---
slug: Redis installation centos 5
deprecated: true
description: 'This guide shows how to deploy applications that depend on the high performance and highly flexible key-value store Redis database on CentOS 5.'
keywords: ["redis centos 5", "redis", "nosql", "database", "key-value store"]
modified: 2024-07-11
modified_by:
  name: Utho
published: 2024-07-11
title: Redis on CentOS 5
relations:
    platform:
        key: how-to-install-redis
        keywords:
            - distribution: CentOS 5
tags: ["nosql","database","centos"]
authors: ["Pawan Kumar"]
---

more important than persistence and absolute data integrity. As part of the "NoSQL" movement, Redis is a popular tool for developers needing rapid data access. This guide provides instructions for deploying the Redis server and offers best practices for maintaining Redis instances.

Before starting, ensure you have completed the steps in our Setting Up and Securing a Compute Instance guide. If you're new to Linux server administration, check out our Introduction to Linux Concepts, Beginner's Guide, and Linux System Administration Basics guides.

## Install Redis

### Prepare System for Redis

Issue the following command to update your system's package repositories and ensure that all installed packages are up to date:

    yum update

Install required prerequisites with the following command:

    yum install make gcc wget

This guide only provides instructions for installing and managing Redis itself. The application you deploy likely requires additional infrastructure, dependencies, and utilities.

### Download and Compile Software

Begin the installation process by issuing the following sequence of commands to download the software and prepare it for use:

    cd /opt/
    mkdir /opt/redis
    wget http://redis.googlecode.com/files/redis-2.2.2.tar.gz
    tar -zxvf /opt/redis-2.2.2.tar.gz
    cd /opt/redis-2.2.2/
    make

This command will download and compile version 2.2.2 of Redis. Always check the Redis official website to ensure you are downloading the latest version. Using the most up-to-date version helps avoid security flaws, bugs, and ensures access to the latest features. Remember, when you manually compile software, you must keep it updated yourself, without relying on your system's package management tools.

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

When running software compiled or installed directly from sources provided by upstream developers, you are responsible for monitoring updates, bug fixes, and security issues. After becoming aware of releases and potential issues, update your software to resolve flaws and prevent possible system compromise. Monitoring releases and maintaining up to date versions of all software is crucial for the security and integrity of a system.

Please stay informed by monitoring the Redis Project mailing lists for updates. This ensures you're aware of all software updates and can upgrade or apply patches and recompile as necessary.

When upstream sources offer new releases, repeat the instructions for installing Redis and recompile your software when needed. These practices are crucial for the ongoing security and functioning of your system.

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
    wget -O init-rpm.sh http://www.Utho.com/docs/assets/631-redis-init-rpm.sh
    useradd -M -r --home-dir /opt/redis redis
    mv /opt/init-rpm.sh /etc/init.d/redis
    chmod +x /etc/init.d/redis
    chown -R redis:redis /opt/redis
    touch /var/log/redis.log
    chown redis:redis /var/log/redis.log
    chkconfig --add redis
    chkconfig redis on

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

You might consider running this command regularly, possibly in a cron job, to prevent the transaction journal from growing excessively. The bgrewriteaof operation is non-destructive and handles failures gracefully.

## Distributed Data Stores with Master Slave Replication

Redis contains limited support for master-slave replication which allows you to create a second database that provides a direct real time copy of the data collection on a second system. In addition to providing a "hot spare" or multiple spares for your Redis instance, master-slave systems also allow you to distribute load amongst a group of servers. As long as all write options are applied to the master node, read operations can be distributed to as many slave nodes as required.

To configure master-slave operation, ensure that the following configuration options are applied to the *slave* instance:

{{< file "redis.conf" >}}
slaveof 192.168.10.101 6379

{{< /file >}}

The `slaveof` directive takes two arguments: the first is the IP address of the master node, and the second is the Redis port specified in the master's configuration.

When you restart the slave Redis instance, it will attempt to synchronize its data set to the master, and then propagate the changes. Slave Redis instances can accept slave connections, which allows administrators to distribute the slave-replication load in multi-slave architectures. It's also possible to use a master with less stringent data persistence policies with a slave that keeps a more persistent copy. Master/slave replication creates a number of powerful architectural possibilities that may suit the needs of your application.

The traffic between slave instances and the master instance is not encrypted and does not require authentication in the default configuration. Authentication is available, and can be configured according to the documentation in the `/opt/redis/redis.conf.default` file; however, this is not the default method for securing Redis instances.

The recommended approach to manage access to Redis instances is by using iptables for network traffic control and possibly implementing encryption like an SSH tunnel for enhanced security. In case of network failures, slaves will attempt to reconnect to the master node autonomously under various conditions. However, clusters do not automatically promote slave nodes to master status; this management must be handled within your application.







