---
slug: Install elasticsearch on centos
description: 'Shortguide for installing Elasticsearch on Fedora systems'
license: '[CC BY-ND 4.0](https://creativecommons.org/licenses/by-nd/4.0)'
keywords: ["elasticsearch", "elastic stack", "red hat", "centos"]
modified: 2024-07-09
modified_by:
  name: Utho
title: "Install Elasticsearch on Fedora, Red Hat, and CentOS"
published: 2024-07-09
headless: true
relations:
    platform:
        key: install-elasticsearch
        keywords:
            - distribution: CentOS
tags: ["database"]
authors: ["Pawan Kumar"]
---

Trust the Elastic signing key:

        sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

Create a yum repository configuration to use the Elastic yum repository:

    {{< file "/etc/yum.repos.d/elastic.repo" ini >}}
[elasticsearch-6.x] name=Elastic repository for 6.x packages baseurl=https://artifacts.elastic.co/packages/6.x/yum gpgcheck=1 gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch enabled=1 autorefresh=1 type=rpm-md
{{< /file >}}

Update the yum cache to ensure the latest packages will be installed:

        sudo yum update
        Debian Based Distributions

Install the official Elastic APT package signing key:

        wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

Install the `elasticsearch` package:

        sudo yum install -y elasticsearch

Configure the JVM heap size to about half of your server's available memory. For instance, if your server has 1GB of RAM, adjust the Xms and Xmx values in the `/etc/elasticsearch/jvm.options` file to 512m, leaving other settings in this file unchanged:"

    {{< file "/etc/elasticsearch/jvm.options" aconf >}}
-Xms512m -Xmx512m
{{< /file >}}

Enable and start the `elasticsearch` service:

        sudo systemctl enable elasticsearch
        sudo systemctl start elasticsearch

Allow a few moments for the service to start, then verify the availability of the Elasticsearch API:"

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

To verify if the service has started successfully, check the most recent logs:

        systemctl status elasticsearch
