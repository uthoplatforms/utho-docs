---
slug: "Setting Up and Configuring a Redis Cluster on Ubuntu 16.04: A Step-by-Step Guide"
description: 'Learn how to establish a high-performance Redis cluster with ease. Follow this comprehensive guide, which utilizes three Utho servers and entails elevating a replica to assume the role of a master node.'
keywords: ["redis", "redis cluster installation", "data store", "cache", "sharding", "redis cluster setup", "redis cluster set up", "ubuntu"]
tags: ["nosql","database","ubuntu","redis"]
modified: 2024-03-18
modified_by:
  name: Pawan Kumar
published: 2024-03-18
title: "Comprehensive Guide: Setting Up and Configuring a Redis Cluster on Ubuntu 16.04"
authors: ["Pawan Kumar"]
---

Redis is a powerful NoSQL database designed for handling large amounts of data quickly. Its clusters are widely used for tasks like caching and queuing due to their ability to scale up rapidly while maintaining speed. In this guide, we'll walk you through setting up a Redis cluster using three Utho servers, showcasing a technique called sharding. Additionally, we'll show you how to promote a replica within the cluster to serve as a master node, ensuring reliability in case of a failure.

Redis excels at lightning-fast operations like caching and queuing, thanks to its in-memory storage capability. By setting up a cluster, you can enhance Redis's reliability by minimizing potential points of failure.

Before diving in, it's a good idea to familiarize yourself with the following concepts:

* [Firewall settings using iptables or ufw](/docs/guides/configure-firewall-with-ufw/)
* [Getting Started with VLANs](/docs/products/networking/vlans/get-started/)
* [Master-Replicas Replication](/docs/guides/how-to-install-a-redis-server-on-ubuntu-or-debian8/)

### Choosing Between Redis Sentinel and Redis Cluster: Which One Fits Your Needs?

Choosing between a Redis cluster and Redis Sentinel depends on your specific environment and requirements.

