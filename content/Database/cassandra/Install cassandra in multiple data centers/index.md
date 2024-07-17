---
slug: Install cassandra in multiple data centers
title: "How to Install Cassandra Across Multiple Data Centers"
description: 'This guide introduces the Cassandra distributed database and explains how to install, configure, and use it.'
keywords: ['install Cassandra','configure Cassandra','Cassandra CQL','create keyspace Cassandra']
authors: ["Pawan Kumar"]
published: 2024-07-08
modified_by:
  name: Utho

Apache Cassandra is a distributed database built for low-latency replication across geographically dispersed data centers. It enables users to specify the number of replicas to store in each center, ensuring high resilience. This guide offers an overview of Cassandra, covering installation, configuration, keyspace definition, and the addition of tables and data.

## What is Cassandra?

Cassandra is an open-source NoSQL database originally developed by Facebook as an alternative to traditional relational database management systems (RDBMS). NoSQL databases like Cassandra offer a flexible data modeling approach without the rigid tabular structure of RDBMS, allowing for faster throughput and scalability. However, they may sacrifice some data consistency.

Cassandra's architecture minimizes latency and supports true real-time capabilities, crucial for maintaining high client satisfaction and efficient transaction processing, such as fraud detection. It excels in scalability, robustness, and responsiveness, efficiently replicating data and maintaining performance under heavy user loads.

In a cluster-based architecture, Cassandra operates without a master node, distributing data evenly across nodes using a hash function for replication. This decentralized approach ensures high availability; individual node failures do not impact the entire cluster. Cassandra automatically manages data consistency, node additions, and removals.

Clusters can span multiple geographically distributed data centers worldwide, enabling data proximity to end-users and reducing latency. Data centers within a cluster consist of multiple racks, each potentially spanning different physical locations but sharing the same logical IP address. This setup ensures resilience and geographic redundancy, allowing seamless integration and replication of data across centers.

Cassandra's versatility and distributed architecture make it ideal for applications requiring high availability, low latency, and scalable data management across diverse geographic locations.

### Data Management in Cassandra

Cassandra utilizes a simplified variant of SQL known as the Cassandra Query Language (CQL). Similar to other NoSQL languages, CQL is designed for ease of use compared to traditional SQL. You can access the Cassandra CQL shell using the cqlsh command.

At its core, CQL organizes data into keyspaces, which function similarly to databases in relational database management systems (RDBMS). A keyspace in Cassandra defines replication settings for all its associated tables. Unlike RDBMS tables, Cassandra tables store data as lists of key-value pairs, which can be nested to multiple levels and are typically denormalized.

Cassandra requires a different approach to data modeling compared to an RDBMS application. To efficiently model tables and columns, follow these principles:

- Map each Cassandra table to specific queries.
- Include all necessary columns within each table to support these queries.
- Consider duplicating data across different tables if it optimizes query performance, prioritizing speed over data normalization.
- Ensure each table has a primary key to uniquely identify entries, enabling efficient data partitioning across Cassandra nodes.

Before deploying Cassandra in production, it's essential to grasp its architecture. For detailed information, refer to the Cassandra Documentation. Review the Data Modeling Guidelines for guidance on database design.

### Advantages and Disadvantages of Cassandra

In addition to data distribution and replication, there are many advantages and use cases for Cassandra:

- Capable of handling sudden increases in traffic smoothly.
- Offers highly adaptable data management capabilities.
- Ideal for scenarios requiring redundancy and enhanced reliability.
- Significantly reduces latency across diverse geographic locations.
- Facilitates disaster recovery and resilience against entire data center failures.
- Includes advanced analytics and logging functionalities.
- Efficiently manages sparse data where not all columns are present in every record.

Cassandra may not be suitable for small or infrequently accessed datasets where replication and high availability aren't critical, as it adds unnecessary complexity and overhead. It lacks support for table joins, making it unsuitable for heavily normalized data. Additionally, Cassandra's complexity may require extensive learning and tuning before deployment in production environments.

## Before You Begin

If you haven't already, create a Utho account and set up a Compute Instance. Refer to our Getting Started with Utho and Creating a Compute Instance guides.

Follow our Setting Up and Securing a Compute Instance guide to update your system. You can also configure the timezone, hostname, create a restricted user account, and enhance SSH security.

Each data center should include at least two nodes. Cassandra recommends a minimum of 4GB of memory for all nodes.

