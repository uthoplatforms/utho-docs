---
slug: "Visualizing Apache Web Server Logs Using Elastic Stack on Debian 8"
description: 'This guide will show how to use Elasticsearch, Logstash, and Kibana to collect and visualize web server logs.'
og_description: 'The Elastic Stack - Elasticsearch, Logstash, & Kibana - provides a free, open-source solution to search, collect, and analyze data. This guide shows how to install all three components to explore Apache web server logs in Kibana.'
keywords: ["apache debian 8", "linux web server", "elasticsearch", "logstash", "kibana", "elk stack", "elastic stack"]
published: 2024-07-09
modified: 2024-07-09
modified_by:
  name: Utho
title: "Visualizing Apache Logs Using the Elastic Stack on Debian 8"
title_meta: "Visualizing Apache Logs With Elastic Stack on Debian 8"
dedicated_cpu_link: true
tags: ["debian","analytics","database","monitoring"]
relations:
    platform:
        key: visualize-apache-logs-using-elastic-stack
        keywords:
            - distribution: Debian 8
authors: ["Pawan Kumar"]
---

## What is an Elastic Stack?

The Elastic stack, comprising Elasticsearch, Logstash, and Kibana, offers a comprehensive suite of open-source tools. It enables searching, collection, analysis of data from diverse sources and formats, and real-time visualization.

This guide outlines the installation of all three components and demonstrates their use in exploring Apache web server logs using Kibana. The instructions cover the setup of version 5 of the Elastic stack, the latest available at the time of writing.

{{< note >}}
This guide is tailored for a non-root user. Commands that necessitate elevated privileges are prefixed with sudo. If you're unfamiliar with the sudo command, refer to our Users and Groups guide for detailed instructions.
{{< /note >}}

## Before You Begin

If you haven't already, create a Utho account and set up a Compute Instance using our Getting Started with Utho and Creating a Compute Instance guides.

Secure your Compute Instance by following our Setting Up and Securing a Compute Instance guide. This includes updating your system, configuring the timezone, setting the hostname, creating a limited user account, and tightening SSH access.

Refer to our Apache Web Server on Debian 8 (Jessie) guide to install and configure Apache on your server.

## Install OpenJDK 8

To run Elasticsearch, you need the latest Java versions, which aren't compatible with the default OpenJDK version on Debian Jessie. Follow these steps to install OpenJDK 8 from jessie-backports:

Add jessie-backports to your APT sources list:

        echo deb http://ftp.debian.org/debian jessie-backports main | sudo tee -a /etc/apt/sources.list.d/jessie-backports.list

Update the APT package cache:

        sudo apt-get update

Install OpenJDK 8:

        sudo apt-get install -y -t jessie-backports openjdk-8-jre-headless ca-certificates-java

Ensure your system is using the updated Java version. Execute the following command and select java-8-openjdk-amd64/jre/bin/java from the interactive menu that appears:"

        sudo update-alternatives --config java

## Install Elastic APT Repository

To ensure all necessary packages are available, start by installing the Elastic package repositories before proceeding with individual services.

Install the official Elastic APT package signing key:

        wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

Install the `apt-transport-https` package, necessary for fetching deb packages served via HTTPS on Debian 8.

        sudo apt-get install apt-transport-https

Add the Elastic APT repository information to your server's list of sources.

        echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list

Refresh the list of available packages.

        sudo apt-get update

## Install Elastic Stack

Before configuring and loading log data, install each piece of the stack, individually.

### Elasticsearch

1.  Install the `elasticsearch` package:

         sudo apt-get install elasticsearch

Adjust the JVM heap size to approximately half of your server's total memory. For instance, if your server has 1GB of RAM, modify the Xms and Xmx values in the /etc/elasticsearch/jvm.options file as follows, keeping other settings unchanged:

    {{< file "/etc/elasticsearch/jvm.options" aconf >}}
-Xms512m
-Xmx512m

{{< /file >}}

3.  Start and enable the `elasticsearch` service:

         sudo systemctl enable elasticsearch
         sudo systemctl start elasticsearch

3.  Wait a few moments for the service to start, then confirm that the Elasticsearch API is available:

         curl localhost:9200

Elasticsearch may require some time to initialize. To check if the service has started successfully, use the systemctl status elasticsearch command to view the latest logs. The Elasticsearch REST API should return a JSON response similar to this:

         {
           "name" : "e5iAE99",
           "cluster_name" : "elasticsearch",
           "cluster_uuid" : "lzuLNZa0Qo-7_puJZZjR4Q",
           "version" : {
             "number" : "5.5.2",
             "build_hash" : "b2f0c09",
             "build_date" : "2017-08-14T12:33:14.154Z",
             "build_snapshot" : false,
             "lucene_version" : "6.6.0"
           },
           "tagline" : "You Know, for Search"
         }

### Logstash

Install the `logstash` package:

     sudo apt-get install logstash

### Kibana

Install the `kibana` package:

     sudo apt-get install kibana

## Configure Elastic Stack

### Elasticsearch

