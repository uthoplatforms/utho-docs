---
slug: Database solutions
title: "Determine the Best Database and Cloud Hosting Solution for Your Next Application"
description: "Learn about the most popular database management systems and how to host them on the Utho cloud computing platform."
keywords: ['DBMS', 'managed database']
published: 2024-07-10
modified_by:
  name: Utho
authors: ["Pawan Kumar"]
---

Most applications rely on databases to store and organize the information they process. Choosing the right database software is a critical decision when building a custom application or deploying an existing one. This guide highlights some of the most popular database software options and offers advice on selecting and hosting a database on Utho.

- Select a Database Management System
- Select a Database Hosting Solution

## Select a Database Management System

Before deciding how to deploy and host a database on the Utho cloud computing platform, it's important to first choose which database management system (DBMS) to use. This section discusses various databases, including both relational and non-relational (NoSQL) databases, and outlines the factors to consider in making your decision.

{{< note >}}
For a comprehensive comparison of database management systems (DBMSs) and to determine which DBMS is best suited for you and your application, refer to the Comparing DBMSs: The 8 Most Popular Databases guide.
{{< /note >}}

### Determining Factors

When selecting a database, consider the following factors:

- **Application support**: If you are integrating your database with an off-the-shelf application or tool, you are likely limited to the databases supported by that application. When building your own application, there may be more flexibility.

- **Data structure (or model)**: Beyond application support, the structure of your data is a primary factor in selecting a DBMS. Data with a consistent structure that fits well into tables and rows should likely use a relational database. Unstructured data and complex data models may require a NoSQL database. Additionally, some databases offer increased performance or usability benefits for specific data models.

- **Performance and efficiency**: After narrowing down your choice based on application support and data models, consider which of the viable database solutions offers the best performance and efficiency for your use case. You may want to consult benchmarks of similar applications or run your own benchmarks.

- **Cost**: Some database management systems are free, while others have various products, pricing tiers, and service contracts. The ability of the DBMS to handle your particular data models and workloads efficiently can affect infrastructure fees and impact the total cost.

Additional factors such as usability, security, portability, and data consistency should also be considered.

### Relational Databases

A relational database, also known as a relational database management system (RDBMS), stores information in tables consisting of columns and rows. As the name implies, RDBMSs enable data to maintain relationships, even when stored in separate tables. These relationships correlate rows from two different tables into a third table. Relational databases are suitable for most database workloads, especially when the data is relatively static and data accuracy is crucial.