Redis Sentinel offers automatic failover by promoting a replica node when a master node fails. However, it operates with just one master node handling all data traffic continuously. Essentially, Sentinel serves as a backup for Redis servers. If you're operating in a smaller environment, Sentinel might be the better choice for you. Check out Redis's [documentation for more information](https://redis.io/topics/sentinel) for further details.

On the other hand, a Redis cluster offers similar backup protection as Sentinel but differs in its approach by spreading data traffic across all nodes. This means that in high-traffic environments, a Redis cluster can better handle the demands of traffic distribution compared to pushing it all through a single master node.

## Installing Redis on Every Utho Server

Depending on your Linux distribution, you might be able to install Redis using a package manager. Keep in mind that Redis version 3.0 or higher is required for clustering. Below are steps for installing the most recent stable version of Redis.

1.  Make sure your system is up to date, and then proceed to install the necessary dependencies.

        sudo apt-get update && sudo apt-get upgrade
        sudo apt install make gcc libc6-dev tcl

    {{< note respectIndent=false >}}

Alternatively, you have the option to install the "build-essential" meta-package, which will automatically install the dependencies required for Redis.

        sudo apt install build-essential tcl
{{< /note >}}

2.  Retrieve the latest stable branch from the documentation and proceed to extract it.

        wget http://download.redis.io/redis-stable.tar.gz
        tar xvzf redis-stable.tar.gz
        cd redis-stable
        sudo make install

3.  Confirm that the installation was successful by executing the following command:

        make test

 Upon successful installation, the console will display:

        >\o/ All tests passed without errors!

4.  Repeat the installation process for each server that is intended to be part of the cluster.

## Configuring Redis Masters and Replicas: Setting Up the Master-Replica Architecture

In this guide, we'll manually establish connections between the masters and replicas across three Uthos. You may find it beneficial to utilize [tmux](/docs/guides/persistent-terminal-sessions-with-tmux/) for efficient management of multiple terminal windows.

The setup described here requires a minimum of six nodes arranged in the following topology:

In this setup, three Uthos are utilized, each running two instances of Redis server: one acting as a master node and the other as a replica node. Initially, it's crucial to ensure that each host functions independently. Additional nodes can be added as necessary to meet uptime requirements.

1.  Access **Server 1** via SSH. Once logged in, go to the `redis-stable/` directory and duplicate the redis.conf file. Note that configuration file names in this guide adhere to the naming convention illustrated in the figure above:

        cp redis.conf a_master.conf
        cp redis.conf c_replica.conf

2.  Within the `a_master.conf file`, append the following lines at the end of the document. Ensure to replace `192.1.2.2` with the IP address of your Utho:

        {{< file "~/redis-stable/a_master.conf" >}}
        bind 127.0.0.1 192.0.2.1
        protected-mode no
        port 6379
        pidfile /var/run/redis_6379.pid
        cluster-enabled yes
        cluster-config-file nodes-6379.conf
        cluster-node-timeout 15000
        {{< /file >}}

{{< note type="alert" respectIndent=false >}}

Without implementing additional security measures, your Redis nodes might be accessible over the public internet through their respective public IP addresses. This could potentially leave your nodes vulnerable to automated attacks. For further details, refer to the following resources: [Redis Security](https://redis.io/topics/security).

To safeguard your Redis cluster from external threats, contemplate leveraging [Cloud Firewalls](/docs/products/networking/cloud-firewall/) or [VLANs](/docs/products/networking/vlans/) to restrict access to your cluster Uthos.

When employing VLANs, substitute `192.1.2.2` with the corresponding Utho's IPAM address in each configuration file.
{{< /note >}}

3.  In the `c_replica.conf` file, the configuration remains largely similar, with the exception of updating the port number. Later on, `redis-cli` will be employed to configure this as a replica for the relevant master.

        {{< file "~/redis-stable/c_replica.conf" >}}
        bind 127.0.0.1 192.0.2.1
        protected-mode no
        port 6381
        pidfile /var/run/redis_6381.pid
        cluster-enabled yes
        cluster-config-file nodes-6381.conf
        cluster-node-timeout 15000
        {{< /file >}}

4.  Repeat this procedure for the remaining two Uthos, ensuring to specify the port numbers for all master-replica pairs accurately. The port numbers in this guide range from 6379 to 6381.

    | Server | Master | Master Filename | Replica | Replica Filename |
    |:-------|:-------|:----------------|:--------|:-----------------|
    |    1   |  6379  |  a_master.conf  | 6381    | c_replica.conf   |
    |    2   |  6380  |  b_master.conf  | 6379    | a_replica.conf   |
    |    3   |  6381  |  c_master.conf  | 6380    | b_replica.conf   |

    {{< note respectIndent=false >}}
For each node in the Redis cluster, it's essential to have the defined port open as well as the port plus 10000. For instance, **Server** 1 should have TCP ports 6379 and 16379 open for the master node, and TCP ports 6381 and 16381 open for the replica node. Make sure that iptables or ufw is appropriately configured for each server.
{{< /note >}}

## Linking Redis Masters and Replicas: Establishing Communication between Nodes

To enable master-replica replication across three nodes, run two instances of a Redis server on each node.

1.  Log in to **Server 1** via SSH and initiate the two Redis instances, ideally within separate `tmux` sessions or using another method that ensures the instances persist even after disconnecting from the Utho.

        redis-server ~/redis-stable/a_master.conf
        redis-server ~/redis-stable/c_replica.conf

2.  Replace `a_master.conf` and `c_replica.conf` with the corresponding configuration files for the remaining two servers. Ensure that all nodes start in cluster mode.

      {{< output >}}
.               _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 6.2.3 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in cluster mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 26533
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           https://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

{{< /output >}}

## Establishing a Redis Cluster Using redis-cli

Now, each Utho hosts two separate master nodes. To set up and manage your cluster, the Redis installation includes a `redis-cli` tool.

1.  Login to Server 1 and use this command to create a Redis cluster with your three master nodes:

    {{< note respectIndent=false >}}
If you're using a [VLAN](/docs/products/networking/vlans/get-started/), make sure to utilize the IPAM address of each Utho.
{{< /note >}}

        redis-cli --cluster create \
          $SERVER_1_IP_ADDRESS:6379 \
          $SERVER_2_IP_ADDRESS:6380 \
          $SERVER_3_IP_ADDRESS:6381

2.  Confirm the configuration with three masters. A successfully set up cluster will display the following message:

        >>>Creating cluster
        >>>Performing hash slots allocation on 3 nodes...
        Can I set the above configuration? (type 'yes' to accept): yes
        >>> Nodes configuration updated
        >>> Assign a different config epoch to each node
        >>> Sending CLUSTER MEET messages to join the cluster
        Waiting for the cluster to join.
        [OK] All nodes agree about slots configuration.
        >>> Check for open slots...
        >>> Check slots coverage...
        [OK] All 16384 slots covered.

3.  To view all the current nodes connected to the cluster, utilize the `redis-cli` tool. Use the `-c` flag to specify the connection to the cluster.

        redis-cli cluster nodes

This command returns a list of nodes currently present in the cluster, identified by their ID, along with any replicas if they exist.

## Adding Redis Cluster Replicas: Expanding the Cluster with Replica Nodes

You can utilize the `redis-cli` tool to add new nodes to the cluster. With `redis-cli`, you can manually link the remaining replica nodes to their corresponding master nodes.

1.  Add replica node "C" on **Server 1** to the cluster and connect it to master node "C" on **Server 3** using the following command, replacing the ID of master node "C" obtained from the `redis-cli` `cluster nodes` command:

          redis-cli --cluster add-node \
          $SERVER_1_IP_ADDRESS:6381 \
          $SERVER_3_IP_ADDRESS:6381 \
          --cluster-slave \
          --cluster-master-id $MASTER_ID_C

      The output that follows should be:

        >>> Adding node $SERVER_1_IP_ADDRESS:6381 to cluster $SERVER_3_IP_ADDRESS:6381
        >>> Performing Cluster Check (using node $SERVER_3_IP_ADDRESS:6381)
        M: $MASTER_ID_C $SERVER_3_IP_ADDRESS:6381
           slots:10923-16383 (5461 slots) master
        M: $MASTER_ID_A $SERVER_1_IP_ADDRESS:6379
           slots:0-5460 (5461 slots) master
        M: $MASTER_ID_B $SERVER_2_IP_ADDRESS:6380
           slots:5461-10922 (5462 slots) master
        [OK] All nodes agree about slots configuration.
        >>> Check for open slots...
        >>> Check slots coverage...
        [OK] All 16384 slots covered.
        >>> Send CLUSTER MEET to node $SERVER_1_IP_ADDRESS:6381 to make it join the cluster.
        Waiting for the cluster to join

        >>> Configure node as replica of $SERVER_3_IP_ADDRESS:6381.

2.  Proceed to repeat the process for the remaining two nodes.

        redis-cli --cluster add-node \
          $SERVER_2_IP_ADDRESS:6379 \
          $SERVER_1_IP_ADDRESS:6379 \
          --cluster-slave \
          --cluster-master-id $MASTER_ID_A

        redis-cli --cluster add-node \
          $SERVER_3_IP_ADDRESS:6380 \
          $SERVER_2_IP_ADDRESS:6380 \
          --cluster-slave \
          --cluster-master-id $MASTER_ID_B

## Add Key-Value Pairs and Sharding

The command line interface lets you both `set` and `get` keys, as well as gather details about the cluster. You can connect to any of the master nodes from your local computer to explore Redis cluster features.

1.  If necessary, reinstall Redis on your local computer. Ensure that the firewall settings permit communication with the master nodes.

        redis-cli -c -h $SERVER_1_IP_ADDRESS -p 6379

2.  Utilize the `CLUSTER INFO` command to view details about the cluster's status, including its size, hash slots, and any failures.

        $SERVER_1_IP_ADDRESS:6379>CLUSTER INFO
        cluster_state:ok
        cluster_slots_assigned:16384
        cluster_slots_ok:16384
        cluster_slots_pfail:0
        cluster_slots_fail:0
        cluster_known_nodes:6
        cluster_size:3
        cluster_current_epoch:6
        cluster_my_epoch:1
        cluster_stats_messages_ping_sent:8375
        cluster_stats_messages_pong_sent:9028
        cluster_stats_messages_meet_sent:1
        cluster_stats_messages_sent:17404
        cluster_stats_messages_ping_received:9022
        cluster_stats_messages_pong_received:8376
        cluster_stats_messages_meet_received:6
        cluster_stats_messages_received:17404

3.  To evaluate master-replica replication, running the command INFO replication yields insights into the status of the replica.

        $SERVER_1_IP_ADDRESS:6379>INFO replication
        role:master
        connected_slaves:1
        slave0:ip=$SERVER_1_IP_ADDRESS,port=6381,state=online,offset=213355,lag=1
        master_replid:cd2e27cba094f2e7ed38b0313dcd6a979ab29b7a
        master_replid2:0000000000000000000000000000000000000000
        master_repl_offset:213355
        second_repl_offset:-1
        repl_backlog_active:1
        repl_backlog_size:1048576
        repl_backlog_first_byte_offset:197313
        repl_backlog_histlen:16043

4.  To demonstrate sharding, you can make a few sets of key-value pairs. When you assign a key, the value will be sent to one of the hash slots across the three master nodes.

        $SERVER_1_IP_ADDRESS:6379> SET John Adams
        -> Redirected to slot [6852] located at $SERVER_2_IP_ADDRESS:6380
        OK
        $SERVER_2_IP_ADDRESS:6380> SET James Madison
        -> Redirected to slot [2237] located at $SERVER_1_IP_ADDRESS:6379
        OK
        $SERVER_1_IP_ADDRESS:6379> SET Andrew Jackson
        -> Redirected to slot [15768] located at $SERVER_3_IP_ADDRESS:6381
        OK
        $SERVER_3_IP_ADDRESS:6381> GET John
        -> Redirected to slot [6852] located at $SERVER_2_IP_ADDRESS:6380
        "Adams"
        $SERVER_2_IP_ADDRESS:6380>

## Elevate a Redis Replica to Master Status

According to the existing topology, the cluster will stay operational even if one of the Uthos fails. At that moment, you can anticipate a replica to be promoted to a master with the replicated data.

1.  Insert a key-value pair.

        $SERVER_1_IP_ADDRESS:6379> SET foo bar
        -> Redirected to slot [12182] located at $SERVER_3_IP_ADDRESS:6381
        OK
        $SERVER_3_IP_ADDRESS:6381> GET foo
        "bar"

The key `foo`is initially added to a master on **Server 3** and then replicated to a replica on **Server 1**.

2.  If **Server 3** experiences downtime, the replica on **Server 1** will transition into a master, ensuring the cluster's continuity online.

        $SERVER_1_IP_ADDRESS:6379> CLUSTER NODES
        $REPLICA_ID_B $SERVER_3_IP_ADDRESS:6380@16380 slave,fail $MASTER_ID_B 1502722149010 1502722147000 6 connected
        $REPLICA_ID_B $SERVER_2_IP_ADDRESS:6379@16379 slave $MASTER_ID_A 0 1502722242000 5 connected
        $REPLICA_ID_C_PROMOTED $SERVER_1_IP_ADDRESS:6381@16381 master - 0 1502722241651 7 connected 10923-16383
        $MASTER_ID_B $SERVER_2_IP_ADDRESS:6380@16380 master - 0 1502722242654 2 connected 5461-10922
        $MASTER_ID_C $SERVER_3_IP_ADDRESS:6381@16381 master,fail - 1502722149010 1502722145402 3 connected
        $MASTER_ID_A $SERVER_1_IP_ADDRESS:6379@16379 myself,master - 0 1502722241000 1 connected 0-5460

3.  A key that was previously situated in a hash slot on **Server 3** is now housed on **Server 1** along with its corresponding value pair.

        $SERVER_1_IP_ADDRESS:6379> GET foo
        -> Redirected to slot [12182] located at $SERVER_1_IP_ADDRESS:6381
        "bar"

  This document does not cover extra features like adding more nodes, setting up multiple replicas, or reshaping the data distribution (resharding). For detailed instructions on these tasks, refer to the official Redis documentation.
