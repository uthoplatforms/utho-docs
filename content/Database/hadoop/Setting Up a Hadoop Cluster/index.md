---
slug: Setting Up a Hadoop Cluster
description: 'This Utho guide will show you how to install and set up a 3-node Hadoop cluster.'
keywords: ["Hadoop", " YARN", " HDFS"]
published: 2024-07-10
modified: 2024-07-10
modified_by:
  name: Utho
title: 'How to Install and Set Up a 3-Node Hadoop Cluster'
tags: ["database"]
authors: ["Pawan Kumar"]
tags: ["saas"]
---

## What is Hadoop?

Hadoop is an open-source Apache project designed for creating parallel processing applications that operate on large data sets distributed across networked nodes. It consists of two primary components: the Hadoop Distributed File System (HDFS™), which manages scalability and data redundancy across nodes, and Hadoop YARN, a framework for job scheduling that facilitates the execution of data processing tasks across the cluster.

## Before You Begin

Deploy 3 Utho Compute Instances. These instances will be referred to as node-master, node1, and node2 throughout this guide. For instructions, refer to our guides on Getting Started with Utho and Creating a Compute Instance.

Configure a Private IP Address for each Utho to enhance security and enable communication within your cluster.

Follow the Setting Up and Securing a Compute Instance guide to harden each server. It's recommended to set the hostname of each Utho to match their assigned names (e.g., node-master, node1, node2). Create a regular user for the Hadoop installation and a user named hadoop for running Hadoop daemons. Do not generate SSH keys for the hadoop user; SSH keys will be configured in a later step.

Install the JDK using the appropriate guide for your Linux distribution: Debian, CentOS, Ubuntu, or install the latest JDK from Oracle.

The steps below provide example IP addresses for each node. Adjust these examples to match your specific configuration:

    -  **node-master**: 192.0.2.1
    -  **node1**: 192.0.2.2
    -  **node2**: 192.0.2.3

    {{< note respectIndent=false >}}
This guide is intended for a non-root user. Commands that need elevated privileges are prefixed with sudo. If you’re unfamiliar with the sudo command, refer to the Users and Groups guide. All commands in this guide assume they are run with the hadoop user unless stated otherwise.
{{< /note >}}

## Architecture of a Hadoop Cluster

Before configuring the master and worker nodes, it's essential to understand the different components of a Hadoop cluster.

A master node maintains knowledge about the distributed file system, akin to the inode table in an ext3 filesystem, and oversees resource allocation. In this guide, node-master will fulfill this role and host two key daemons:

- The NameNode manages the distributed file system, keeping track of where data blocks are stored within the cluster.

- The ResourceManager handles YARN jobs, responsible for scheduling and executing tasks on worker nodes.

Worker nodes store the actual data and provide processing power to execute jobs. In this setup, node1 and node2 will serve as worker nodes, each hosting two important daemons:

- The DataNode manages physical data storage on the node, reporting to the NameNode.

- The NodeManager oversees task execution on the node, managed by the ResourceManager.

## Configure the System

### Create Host File on Each Node

To enable communication between nodes using their hostnames, modify the /etc/hosts file on each server to include the private IP addresses of all three servers. Replace the placeholder IPs with the actual IP addresses of your nodes.

{{< file "/etc/hosts" >}}
192.0.2.4    node-master
192.0.2.5    node1
192.0.2.6    node2

{{< /file >}}

### Distribute Authentication Key-pairs for the Hadoop User

The master node will use an SSH connection to connect to other nodes with key-pair authentication. This will allow the master node to actively manage the cluster.

1.  Login to **node-master** as the `hadoop` user, and generate an SSH key:

        ssh-keygen -b 4096

     When generating this key, leave the password field blank so your Hadoop user can communicate unprompted.

1.  View the **node-master** public key and copy it to your clipboard to use with each of your worker nodes.

        less /home/hadoop/.ssh/id_rsa.pub

1.  In each Utho, make a new file `master.pub` in the `/home/hadoop/.ssh` directory. Paste your public key into this file and save your changes.

1.  Copy your key file into the authorized key store.

        cat ~/.ssh/master.pub >> ~/.ssh/authorized_keys

### Download and Unpack Hadoop Binaries

Log into node-master as the hadoop user, download the Hadoop tarball from the Hadoop project page, and extract it:

    cd
    wget http://apache.cs.utah.edu/hadoop/common/current/hadoop-3.1.2.tar.gz
    tar -xzf hadoop-3.1.2.tar.gz
    mv hadoop-3.1.2 hadoop

