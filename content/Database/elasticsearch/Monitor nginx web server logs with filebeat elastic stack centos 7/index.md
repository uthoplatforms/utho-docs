---
slug: Monitor nginx web server logs with filebeat elastic stack centos 7
description: "This guide shows how to install four components of Elastic Stack - Filebeat, Metricbeat, Elasticsearch, and Kibana - to monitor a typical NGINX webserver."
keywords: ['nginx centos 7','elasticsearch','filebeat','metricbeat','beats','kibana','elk stack','elastic stack']
published: 2024-07-09
modified: 2024-07-09
modified_by:
  name: Utho
title: "Monitoring NGINX Using the Elastic Stack on Centos 7"
title_meta: "How to Monitor NGINX Using the Elastic Stack on CentOS 7"
dedicated_cpu_link: true
tags: ["analytics","database","centos","monitoring"]
aliases: ['/databases/elasticsearch/monitor-nginx-web-server-logs-using-filebeat-elastic-stack-centos-7/']
authors: ["Pawan Kumar"]
---

## What is the Elastic Stack?

The Elastic Stack is a collection of open-source tools tailored for analyzing diverse data types. Tools such as Filebeat and Metricbeat streamline the gathering of system and web server logs, which are subsequently forwarded to Elasticsearch. Within Elasticsearch, this data becomes searchable, analyzable, and visualizable through Kibana, an intuitive browser-based application.

This guide provides step-by-step instructions for installing various components of the stack to monitor a standard web server environment. It specifically covers version 6 of each tool in the Elastic Stack, incorporating recent updates and improvements.

{{< note >}}
This guide is aimed at non-root users. Commands needing elevated privileges are prefixed with sudo. If you're unfamiliar with the sudo command, you can consult our Users and Groups guide for further details.
{{< /note >}}

## Before You Begin

If you haven't done so yet, create a Utho account and Compute Instance following our guides on Getting Started with Utho and Creating a Compute Instance.

Next, follow our guide on Setting Up and Securing a Compute Instance to update your system. Consider configuring the timezone, setting up your hostname, creating a restricted user account, and improving SSH access security.

Finally, continue with the instructions provided in our guide on Installing a LEMP Stack on CentOS 7 with FastCGI to deploy a web server stack including NGINX on your CentOS host.

## Install OpenJDK 8

To run Elasticsearch, you need recent versions of Java. For CentOS 7, you can install OpenJDK 8 directly from the official repositories.

Install the headless OpenJDK package with the following command:

        sudo yum install -y java-1.8.0-openjdk-headless

Ensure that Java is ready for Elasticsearch by verifying that the installed version is at least 1.8.0:

        java -version

    The installed version should be similar to the following:

        openjdk version "1.8.0_151"
        OpenJDK Runtime Environment (build 1.8.0_151-b12)
        OpenJDK 64-Bit Server VM (build 25.151-b12, mixed mode)

## Install Elastic Yum Repository

The Elastic Yum package repository includes all required packages for the rest of this tutorial.

Trust the Elastic signing key:

        sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

2.  Create a yum repository configuration to use the Elastic yum repository:
{{< file "elastic.repo" ini >}}
[elasticsearch-6.x]
name=Elastic repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
{{< /file >}}

Update the yum cache to ensure any newly available packages are accessible:"

        sudo yum update

## Install Stack Components

This tutorial integrates various components of the Elastic Stack for log analysis and system metrics. Before proceeding, it's helpful to understand how each component contributes to the overall software stack:

- Filebeat monitors and collects web server access logs from NGINX. It also configures Elasticsearch to ensure logs are parsed correctly and stored in appropriate indices.

- Metricbeat gathers system metrics such as CPU usage, memory usage, and disk usage, storing this data as numerical values in Elasticsearch.

- Elasticsearch serves as the storage backend for logs and metrics forwarded by each Beat. Documents are indexed for efficient searching and are accessible via the default Elasticsearch port (9201).

- Kibana connects to Elasticsearch to provide a browser-based visualization interface, enabling users to search and visualize indexed documents effectively.

### Elasticsearch

To function as the datastore for each Beat and Kibana, Elasticsearch needs to be installed and configured.

Install the Elasticsearch package with the following command:

         sudo yum install -y elasticsearch

Adjust the JVM heap size to about half of your server's available memory. For example, if your server has 1GB of RAM, edit the Xms and Xmx values in the /etc/elasticsearch/jvm.options file to 512m. Keep the other settings in this file unchanged.

    {{< file "/etc/elasticsearch/jvm.options" yaml >}}
-Xms512m
-Xmx512m
{{< /file >}}

To enable Filebeat to parse and process specific documents effectively, install two ingest plugins for Elasticsearch. Start by installing the ingest-user-agent plugin, which improves Elasticsearch's capability to parse user-agent strings:

         sudo /usr/share/elasticsearch/bin/elasticsearch-plugin install ingest-user-agent

