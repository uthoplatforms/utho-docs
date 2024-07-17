---
slug: Guide to elasticsearch plugins
description: "Elasticsearch supports a wide variety of plugins which enable more powerful search features. Learn how to manage, install, and use them."
keywords: ['elastic', 'elasticsearch', 'plugins', 'search', 'analytics', 'search engine']
published: 2024-07-09
modified: 2024-07-09
modified_by:
  name: Utho
title: "Installing and Using Elasticsearch Plugins"
title_meta: "How to Install and Use Elasticsearch Plugins"
dedicated_cpu_link: true
tags: ["ubuntu","debian","database","java"]
authors: ["Pawan Kumar"]
---

## What are Elasticsearch Plugins?

Elasticsearch is an open-source, scalable search engine. While Elasticsearch offers many features out-of-the-box, it can be extended with a variety of plugins to provide advanced analytics and handle different data types.

This guide will show you how to install and interact with the following Elasticsearch plugins using the Elasticsearch API:

- **ingest-attachment**: Allows Elasticsearch to index and search base64-encoded documents in formats such as RTF, PDF, and PPT.
- **analysis-phonetic**: Identifies search results that sound similar to the search term.
- **ingest-geoip**: Adds location information to indexed documents based on any IP addresses within the document.
- **ingest-user-agent**: Parses the User-Agent header of HTTP requests to provide identifying information about the client that sent each request.

{{< note >}}
This guide is intended for a non-root user. Commands requiring elevated privileges are prefixed with sudo. If you're not familiar with the sudo command, please refer to our Users and Groups guide.
{{< /note >}}

## Before You Begin

If you haven't already, create a Utho account and a Compute Instance. Refer to our Getting Started with Utho and Creating a Compute Instance guides.

Follow our Setting Up and Securing a Compute Instance guide to update your system. You may also want to set the timezone, configure your hostname, create a limited user account, and harden SSH access.

## Installation

### Java

As of this writing, Elasticsearch requires Java 8.

Install the headless OpenJDK 8 package from the official repositories:

        sudo apt install openjdk-8-jre-headless

Verify the Java installation:

        java -version

    The output should be similar to:

        openjdk version "1.8.0_151"
        OpenJDK Runtime Environment (build 1.8.0_151-8u151-b12-1~deb9u1-b12)
        OpenJDK 64-Bit Server VM (build 25.151-b12, mixed mode)

### Elasticsearch

{{< content "install_elasticsearch_debian_ubuntu" >}}

  You are now ready to install and use Elasticsearch plugins.

## Elasticsearch Plugins

"The remainder of this guide will cover various plugins and common use cases. Many of the steps involve interacting with the Elasticsearch API. For instance, to index a sample document into Elasticsearch, send a POST request with a JSON payload to /{index name}/{type}/{document id}:"

    POST /exampleindex/doc/1
    {
      "message": "this the value for the message field"
    }

"There are several tools available for issuing this request. The simplest method is to use `curl` from the command line:"

    curl -H'Content-Type: application/json' -XPOST localhost:9200/exampleindex/doc/1 -d '{ "message": "this the value for the message field" }'

Other options include the vim-rest-console, the Emacs plugin es-mode, or the Console plugin for Kibana. Choose the tool that is most convenient for you.

### Prepare an Index

Before installing any plugins, create a test index.

1.  Create an index named `test` with one shard and no replicas:

        POST /test
        {
          "settings": {
            "index": {
              "number_of_replicas": 0,
              "number_of_shards": 1
            }
          }
        }

    {{< note respectIndent=false >}}
These settings are suitable for testing, but additional shards and replicas should be used in a production environment.
{{< /note >}}

2.  Add an example document to the index:

        POST /test/doc/1
        {
          "message": "this is an example document"
        }

3.  Searches can be performed by using the `_search` URL endpoint. Search for "example" in the message field across all documents:

        POST /_search
        {
          "query": {
            "terms": {
              "message": ["example"]
            }
          }
        }

    The Elasticsearch API should return the matching document.

### Elasticsearch Attachment Plugin