### Set Environment Variables

1.  Add Hadoop binaries to your PATH. Edit `/home/hadoop/.profile` and add the following line:

    {{< file "/home/hadoop/.profile" shell >}}
PATH=/home/hadoop/hadoop/bin:/home/hadoop/hadoop/sbin:$PATH
{{< /file >}}

1.  Add Hadoop to your PATH for the shell. Edit `.bashrc` and add the following lines:

    {{< file "/home/hadoop/.bashrc" shell >}}
export HADOOP_HOME=/home/hadoop/hadoop
export PATH=${PATH}:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin
{{< /file >}}

## Configure the Master Node

Configuration will be performed on **node-master** and replicated to other nodes.

### Set JAVA_HOME

Determine your Java installation path, referred to as `JAVA_HOME`. If you installed OpenJDK via your package manager, you can find the path using the following command:

        update-alternatives --display java

For example, on Debian-based systems, the command might return a path like /usr/lib/jvm/java-8-openjdk-amd64/jre. In this case, JAVA_HOME should be set to /usr/lib/jvm/java-8-openjdk-amd64/jre.

If you installed Java from Oracle, JAVA_HOME should be set to the directory where you extracted the Java archive.

2.  Edit `~/hadoop/etc/hadoop/hadoop-env.sh` and replace this line:

        export JAVA_HOME=${JAVA_HOME}

    with your actual java installation path. On a Debian 9 Utho with open-jdk-8 this will be as follows:

    {{< file "~/hadoop/etc/hadoop/hadoop-env.sh" shell >}}
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre

{{< /file >}}

### Set NameNode Location

Update your `~/hadoop/etc/hadoop/core-site.xml` file to set the NameNode location to **node-master** on port `9000`:

{{< file "~/hadoop/etc/hadoop/core-site.xml" xml >}}
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
    <configuration>
        <property>
            <name>fs.default.name</name>
            <value>hdfs://node-master:9000</value>
        </property>
    </configuration>

{{< /file >}}


### Set path for HDFS

Edit `hdfs-site.conf` to resemble the following configuration:

{{< file "~/hadoop/etc/hadoop/hdfs-site.xml" xml >}}
<configuration>
    <property>
            <name>dfs.namenode.name.dir</name>
            <value>/home/hadoop/data/nameNode</value>
    </property>

    <property>
            <name>dfs.datanode.data.dir</name>
            <value>/home/hadoop/data/dataNode</value>
    </property>

    <property>
            <name>dfs.replication</name>
            <value>1</value>
    </property>
</configuration>

{{< /file >}}


The final property, `dfs.replication`, specifies the number of times data is replicated within the cluster. For example, setting it to 2 ensures that all data is duplicated across two nodes. Ensure not to specify a value higher than the total number of worker nodes in your cluster.

### Set YARN as Job Scheduler

Edit the `mapred-site.xml` file and configure YARN as the default framework for MapReduce operations:

{{< file "~/hadoop/etc/hadoop/mapred-site.xml" xml >}}
<configuration>
    <property>
            <name>mapreduce.framework.name</name>
            <value>yarn</value>
    </property>
    <property>
            <name>yarn.app.mapreduce.am.env</name>
            <value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
    </property>
    <property>
            <name>mapreduce.map.env</name>
            <value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
    </property>
    <property>
            <name>mapreduce.reduce.env</name>
            <value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
    </property>
</configuration>

{{< /file >}}


### Configure YARN

Edit yarn-site.xml and replace 203.0.113.1 with the public IP address of node-master in the value field for yarn.resourcemanager.hostname:

{{< file "~/hadoop/etc/hadoop/yarn-site.xml" xml >}}
<configuration>
    <property>
            <name>yarn.acl.enable</name>
            <value>0</value>
    </property>

    <property>
            <name>yarn.resourcemanager.hostname</name>
            <value>203.0.113.0</value>
    </property>

    <property>
            <name>yarn.nodemanager.aux-services</name>
            <value>mapreduce_shuffle</value>
    </property>
</configuration>

{{< /file >}}

### Configure Workers

Edit the workers file located at `~/hadoop/etc/hadoop/workers` to include both nodes:

{{< file "~/hadoop/etc/hadoop/workers" resource >}}
node1
node2

{{< /file >}}

## Configure Memory Allocation

Memory allocation can be challenging on nodes with low RAM, particularly when default values are not suitable, especially for nodes with less than 8GB of RAM. This section will explain how memory allocation functions for MapReduce jobs and provide a sample configuration tailored for nodes with 2GB of RAM.

