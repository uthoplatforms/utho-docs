---
slug: Visualize Apache Web Server Logs Using Elastic Stack on CentOS Stream 8
description: "This guide shows how to install all three components of Elastic Stack on CentOS to explore Apache web server logs in Kibana."
keywords: ["apache centos stream 8", "linux web server", "elasticsearch", "logstash", "kibana", "elk stack", "elastic stack"]
published: 2024-07-09
modified: 2024-07-09
modified_by:
  name: Utho
title: "Visualizing Apache Logs Using Elastic Stack on CentOS Stream 8"
title_meta: "Visualizing Apache Logs With Elastic Stack on CentOS Stream 8"
dedicated_cpu_link: true
tags: ["centos","analytics","database","monitoring","apache"]
relations:
    platform:
        key: visualize-apache-logs-using-elastic-stack
        keywords:
            - distribution: CentOS Stream 8
author: ["Pawan Kumar"]
---

The Elastic stack comprises Elasticsearch, Logstash, and Kibana. These tools offer a free and open-source solution for searching, collecting, and analyzing data from any source and in any format. They also provide real-time visualization of the data.

{{< note >}}
This guide utilizes version 7.11 of the Elastic Stack, which is the latest version available at the time of writing.
{{< /note >}}

## In this Guide

This guide covers the following steps:

Install Elasticsearch, Logstash, and Kibana.

Configure Elasticsearch with one shard and no replicas, suitable for testing on a single Utho.

Configure Logstash to process logs from the Apache web server.

Connect Kibana to the Logstash index.

View the logs from your connected Logstash index in Kibana.

Search the logs using Kibana.

Visualize the logs in Kibana using a pie chart categorized by HTTP response code.

## Before You Begin

Start by reviewing our Getting Started guide and create a Utho to install the Elastic Stack on. Follow the steps to set your Utho's hostname and timezone.

    {{< note respectIndent=false >}}
Multiple services will run on a single Utho in this guide. We recommend using a Utho instance size of at least 2G (e.g., g6-standard-1) to support these services effectively.
{{< /note >}}

This guide employs sudo wherever necessary. Refer to our Setting Up and Securing a Compute Instance guide to create a standard user account with sudo privileges, enhance SSH security, and disable unnecessary network services.

    {{< note respectIndent=false >}}
Commands requiring elevated privileges are prefixed with sudo. If you are unfamiliar with the `sudo` command, you can refer to our Users and Groups guide for detailed instructions.
{{< /note >}}

1.  Update your system:

        sudo yum update

Refer to our Apache Web Server on CentOS 8 guide to set up and configure Apache on your server. The instructions in this guide are compatible with CentOS Stream 8.

Elasticsearch includes its own bundled Java runtime, but Logstash requires Java to be installed on the system. Install the OpenJDK package for CentOS 8:

        sudo yum install java-11-openjdk-headless

## Install the Elastic Stack

Before configuring and loading log data, install each component of the stack individually.

### Install the Elastic Yum Repository

First, install the Elastic package repository, which contains all the necessary packages for this tutorial, before proceeding with the installation of individual services.

1.  Trust the official Elastic package signing key:

        sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

1.  Create a yum repository configuration to use the Elastic yum repository:

    {{< file "/etc/yum.repos.d/elastic.repo" ini >}}
[elastic-7.x]
name=Elasticsearch repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=0
autorefresh=1
type=rpm-md
{{< /file >}}

    {{< note respectIndent=false >}}
The repository configuration file does not enable the repository by default when using yum commands to prevent potential package conflicts with default packages. In subsequent commands throughout this guide, use the yum flag --enablerepo=elastic-7.x whenever installing a package from this repository is necessary.
{{< /note >}}

### Install Elasticsearch

1.  Install the `elasticsearch` package:

        sudo yum --enablerepo=elastic-7.x install elasticsearch

Adjust the JVM heap size to approximately one-quarter of your server's available memory. For instance, on a Utho instance with 2GB of memory, ensure the Xms and Xmx values in the /etc/elasticsearch/jvm.options file are configured as follows, while retaining the other settings in the file unchanged.

    {{< file "/etc/elasticsearch/jvm.options" aconf >}}
-Xms512m
-Xmx512m
{{< /file >}}

    {{< note respectIndent=false >}}
By default, these options are commented out and have the following values. So, you need to uncomment the lines as well (by removing the two `#` symbols at the beginning of the line):

```
## -Xms4g
## -Xmx4g
```
{{< /note >}}