The attachment plugin allows Elasticsearch to accept base64-encoded documents and index their contents for easy searching. This is particularly useful for searching PDF or rich text documents with minimal overhead.

Install the `ingest-attachment` plugin using the `elasticsearch-plugin` tool:"

        sudo /usr/share/elasticsearch/bin/elasticsearch-plugin install ingest-attachment

Restart elasticsearch:

        sudo systemctl restart elasticsearch

Verify that the plugin is installed correctly by using the _cat API:

        GET /_cat/plugins

The ingest-attachment plugin should appear in the list of installed plugins.

To utilize the attachment plugin, you need to use an ingest pipeline to process base64-encoded data within a document field. An ingest pipeline allows additional processing steps during document indexing in Elasticsearch. While Elasticsearch includes some pre-installed pipeline processors (which can perform actions like adding or removing fields), the attachment plugin adds an additional processor that can be used when defining a pipeline.

Create a pipeline named `doc-parser` that retrieves data from a field named `encoded_doc` and applies the attachment processor to the field:

        PUT /_ingest/pipeline/doc-parser
        {
          "description" : "Extract text from base-64 encoded documents",
          "processors" : [ { "attachment" : { "field" : "encoded_doc" } } ]
        }

The `doc-parser` pipeline can now be specified when indexing documents to extract data from the `encoded_doc` field.

    {{< note respectIndent=false >}}
By default, the attachment processor will generate a new field named attachment containing the parsed content from the specified field. For more details, refer to the attachment processor documentation.
{{< /note >}}

Index a sample RTF (rich-text formatted) document. The following string represents an RTF document containing text we want to search. It includes the base64-encoded text "Hello from inside of a rich text RTF document":

        e1xydGYxXGFuc2kKSGVsbG8gZnJvbSBpbnNpZGUgb2YgYSByaWNoIHRleHQgUlRGIGRvY3VtZW50LgpccGFyIH0K

Add this document to the test index, using the ?pipeline=doc_parser parameter to specify the new pipeline:

        PUT /test/doc/rtf?pipeline=doc-parser
        {
          "encoded_doc": "e1xydGYxXGFuc2kKSGVsbG8gZnJvbSBpbnNpZGUgb2YgYSByaWNoIHRleHQgUlRGIGRvY3VtZW50LgpccGFyIH0K"
        }

Search for the term "rich", which should retrieve the indexed document:

        POST /_search
        {
          "query": {
            "terms": {
              "attachment.content": ["rich"]
            }
          }
        }

This method can be applied to index and search other document types such as PDF, PPT, and XLS. For more supported file formats, refer to the Apache Tika Project, which offers the underlying text extraction implementation.

### Phonetic Analysis Plugin

Elasticsearch is highly effective in analyzing textual data. It comes equipped with several bundled analyzers that can perform robust text analyses.

One such analyzer is the Phonetic Analysis plugin. This plugin allows you to search for terms that sound similar to other words.

Install the analysis-phonetic plugin:

          sudo /usr/share/elasticsearch/bin/elasticsearch-plugin install analysis-phonetic

Restart Elasticsearch:

        sudo systemctl restart elasticsearch

Confirm that the plugin has been successfully installed:

        GET /_cat/plugins

To utilize this plugin, the following adjustments need to be made to the test index:

- Create a filter to process tokens generated for indexed document fields.
- Incorporate this filter into an analyzer. An analyzer defines how a field is tokenized and processes these tokens through filters.
- Configure the test index to apply this analyzer to a field using a mapping.

An index must be closed before adding analyzers and filters.

Close the test index:

        POST /test/_close

Define the analyzer and filter for the test index using the `_settings` API:

        PUT /test/_settings
        {
          "analysis": {
            "analyzer": {
              "my_phonetic_analyzer": {
                "tokenizer": "standard",
                "filter": [
                  "standard",
                  "lowercase",
                  "my_phonetic_filter"
                ]
              }
            },
            "filter": {
              "my_phonetic_filter": {
                "type": "phonetic",
                "encoder": "metaphone",
                "replace": false
              }
            }
          }
        }