| <div class="w-48">Database</div> | Description |
| -- | -- |
| [MySQL](https://www.mysql.com/) (and [MariaDB](https://mariadb.org/)) | MySQL is an extremely popular and well known open-source relational DBMS, used in many common applications (such as WordPress). It is free, has an easy to use GUI (MySQL Workbench), and is widely supported. MariaDB is a MySQL fork and is 100% compatible with MySQL 5.7 and earlier. As both DBMSs continue to develop, their feature set has diverged --- though MySQL and MariaDB have many more commonalities than differences. [Learn more &rarr;](/docs/guides/list-of-databases/#mysql)<br><br>*Best for many common PHP applications and general workloads.* |
| [PostgreSQL](https://www.postgresql.org/) | PostgreSQL (also called Postgres) is another extremely popular free and open-source DBMS. While it can be slower and more complicated than MySQL, it can handle more complex data and even includes NoSQL support. [Learn more &rarr;](/docs/guides/list-of-databases/#postgresql)<br><br>*Best for complex enterprise-level applications.* |
| [Microsoft SQL Server](https://www.microsoft.com/en-us/sql-server) | Microsoft SQL Server (also called MSSQL) is a very popular proprietary DMBS regarded for its Windows support (though it does now include Linux support), .NET integration, relative ease of use, and extensive first party management tools. [Learn more &rarr;](/docs/guides/list-of-databases/#microsoft-sql-server)<br><br>*Best for .NET and Windows applications.* |
| [Oracle Database](https://www.oracle.com/database/technologies/) | Oracle Database is highly scalable (for a relational DMBS) and is very widely used for enterprise workloads. It offers high performance, efficient memory caching, and high availability and scalability (through Oracle RAC clusters), though this comes at higher costs and increased complexity compared with other relational DBMSs. [Learn more &rarr;](/docs/guides/list-of-databases/#oracle)<br><br>*Best for enterprise applications that require scalability.* |

### Non-Relational (NoSQL) Databases

Non-relational databases, also known as NoSQL Databases, do not store information in tables like RDBMSs. Instead, data is stored as JSON documents, key-value pairs, or using other data models. This generally makes NoSQL databases more flexible, as they can accommodate a wide variety of data types.

- **Document-oriented**: Data is stored as JSON documents.
- **Key-value**: Data is stored as key-value pairs.
- **Graph**: Data is stored in a node-edge-node structure.
- **Wide-column**: Data is stored in a tabular format with flexible columns that can vary from row to row.

| <div class="w-48">Database</div> | Description |
| -- | -- |
| [MongoDB](https://www.mongodb.com/) | MongoDB is an extremely flexible general-purpose NoSQL database. Data is stored within BSON documents as JSON formatted key-value pairs. It is schema-less and it does not enforce a data structure. You can query MongoDB data using its own [MongoDB Query API](https://www.mongodb.com/docs/v6.0/query-api/). [Learn more &rarr;](/docs/guides/list-of-databases/#mongodb)<br><br>*Best for compatible general-purpose workloads, content management, analytics, and prototyping. Not ideal for large amounts of related data.* |
| [Redis](https://redis.com/) | Redis is a relatively simple NoSQL solution and stores data as key-value pairs within system memory. This enables high performance data retrieval, but it isn't suitable for many complex workloads. Due to this, Redis is often used as part of a caching system instead of a primary database. [Learn more &rarr;](/docs/guides/list-of-databases/#redis)<br><br>*Best for caching systems. Not ideal for an application's primary database.* |
| [Apache Cassandra](https://cassandra.apache.org/_/index.html) | Apache Cassandra is a wide column NoSQL database. While it can be compared to relational databases, it is schema-less and supports both structured and unstructured data. Its distributed nature (all nodes serve the same role) makes it scalable and fault tolerant. [CQL (Cassandra Query Language)](https://cassandra.apache.org/doc/latest/cassandra/cql/), Apache Cassandra own query language, is similar to SQL. [Learn more &rarr;](/docs/guides/list-of-databases/#apache-cassandra)<br><br>*Best for transaction logging, event logging, time-series data, and for high-write low-read applications. Not ideal for an application's primary database* |
| [Apache CouchDB](https://couchdb.apache.org/) | CouchDB is another document-oriented database, similar to MongoDB. CouchDB is known for its reliability and scalability. You can interact with data using its unique HTTP REST-like API and data is stored in JSON format. [Learn more &rarr;](/docs/guides/list-of-databases/#couchdb)<br><br>*Best for compatible general-purpose workloads and mobile applications.* |

## Select a Database Hosting Solution

After selecting the appropriate database management system for your application, the next step is to decide how to deploy, configure, and manage that system in the cloud. The Utho cloud computing platform provides the following solutions:

- [Managed Databases](#managed-databases)

    {{< content "dbass-eos" >}}

- [Marketplace Apps (and Clusters)](#marketplace-apps-and-clusters)
- [Self or custom deployment on Compute Instances](#custom-deployment)

{{< note >}}
Many users utilize provisioning tools such as Terraform and configuration management tools like Puppet and Ansible to automate application deployment on the cloud. While this guide does not cover these tools, they can be used to deploy any of the database solutions discussed.
{{< /note >}}

### Managed Databases

Use Managed Databases when you want to offload database software and infrastructure management and do not require full root control or extensive customization.

The Managed Database service offers a user-friendly and fully-managed database solution. When deploying a database through Managed Databases, the infrastructure, software, firewall, and high availability systems are automatically configured, saving you time and resources. After provisioning, you can add your application's IP addresses to allow traffic and then connect directly to the database from your application.

Managed Databases can be deployed with a single node (1 underlying machine) or a cluster of 3 nodes. A 3-node setup provides a highly available database cluster with data redundancy and automatic failover. You can also customize the node size and choose between Dedicated CPU or Shared CPU Compute Instance plans. Since the underlying machines are fully managed, direct root or console access is not provided, and there are limited customization options for the database software.

{{< note >}}
Updates and security patches are automatically applied to the underlying operating system, but not to the database software. For more details, refer to the Automatic Updates and Maintenance Windows guide.
{{< /note >}}

### Marketplace Apps and Clusters

Marketplace Apps are ideal when you want to automatically install popular databases while retaining full control over the software and underlying system.

Another solution offered on our platform is Marketplace Apps, designed to simplify application provisioning. When deploying a Marketplace App, a Compute Instance is created within your account. Upon the instance's initial startup, a script executes to automatically install and configure the application. With root access and full control over the Compute Instance, you assume responsibility for managing and configuring the application, as well as installing updates and security patches.

The following Marketplace Apps (and Clusters) are available on the Utho cloud computing platform:

- **Marketplace Apps (single instance)**: Deploys a single Compute Instance. For applications requiring high availability and scalability, additional manual configuration is necessary.

- **Marketplace App Clusters** (multi-instance): Deploys multiple Compute Instances as a database cluster, providing high availability, improved fault tolerance, and enhanced scalability.

### Custom Deployment or Self-Install {#custom-deployment}

*Directly provision your databases on Compute Instances to have full control over the installation and configuration, ideal for applications that require extensive database customization or complex configurations.*

In addition to Managed Databases and Marketplace Apps, you have the flexibility to deploy any of your database workloads to the cloud using Compute Instances. These are Linux-based virtual machines capable of running compatible database software packages available on your chosen Linux distribution. When manually hosting your database workloads, you take on responsibilities such as installation, configuration, and all aspects of database management, including applying regular security updates.