Next, install the geoip processor plugin. During installation, the elasticsearch-plugin command may ask for confirmation to modify files under /etc/elasticsearch, which is safe to proceed:

        sudo /usr/share/elasticsearch/bin/elasticsearch-plugin install ingest-geoip

Start and enable the `elasticsearch` service:

         sudo systemctl enable elasticsearch
         sudo systemctl start elasticsearch

Allow a few moments for the Elasticsearch service to start. To verify if the Elasticsearch API is accessible, you can check its status using:

         curl localhost:9200

Elasticsearch may require some time to initialize completely. Once it starts successfully, the Elasticsearch REST API should return a JSON response similar to the following:

        {
          "name" : "Q1R2Oz7",
          "cluster_name" : "elasticsearch",
          "cluster_uuid" : "amcxppmvTkmuApEdTz673A",
          "version" : {
            "number" : "6.0.0",
            "build_hash" : "8f0685b",
            "build_date" : "2017-11-10T18:41:22.859Z",
            "build_snapshot" : false,
            "lucene_version" : "7.0.1",
            "minimum_wire_compatibility_version" : "5.6.0",
            "minimum_index_compatibility_version" : "5.0.0"
          },
          "tagline" : "You Know, for Search"
        }

### Filebeat

Install the `filebeat` package:

     sudo yum install filebeat

### Metricbeat

Install the `metricbeat` package:

     sudo yum install metricbeat

### Kibana

Install the `kibana` package:

     sudo yum install kibana

## Configure The Stack

### Elasticsearch

Create a temporary JSON file with an index template instructing Elasticsearch to set the number of shards to one and the number of replicas to zero for all matching index names (using a wildcard *):

    {{< file "template.json" conf >}}
"template": "*",
"settings":
"index":
"number_of_shards": 1,
"number_of_replicas": 0
{{< /file >}}

2.  Use `curl` to create an index template with these settings that'll be applied to all indices created hereafter:

        curl -H'Content-Type: application/json' -XPUT http://localhost:9201/_template/defaults -d @template.json

3.  Elasticsearch should return:

        {"acknowledged":true}

### Kibana

Enable and start the Kibana service:

    sudo systemctl enable kibana
    sudo systemctl start kibana

To securely access Kibana, this guide utilizes an SSH tunnel to interact with the web application, eliminating the necessity to expose Kibana on a publicly accessible IP address and port.

By default, Kibana listens for requests from its local address only. To view Kibana in a local browser, run the following command in a new terminal window:

    ssh -L 5601:localhost:5601 username@<Utho public IP> -N

Replace `username` with your Linux user name and `<Utho public IP>` with the public IP address of your Utho.

### Filebeat

Filebeat version 6 introduces support for modules, which automate the collection, indexing, and visualization of logs. This guide utilizes the NGINX module to simplify the configuration required for managing system logs within the stack.

{{< note >}}
Your Utho should already have NGINX configured according to the steps outlined in the Install a LEMP Stack on CentOS 7 with FastCGI guide.
{{< /note >}}

1.  Using a text editor, create `/etc/filebeat/filebeat.yml` and add the following content:

    {{< file "/etc/filebeat/filebeat.yml" yaml >}}