### The Memory Allocation Properties

A YARN job utilizes two types of resources:

An Application Master (AM) oversees the application, monitoring its progress and coordinating distributed executors within the cluster.
Executors, created by the AM, actually execute the job tasks. For MapReduce jobs, these executors handle map or reduce operations in parallel.
Both the AM and executors run within containers on worker nodes. Each worker node is equipped with a NodeManager daemon responsible for creating these containers. The entire cluster is managed by a ResourceManager that allocates containers across all worker nodes based on capacity requirements and current workload.

To ensure the cluster functions properly, four types of resource allocations must be configured correctly:

Container Memory Allocation: Specifies the maximum amount of memory that can be allocated for YARN containers on a single node. This limit should be higher than any other configured memory limits to prevent container allocation failures and application crashes. However, it should not consume all available RAM on the node.

    This value is configured in `yarn-site.xml` with `yarn.nodemanager.resource.memory-mb`.

Specify the maximum memory consumption per container and the minimum allowable memory allocation. Containers cannot exceed their maximum limit; otherwise, allocation will fail. Memory is always allocated in multiples of the minimum RAM size.

    Those values are configured in `yarn-site.xml` with `yarn.scheduler.maximum-allocation-mb` and `yarn.scheduler.minimum-allocation-mb`.

Define the constant memory allocation for the ApplicationMaster, ensuring it fits within the container's maximum capacity.

    This is configured in `mapred-site.xml` with `yarn.app.mapreduce.am.resource.mb`.

Determine the memory allocated for each map or reduce operation, ensuring it does not exceed the maximum container size.

    This is configured in `mapred-site.xml` with properties `mapreduce.map.memory.mb` and `mapreduce.reduce.memory.mb`.

The relationship between all those properties can be seen in the following figure:

### Sample Configuration for 2GB Nodes

For 2GB nodes, a working configuration may be:

| Property | Value |
| ---------------- |:-------------:|
| yarn.nodemanager.resource.memory-mb     | 1536          |
| yarn.scheduler.maximum-allocation-mb| 1536 |
| yarn.scheduler.minimum-allocation-mb| 128 |
| yarn.app.mapreduce.am.resource.mb| 512 |
| mapreduce.map.memory.mb| 256 |
| mapreduce.reduce.memory.mb| 256 |


1.  Edit `/home/hadoop/hadoop/etc/hadoop/yarn-site.xml` and add the following lines:

    {{< file "~/hadoop/etc/hadoop/yarn-site.xml" xml >}}
<property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>1536</value>
</property>

<property>
        <name>yarn.scheduler.maximum-allocation-mb</name>
        <value>1536</value>
</property>

<property>
        <name>yarn.scheduler.minimum-allocation-mb</name>
        <value>128</value>
</property>

<property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
</property>

{{< /file >}}


    The last property disables virtual-memory checking which can prevent containers from being allocated properly with JDK8 if enabled.


2.  Edit `/home/hadoop/hadoop/etc/hadoop/mapred-site.xml` and add the following lines:

    {{< file "~/hadoop/etc/hadoop/mapred-site.xml" xml >}}
<property>
        <name>yarn.app.mapreduce.am.resource.mb</name>
        <value>512</value>
</property>

<property>
        <name>mapreduce.map.memory.mb</name>
        <value>256</value>
</property>

<property>
        <name>mapreduce.reduce.memory.mb</name>
        <value>256</value>
</property>

{{< /file >}}

## Duplicate Config Files on Each Node

1.  Copy the Hadoop binaries to worker nodes:

        cd /home/hadoop/
        scp hadoop-*.tar.gz node1:/home/hadoop
        scp hadoop-*.tar.gz node2:/home/hadoop

2.  Connect to **node1** via SSH. A password isn't required, thanks to the SSH keys copied above:

        ssh node1

3.  Unzip the binaries, rename the directory, and exit **node1** to get back on the node-master:

        tar -xzf hadoop-3.1.2.tar.gz
        mv hadoop-3.1.2 hadoop
        exit

4.  Repeat steps 2 and 3 for **node2**.

