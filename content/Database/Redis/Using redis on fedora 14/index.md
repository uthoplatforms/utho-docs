---
slug: Using redis on fedora 14
deprecated: true
description: 'This guide shows how to deploy applications that depend on the high performance and highly flexible key-value store Redis database on Fedora 14.'
keywords: ["redis fedora 14", "redis", "nosql", "database", "key-value store"]
modified: 2024-07-12
modified_by:
  name: Utho
published: 2024-07-12
title: Redis on Fedora 14
relations:
    platform:
        key: how-to-install-redis
        keywords:
            - distribution: Fedora 14
tags: ["nosql","database","fedora"]
authors: ["Pawan Kumar"]
---

Redis is a high performance persistent key-value store, and is intended as a datastore solution for applications where performance and flexibility are more critical than persistence and absolute data integrity. As such, Redis may be considered a participant in the "NoSQL" movement and is an attractive tool for developers of some kinds of applications. This document provides both instructions for deploying the Redis server and an overview of best practices for maintaining Redis instances.

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

To download and compile Redis version 2.2.2, refer to the Redis upstream project source to ensure you're obtaining the latest version available. Using the most current software version is crucial to mitigate security vulnerabilities and bugs, and to leverage the latest features. When manually compiling software, it's your responsibility to ensure your system is up to date, as you won't have the support of your system's package management tools.

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

{{< file "/opt/redis/redis.conf" >}}
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

dir /opt/redis/
appendonly no

glueoutputbuf yes

{{< /file >}}

Most of the values in this configuration mirror the default Redis configuration. However, this configuration configures Redis to run in a daemon mode bound only to the local network interface. You may want to change these values depending on the needs of your application.

## Monitor for Software Updates and Security Notices

When using software compiled or installed directly from upstream sources, it's your responsibility to stay informed about updates, bug fixes, and security vulnerabilities. Regularly check for new releases and potential issues, and promptly update your software to address any flaws and prevent potential system vulnerabilities. Keeping all software up to date is essential for maintaining the security and reliability of your system.

To stay informed about updates to Redis, monitor the Redis Project mailing lists regularly. This ensures you're aware of all software updates and can upgrade or apply patches and recompile as necessary.

Whenever new releases are available from upstream sources, follow the installation instructions again for Redis and recompile your software accordingly. These practices are critical for ensuring the ongoing security and functionality of your system.

## Managing Redis Instances

### Running a Redis Datastore

In the default configuration, Redis runs in an interactive mode after being invoked at the command line. To start Redis in this manner issue the following command:

    /opt/redis/redis-server /opt/redis/redis.conf.default

You can now interact with Redis using language-specific bindings or the built-in command-line interface to access the Redis instance. Simply prefix any Redis command with the following string:

    /opt/redis/redis-cli

While running the Redis instance in this configuration is useful for testing and initial deployment, production deployments may have better results by creating a dedicated and unprivileged system user for the Redis instance and controlling Redis using an "init" script. This section covers the creation of an init script and strategies for managing production Redis instances.

### Deploy Init Script

Issue the following sequence of commands to download a basic init script, create a dedicated system user, mark this file as executable, and ensure that the Redis process will start following the next boot cycle:

    cd /opt
    wget -O init-rpm.sh http://www.utho.com/docs/assets/632-redis-init-rpm.sh
    useradd -M -r --home-dir /opt/redis redis
    mv /opt/init-rpm.sh /etc/init.d/redis
    chmod +x /etc/rc.d/init.d/redis /etc/init.d/redis
    chown -R redis:redis /opt/redis
    touch /var/log/redis.log
    chown redis:redis /var/log/redis.log
    chkconfig --add redis
    chkconfig redis on

You may use the following commands to start and stop the Redis instance:

    /etc/init.d/redis start
    /etc/init.d/redis stop

## Managing Datastore Persistence

Redis is not necessarily intended to provide a completely consistent and fault tolerant data storage layer, and in the default configuration there are some conditions that may cause your data store to lose up to 60 seconds of the most recent data. Make sure you understand the risks associated and the potential impact that this kind of data loss may have on your application before deploying Redis.

If persistence is a major issue for your application, it is possible to use Redis in a transaction journaling mode that provides greater data resilience at the expense of some performance.

To use this mode, ensure that the following values are set in `redis.conf`:

{{< file "/opt/redis/redis.conf" >}}
appendonly yes
appendfsync everysec

{{< /file >}}


The first directive enables the journaled "append only file" mode, while the second directive forces Redis to write the journal to the disk every second. The `appendfsync` directive also accepts the argument `always` to force writes after every operation which provides maximum durability, or `never` which allows the operating system to control when data is written to disk which is less reliable.

After applying these configuration changes, restart Redis. All modifications to the data store will be logged. Every time Redis restarts it will "replay" all transactions and ensure that your data store matches the log. However, in some conditions this may render the data-store unusable while the database restores. To avoid this condition, issue the following command to the Redis command line:

    /opt/redis/redis-cli bgrewriteaof

To prevent the transaction journal from expanding exponentially, consider scheduling this command regularly, such as in a cron job. The bgrewriteaof command is non-destructive and handles failures gracefully.

## Distributed Data Stores with Master Slave Replication

Redis contains limited support for master-slave replication which allows you to create a second database that provides a direct real time copy of the data collection on a second system. In addition to providing a "hot spare" or multiple spares for your Redis instance, master-slave systems also allow you to distribute load amongst a group of servers. As long as all write options are applied to the master node, read operations can be distributed to as many slave nodes as required.

To configure master-slave operation, ensure that the following configuration options are applied to the *slave* instance:

{{< file "redis.conf" >}}
slaveof 192.168.10.101 6379

{{< /file >}}

The slaveof directive requires two arguments: the IP address of the master node and the Redis port specified in the master's configuration.

Upon restarting the slave Redis instance, it initiates synchronization with the master to replicate its dataset and propagate subsequent changes. Slave Redis instances can accept multiple slave connections, distributing replication load in multi-slave architectures. This setup allows for flexible data persistence strategies, such as pairing a master with less stringent policies with a slave maintaining a more persistent copy. Master/slave replication offers versatile architectural options that can align with your application's requirements.

By default, traffic between slave instances and the master is unencrypted and does not require authentication. While authentication can be configured in /opt/redis/redis.conf.default, it's not enabled by default for securing Redis instances.

For secure access control, it's recommended to use tools like iptables and consider encryption methods such as SSH tunnels to protect traffic. In case of link failure, slaves attempt automatic reconnection to the master. However, automatic promotion of slaves to master status requires manual intervention within your application's cluster management.