3.  Re-open the index to enable searching and indexing:

        POST /test/_open

4.  Define a mapping for a field named `phonetic` which will use the `my_phonetic_analyzer` analyzer:

        POST /test/_mapping/doc
        {
          "properties": {
            "phonetic": {
              "type": "text",
              "analyzer": "my_phonetic_analyzer"
            }
          }
        }

5.  Index a document with a JSON field called `phonetic` with content that should be passed through the phonetic analyzer:

        POST /test/doc
        {
          "phonetic": "black leather ottoman"
        }

6.  Perform a `match` search for the term "ottoman". However, instead of spelling the term correctly, misspell the word such that the misspelled word is phonetically similar:

        POST /_search
        {
          "query": {
            "match": {
              "phonetic": "otomen"
            }
          }
        }

The phonetic analysis plugin should correctly identify that "otomen" and "ottoman" are phonetically similar, returning the expected result.

### Geoip Processor Plugin

When indexing documents such as log files, some fields may contain IP addresses. The Geoip plugin can process IP addresses in order to enrich documents with location data.

1.  Install the plugin:

        sudo /usr/share/elasticsearch/bin/elasticsearch-plugin install ingest-geoip

2.  Restart Elasticsearch:

        sudo systemctl restart elasticsearch

3.  Confirm the plugin is installed by checking the API:

        GET /_cat/plugins

Similar to the ingest-attachment pipeline plugin, the ingest-geoip plugin functions as a processor within an ingest pipeline. The Geoip plugin documentation details the available settings for creating processors within a pipeline.

Define a pipeline named `parse-ip` that takes an IP address from a field named ip and generates regional information under the default field (`geoip`):

        PUT /_ingest/pipeline/parse-ip
        {
          "description" : "Geolocate an IP address",
          "processors" : [ { "geoip" : { "field" : "ip" } } ]
        }

Add a mapping to the index specifying that the ip field should be stored as an IP address in the underlying storage engine:

        POST /test/_mapping/doc
        {
          "properties": {
            "ip": {
              "type": "ip"
            }
          }
        }

Index a document with the ip field set to an example address, and include pipeline=parse-ip in the request to utilize the parse-ip pipeline for document processing:

        PUT /test/doc/ipexample?pipeline=parse-ip
        {
          "ip": "8.8.8.8"
        }

Retrieve the document to view the fields created by the pipeline:

        GET /test/doc/ipexample

The response will contain a geoip JSON key with fields such as city_name derived from the source IP address. The plugin accurately identifies the IP address location, confirming it is in California.

### User Agent Processor Plugin

A typical use case for Elasticsearch involves indexing log files. By parsing specific fields from web server access logs, requests can be effectively searched by response code, URL, and more. The ingest-user-agent plugin enhances this capability by parsing the User-Agent header contents from web requests, enabling the creation of additional fields that identify the client platform responsible for each request.

Install the plugin:

        sudo /usr/share/elasticsearch/bin/elasticsearch-plugin install ingest-user-agent

Restart Elasticsearch:

        sudo systemctl restart elasticsearch

Confirm the plugin is installed:

        GET /_cat/plugins

Create an ingest pipeline which instructs Elasticsearch which field to reference when parsing a user agent string:

        PUT /_ingest/pipeline/useragent
        {
          "description" : "Parse User-Agent content",
          "processors" : [ { "user_agent" : { "field" : "agent" } } ]
        }

Index a document with the `agent` field set to an example `User-Agent` string:

        PUT /test/doc/agentexample?pipeline=useragent
        {
          "agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36"
        }

Retrieve the document to inspect the fields generated by the pipeline:

        GET /test/doc/agentexample

The indexed document will contain user data within the user_agent JSON key. The User Agent plugin is capable of interpreting various User-Agent strings and can accurately parse User-Agent fields from access logs generated by web servers like Apache and NGINX.

## Conclusion

The plugins discussed in this tutorial represent only a fraction of those offered by Elastic or developed by third parties. For more information on Elasticsearch and plugin usage, refer to the links provided in the More Information section below.