5.  Copy the Hadoop configuration files to the worker nodes:

        for node in node1 node2; do
            scp ~/hadoop/etc/hadoop/* $node:/home/hadoop/hadoop/etc/hadoop/;
        done

## Format HDFS

"HDFS requires formatting like traditional file systems. On the `node-master`, execute the following command:

    hdfs namenode -format

Your Hadoop installation is now configured and ready to run.

## Run and monitor HDFS

This section will guide you through starting HDFS on both the NameNode and DataNodes, and ensuring proper interaction and functionality with HDFS data."

### Start and Stop HDFS

1.  Start the HDFS by running the following script from **node-master**:

        start-dfs.sh

    This will start **NameNode** and **SecondaryNameNode** on node-master, and **DataNode** on **node1** and **node2**, according to the configuration in the `workers` config file.

2.  Check that every process is running with the `jps` command on each node. On **node-master**, you should see the following (the PID number will be different):

        21922 Jps
        21603 NameNode
        21787 SecondaryNameNode

    And on **node1** and **node2** you should see the following:

        19728 DataNode
        19819 Jps

3.  To stop HDFS on master and worker nodes, run the following command from **node-master**:

        stop-dfs.sh

### Monitor your HDFS Cluster

1.  You can get useful information about running your HDFS cluster with the `hdfs dfsadmin` command. Try for example:

        hdfs dfsadmin -report

    This will print information (e.g., capacity and usage) for all running DataNodes. To get the description of all available commands, type:

        hdfs dfsadmin -help

2.  You can also automatically use the friendlier web user interface. Point your browser to http://node-master-IP:9870, where node-master-IP is the IP address of your node-master, and you'll get a user-friendly monitoring console.

### Put and Get Data to HDFS

Writing and reading to HDFS is done with command `hdfs dfs`. First, manually create your home directory. All other commands will use a path relative to this default home directory:

    hdfs dfs -mkdir -p /user/hadoop

Let's use some textbooks from the [Gutenberg project](https://www.gutenberg.org/) as an example.

1.  Create a *books* directory in HDFS. The following command will create it in the home directory, `/user/hadoop/books`:

        hdfs dfs -mkdir books

2.  Grab a few books from the Gutenberg project:

        cd /home/hadoop
        wget -O alice.txt https://www.gutenberg.org/files/11/11-0.txt
        wget -O holmes.txt https://www.gutenberg.org/files/1661/1661-0.txt
        wget -O frankenstein.txt https://www.gutenberg.org/files/84/84-0.txt

3.  Put the three books through HDFS, in the `books`directory:

        hdfs dfs -put alice.txt holmes.txt frankenstein.txt books

4.  List the contents of the `book` directory:

        hdfs dfs -ls books

5.  Move one of the books to the local filesystem:

        hdfs dfs -get books/alice.txt

6.  You can also directly print the books from HDFS:

        hdfs dfs -cat books/alice.txt

There are numerous commands available to manage your HDFS. For a comprehensive list, refer to the Apache HDFS shell documentation, or print help using:

    hdfs dfs -help

## Run YARN

HDFS is a distributed storage system, and doesn't provide any services for running and scheduling tasks in the cluster. This is the role of the YARN framework. The following section is about starting, monitoring, and submitting jobs to YARN.

### Start and Stop YARN

Start YARN with the script:

        start-yarn.sh

Verify that everything is running using the jps command. Alongside the previously listed HDFS daemon, you should observe a ResourceManager on node-master, and NodeManagers on node1 and node2."

To stop YARN, run the following command on **node-master**:

        stop-yarn.sh

### Monitor YARN

The `yarn` command provides utilities to manage your YARN cluster. You can also print a report of running nodes with the command:

        yarn node -list

    Similarly, you can get a list of running applications with command:

        yarn application -list

    For a comprehensive list of all available parameters for the yarn command, refer to the Apache YARN documentation.

Similar to HDFS, YARN also offers a user-friendly web UI, which is typically started on port 8088 of the Resource Manager by default. Open your web browser and navigate to http://node-master-IP:8088, where node-master-IP is the IP address of your node-master, to access the UI:

### Submit MapReduce Jobs to YARN

`YARN` jobs are packaged into jar files and submitted for execution using the yarn jar command. The Hadoop installation includes sample applications that serve as tests for your cluster. These applications will be used to perform a word count on the three books previously uploaded to HDFS.

Submit a job with the sample `jar` to YARN. On **node-master**, run:

        yarn jar ~/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.2.jar wordcount "books/*" output

    The last argument is where the output of the job will be saved - in HDFS.

Once the job completes, retrieve the results by querying HDFS using hdfs dfs -ls output. If successful, the output will resemble:"

        Found 2 items
        -rw-r--r--   2 hadoop supergroup          0 2019-05-31 17:21 output/_SUCCESS
        -rw-r--r--   2 hadoop supergroup     789726 2019-05-31 17:21 output/part-r-00000

3.  Print the result with:

        hdfs dfs -cat output/part-r-00000 | less