1.  Start and enable the `elasticsearch` service:

         sudo systemctl enable elasticsearch
         sudo systemctl start elasticsearch

1.  Wait a few moments for the service to start, then confirm that the Elasticsearch API is available:

         curl localhost:9200

Elasticsearch may take some time to start up. To check if the service has started successfully, you can use the `systemctl status elasticsearch` command to view the most recent logs. Once Elasticsearch has started, the Elasticsearch REST API should return a JSON response similar to the following:

    {{< output >}}
{
  "name" : "localhost",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "tTuwONK4QMW918XkF9VecQ",
  "version" : {
    "number" : "7.11.1",
    "build_flavor" : "default",
    "build_type" : "deb",
    "build_hash" : "ff17057114c2199c9c1bbecc727003a907c0db7a",
    "build_date" : "2021-02-15T13:44:09.394032Z",
    "build_snapshot" : false,
    "lucene_version" : "8.7.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
{{< /output >}}

### Install Logstash

Install the `logstash` package:

     sudo yum --enablerepo=elastic-7.x install logstash

### Install Kibana

Install the `kibana` package:

     sudo yum --enablerepo=elastic-7.x install kibana

## Configure the Elastic Stack

### Configure Elasticsearch

By default, Elasticsearch creates five shards and one replica for each newly created index, which are suitable settings for production deployments. However, in this tutorial, Elasticsearch is configured on a single server, making multiple shards and replicas unnecessary and potentially adding overhead.

Create a temporary JSON file in your user's home folder containing an index template. This template directs Elasticsearch to set the number of shards to one and the number of replicas to zero for all matching index names (using a wildcard *):

    {{< file "template.json" json >}}
{
  "index_patterns": ["*"],
  "template": {
    "settings": {
      "index": {
        "number_of_shards": 1,
        "number_of_replicas": 0
      }
    }
  }
}

{{< /file >}}


Utilize curl to create an index template with these settings, which will be applied to all indices created subsequently:

        curl -XPUT -H'Content-type: application/json' http://localhost:9200/_index_template/defaults -d @template.json

    Elasticsearch should return:

    {{< output >}}
{"acknowledged":true}
{{< /output >}}

### Configure Logstash

To collect Apache access logs, Logstash needs to be configured to monitor specific files and process them before sending them to Elasticsearch.

Adjust the JVM heap size to approximately one quarter of your server's available memory. For instance, if your server has 2GB of RAM, modify the Xms and Xmx values in the `/etc/logstash/jvm.options` file as follows, while keeping the other values unchanged:

    {{< file "/etc/logstash/jvm.options" aconf >}}
-Xms512m
-Xmx512m
{{< /file >}}

    {{< note respectIndent=false >}}
These options have the following values by default:

```
## -Xms1g
## -Xmx1g
```
{{< /note >}}

1.  Create the following Logstash configuration:

    {{< file "/etc/logstash/conf.d/apache.conf" aconf >}}
input {
  file {
    path => '/var/www/*/logs/access.log'
    start_position => 'beginning'
  }
}

filter {
  grok {
    match => { "message" => "%{COMBINEDAPACHELOG}" }
  }
}

output {
  elasticsearch { }
}
{{< /file >}}

    {{< note respectIndent=false >}}
This example configuration assumes that your website logs are located at /var/www/*/logs/access.log.

If your site was set up following the Configure Apache for Virtual Hosting section of the Apache Web Server on CentOS 8 guide, then your logs will be stored in this directory. If your website logs are stored elsewhere, update the file path in the configuration file before proceeding.

1.  Start and enable `logstash`:

        sudo systemctl enable logstash
        sudo systemctl start logstash

### Configure Kibana

Enable and start the Kibana service:

        sudo systemctl enable kibana
        sudo systemctl start kibana

For Kibana to retrieve log entries, logs must first be sent to Elasticsearch. Once all three daemons are running, Logstash collects log files and stores them in Elasticsearch. To generate logs, send multiple requests to Apache:

        for i in `seq 1 5` ; do curl localhost ; sleep 0.2 ; done

By default, Kibana binds to the local address 127.0.0.2, allowing connections only from localhost. This setup is recommended to prevent exposing the dashboard to the public internet. However, to access Kibana's web interface in a browser, you can use the ssh command to forward the port to your workstation.

Run the following command in a local terminal (on your computer, not your Utho). Keep the command running throughout the tutorial. Press Ctrl-C to terminate the port forward when you finish the tutorial.

        ssh -N -L 5601:localhost:5601 <your Utho's IP address>

Next, open Kibana in your browser at http://localhost:5602. The landing page should resemble the following. Click on Explore on my own to start configuring Kibana.

    {{< note respectIndent=false >}}
During its initial startup, Kibana performs several optimization tasks that may cause a delay in availability. If the web page is not accessible immediately, wait a few minutes or check the logs using the command `sudo journalctl -u kibana`.
{{< /note >}}

The following dashboard interface appears. Click on the hamburger button on the left side of the top navigation to open the sidebar interface.

Select the Discover menu item from the sidebar.

Kibana prompts you to create an index pattern to identify the logs to retrieve. Click on the Create index pattern button.

The Create index pattern form will appear. Enter logstash-* into the Index pattern name field. Then click the Next step button.

From the Time field drop-down menu, select @timestamp as the time field, corresponding to the parsed time from web server logs. Click the Create index pattern button to continue.

Kibana is now ready to display logs stored in indices matching the logstash-* wildcard pattern.

## View Logs in Kibana

When the earlier `curl` commands generated entries in the Apache access logs, Logstash indexed them into Elasticsearch. Now, these logs are visible in Kibana.

{{< note >}}
Logs are retrieved based on a time window displayed in the upper right corner of the Kibana interface. By default, this panel shows "Last 15 minutes". Sometimes, you may not see log entries in the Kibana interface. If this occurs, click on the timespan panel and select a broader range, like "Last hour" or "Last 4 hours". After selecting an appropriate time range, your logs should appear.
{{< /note >}}

Expand the available menu options by clicking the hamburger icon on the left.

Select the Discover menu item.

The Discover interface displays a timeline of log events:

As more requests are made to the web server via curl or a browser over time, additional logs can be viewed and searched within Kibana. The Discover tab is useful for understanding the structure of indexed logs and planning searches and analyses.

To view details of a log entry, click the drop-down arrow to explore individual document fields:

Fields represent parsed values from Apache logs, such as agent (representing the User-Agent header) and bytes (indicating the size of the web server response).

## Search Logs in Kibana

Generate a couple of dummy 404 log events in your web server logs to demonstrate how to search and analyze logs within Kibana:

        for i in `seq 1 2` ; do curl localhost/notfound-$i ; sleep 0.2 ; done

Utilize the top search bar in the Kibana interface to search queries using the Kibana Query Language and find results. For instance, to locate 404 error requests generated among 200 OK requests, input the following into the search box:

        response:404

Then, click the **Update** button. The user interface now only returns logs that contain the "404" code in their response field.

## Visualize Logs in Kibana

Kibana supports various Elasticsearch queries to gain insights into indexed data. For instance, you can analyze traffic that resulted in a "404 - not found" response code. Using aggregations, you can extract and display useful data summaries directly in Kibana.

To create a visualization, start by selecting the Visualize menu item from the sidebar. You may need to expand the menu by clicking the hamburger icon in the upper left-hand corner of the interface first:

Click on the Create new visualization button on the page.

Choose the Aggregation based option from the available choices.

Scroll down and select Pie to create a new pie chart. It may be necessary to scroll further to locate the Pie visualization option.

Select the logstash-* index pattern to specify where the data for the pie chart will be retrieved from.

Once selected, a pie chart will appear in the interface, ready for configuration. Follow these steps to configure the visualization in the pane that appears on the left of the pie chart:

- Click on + Add under the Buckets section in the right-hand sidebar.
- Choose Split Slices to create multiple slices in the visualization.
- From the Aggregation drop-down menu, select Terms to base each slice of the pie chart on unique terms from a field.
- From the Field drop-down menu, select response.keyword. This specifies that the size of each pie chart slice is determined by the response field.
- Finally, click the Update button to apply the changes and finalize the visualization.

    {{< note respectIndent=false >}}
You may need to view a longer time span for the pie chart to show both 200 and 404 HTTP responses. This may be done by clicking the calendar icon next to the search bar and selecting a longer time period, such as "Last 1 Hour".
{{< /note >}}

Note that only a fraction of requests have resulted in a 404 response code (adjust the time span if your curl requests occurred earlier than your current view). This method of aggregating statistics based on field values in your logs can similarly be applied to other fields like HTTP verbs (GET, POST, etc.). You can also create summaries of numerical data, such as total bytes transferred over a specific time frame.

To save this visualization for future use, click the Save button located near the top of the browser window. Give your visualization a name and save it permanently to Elasticsearch.