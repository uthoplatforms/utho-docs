---
slug: Install elasticsearch on debian ubuntu
description: 'Shortguide for installing Elasticsearch on Debian systems'
keywords: ["elasticsearch", "elastic stack"]
modified: 2024-07-09
modified_by:
  name: Utho
title: "Install Elasticsearch on Debian and Ubuntu"
published: 2024-07-09
headless: true
relations:
    platform:
        key: install-elasticsearch
        keywords:
            - distribution: Debian/Ubuntu
tags: ["database"]
aliases: ['/databases/elasticsearch/install_elasticsearch_debian_ubuntu/']
authors: ["Pawan Kumar"]
---

Install the official Elastic APT package signing key:

        wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

Install the `apt-transport-https` package, necessary for retrieving deb packages served over HTTPS:

        sudo apt-get install apt-transport-https

Add the APT repository information to your server's list of sources:

        echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic.list

Update the list of available packages:

        sudo apt-get update

Install the `elasticsearch` package:

        sudo apt-get install -y elasticsearch

Configure the JVM heap size to approximately half of your server's available memory. For instance, if your server has 1GB of RAM, adjust the Xms and Xmx values in the /etc/elasticsearch/jvm.options file to 512m. Keep the other settings in this file unchanged:

    {{< file "/etc/elasticsearch/jvm.options" conf >}}
-Xms512m
-Xmx512m
{{< /file >}}

Enable and start the `elasticsearch` service:

        sudo systemctl enable elasticsearch
        sudo systemctl start elasticsearch

Wait a few moments for the service to start, then confirm that the Elasticsearch API is available:

        curl localhost:9200

    The Elasticsearch REST API should return a JSON response similar to the following:

    {{< output >}}
{
  "name" : "Sch1T0D",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "MH6WKAm0Qz2r8jFK-TcbNg",
  "version" : {
    "number" : "6.1.1",
    "build_hash" : "bd92e7f",
    "build_date" : "2017-12-17T20:23:25.338Z",
    "build_snapshot" : false,
    "lucene_version" : "7.1.0",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
{{</ output >}}

To determine whether or not the service has started successfully, view the most recent logs:

        systemctl status elasticsearch