By default, Elasticsearch creates five shards and one replica for each index. These settings are suitable for production deployments. However, for this tutorial with a single server Elasticsearch setup, multiple shards and replicas are unnecessary and can introduce unnecessary overhead.

Create a temporary JSON file containing an index template. This template instructs Elasticsearch to set the number of shards to one and the number of replicas to zero for all matching index names (using the wildcard *):

    {{< file "template.json" json >}}
{
  "template": "*",
  "settings": {
    "index": {
      "number_of_shards": 1,
      "number_of_replicas": 0
    }
  }
}

{{< /file >}}

2.  Use `curl` to create an index template with these settings that'll be applied to all indices created hereafter:

        curl -XPUT http://localhost:9200/_template/defaults -d @template.json

3.  Elasticsearch should return:

        {"acknowledged":true}

### Logstash

To collect Apache access logs, Logstash needs to be configured to monitor relevant files and process them for Elasticsearch. This configuration assumes that the site has been set up following the earlier Apache Web Server on Debian 8 (Jessie) guide to locate the correct log path.

1.  Create the following Logstash configuration:

    {{< file "/etc/logstash/conf.d/apache.conf" aconf >}}
input {
  file {
    path => '/var/www/*/logs/access.log'
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


2.  Start and enable `logstash`:

        sudo systemctl enable logstash
        sudo systemctl start logstash

### Kibana

Open `/etc/kibana/kibana.yml`. Uncomment the following two lines and replace localhost with the public IP address of your Utho. Ensure that your server's firewall allows connections on port 5602.

    {{< file "/etc/kibana/kibana.yml" >}}
server.port: 5602
server.host: "localhost"

{{< /file >}}

Enable and start the Kibana service:

        sudo systemctl enable kibana
        sudo systemctl start kibana

For Kibana to locate log entries and configure an index pattern, logs must first be sent to Elasticsearch. Once all three daemons are running, Logstash should collect log files and store them in Elasticsearch. To generate logs, send several requests to Apache:

        for i in `seq 1 5` ; do curl localhost ; sleep 0.2 ; done

Now, access Kibana in your web browser. Kibana listens for requests on port `5602`, so depending on your Utho's setup, you might need to port-forward Kibana via SSH. The landing page should resemble the following:

 This screen allows you to create an index pattern, which helps Kibana identify the indices to search when browsing logs and building dashboards. The default value, logstash-*, matches the indices typically created by Logstash. Clicking "Create" on this screen is sufficient to configure Kibana and start exploring logs.

    {{< note respectIndent=false >}}
In this section, logs will be fetched based on a time window located in the upper right corner of the Kibana interface (e.g., "Last 15 Minutes"). If at any point log entries are no longer visible in the Kibana interface, click on this time range and select a broader option, like "Last Hour," "Last 1 Hour," or "Last 4 Hours," to view more logs
{{< /note >}}

## View Logs

After executing the previous curl commands, which created entries in the Apache access logs, Logstash indexes them in Elasticsearch. You can now view these logs in Kibana.

The "Discover" tab on the left side of Kibana's interface (typically open by default after setting up your index pattern) displays a timeline of log events:

As more requests are made to the web server via curl or a browser, additional logs can be viewed and searched within Kibana. The Discover tab provides a convenient way to explore the structure of indexed logs and decide what to search and analyze.

To examine the details of a log entry, click the dropdown arrow to view individual document fields:

Fields represent parsed values from Apache logs, such as agent for the User-Agent header and bytes indicating the size of the web server response.

### Analyze Logs

Before proceeding, generate a few dummy 404 log events in your web server logs to illustrate how to search and analyze logs in Kibana

    for i in `seq 1 2` ; do curl localhost/notfound-$i ; sleep 0.2 ; done

#### Search Data

The search bar at the top of the Kibana interface enables you to search queries using the query string syntax to locate results.

For instance, to find the 404 error requests among the 200 OK requests you generated, enter the following into the search box:

    response:404

Then, click the search magnifying glass icon.

The user interface will now display logs that specifically contain the "404" code in their response field.

#### Analyze Data

Kibana offers various Elasticsearch query capabilities to gain insights from indexed data. For instance, you can analyze traffic resulting in a "404 - not found" response code using aggregations to extract and display useful data summaries directly in Kibana.

To create such visualizations, start by navigating to the "Visualize" tab:

Click on one of the icons to begin creating a visualization. For example, select "Pie" to create a new pie chart.

Choose the logstash-* index pattern to specify where the data for the pie chart will be sourced from.

Once the pie chart appears, configure it using the options in the left-side pane:

- Click "Split Slices" to create multiple slices in the visualization.
- From the "Aggregation" drop-down menu, select "Terms" to base each slice on unique terms from a field.
- Choose response.keyword from the "Field" drop-down menu to use the response field for slice sizes.
- Click the "Play" button to update the pie chart with your configuration.

Notice how only a portion of requests result in a 404 response code (adjust the time span if necessary to view recent events). This method of summarizing field values in your logs can similarly analyze other fields like HTTP verbs (GET, POST, etc.) or numeric data such as total bytes transferred over time.

To save this visualization for future use, click the "Save" button at the top of the window. Provide a name and save it permanently to Elasticsearch.