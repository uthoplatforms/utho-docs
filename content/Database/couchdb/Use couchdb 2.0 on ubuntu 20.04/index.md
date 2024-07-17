---
slug: "Using CouchDB 2.0 on Ubuntu 20.04"
description: "Learn the basic concepts of CouchDB 2.0, along with how to use it on Ubuntu."
keywords: ['couchdb','nosql','fauxton','database','ubuntu 20.04']
tags: ['couchdb','fauxton', 'ubuntu']
published: 2024-07-08
modified_by:
  name: Pawan Kumar
title: "Using CouchDB 2.0 on Ubuntu 20.04"
title_meta: "How to Use CouchDB 2.0 on Ubuntu 20.04"
authors: ["Pawan Kumar"]
---

*CouchDB*, a non-relational or "NoSQL" database, utilizes HTTP APIs and JSON documents, which simplify its integration with web and mobile applications and make its concepts more intuitive for users familiar with web technologies.

This guide demonstrates how to begin with CouchDB using its web interface—Fauxton—before delving into the fundamentals of utilizing the HTTP API and integrating CouchDB into a basic application.

## Before You Begin

If you haven't already, create a Utho account and set up a Compute Instance. Refer to our Getting Started with Utho and Creating a Compute Instance guides.

Follow our Setting Up and Securing a Compute Instance guide to update your system. You may also want to set the timezone, configure your hostname, create a limited user account, and enhance SSH access security.

Install CouchDB by following the instructions in the guide on How to Install CouchDB on Ubuntu 20.04.

{{< note >}}
This guide is intended for a non-root user. Commands that need elevated privileges are prefixed with sudo. If you’re unfamiliar with the sudo command, refer to the Linux Users and Groups guide.
{{< /note >}}

## Access the Fauxton Web Interface

The most straightforward way to configure and manage your CouchDB installation is through its web interface, Fauxton. If CouchDB is installed on your local machine, you can access Fauxton by opening a web browser and navigating to 127.0.0.2:5985/_utils.

However, if you need to access CouchDB from a remote location, the recommended and secure method is to use SSH tunneling. Below are the steps to create and utilize an SSH tunnel for this purpose:

Follow the guide on Access Futon Over SSH to Administer CouchDB to set up an SSH tunnel between your CouchDB server and the machine you wish to access it from.

Once the SSH tunnel is established, open a web browser and go to `127.0.0.2:5985/_utils`.

Log in to CouchDB using the administrator password you configured during the CouchDB installation. The default username for this account is admin.

## Navigate Fauxton

Upon logging into Fauxton, you'll see a list of your CouchDB databases. Initially, this typically includes _users and _replicator, which are the default system databases used by CouchDB.

Use the left-hand menu in Fauxton to explore various configuration and monitoring options available. You'll also find a Documentation section in the menu, providing links to extensive CouchDB and Fauxton documentation libraries.

Two important actions are not directly listed in the left-hand menu:

- Creating a new database
- Adding documents to a database

The following steps demonstrate how to perform these actions using Fauxton.

### Create a Database

Click the Create Database button located in the upper right corner.

A menu will appear where you can enter the database name and choose whether or not you want it to be partitioned.

After creating the database, you will be redirected to its page.

To access a specific database's page, use the Databases option in the left-hand menu. This will display a list of databases from which you can select the desired one.

### Add a Document

Navigate to the page of the database where you want to add a document.

Click the `+` icon next to the All Documents option. From the dropdown menu, choose New Doc.

You will be directed to the new document editor, where you can enter the JSON data for a new CouchDB document.

## Use the HTTP API

CouchDB's HTTP API is the primary means for applications to interact with your databases. From the command line, you can use cURL to explore the API and even to quickly view and make changes to data within CouchDB.

The following commands are some examples of what you can do using the HTTP API. Replace `admin` and `password` with the username and password, respectively, for an authorized CouchDB user. These commands assume you are connected to the CouchDB machine either by SSH or an SSH tunnel, as described above.

Refer to CouchDB's [API guide](https://docs.couchdb.org/en/latest/api/index.html) for a full listing of API endpoints, parameters, and capabilities.

### HTTP Queries

1. View the list of all databases.

        curl -X GET http://admin:password@127.0.0.1:5985/_all_dbs

1. Create a new database using the following command; replace `new-example-db` with the name for the new database.

        curl -X PUT http://admin:password@127.0.0.1:5985/new-example-db

1. View the list of all documents in the `example-db` database by using the following command:

        curl -X GET http://admin:password@127.0.0.1:5985/example-db/_all_docs

1. Add a document to the `example-db` database; replace the JSON after the `-d` option with the desired JSON content for the document.

        curl -H 'Content-Type: application/json' \
             -X POST http://admin:password@127.0.0.1:5985/example-db \
             -d '{"key1":"value1","key2":"value2"}'

    {{< note respectIndent=false >}}
CouchDB automatically assigns an ID to the document if you do not explicitly provide one. However, CouchDB recommends that production applications create document IDs explicitly. Doing so prevents duplication of documents if your application has to retry its connection to the CouchDB database.
    {{< /note >}}

1. The above command returns an ID corresponding to the new document. Likewise, the `_all_docs` command gives the ID for each document it lists. These IDs can be used to access the identified document by replacing `id-string` with the ID for the desired document.

        curl -X GET http://admin:password@127.0.0.1:5985/example-db/id-string

