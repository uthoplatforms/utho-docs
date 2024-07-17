---
slug: Visualize apache web server logs using elastic stack on ubuntu 18.04
description: "This guide shows how to install all three Elastic Stack components to explore Apache web server logs in Kibana."
external_resources:
 - '[Elastic Documentation](https://www.elastic.co/guide/index.html)'
keywords: ["apache ubuntu 18.04", "linux web server", "elasticsearch", "logstash", "kibana", "elk stack", "elastic stack"]
published: 2024-07-09
modified: 2024-07-09
modified_by:
  name: Utho
title: "Visualize Apache Logs With Elastic Stack on Ubuntu 18.04"
dedicated_cpu_link: true
tags: ["ubuntu","analytics","database","monitoring","apache","digital agencies"]
relations:
    platform:
        key: visualize-apache-logs-using-elastic-stack
        keywords:
            - distribution: Ubuntu 18.04
author: ["Pawan Kumar"]
---

The Elastic stack comprises Elasticsearch, Logstash, and Kibana. This suite of tools offers a comprehensive open-source solution for searching, collecting, and analyzing data from diverse sources and formats. Additionally, it provides real-time visualization capabilities.

{{< note >}}
Version 7.11 of the Elastic stack is used in this guide, which is the latest at the time of this writing.
{{< /note >}}

## In this Guide

This guide covers the following steps:

Install Elasticsearch, Logstash, and Kibana.

Configure Elasticsearch to run with one shard and no replicas, suitable for single Utho testing.

Configure Logstash to process logs from the Apache web server.

Connect Kibana to the Logstash index.

View the logs from your connected Logstash index in Kibana.

Search the logs using Kibana.

Visualize the logs in a pie chart categorized by HTTP response code in Kibana.

## Before You Begin

Begin by following our Getting Started guide to create a Utho and prepare it for installing the Elastic Stack. Ensure you set up your Utho's hostname and timezone.

    {{< note respectIndent=false >}}
"In this guide, multiple services are hosted on a single Utho. We recommend using a Utho instance with at least 2GB of RAM (g6-standard-1) to adequately support these services."
{{< /note >}}

This guide utilizes sudo whenever necessary. Refer to our Setting Up and Securing a Compute Instance guide to set up a standard user account with sudo privileges, secure SSH access, and eliminate unnecessary network services.

    {{< note respectIndent=false >}}
Commands that need elevated privileges are prefixed with `sudo`. If you're unfamiliar with the sudo command, you can refer to our Users and Groups guide for detailed instructions.
{{< /note >}}

1.  Update your system:

        sudo apt-get update && sudo apt-get upgrade

Follow the instructions in our Apache Web Server on Ubuntu 18.04 guide to install and configure Apache on your server.

Elasticsearch includes its own Java runtime, but Logstash requires Java to be installed on the system. Install the default Java version available on Ubuntu 18.04:

        sudo apt-get install default-jre-headless

## Install the Elastic Stack

Before configuring and loading log data, install each piece of the stack, individually.

### Install the Elastic APT Repository

The Elastic package repositories contain all of the necessary packages for this tutorial, so install it first before proceeding with the individual services.

1.  Install the official Elastic APT package signing key:

        wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

1.  Add the APT repository information to your server's list of sources:

        echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list

1.  Refresh the list of available packages:

        sudo apt-get update

### Install Elasticsearch

1.  Install the `elasticsearch` package:

        sudo apt-get install elasticsearch

1.  Set the JVM heap size to approximately one-quarter of your server's available memory. For example, on a Utho instance with 2GB of memory, ensure that the `Xms` and `Xmx` values in the `/etc/elasticsearch/jvm.options` file are set to the following, and leave the other values in this file unchanged.

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

    Elasticsearch may take some time to start up. If you need to determine whether the service has started successfully or not, you can use the `systemctl status elasticsearch` command to see the most recent logs. The Elasticsearch REST API should return a JSON response similar to the following:

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

     sudo apt-get install logstash

### Install Kibana

Install the `kibana` package:

     sudo apt-get install kibana

## Configure the Elastic Stack

### Configure Elasticsearch

By default, Elasticsearch creates five shards and one replica for every index that's created. When deploying to production, these are reasonable settings to use. In this tutorial, only one server is used in the Elasticsearch setup. Multiple shards and replicas are unnecessary. Changing these defaults can avoid unnecessary overhead.

1.  Create a temporary JSON file in your user's home folder with an *index template*. This template instructs Elasticsearch to set the number of shards to one and the number of replicas to zero for all matching index names (in this case, a wildcard `*`):

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


1.  Use `curl` to create an index template with these settings that is applied to all indices created hereafter:

        curl -XPUT -H'Content-type: application/json' http://localhost:9200/_index_template/defaults -d @template.json

    Elasticsearch should return:

    {{< output >}}
{"acknowledged":true}
{{< /output >}}

### Configure Logstash

In order to collect Apache access logs, Logstash must be configured to watch any necessary files and then process them, eventually sending them to Elasticsearch.

1.  Set the JVM heap size to approximately one quarter of your server's available memory. For example, if your server has 2GB of RAM, change the `Xms` and `Xmx` values in the `/etc/logstash/jvm.options` file to the following, and leave the other values in this file unchanged:

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
This example configuration assumes that your website logs are stored in the `/var/www/*/logs/access.log` file path.