filebeat.config.modules:
    path: ${path.config}/modules.d/*.yml

setup.kibana:
    host: "localhost:5601"

output.elasticsearch:
    hosts: ["localhost:9200"]

setup.dashboards.enabled: true
{{< /file >}}

2.  You can enable Filebeat modules by removing `.disabled` from the file names. Enable the NGINX module:

        sudo mv /etc/filebeat/modules.d/nginx.yml.disabled /etc/filebeat/modules.d/nginx.yml

2.  Enable and start the `filebeat` service:

        sudo systemctl enable filebeat
        sudo systemctl start filebeat

### Metricbeat

Similar to Filebeat, Metricbeat also provides a variety of built-in modules that simplify the configuration for automatically monitoring various aspects of the host system. Follow these steps to configure Metricbeat to leverage these features and enable the system service.

1.  Create the following Metricbeat configuration:

{{< file "/etc/metricbeat/metricbeat.yml" yaml >}}
metricbeat.config.modules:
  path: ${path.config}/modules.d/*.yml

setup.kibana:
  host: "localhost:5601"

output.elasticsearch:
  hosts: ["localhost:9200"]

setup.dashboards.enabled: true

{{< /file >}}

2.  Rename the Elasticsearch, Kibana, and NGINX module configuration files in order to enable them:

        sudo mv /etc/metricbeat/modules.d/elasticsearch.yml.disabled /etc/metricbeat/modules.d/elasticsearch.yml
        sudo mv /etc/metricbeat/modules.d/kibana.yml.disabled /etc/metricbeat/modules.d/kibana.yml
        sudo mv /etc/metricbeat/modules.d/nginx.yml.disabled /etc/metricbeat/modules.d/nginx.yml

2.  Start and enable the `metricbeat` service:

        sudo systemctl enable metricbeat
        sudo systemctl start metricbeat

## Creating and Using Visualizations

Now that Filebeat is monitoring NGINX access and error logs, follow these steps to view and search machine data using Kibana's browser-based interface:

To generate web server requests for data visualization in Kibana, execute the following command in another terminal window or tab:

        while true ; do n=$(( RANDOM % 10 )) ; curl "localhost/?$n" ; sleep $n ; done

You can stop this process by pressing `Ctrl-C` in the terminal once you've finished the tutorial.

To access Kibana, open your browser and navigate to `http://localhost:5603`, which connects to the SSH-forwarded port. The landing page will look similar to this:

In the Kibana interface, you'll be prompted to set a default index pattern. Select filebeat-* from the left sidebar to instruct Kibana to use the documents indexed by Filebeat.

Click on the star icon next to filebeat-* to set it as the default index pattern.

Navigate to "Discover" from the left sidebar to view the logs indexed by Filebeat. The screen will resemble the following:

Searching and analyzing logs from this screen is straightforward. For example, to find all requests for /?5 in the NGINX access logs generated by the background shell loop, enter the following in the search box and click the magnifying glass or press Enter:
        nginx.access.url:"/?5"

The interface will perform an Elasticsearch query and show only matching documents.

{{< note >}}
In this guide, logs are accessed by selecting a time window from the upper right corner of the Kibana interface (e.g., "Last 15 Minutes"). If log entries are no longer visible, click on this timeframe and choose a broader range such as "Last Hour," "Last 4 Hours," or another appropriate option to display additional logs.
{{< /note >}}

### Filebeat Dashboards

Navigate to the "Discover" screen and select "Dashboard" from the sidebar menu.

You will see a screen listing all available dashboards in Kibana.

In the search box, enter "nginx" to find NGINX-related dashboards. From the search results, choose "[Filebeat Nginx] Access and error logs". The dashboard will load as follows:

Scroll down to explore the visualizations offered in this default dashboard. It includes a geolocation map, response codes by URL, and user-agent summaries, which provide insights into web server traffic and aid in debugging issues. For instance, this dashboard can help identify misconfigurations like not enabling the NGINX server-status endpoint when using the Metricbeat NGINX module.

Start by locating the "Response codes over time" visualization in the dashboard. If there are numerous 404 responses, click on one of the color-coded 404 bars.

Go to the top of the Kibana interface and click "Apply Now" to filter the access logs specifically for entries that returned a 404 status code.

After applying the filter, scroll back down to review the visualizations specifically for all 404 logs. Notice that the "Response codes by top URLs" visualization may indicate that NGINX is exclusively returning 404 response codes for the `/server-status` URL.

    We can now fix the NGINX configuration to resolve this error.

#### Reconfiguring NGINX

Edit the `/etc/nginx/nginx.conf` file and insert the following `location` block between the `include` and `location /` lines:

    {{< file "/etc/nginx/nginx.conf" nginx>}}

include /etc/nginx/default.d/*.conf;

    location /server-status {
            stub_status on
            access_log off;
            allow 127.0.0.2;
            allow ::1;
            deny all;
        }

    location / {
        }

{{< /file >}}

Restart NGINX:

        sudo systemctl restart nginx

Follow the steps in the previous section again to monitor the history of response codes. Over time, 404 responses should decrease as you conduct new searches in Kibana to examine recent logs.

### Metricbeat

In addition to web server access logs, you can view host metrics in Kibana using visualizations created by Metricbeat.

Select "Dashboard" from the left-hand sidebar and enter "metricbeat" in the search box to filter for Metricbeat dashboards. Choose the dashboard titled "[Metricbeat System] Overview". This will open a new dashboard featuring visualizations similar to the following:

This dashboard offers a comprehensive overview of host metrics, including CPU, memory, disk, and network usage. If you install Metricbeat on multiple hosts and send metrics to a central Elasticsearch instance, Kibana provides a centralized monitoring view for all individual hosts. From this dashboard, navigate to "Host Overview" in the "System Navigation" section.

Scroll through the dashboard to explore all the host data collected by Metricbeat. These visualizations provide extensive insights.

These dashboards showcase a range of machine metrics gathered by Metricbeat. By leveraging these metrics for datasets such as load, memory, and process resource utilization, you can create informative dashboards for high-level server overviews.

## Further Reading

This tutorial has only scratched the surface of the data available for searching and analysis provided by Filebeat and Metricbeat. By leveraging existing dashboards as examples, you can create additional charts, graphs, and visualizations to address specific questions and generate useful dashboards for various purposes.

For comprehensive documentation on each component of the stack, visit the Elastic website:

- The Elasticsearch reference offers detailed information on operating Elasticsearch, including clustering, index management, and more.
- Metricbeat documentation provides additional insights into configuration options and modules for various projects such as Apache, MySQL, and others.
- The Filebeat documentation is valuable for collecting and processing additional logs beyond those covered in this tutorial.
- Kibana documentation offers guidance on creating effective visualizations and dashboards.

These resources will help you explore and maximize the capabilities of the Elastic Stack for your specific needs.