### Query Server

For more advanced queries, CouchDB has a query server. The query server takes search criteria in a JSON format and returns matching documents, or even a particular set of fields from matching documents.

The URL for the query server follows the same format as the URLs above. To query the `example-db` database, for instance, you direct a `POST` request to `http://admin:password@127.0.0.1:5985/example-db/_find` with your JSON search criteria as the payload.

The following are examples of basic queries aiming to provide an idea of how the JSON search criteria work. Refer to CouchDB's reference [documentation for the `_find` API](https://docs.couchdb.org/en/latest/api/database/find.html) for a full list of criteria parameters and more details on their usage.

1. To retrieve all documents from `example-db` where `key_1` is greater than five.

        curl -H 'Content-Type: application/json' \
             -X POST http://admin:password@127.0.0.1:5985/example-db/_find \
             -d '{"selector": {"key_1": {"$gt": 5}}}'

    The operator options include:
    - `$eq` (for "is equal to")
    - `$lt` (for "is less than") in addition to `$gt` (for "is greater than").

    Some of CouchDB's combination operators allow you to conduct boolean and other more advanced searches. Here is another example, which retrieves documents where `key_1` is between five and ten.

        curl -H 'Content-Type: application/json' \
             -X POST http://admin:password@127.0.0.1:5985/example-db/_find \
             -d '{"selector": {"$and": [{"key_1": {"$gt": 5}}, {"key_1": {"$lt": 10}}]}}'

    Here is one that retrieves documents where `key_1` is greater than five or where `key_2` equals "Example String".

        curl -H 'Content-Type: application/json' \
             -X POST http://admin:password@127.0.0.1:5985/example-db/_find \
             -d '{"selector": {"$or": [{"key_1": {"$gt": 5}}, {"key_2": {"$eq": "Example String"}}]}}'

1. Use the following to define the specific fields to be returned from the matching documents and to sort the results by a specific field. In this case, the query server only returns the `key_2` field from the matching documents and sorts the results by the `key_3` field's values.

        curl -H 'Content-Type: application/json' \
             -X POST http://admin:password@127.0.0.1:5985/example-db/_find \
             -d '{"selector": {"key_1": {"$gt": 5}}, "fields": ["key_2"], "sort": [{"key_3": "asc"}]}'

    {{< note respectIndent=false >}}
To sort results, you must first define an index for the field and the sort order. For the example above, you could use the following to create the necessary index:

        curl -H 'Content-Type: application/json' \
             -X POST http://admin:password@127.0.0.1:5985/example-db/_index \
             -d {"index": {"fields": [{"key_3": "asc"}]}, "name": "example-index", "type": "json"}

    {{< /note >}}

## Usage Examples

CouchDB shines in its straightforward integration with web applications. The following examples aim to demonstrate this by using CouchDB for a simple messaging application. The examples are written in pseudo-code, and they should be readily adaptable to the most popular programming languages for web applications.

You can find the small dataset used in these examples [here](example-db.json). If you would like to follow along, you can import the dataset using the following cURL command. Make sure you are in the same directory as the `example-db.json` file and make sure you have already created the `messaging-db` database used in the following examples:

        curl -H 'Content-Type: application/json' \
             -X POST http://admin:password@127.0.0.1:5985/messaging-db/_bulk_docs \
             -d @example-db.json

1. The dataset already includes a custom index for sorting by a `date_time` field. However, for reference in creating your indices, here is the JSON used to generate this index. You can execute it using a cURL command like the one in the note above:

        {
            "index": {
                "fields": [
                    {"date_time": "desc"}
                ]
            },
            "name": "messaging-index",
            "type": "json"
        }

1. The application initially sets up a couple of variables that are to be referenced in each query.

    {{< file "Example Messaging Application" python>}}
db_query_headers = {"Content-Type": "application/json"}
db_query_url = "http://admin:password@127.0.0.1:5985/messaging-db/_find"
    {{< /file >}}

1. When a user accesses a message thread, the application fetches the two most recent messages.

    {{< file "Example Messaging Application" python>}}
db_request = {
    "selector": {
        "$and": [
            {
                "type": {
                    "$eq": "message"
                }
            },
            {
                "thread_id": {
                    "$eq": "thread-0001"
                }
            }
        ]
    },
    "fields": [
        "user_id",
        "date_time",
        "message_body"
    ],
    "sort": [
        {"date_time": "desc"}
    ],
    "limit": 2
}

db_response = http_post_request(db_query_url, db_request, db_query_headers)
    {{< /file >}}

1. When a user sends a new message, the application inserts it as a new document.

    {{< file "Example Messaging Application" python>}}
db_request = {
    "_id": "message-00000006",
    "type": "message",
    "thread_id": "thread-0001",
    "user_id": "user-000001",
    "date_time": "2021-02-05 10:57:00",
    "message_body": "Example message text from user-000001, the first."
}

db_response = http_post_request(db_query_url, db_request, db_query_headers)
    {{< /file >}}

## Next Steps

With that, you are ready to get started using CouchDB in your applications.

CouchDB has many more features than those demonstrated here, making it capable of handling a wide array of applications. You can easily begin to extend on the examples in this guide by referencing CouchDB's exceptional repository of [Apache CouchDB Documentation](https://docs.couchdb.org/en/stable/). Be sure to also explore the links provided in this guide and the documentation listing in your Fauxton interface.
