---
slug: Redis server command line interface
description: "Redis provides a powerful command-line tool to view and update its configuration options. Learn how to use the Redis CLI set, get, and write commands to update your configurations."
keywords: ['redis configuration','redis command line','change redis settings']
tags: ['ubuntu', 'debian', 'centos', 'fedora']
published: 2024-07-12
modified_by:
  name: Utho
title: "Configure a Redis Server from the Command Line"
title_meta: "How to Configure a Redis Server from the Command Line"
authors: ["Pawan Kumar"]
---

Redis is an open-source NoSQL database boasting quick transactions and low latency. This guide shows you how to make and adjust settings for your Redis server from the command line.

## Before You Begin

Refer to our Getting Started with Utho guide to set up your Utho's hostname and timezone.

Throughout this guide, we use sudo where applicable. Follow the steps in our How to Secure Your Server guide to create a standard user account, secure SSH access, and disable unnecessary network services.

Update your system.

    - On **Debian** and **Ubuntu**, use the following command:

            sudo apt update && sudo apt upgrade

    - On **AlmaLinux**, **CentOS** (8 or later), or **Fedora**, use the following command:

            sudo dnf upgrade

Refer to our How to Install and Configure Redis guide for instructions on installing a Redis server and command-line interface (CLI). Use the drop-down menu at the top of the page to select your Linux distribution and follow the corresponding steps.

{{< note respectIndent=false >}}
This guide is tailored for non-root users. Commands needing elevated privileges are prefixed with sudo. If you're unfamiliar with the sudo command, refer to the Linux Users and Groups guide for more information.
{{< /note >}}

## How Redis Configurations Work

Redis comes with an extensive configuration file by default, listing all of its configuration options. But Redis can also operate without any explicit configuration, using a default configuration suited for development and testing. However, it is recommended that you adjust the default configuration to suit your needs, especially before you start using Redis in a production setting.

Typically, you can find your Redis instance's configuration file at `/etc/redis/redis.conf` on Debian and Ubuntu and at `/etc/redis.conf` on CentOS and Fedora.

Each setting in Redis is controlled using a configuration directive. A directive is a line with:

- A configuration keyword
- One or more arguments for the configuration

The following file contains an example configuration directive. This example is extracted from a configuration directive featured in the initial guide of our Redis series — Connecting to Redis and Using Redis Databases:

{{< file "/etc/redis/redis.conf" >}}
# [...]

user example-user +@all allkeys on >password

# [...]
{{< /file >}}

This directive uses the keyword `user` to start defining a Redis user. The keyword is then followed by a series of arguments defining the username and a series of ACL (security) rules, including the user's password.

### Available Configuration Directives

The full list of available configuration directives for Redis can be found in the default `redis.conf` file which comes with each Redis installation.

You can also access copies of these files in Redis's configuration documentation. The page provides configuration files for different historical Redis versions, allowing you to choose the version that suits your needs.

## How to Change Configurations via the Command Line

The default route for configuring your Redis instance is through edits to the configuration file covered above. However, Redis comes with several commands to let you work with configuration directives directly from the command line.

There are a few primary reasons for doing so:

- Verifying current configurations on the fly
- Making temporary configuration changes
- Testing particular configurations
- Writing those configurations automatically to the configuration file

The remaining sections of this tutorial walk you through the various configuration commands for the Redis command-line interface (CLI). These commands can help you accomplish the goals listed above and many more when it comes to fine-tuning your Redis server.

You **cannot** manipulate all of the Redis's configuration directives from the command line. The sections below include a few that can be manipulated from the command line.

### Check the Existing Redis Configuration

Use the `CONFIG GET` command to fetch the current value of configuration directives matching a given pattern.

The command below fetches exactly and only the configuration directive you name.

    CONFIG GET bind

{{< output >}}
1) "bind"
2) "127.0.0.1 192.0.2.0"
{{< /output >}}

The command also supports wildcards (`*`). With these, you can fetch all configuration directives matching particular patterns. This feature is especially useful when you want to see all of the settings related to a certain subject, like TLS:

    CONFIG GET tls-*

{{< output >}}
1) "tls-port"
2) "0"
3) "tls-session-cache-size"
4) "20480"
5) "tls-session-cache-timeout"
6) "300"
7) "tls-cluster"
8) "no"
9) "tls-replication"
10) "no"
[...]
{{< /output >}}

You can use the wildcard by itself to fetch your Redis server's current (in-memory) configuration directives.

    CONFIG GET *

{{< output >}}
1) "rdbchecksum"
2) "yes"
3) "daemonize"
4) "yes"
5) "io-threads-do-reads"
6) "no"
7) "lua-replicate-commands"
8) "yes"
9) "always-show-logo"
10) "no"
[...]
326) "normal 0 0 0 slave 268435456 67108864 60 pubsub 33554432 8388608 60"
327) "unixsocketperm"
328) "0"
329) "slaveof"
330) ""
331) "notify-keyspace-events"
332) ""
333) "bind"
334) "127.0.0.1 192.0.2.0"
335) "oom-score-adj-values"
336) "0 200 800"
{{< /output >}}

Using `CONFIG GET *` is especially useful since it shows all of the configuration directives supported for fetching, setting, and writing from the command line.

### Make Changes to the Redis Configuration

Use the `CONFIG SET` command to make or alter a configuration directive.

The command below takes the name of a directive followed by the directive's argument. Here, the `repl-timeout` directive is set to `70` seconds, from its default of `60` seconds.

    CONFIG SET repl-timeout 70

Multiple arguments, or arguments with spaces, can be handled using quotes. The example below adds an address template to the `bind` directive shown above. The added template is the default for loop-back connections on Redis.

    CONFIG SET bind "127.0.0.1 192.0.2.0 -::1"

Configuration changes made in this way take effect immediately. For that reason, `CONFIG SET` works extraordinarily well for testing settings for your Redis server.

Further, configurations made with `CONFIG SET` are in memory. Resetting any changes you make only requires restarting your Redis server, which you can typically do with the following command:

    sudo systemctl restart redis-server

Or:

    sudo systemctl restart redis

### Write Redis Configuration Changes

After testing some settings with `CONFIG SET`, you may want to make those settings persistent. You can do that using the `CONFIG REWRITE` command.

This command has Redis write your in-memory configuration directives, like those created or changed with `CONFIG SET`, to the configuration file.

The command does not take any arguments, so you can execute it using the following command:

    CONFIG REWRITE

The command attempts to preserve, as much as is feasible, the original structure of the configuration file while writing only the necessary lines to it. This means that:

- Modifications to existing directives are typically written in their original places in the file
- Directives created with default values do not actually get written to the configuration file (default values do not need to be explicitly stated)
- New directives are added to the end of the configuration file

## Conclusion

With the tools provided in this tutorial, you're equipped to manipulate Redis configurations directly from the command line. These tools are particularly useful for quickly testing different settings.

For further insights into Redis and mastering Redis databases, explore our comprehensive series of guides. They cover topics ranging from connecting to a remote Redis server to utilizing sorted sets in Redis databases.