If your site was set up by following the [Configure Apache for Virtual Hosting](/docs/guides/how-to-install-apache-web-server-ubuntu-18-04/#configure-virtual-hosting) section of the [Apache Web Server on Ubuntu 18.04](/docs/guides/how-to-install-apache-web-server-ubuntu-18-04/) guide, then your logs are stored in this location. If you website logs are stored in another location, update the file path in the configuration file before proceeding.
{{< /note >}}

1.  Start and enable `logstash`:

        sudo systemctl enable logstash
        sudo systemctl start logstash

### Configure Kibana

Enable and start the Kibana service:

        sudo systemctl enable kibana
        sudo systemctl start kibana

In order for Kibana to find log entries, logs must first be sent to Elasticsearch. With the three daemons started, log files should be collected with Logstash and stored in Elasticsearch. To generate logs, issue several requests to Apache:

        for i in `seq 1 5` ; do curl localhost ; sleep 0.2 ; done

By default, Kibana binds to the local address `127.0.0.2`. This only permits connections that originate from localhost. This is recommended in order to avoid exposing the dashboard to the public internet. However, in order to access Kibana's web interface in a browser, the `ssh` command can forward the port to your workstation.

    Run the following command in a local terminal (on your local computer, not your Utho). Leave the command running for the duration of the tutorial. Press `Ctrl-C` to end the port forward at the conclusion of this tutorial.

        ssh -N -L 5601:localhost:5601 <your Utho's IP address>

Open Kibana in your browser by navigating to http://localhost:5602. The landing page should resemble the following screenshot. Click Explore on my own to start configuring Kibana.

    {{< note respectIndent=false >}}
The first time that Kibana starts, the daemon performs several optimization steps that may delay startup time. If the web page is not available immediately, wait a few minutes or check the logs with the command `sudo journalctl -u kibana`.
{{< /note >}}

Click the hamburger button on the top left to open the sidebar interface.

Select Discover from the sidebar menu.

Kibana will prompt you to create an index pattern to identify the logs to retrieve. Click the Create index pattern button.

In the Create index pattern form, enter logstash-* in the Index pattern name field. Then click Next step to proceed.

From the Time field dropdown menu, select @timestamp as the time field, which corresponds to the parsed time from web server logs. Click Create index pattern to finalize.

Kibana is now configured to display logs stored in indices matching the logstash-* wildcard pattern.

## View Logs in Kibana

When the previous `curl` commands generated entries in the Apache access logs, Logstash indexed them in Elasticsearch. These are now visible in Kibana.

{{< note >}}
Logs in Kibana are fetched based on the time window displayed in the upper right corner of the interface. By default, this panel shows "Last 15 minutes". If you notice that log entries are not visible in the Kibana interface, click on the timespan panel and select a broader range, such as "Last hour" or "Last 4 hours". After selecting an appropriate time range, your logs should become visible.
{{< /note >}}

Expand the available menu options by clicking the hamburger icon on the left sidebar.

Select Discover from the menu.

The Discover interface displays a timeline of log events:

Over time, as requests are made to the web server via curl or a browser, additional logs can be viewed and searched in Kibana. The Discover tab is useful for understanding the structure of indexed logs and determining what to search and analyze.

To view the details of a log entry, click the dropdown arrow to reveal individual document fields:

Fields represent parsed values from Apache logs, such as agent for the User-Agent header and bytes indicating the size of the web server response.

## Search Logs in Kibana

1. Generate a couple of dummy 404 log events in your web server logs to demonstrate how to search and analyze logs within Kibana:

        for i in `seq 1 2` ; do curl localhost/notfound-$i ; sleep 0.2 ; done

1. The top search bar within the Kibana interface allows you to search for queries following the [Kibana Query Language](https://www.elastic.co/guide/en/kibana/current/kuery-query.html) to find results. For example, to find the 404 error requests you generated from among 200 OK requests, enter the following in the search box:

        response:404

1. Then, click the **Update** button. The user interface now only returns logs that contain the "404" code in their response field.

## Visualize Logs in Kibana

Kibana offers various Elasticsearch query capabilities to gain insights from indexed data, such as analyzing traffic resulting in a "404 - not found" response code. Using aggregations, you can extract useful data summaries and display them directly in Kibana.

To create a visualization, start by expanding the sidebar using the hamburger icon in the upper left-hand corner of the interface and selecting Visualize.

Click Create new visualization on the page that appears.

Choose Aggregation based from the available options.

Select Pie to create a new pie chart. You may need to scroll down to locate the Pie visualization.

Choose the logstash-* index pattern to specify where the data for the pie chart will be sourced from.

A pie chart will now appear in the interface, ready for configuration. Follow these steps to configure the visualization in the sidebar to the left of the pie chart:

- Click + Add under the Buckets section in the right-hand sidebar.
- Choose Split Slices to generate multiple slices in the visualization.
- From the Aggregation drop-down menu, select Terms to base each slice on unique terms from a field.
- Select response.keyword from the Field drop-down menu to use the response field for slice sizes.
- Finally, click Update to apply your changes and finalize the pie chart visualization.

    {{< note respectIndent=false >}}
To ensure the pie chart displays both 200 and 404 HTTP responses, consider extending the time span. Click the calendar icon beside the search bar and select a broader time range, like "Last 1 Hour".
{{< /note >}}

Note that only a fraction of requests may have resulted in a 404 response code (adjust the time span if your curl requests occurred earlier than the current view). This method of collecting summarized statistics about field values in your logs can also be applied to other fields, such as HTTP verbs (GET, POST, etc.). Additionally, you can create summaries of numerical data, such as the total bytes transferred over a specific time frame.

To save this visualization for future use, click the `Save` button at the top of the browser window. Name the visualization and save it permanently to Elasticsearch.

