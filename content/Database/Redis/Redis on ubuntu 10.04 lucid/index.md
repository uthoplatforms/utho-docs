---
slug: Redis on ubuntu 10.04 lucid
deprecated: true
description: 'This guide shows how to deploy applications that depend on the high performance and highly flexible key-value store Redis database on Ubuntu 10.04 "Lucid".'
keywords: ["redis ubuntu 10.04", "redis lucid", "nosql", "database", "key-value store"]
modified: 2024-07-12
modified_by:
  name: Utho
published: 2010-07-28
title: 'Redis on Ubuntu 10.04 (Lucid)'
relations:
    platform:
        key: how-to-install-redis
        keywords:
            - distribution: Ubuntu 10.04
tags: ["ubuntu","database","nosql"]
authors: ["Pawan Kumar"]
---

Redis is a high-performance persistent key-value store designed for applications prioritizing speed and flexibility over absolute data integrity. It aligns with the principles of the "NoSQL" movement and is a favored tool for developers of certain applications. This document offers instructions for deploying the Redis server and provides an overview of best practices for maintaining Redis instances.

Before proceeding with the Redis installation guide, ensure you have completed the steps outlined in our Setting Up and Securing a Compute Instance. For those new to Linux systems administration, we recommend reviewing our using Linux series, particularly the administration basics guide.

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

To compile Redis version 2.2.2, download it from the Redis upstream project source and verify it's the latest version available. Using the most current software version is crucial to prevent security vulnerabilities and bugs, and to access new features. When manually compiling software, ensure your system runs the latest version, as package management tools won't handle updates for manually compiled software.

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

When using software compiled or installed directly from upstream sources, it's your responsibility to stay vigilant about updates, bug fixes, and security vulnerabilities. Regularly monitor releases and stay informed about potential issues to promptly address them and mitigate any risks to your system.

To stay updated with Redis, subscribe to the Redis Project mailing lists to receive notifications about new updates and patches. When new releases are available, follow the installation instructions again and recompile your software as necessary. These practices are essential for maintaining the security and optimal performance of your system over time.

## Managing Redis Instances

### Running a Redis Datastore

In the default configuration, Redis runs in an interactive mode after being invoked at the command line. To start Redis in this manner issue the following command:

    /opt/redis/redis-server /opt/redis/redis.conf.default

Now, you can interact with Redis using language-specific bindings or utilize the built-in command-line interface to communicate with the Redis instance. Just prefix any Redis command with the following string:

    /opt/redis/redis-cli

While running the Redis instance in this configuration is useful for testing and initial deployment, production deployments may have better results by creating a dedicated and unprivileged system user for the Redis instance and controlling Redis using an "init" script. This section covers the creation of an init script and strategies for managing production Redis instances.

### Deploy Init Script

Issue the following sequence of commands to download a basic init script, create a dedicated system user, mark this file as executable, and ensure that the Redis process will start following the next boot cycle:

    cd /opt/
    wget -O init-deb.sh http://www.utho.com/docs/assets/629-redis-init-deb.sh
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

Consider scheduling this command regularly, possibly using a cron job, to prevent the transaction journal from growing excessively. `bgrewriteaof` is a non-destructive operation that handles failures gracefully.

## Distributed Data Stores with Master Slave Replication

Redis contains limited support for master-slave replication which allows you to create a second database that provides a direct real time copy of the data collection on a second system. In addition to providing a "hot spare" or multiple spares for your Redis instance, master-slave systems also allow you to distribute load amongst a group of servers. As long as all write options are applied to the master node, read operations can be distributed to as many slave nodes as required.

To configure master-slave operation, ensure that the following configuration options are applied to the *slave* instance:

{{< file "redis.conf" >}}
slaveof 192.168.10.101 6378

{{< /file >}}


The `slaveof` directive requires two arguments: the IP address of the master node and the Redis port specified in the master's configuration.

Upon restarting the slave Redis instance, it will synchronize its dataset with the master and propagate any changes. Slave Redis instances can accept connections from other slaves, enabling administrators to distribute replication load in multi-slave architectures. This setup allows using a master with less strict data persistence policies while maintaining a more persistent copy on the slave. Master/slave replication offers various architectural possibilities that may align with your application's requirements.

In the default configuration, traffic between slave instances and the master is unencrypted and does not require authentication. Although authentication can be configured in /opt/redis/redis.conf.default, it's not the default method for securing Redis instances.

For secure access control, it's recommended to use tools like iptables and potentially encryption such as an SSH tunnel. Slaves will automatically attempt to reconnect to the master if the connection fails under various conditions. However, promoting members from slave to master status in clusters requires manual management within your application.