{{< note >}}
This guide assumes you are using a non-root user. Commands requiring administrative privileges are prefixed with sudo. If you're unfamiliar with the sudo command, refer to the Users and Groups guide.
{{< /note >}}

## How to Install Cassandra

This guide is primarily for Ubuntu 22.04 LTS users but is generally applicable to other Ubuntu releases and various Linux distributions. It's important for all nodes in the cluster to use the same software release to prevent unexpected interoperability issues.

Follow these steps to install Cassandra. Unless stated otherwise, execute the following commands on every node within the Cassandra cluster.

1.  Ensure the system is up to date. Reboot the system if necessary:

    ```command
    sudo apt update -y && sudo apt upgrade -y
    ```

1.  Cassandra requires the use of a Java runtime. There are several different versions of Java to choose from, including OpenJDK and Oracle Java. This guide uses OpenJDK 11. Install OpenJDK using `apt`:

    ```command
    sudo apt install default-jre
    ```

1.  Use the `java -version` command to confirm that OpenJDK 11 is installed:

    {{< note type="secondary" title="Optional" >}}
    Cassandra does not require the Java compiler or JDK, but many administrators choose to install it anyway. Optionally install the default JDK using the following command:

    ```command
    sudo apt install default-jdk
    ```
    {{< /note >}}

    ```command
    java -version
    ```

    ```output
    openjdk version "11.0.19" 2023-04-18
    ```

Cassandra supports multiple installation methods. This guide utilizes apt for Cassandra installation. Begin by adding the Cassandra repository to the package list. The example below adds the package for version 4.1. To install a different version, replace 41x with the appropriate major and minor release numbers.

    {{< note >}}
   Cassandra is also available as a Docker image or for installation via binary files. For details on these methods, refer to the Cassandra Installation Documentation.
    {{< /note >}}

    ```command
    echo "deb https://debian.cassandra.apache.org 41x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
    ```

    ```output
    deb https://debian.cassandra.apache.org 41x main
    ```

1.  Add the repository GPG keys to the list of trusted keys:

    ```command
    curl https://downloads.apache.org/cassandra/KEYS | sudo tee /etc/apt/trusted.gpg.d/cassandra.asc
    ```

1.  Update the list of packages:

    ```command
    sudo apt-get update
    ```

1.  Use `apt` to install the application:

    ```command
    sudo apt-get install cassandra
    ```

1.  Confirm that the status of the Cassandra service is `active`:

    ```command
    sudo systemctl status cassandra
    ```

    ```output
    ● cassandra.service - LSB: distributed storage system for structured data
         Loaded: loaded (/etc/init.d/cassandra; generated)
         Active: active (running) since Wed 2023-06-21 11:43:53 EDT; 41s ago
    ```

Verify that the cqlsh command connects to the database and displays the cqlsh prompt.

    {{< note >}}
    Cassandra typically takes about a minute to initialize fully. During this period, it rejects any connection attempts.
    {{< /note >}}

    ```command
    cqlsh
    ```

    ```output
    [cqlsh 6.1.0 | Cassandra 4.1.2 | CQL spec 3.4.6 | Native protocol v5]
    Use HELP for help.
    cqlsh>
    ```

1.  Use the `exit` command to quit the CQL shell:

    ```command
    exit
    ```

Follow the steps outlined in this section for each node in the cluster.

## How to Configure Cassandra to Run in Multiple Data Centers

Once all nodes in the cluster are operational, they can be grouped together. For each node, identify its data center and assign a unique rack name within that data center.

Each Cassandra node's configuration is defined in the cassandra.yaml file. While this file contains numerous settings, only a few attributes are essential for adding a node to a cluster. For detailed configuration options, refer to the cassandra.yaml configuration guide.

To complete the cluster configuration, follow these steps:

1.  Configure the `ufw` firewall on each node to allow SSH connections, open ports `7000`, `9042`, and `9160`, and activate the firewall:

    ```command
    sudo ufw allow OpenSSH
    sudo ufw allow 7000/tcp
    sudo ufw allow 9042/tcp
    sudo ufw allow 9160/tcp
    sudo ufw enable
    ```

    ```output
    Rules updated
    Rules updated (v6)
    Rules updated
    Rules updated (v6)
    Rules updated
    Rules updated (v6)
    Rules updated
    Rules updated (v6)
    Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
    Firewall is active and enabled on system startup
    ```

    {{< note type="secondary" title="Optional">}}
    For extra security, only allow connections from the other nodes in the cluster. The format for these commands is:

    ```command
    sudo ufw allow OpenSSH
    sudo ufw allow from remote-IP to local-IP proto tcp port 7000
    sudo ufw allow from remote-IP to local-IP proto tcp port 9042
    sudo ufw allow from remote-IP to local-IP proto tcp port 9160
    sudo ufw enable
    ```

