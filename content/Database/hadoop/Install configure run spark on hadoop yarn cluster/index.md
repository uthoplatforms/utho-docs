---
slug: Install configure run spark on hadoop yarn cluster
description: "This guide shows you how to install, configure, and run Spark on top of a Hadoop YARN cluster."
keywords: ["spark", "hadoop", "yarn", "hdfs"]
published: 2024-07-10
modified: 2024-07-10
modified_by:
  name: Utho
title: "Running Spark on Top of a Hadoop YARN Cluster"
title_meta: "How to Run Spark on Top of a Hadoop YARN Cluster"
tags: ["ubuntu","debian","database","centos"]
aliases: ['/databases/hadoop/install-configure-run-spark-on-top-of-hadoop-yarn-cluster/']
authors: ["Pawan Kumar"]
---

Spark is a general purpose cluster computing system. It can deploy and run parallel applications on clusters ranging from a single node to thousands of distributed nodes. Spark was originally designed to run Scala applications, but also supports Java, Python and R.

Spark can run as a standalone cluster manager, or by taking advantage of dedicated cluster management frameworks like [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) or [Apache Mesos](http://mesos.apache.org/).

## Before You Begin

1. Refer to our guide on installing and configuring a three-node Hadoop cluster to establish your YARN cluster. The master node, which hosts the HDFS NameNode and YARN ResourceManager, is named node-master. The slave nodes, serving as HDFS DataNode and YARN NodeManager, are named node1 and node2."

    Run the commands in this guide from **node-master** unless otherwise specified.

2.  Be sure you have a `hadoop` user that can access all cluster nodes with SSH keys without a password.

3.  Note the path of your Hadoop installation. This guide assumes it is installed in `/home/hadoop/hadoop`. If it is not, adjust the path in the examples accordingly.

4.  Run `jps` on each of the nodes to confirm that HDFS and YARN are running. If they are not, start the services with:

        start-dfs.sh
        start-yarn.sh

{{< note >}}
This guide is written for a non-root user. Commands that require elevated privileges are prefixed with `sudo`. If you’re not familiar with the `sudo` command, see the [Users and Groups](/docs/guides/linux-users-and-groups/) guide.
{{< / note >}}

## Download and Install Spark Binaries

Spark binaries are available from the [Apache Spark download page](https://spark.apache.org/downloads.html). Adjust each command below to match the correct version number.

1.  Get the download URL from the Spark download page, download it, and uncompress it.

    For Spark 2.2.0 with Hadoop 2.7 or later, log on `node-master` as the `hadoop` user, and run:

        cd /home/hadoop
        wget https://d3kbcqa49mib13.cloudfront.net/spark-2.2.0-bin-hadoop2.7.tgz
        tar -xvf spark-2.2.0-bin-hadoop2.7.tgz
        mv spark-2.2.0-bin-hadoop2.7 spark

2.  Add the Spark binaries directory to your `PATH`. Edit `/home/hadoop/.profile` and add the following line:

    **For Debian/Ubuntu systems:**

    {{< file "/home/hadoop/.profile" shell >}}
PATH=/home/hadoop/spark/bin:$PATH
{{< /file >}}

    **For RedHat/Fedora/CentOS systems:**

    {{< file "/home/hadoop/.profile" shell >}}
pathmunge /home/hadoop/spark/bin
{{< /file >}}

## Integrate Spark with YARN

To communicate with the YARN Resource Manager, Spark needs to be aware of your Hadoop configuration. This is done via the `HADOOP_CONF_DIR` environment variable. The `SPARK_HOME` variable is not mandatory, but is useful when submitting Spark jobs from the command line.

1.  Edit the *hadoop* user profile `/home/hadoop/.profile` and add the following lines:

    {{< file "/home/hadoop/.profile" shell >}}
export HADOOP_CONF_DIR=/home/hadoop/hadoop/etc/hadoop
export SPARK_HOME=/home/hadoop/spark
export LD_LIBRARY_PATH=/home/hadoop/hadoop/lib/native:$LD_LIBRARY_PATH
{{< /file >}}

2. Restart your session by logging out and logging in again.

3. Rename the spark default template config file:

        mv $SPARK_HOME/conf/spark-defaults.conf.template $SPARK_HOME/conf/spark-defaults.conf

4. Edit `$SPARK_HOME/conf/spark-defaults.conf` and set `spark.master` to `yarn`:

    {{< file "$SPARK_HOME/conf/spark-defaults.conf" conf >}}
spark.master    yarn
{{< /file >}}

Spark is now ready to interact with your YARN cluster.

## Understand Client and Cluster Mode

Spark jobs on YARN can operate in two modes: cluster mode and client mode. Understanding these modes is crucial for configuring memory allocation and managing job submissions effectively.

In a Spark job, there are Spark Executors responsible for executing tasks, and a Spark Driver that orchestrates these Executors.

Cluster mode: In this mode, everything runs within the cluster. You can initiate a job from your laptop, and it continues to run even if your local machine is shut down. Here, the Spark Driver is encapsulated within the YARN Application Master.

Client mode: Here, the Spark Driver runs on a client machine, such as your laptop. If the client machine is closed or loses connection, the job fails. However, Spark Executors still execute tasks within the cluster, managed by a small YARN Application Master.

Client mode is ideal for interactive jobs where quick feedback is needed, but it carries the risk of job failure if the client shuts down. For long-running and robust applications, cluster mode is generally more suitable.

## Configure Memory Allocation

Allocating Spark containers to run within YARN containers can fail if memory allocation isn't configured correctly. Nodes with less than 4GB of RAM have inadequate default settings, leading to potential swapping, poor performance, or application initialization failures due to memory shortages.

Before adjusting Spark memory settings, ensure a clear understanding of how Hadoop YARN handles memory allocation. This ensures that any modifications align with your YARN cluster's limitations.

{{< note >}}
For more details on managing memory allocation in your YARN cluster, refer to the memory allocation section of the Install and Configure a 3-Node Hadoop Cluster guide.
{{< / note >}}


### Give Your YARN Containers Maximum Allowed Memory

If the requested memory exceeds the maximum allowed, YARN will reject container creation, preventing your Spark application from starting.

Check the value of yarn.scheduler.maximum-allocation-mb in `$HADOOP_CONF_DIR/yarn-site.xml`. This value represents the maximum allowed memory per container in MB.

Ensure that the Spark memory allocation values, configured in the following section, do not exceed this maximum limit.

This guide assumes a sample value of 1536 for yarn.scheduler.maximum-allocation-mb. Adjust these examples according to your specific configuration, ensuring they remain within your cluster's settings.

### Configure the Spark Driver Memory Allocation in Cluster Mode

In cluster mode, the Spark Driver operates within the YARN Application Master. The memory requested by Spark during initialization can be configured either in spark-defaults.conf or through the command line.

**From `spark-defaults.conf`**

  - Set the default amount of memory allocated to Spark Driver in cluster mode via `spark.driver.memory` (this value defaults to `1G`). To set it to `512MB`, edit the file:

    {{< file "$SPARK_HOME/conf/spark-defaults.conf" conf >}}
spark.driver.memory    512m
{{< /file >}}

**From the Command Line**

  - Use the `--driver-memory` parameter to specify the amount of memory requested by `spark-submit`. See the following section about application submission for examples.

    {{< note respectIndent=false >}}
Values given from the command line will override whatever has been set in `spark-defaults.conf`.
{{< /note >}}

### Configure the Spark Application Master Memory Allocation in Client Mode

"In client mode, the Spark driver does not run on the cluster, so the configuration mentioned above does not apply. However, a YARN Application Master is still required to schedule Spark executors, and you can specify its memory requirements.

You can set the memory allocated to the Application Master in client mode using spark.yarn.am.memory (defaulting to 512M).

{{< file "$SPARK_HOME/conf/spark-defaults.conf" conf >}}
spark.yarn.am.memory    512m
{{< /file >}}

This value can not be set from the command line.

### Configure Spark Executors' Memory Allocation

The Spark Executors' memory allocation is calculated based on two parameters inside `$SPARK_HOME/conf/spark-defaults.conf`:

  - `spark.executor.memory`: sets the base memory used in calculation
  - `spark.yarn.executor.memoryOverhead`: is added to the base memory. It defaults to 7% of base memory, with a minimum of `384MB`

{{< note >}}
Make sure that Executor requested memory, **including** overhead memory, is below the YARN container maximum size, otherwise the Spark application won't initialize.
{{< /note >}}

Example: for `spark.executor.memory` of 1Gb , the required memory is 1024+384=1408MB. For 512MB, the required memory will be 512+384=896MB

To set executor memory to `512MB`, edit `$SPARK_HOME/conf/spark-defaults.conf` and add the following line:

{{< file "$SPARK_HOME/conf/spark-defaults.conf" conf >}}
spark.executor.memory          512m
{{< /file >}}

## How to Submit a Spark Application to the YARN Cluster

Applications are submitted with the `spark-submit` command. The Spark installation package contains sample applications, like the parallel calculation of *Pi*, that you can run to practice starting Spark jobs.

To run the sample *Pi* calculation, use the following command:

    spark-submit --deploy-mode client \
                   --class org.apache.spark.examples.SparkPi \
                   $SPARK_HOME/examples/jars/spark-examples_2.11-2.2.0.jar 10

The first parameter, `--deploy-mode`, specifies which mode to use, `client` or `cluster`.

To run the same application in cluster mode, replace `--deploy-mode client`with `--deploy-mode cluster`.

## Monitor Your Spark Applications

When you submit a job, Spark Driver automatically starts a web UI on port `4040` that displays information about the application. However, when execution is finished, the Web UI is dismissed with the application driver and can no longer be accessed.

Spark provides a History Server that collects application logs from HDFS and displays them in a persistent web UI. The following steps will enable log persistence in HDFS:

1.  Edit `$SPARK_HOME/conf/spark-defaults.conf` and add the following lines to enable Spark jobs to log in HDFS:

    {{< file "$SPARK_HOME/conf/spark-defaults.conf" conf >}}
spark.eventLog.enabled  true
spark.eventLog.dir hdfs://node-master:9000/spark-logs
{{< /file >}}

2.  Create the log directory in HDFS:

        hdfs dfs -mkdir /spark-logs

3.  Configure History Server related properties in `$SPARK_HOME/conf/spark-defaults.conf`:

    {{< file "$SPARK_HOME/conf/spark-defaults.conf" conf >}}
spark.history.provider            org.apache.spark.deploy.history.FsHistoryProvider
spark.history.fs.logDirectory     hdfs://node-master:9000/spark-logs
spark.history.fs.update.interval  10s
spark.history.ui.port             18080
{{< /file>}}

    You may want to use a different update interval than the default `10s`. If you specify a bigger interval, you will have some delay between what you see in the History Server and the real time status of your application. If you use a shorter interval, you will increase I/O on the HDFS.

4.  Run the History Server:

        $SPARK_HOME/sbin/start-history-server.sh

5.  Repeat steps from previous section to start a job with `spark-submit` that will generate some logs in the HDFS:

6.  Access the History Server by navigating to http://node-master:18080 in a web browser:

## Run the Spark Shell

The Spark shell provides an interactive way to examine and work with your data.

1.  Put some data into HDFS for analysis. This example uses the text of *Alice In Wonderland* from the Gutenberg project:

        cd /home/hadoop
        wget -O alice.txt https://www.gutenberg.org/files/11/11-0.txt
        hdfs dfs -mkdir inputs
        hdfs dfs -put alice.txt inputs

2.  Start the Spark shell:

        spark-shell

        var input = spark.read.textFile("inputs/alice.txt")
        // Count the number of non blank lines
        input.filter(line => line.length()>0).count()

The Scala Spark API is not covered in this guide. You can refer to the official documentation on the Apache Spark documentation page for detailed information.

## Where to Go Next ?

"With your Spark cluster up and running, you can now:

- Learn to create Spark applications using Scala, Java, Python, or R APIs from the Apache Spark Programming Guide.
- Explore and manipulate your data using Spark SQL.
- Incorporate machine learning capabilities into your applications using Apache MLlib."