Replace remote-IP with the IP address of another node in the cluster, and local-IP with the IP address of the current node. Repeat this step for each node in the cluster, substituting remote-IP with the respective IP address of each remote node.

Managing this manually can be challenging for larger clusters, as it's easy to overlook connections.

    {{< /note >}}

1.  Confirm the configuration:

    ```command
    sudo ufw status
    ```

    ```output
    Status: active
    To                         Action      From
    --                         ------      ----
    OpenSSH                    ALLOW       Anywhere
    9160/tcp                   ALLOW       Anywhere
    7000/tcp                   ALLOW       Anywhere
    9042/tcp                   ALLOW       Anywhere
    OpenSSH (v6)               ALLOW       Anywhere (v6)
    9160/tcp (v6)              ALLOW       Anywhere (v6)
    7000/tcp (v6)              ALLOW       Anywhere (v6)
    9042/tcp (v6)              ALLOW       Anywhere (v6)
    ```

Shutdown all nodes in the cluster to prevent data corruption or connectivity issues. If any node is currently in production use, schedule this action during a maintenance window.

    ```command
    sudo systemctl stop cassandra
    ```

Remove any test data from the application to prevent unnecessary data replication.

    ```command
    sudo rm -rf /var/lib/cassandra/*
    ```

Repeat the preceding steps for each node in the cluster.

Define the architecture for the cluster.

- Select a name for the entire cluster.
- Organize nodes into data centers based on their geographical proximity.
- Assign meaningful names to each data center.
- In each data center, designate a rack name for every system.

Lastly, establish the seed order within each data center. Seed nodes facilitate cluster discovery and activation. Ensure the cluster has at least two seed nodes, with one designated as the primary seed.

    {{< note >}}
    For more information about seeds, see the [Cassandra FAQ](https://cassandra.apache.org/doc/latest/cassandra/faq/index.html).
    {{< /note >}}

1.  On the first node, edit the main Cassandra YAML file:

    ```command
    sudo nano /etc/cassandra/cassandra.yaml
    ```

1.  Make the following changes:

- Use the cluster name for the cluster_name attribute, ensuring consistency across all nodes in the cluster.
- Inside the seed_provider attribute's parameters record, list seeds as comma-separated values in the seeds variable. Begin with the primary seed for the local data center, followed by other seed nodes within the same data center. Append seeds from other data centers as necessary.
For instance, if dc1 has node1 as primary seed and node2 as secondary seed, and another data center has node3 and node4, the seeds value should be node1_ip, node2_ip, node3_ip, node4_ip.
- Ensure the listen_address field contains the system's IP address. For enhanced security, use the private IP address if configured.
- Optionally, set the rpc_address to 127.0.0.1 for loopback access. If the server hostname is configured, you can leave it as localhost.
- Configure the endpoint_snitch field to GossipingPropertyFileSnitch.

Below is a sample file template illustrating these changes. Replace placeholder values (e.g., NODE1_IP) with the actual IP addresses corresponding to each node. Retain all other sections of the file unchanged.

    ```file {title="/etc/cassandra/cassandra.yaml" lang="yaml"}
    cluster_name: 'Main Cluster'
    ...
    seed_provider:
      - class_name: org.apache.cassandra.locator.SimpleSeedProvider
      parameters:
        - seeds: "NODE1_IP, NODE2_IP, NODE3_IP, NODE4_IP"
    ...
    listen_address: NODE1_IP
    ...
    rpc_address: 127.0.0.1
    ...
    endpoint_snitch: GossipingPropertyFileSnitch
    ```

    {{< note >}}
    Ensure `start_native_transport` is set to `true` and `native_transport_port` is `9042`. Depending on the Cassandra release, these values might already be set correctly.
    {{< /note >}}

1.  When done, press <`kbd>CTRL</kbd>+<kbd>X</kbd>, followed by <kbd>Y</kbd> then <kbd>Enter</kbd> to save the file and exit `nano`.

1.  On the same node, edit the `/etc/cassandra/cassandra-rackdc.properties` file:

    ```command
    sudo nano /etc/cassandra/cassandra-rackdc.properties
    ```

Define the data center and rack name for the system. The example below configures a node in the `london` data center with the rack named `rack1`:

    ```file {title="/etc/cassandra/cassandra-rackdc.properties"}
    dc=london
    rack=rack1
    ```

Configure cassandra.yaml and cassandra-rackdc.properties on the remaining nodes within the first data center.

- Begin with the changes in cassandra.yaml. Ensure each system has identical values for the cluster_name attribute.
- The seeds parameter should contain identical values across all nodes within the same data center, with node addresses listed in the same sequence.
- Set listen_address to the system's IP address.
- In cassandra-rackdc.properties, maintain dc consistent across all nodes in the data center. Each system should have a unique rack name.

The example below demonstrates configuring these files for a second node in the london data center:

    ```file {title="/etc/cassandra/cassandra.yaml" lang="yaml"}
    cluster_name: 'Main Cluster'
    ...
    seed_provider:
      - class_name: org.apache.cassandra.locator.SimpleSeedProvider
      parameters:
        - seeds: "NODE1_IP, NODE2_IP, NODE3_IP, NODE4_IP"
    ...
    listen_address: NODE2_IP
    ...
    rpc_address: 127.0.0.1
    ...
    endpoint_snitch: GossipingPropertyFileSnitch
    ```

    ```file {title="/etc/cassandra/cassandra-rackdc.properties"}
    dc=london
    rack=rack2
    ```

Configure each additional node in the first data center following the same steps, adjusting the listen_address as necessary.

Proceed to configure the nodes in the second data center:

- Ensure cluster_name is consistent across all nodes in all data centers in /etc/cassandra/cassandra.yaml.
- List the seed nodes for the local data center first in the seeds attribute, followed by seeds for remote data centers.
- Set listen_address to the system's IP address in /etc/cassandra/cassandra.yaml.
- Update dc in /etc/cassandra/cassandra-rackdc.properties to reflect the name of the second data center.
- Ensure each rack name is unique within the data center.

The example below demonstrates these configurations for a node in the singapore data center:

    ```file {title="/etc/cassandra/cassandra.yaml" lang="yaml"}
    cluster_name: 'Main Cluster'
    ...
    seed_provider:
      - class_name: org.apache.cassandra.locator.SimpleSeedProvider
      parameters:
        - seeds: "NODE3_IP, NODE4_IP, NODE1_IP, NODE2_IP"
    ...
    listen_address: NODE3_IP
    ...
    rpc_address: 127.0.0.1
    ...
    endpoint_snitch: GossipingPropertyFileSnitch
    ```

    ```file {title="/etc/cassandra/cassandra-rackdc.properties"}
    dc=singapore
    rack=rack1
    ```

## How to Activate a Cassandra Cluster

Nodes in the Cassandra cluster must be activated in a specific sequence. Follow these steps to ensure proper activation:

Start by restarting Cassandra on the primary seed node within one of the data centers. This node is listed first in the seeds configuration.

    ```command
    sudo systemctl start cassandra
    ```

1.  Ensure the Cassandra service is `active`:

    ```command
    sudo systemctl status cassandra
    ```

    ```output
    ● cassandra.service - LSB: distributed storage system for structured data
         Loaded: loaded (/etc/init.d/cassandra; generated)
         Active: active (running) since Wed 2023-06-21 14:05:57 EDT; 19s ago
    ```

Restart the primary seed nodes in all other data centers. Wait until cassandra indicates it is active.

Restart all other nodes in the cluster and wait for two to three minutes to ensure synchronization across all systems.

Verify the status of the cluster:

    ```command
    sudo nodetool status
    ```

    Each node appears in the output. The `Status/State` of each node should be `UN`, which stands for `Up` and `Normal`:

    ```output
    Datacenter: london
    ==================
    Status=Up/Down
    |/ State=Normal/Leaving/Joining/Moving
    --  Address         Load        Tokens  Owns (effective)  Host ID                               Rack
    UN  192.168.1.5  132.68 KiB  16      47.9%             e6905cf5-5a97-447a-b57f-f22f9613510e  rack1
    UN  192.168.1.15 25.56 KiB   16      51.6%             672f85de-3eee-4971-b981-f6dd2c844f52  rack2

    Datacenter: singapore
    =====================
    Status=Up/Down
    |/ State=Normal/Leaving/Joining/Moving
    --  Address         Load        Tokens  Owns (effective)  Host ID                               Rack
    UN  192.168.2.10  132.7 KiB   16      49.5%             c8a9accb-7df7-41ed-8062-7eba46faaa10  rack2
    UN  192.168.2.20  137.83 KiB  16      51.0%             8dd52e5b-4fcb-463f-9c2a-b71158663385  rack1
    ```

    {{< note >}}
    If a node is not showing as Up and Normal, ensure the stability of the cassandra service. Verify the details in cassandra-rackdc.properties to confirm the node belongs to the correct data center and has a unique rack name within that center. After making changes to any configuration files, stop and restart Cassandra to apply the updates.
    {{< /note >}}

## How to Add Tables and Data to Cassandra

Cassandra utilizes the CQL (Cassandra Query Language) to modify database contents. Data can be imported from a file or added manually using the `cqlsh` utility. Before adding tables or data, it's essential to create a keyspace. A keyspace defines how data replication should be handled, specifying the replication factor. Tables within Cassandra are scoped within their respective keyspaces. For detailed information on CQL, refer to the Cassandra CQL documentation.

To add a keyspace, table, and data to Cassandra, follow these steps:

Access the CQL shell on one of the nodes.

    ```command
    cqlsh
    ```

Create a keyspace using the CREATE KEYSPACE statement and define the replication strategy.

- Use NetworkTopologyStrategy for the class when your cluster spans multiple data centers.
- Specify a replication factor for each data center in the cluster, indicating how many copies of the data should be stored in each data center.
- The syntax for the statement is CREATE KEYSPACE IF NOT EXISTS keyspacename WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 2, 'datacenter2' : 2 };.

The following example demonstrates saving two copies of each table entry in the store keyspace: two copies in the london data center and two copies in the singapore data center.

    ```command
    CREATE KEYSPACE IF NOT EXISTS store WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'london' : 2, 'singapore' : 2  };
    ```

Confirm the keyspace is successfully created:

    ```command
    desc keyspaces;
    ```

    The new `store` keyspace is listed alongside any existing keyspaces:

    ```output
    store   system_auth         system_schema  system_views
    system  system_distributed  system_traces  system_virtual_schema
    ```

To add a table to the keyspace, use the syntax `keyspacename.tablename`. Define the schema of columns, including their names and data types. Every table in a Cassandra keyspace must have a primary key, which partitions the table entries.

    ```command
    CREATE TABLE IF NOT EXISTS store.shopping_cart (
    userid text PRIMARY KEY,
    item_count int
    );
    ```

Use the `INSERT` command to add an entry to the table.

    ```command
    INSERT INTO store.shopping_cart (userid, item_count) VALUES ('59', 12);
    ```

Use the `SELECT * FROM` command to view all data in the table.

    ```command
    SELECT * FROM store.shopping_cart;
    ```

    ```output
     userid | item_count
    --------+------------
     59     |         12
    (1 rows)
    ```

To confirm the data has been replicated correctly, access the CQL shell on another node. Execute the same `SELECT` command and verify that the data displayed is consistent across all nodes. Validate across all data centers to ensure Cassandra adheres correctly to the keyspace replication factor.

    {{< note >}}
    Cassandra ensures the minimum number of copies necessary to meet keyspace requirements. For instance, if a data center has a replication factor of two, only two nodes within that data center store the table data. Due to network latency, data may take a moment to propagate to a remote data center, especially during batch reads from a file.
    {{< /note >}}

    ```command
    SELECT * FROM store.shopping_cart;
    ```

    ```output
     userid | item_count
    --------+------------
     59     |         12
    (1 rows)
    ```

{{< note >}}
Before deploying Cassandra into production, it's essential to consider several critical aspects such as authorization, security, encryption, compression, monitoring, and backup strategies. The Operating Cassandra section in the official documentation provides comprehensive guidance on managing Cassandra in production environments.
{{< /note >}}

## Conclusion

Apache Cassandra is a distributed database known for its low latency, high throughput, and robust redundancy features. It supports data replication across multiple data centers, each housing nodes in close proximity. Cassandra can be installed via `apt`or Docker and configured using various YAM.jpg and text configuration files. To store data in Cassandra, start by defining a keyspace to specify data replication across the cluster. Next, create tables within the keyspace and populate them with data. For detailed information on Apache Cassandra, refer to the Cassandra Documentation.