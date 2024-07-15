---
slug: Sharded database
description: 'Database sharding divides data into smaller chunks and distributes it across different database nodes. Learn more about sharding practices and strategies.'
keywords: ['sharded database','db sharding','sharding strategy','database sharding examples']
published: 2024-07-12
modified_by:
  name: Utho
title: "Database Sharding: Concepts, Examples, and Strategies"
authors: ["Pawan Kumar"]
tags: ["saas"]
---

Many software applications utilize a relational database management system (RDBMS) for storing data. As the database grows, managing data storage and access can become increasingly resource-intensive. One popular solution to this challenge is database sharding, where records within a database's tables are distributed across multiple databases on different computer systems. This guide explores the principles behind database sharding, discusses its advantages and disadvantages, outlines common sharding strategies, and provides examples of database sharding implementations.

## What is Database Sharding?

As databases grow larger, they can be scaled in one of two ways. *Vertical scaling* involves upgrading the server hosting the database with more RAM, CPU ability, or disk space. This allows it to store more data and process a query more quickly and effectively. *Horizontal scaling*, which is also known as "scaling out", adds additional servers to distribute the workload.

Data sharding is a common way of implementing horizontal scaling. Database sharding divides the table records in a database into smaller portions. Each section is a *shard*, and is stored on a different server. The database can be divided into shards based on different methods. In a simple implementation, the individual tables can be assigned to different shards. More often, the rows in a single table are divided between the shards.

*Vertical partitioning* and *horizontal partitioning* are two different methods of partitioning tables into shards. Vertical partitioning assigns different columns within a table to different servers, but this technique is not widely used. In most cases, horizontal partitioning/sharding is used to implement sharding, and the two terms are often used interchangeably. Horizontal sharding divides the rows within a table amongst the different shards and keeps the individual table rows intact.

{{< note respectIndent=false >}}
Vertical partitioning and horizontal partitioning should not be confused with vertical and horizontal scaling.
{{< /note >}}

The shards are distributed across the different servers in the cluster. Each shard has the same database schema and table definitions. This maintains consistency across the shards. Sharding allocates each row to a shard based on a sharding key. This key is typically an index or primary key from the table. A good example is a user ID column. However, it is possible to generate a sharding key from any field, or from multiple table columns. The selection of the sharding key should be reasonable for the application and effectively distribute the rows among the shards. For example, a country code or zip code is a good choice to distribute the data to geographically dispersed shards. Sharding is particularly advantageous for databases that store large amounts of data in relatively few tables, and have a high volume of reads and writes.

Each shard can be accessed independently and does not necessarily require access to the other shards. Different tables can use different sharding techniques and not all tables necessarily have to be sharded. As an ideal, sharding strives towards a *shared-nothing* architecture, in which the shards do not share data and there is no data duplication. In practice, it is often advantageous to replicate certain data to each shard. This avoids the need to access multiple servers for a single query and can result in better performance.

The following example demonstrates how horizontal sharding works in practice. Before the database is sharded, the example `store` table is organized in the following way:

| store_ID | city | state | zip_code |
|:-:|:-:|:-:|:-:|
| 1001 | Detroit | MI | 48201 |
| 1350 | Chicago | IL | 60601 |
| 2101| Cleveland | OH | 44114 |
| 2250 | Pittsburgh | PA | 15222 |
| 2455 | Boston | MA | 02108 |
| 2459 | New York | NY | 10022 |

After sharding, one shard has half the rows from the table.

| store_ID | city | state | zip_code |
|:-:|:-:|:-:|:-:|
| 1001 | Detroit | MI | 48201 |
| 2101| Cleveland | OH | 44114 |
| 2455 | Boston | MA | 02108 |

The second shard contains the remainder of the rows.

| store_ID | city | state | zip_code  |
|:-:|:-:|:-:|:-:|
| 1350 | Chicago | IL | 60601 |
| 2250 | Pittsburgh | PA | 15222 |
| 2459 | New York | NY | 10022 |

Sharding itself does not automatically create duplicate copies of data; each record remains stored on a single server. In contrast, replication involves copying data to another server, creating primary and secondary copies to improve reliability and resilience. However, replication adds complexity and requires additional resources. While sharded databases can be replicated, this process can be complex.

For applications that primarily read data from a database, alternatives to sharding include replication and caching. Replication distributes queries across multiple servers, while caching speeds up response times for requests. For more information on configuring data replication, please see our guide on How to Configure Source-Replica Replication in MySQL.

## Pros and Cons of a Sharded Database

Horizontal scaling is generally more robust and effective compared to vertical scaling. Vertical scaling involves hardware upgrades and is simpler to implement, making it suitable for medium-sized databases approaching their limits. However, all systems have scalability limits, and ongoing growth can quickly become unmanageable. This limitation often prompts administrators to explore alternative solutions.

Horizontal scaling, on the other hand, allows systems to scale more extensively. By adding additional servers as needed, databases can grow organically and access more resources. This approach offers administrators greater flexibility and scalability.

Database sharding, a horizontal scaling strategy, shares many advantages with this approach. Moreover, it offers several specific benefits:

- **Improved Performance**: Data retrieval speeds up significantly since the database system can quickly determine which shard contains the data based on the sharding key. Queries are efficiently routed to the appropriate server, as each shard holds only a subset of the total data.

- **Scalability**: Additional computing capacity can be seamlessly added without downtime. Sharding increases both data storage capacity and overall resources available to the database.

- **Cost Efficiency**: Running multiple servers can be more cost-effective than maintaining a single large server, especially as data and workload grow.

- **Simplified Upgrades**: Upgrades can be performed on a shard-by-shard basis, simplifying the process and reducing potential downtime.

- **Resilience**: A sharded approach enhances resilience. If one server goes offline, the remaining shards remain accessible, ensuring continuous availability. When combined with high availability techniques, sharding further boosts reliability.

- **Tool Support**: Many modern database systems offer tools to facilitate sharding, though automation may be partial rather than complete.

Unfortunately, sharding also comes with several drawbacks:

- Sharding significantly increases the complexity of software development projects. Additional logic is required to implement database sharding and route queries to the correct shard, leading to increased development time and costs. It often necessitates a more intricate network infrastructure, which in turn raises operational and infrastructure expenses.
- Latency can be higher compared to traditional database designs.
- SQL join operations across multiple shards can be challenging and may result in longer execution times. Certain operations may become impractically slow. However, careful design can optimize performance for common queries.
- Sharding requires continuous tuning and adjustment as the database scales. This sometimes involves reevaluating the entire sharding strategy and database schema. Even with careful planning, uneven distribution among shards can unexpectedly occur, leading to imbalance.
- Determining the number of shards and servers to use, as well as selecting the appropriate sharding key, can be non-trivial. Poor choices in sharding keys can degrade performance or lead to uneven data distribution, causing some shards to be overloaded while others remain underutilized, resulting in inefficiencies and hotspots.
Changing the database schema after implementing sharding is challenging. Reverting the database to its pre-sharded state is also difficult.
- Shard failures can introduce inconsistencies across shards and other operational issues.
- Backup and replication tasks become more complex in a sharded database environment.
- While most relational database management system (RDBMS) applications offer some level of sharding support, the tools are often not fully robust or complete. Automatic sharding support remains limited in many systems.

## Database Sharding Strategies: Common Architectures

Any sharding implementation must first decide on a db sharding strategy. Database designers must consider how many shards to use and how to distribute the data to the various servers. They must decide what queries to optimize, and how to handle joins and bulk data retrieval. A system in which the data frequently changes requires a different architecture than one that mainly handles read requests. Replication, reliability and a maintenance strategy are also important considerations.

The choice of a sharding architecture is a critical decision, because it affects many of the other considerations. Most sharded databases have one of the following four architectures:

- **Range Sharding**.
- **Hashed Sharding**.
- **Directory-Based Sharding**.
- **Geographic-Based Sharding**.

### Range Sharding

Range sharding involves categorizing data based on the value of a sharding key into distinct ranges, each mapped directly to a different shard. It is also known as dynamic sharding. The sharding key should ideally remain immutable; any change requires recalculating the shard assignment and transferring the record to the new shard to prevent data loss.

For example, if the userID field serves as the sharding key, records with IDs between 1 to 10000 could be stored in one shard, IDs between 10001 and 20000 in a second shard, and so forth.

Implementing range sharding is relatively straightforward and involves comparing the value of the sharding key against predefined ranges using a lookup table. This simplicity facilitates easier design, implementation, and maintenance. Range sharding is particularly suitable for scenarios where records with similar keys are frequently accessed together.

Range sharding performs optimally when there is a large number of potential values evenly distributed across the entire range. However, it can lead to uneven distribution of records among shards if most key values map to the same shard. For instance, if older accounts are more likely to have been deleted over time, the corresponding shard may become relatively sparse, leading to database inefficiencies. To mitigate this, designing larger ranges can help, although it doesn't entirely eliminate the possibility of imbalance.

Below are examples demonstrating how range sharding might be applied to the store database. In this scenario, stores with IDs under 2000 are allocated to one shard, while stores with IDs 2001 and higher are assigned to another shard.

The first shard contains the following rows:

| store_ID | city | state | zip_code |
|:-:|:-:|:-:|:-:|
| 1001 | Detroit | MI | 48201 |
| 1350 | Chicago | IL | 60601 |

The second shard has the following entries:

| store_ID | city | state | zip_code  |
|:-:|:-:|:-:|:-:|
| 2101| Cleveland | OH | 44114 |
| 2250 | Pittsburgh | PA | 15222 |
| 2455 | Boston | MA | 02108 |
| 2459 | New York | NY | 10022 |

This results in a slightly imbalanced distribution of records. However, as new stores are added, they might be assigned larger store IDs. This leads to a greater imbalance as time goes on.

To keep the database running efficiently, shards and ranges have to be regularly rebalanced. This might involve splitting the shards apart and reassigning the data, or merging several smaller shards. If the data is not regularly monitored, performance can steadily degrade.

### Hash Sharding (Key-Based)

Hash-based sharding, also known as key-based or algorithmic sharding, leverages a shard key to determine the shard to which a record is assigned. Instead of directly mapping the key to a shard, it applies a hash function to the shard key. This hash function transforms one or more data points into a new value within a fixed-size range, typically equal to the number of shards available. The database then allocates the record to a shard based on the output of this hash function, resulting in a more evenly distributed allocation of records across shards.

This approach supports the use of multiple fields as a compound shard key, which helps eliminate clumping and clustering, making it ideal when several records may share the same key. Hash functions vary in complexity; a basic hash function might compute the remainder (modulus) of the key divided by the number of shards, while more advanced algorithms apply complex mathematical equations to multiple inputs. Consistency is crucial: the same hash function must be applied to the same keys for every hashing operation. Similar to range sharding, the shard key value should remain immutable; any change requires recalculating the hash value and remapping the database entry.

Hash sharding offers efficiency advantages over range sharding because it doesn't require a lookup table; the hash is computed dynamically for each query. However, it lacks the ability to logically group related records on a shard, often necessitating that bulk queries retrieve records from multiple shards. Hash sharding is particularly beneficial for applications that predominantly access data one record at a time.

Despite these benefits, hash sharding does not guarantee perfectly balanced shards; data patterns can lead to clustering by chance. Moreover, expanding or rebalancing shards is complex. Adding more shards typically involves merging data, recalculating hashes, and reassigning records.

Here's an example illustrating hash sharding in action: Suppose we use a straightforward hash function store_ID % 3 to distribute records in the store database across three shards. The first step involves calculating a hash result for each entry.

{{< note respectIndent=false >}}
The hash results are not actually stored inside the database. They are shown in the final column for clarity.
{{< /note >}}

| store_ID | city | state | zip_code  | hash result |
|:-:|:-:|:-:|:-:|:-:|
| 1001 | Detroit | MI | 48201 | 2
| 1350 | Chicago | IL | 60601 | 0
| 2101| Cleveland | OH | 44114 | 1
| 2250 | Pittsburgh | PA | 15222 | 0
| 2455 | Boston | MA | 02108 | 1
| 2459 | New York | NY | 10022 | 2

Rows having a hash result of `0` map to the first shard.

| store_ID | city | state | zip_code |
|:-:|:-:|:-:|:-:|
| 1350 | Chicago | IL | 60601 |
| 2250 | Pittsburgh | PA | 15222 |

Those that have a hash result of `1` are assigned to shard number two.

| store_ID | city | state | zip_code |
|:-:|:-:|:-:|:-:|
| 2101| Cleveland | OH | 44114 |
| 2459 | New York | NY | 10022 |

The remainder are stored in the third shard.

| store_ID | city | state | zip_code |
|:-:|:-:|:-:|:-:|
| 1001 | Detroit | MI | 48201 |
| 2459 | New York | NY | 10022 |

In this case, although the data set is quite small, the hash function still distributes the entries evenly. This is not always the case with every database. However, as records are added, the distribution is likely to remain reasonably balanced.

### Directory-Based Sharding

Directory-based sharding, also referred to as entity or relationship-based sharding, consolidates related items onto the same shard. It typically relies on a specific field's value to determine the appropriate shard. This method utilizes a static lookup table containing mappings between each possible field value and its corresponding shard. Each key can map to only one shard and must appear exactly once in the lookup table, though multiple keys may map to the same shard.

For example, in a customer table, records can be sharded based on the customer's home state. The lookup table lists all fifty states as shard keys, each mapped to a specific shard. This allows for a system where customers from New England are stored on one shard, those from the Mid-Atlantic on another, and those from the Deep South on a third shard.

Directory-based sharding offers significant control and flexibility in data storage decisions. When implemented thoughtfully, it accelerates common table joins and the retrieval of related data in bulk. This approach is particularly beneficial when the shard key has a limited number of potential values. However, it is susceptible to data clustering and uneven shard distribution, and the overhead of accessing the lookup table can degrade performance. Nevertheless, the advantages of this architecture often outweigh its drawbacks.

Directory-based sharding is well-suited for databases like `stores`, where store entries can be distributed across shards based on their geographical location. For instance, stores in New England and the Mid-Atlantic could be assigned to the first shard, serving as the North-East shard, while stores in the Midwest are stored on the second shard.

The first shard contains the entries displayed below.

| store_ID | city | state | zip_code |
|:-:|:-:|:-:|:-:|
| 2250 | Pittsburgh | PA | 15222 |
| 2455 | Boston | MA | 02108 |
| 2459 | New York | NY | 10022 |

The second shard contains the remainder of the data.

| store_ID | city | state | zip_code |
|:-:|:-:|:-:|:-:|
| 1001 | Detroit | MI | 48201 |
| 1350 | Chicago | IL | 60601 |
| 2101| Cleveland | OH | 44114 |

Although these two shards are perfectly balanced, this is not the main goal of directory sharding. It instead seeks to generate useful and relevant shards of closely-related information, which this example also accomplishes.

### Geographic-Based Sharding

Geographic-based sharding, also known as *Geo-sharding*, is a specific form of directory-based sharding. It involves partitioning data among shards based on the geographic location of the data entry, corresponding to the server's location hosting that shard. The sharding key typically represents a city, state, region, country, or continent, grouping data with similar geographic attributes onto the same shard. This operates similarly to directory-based sharding.

An illustrative example of geo-sharding involves geographically dispersed customer data. The customer's home state serves as the sharding key. A lookup table assigns customers residing in states within the same sales region to the same shard. Each shard resides on a server located in the region where the customer data it contains originates. This setup ensures swift and efficient access to customer data by regional sales teams.

## Is Sharding Right For Your Business?

Sharding offers both advantages and disadvantages, making it crucial to assess whether a database will benefit from this strategy. The initial step in any sharding strategy is determining whether sharding is necessary at all. Generally, sharding is most suitable for high-volume databases that store substantial data in a few straightforward tables. It becomes particularly compelling when anticipating significant database growth or when needing regional data access or co-location, such as ensuring users access servers within the same country or continent, as required by large social media companies.

Conversely, for databases with numerous small to medium-sized tables, the complexity and challenges associated with sharding may outweigh its benefits. In such cases, alternatives like vertical scaling—increasing storage and computing power on a single server—or strategies such as replication for enhanced resilience and read-only throughput may be more practical.

## Conclusion

This guide explores the concept of database sharding. Sharding involves distributing data within a database table across multiple shards based on a designated sharding key. Each shard resides on a separate server. Ideally, records in a sharded database are evenly distributed among shards, all sharing the same table definitions and schemas, but each record resides on only one shard.

Sharding enables horizontal scaling of databases, leveraging the increased storage, memory, and processing capabilities of multiple servers. It enhances resilience and performance by reducing the number of records each query needs to search through, thereby speeding up data retrieval. However, sharding complicates database management and complicates operations like joins and schema modifications.

Sharding can be implemented using different strategies such as range sharding, hash sharding, or directory-based sharding. Range sharding is simpler but may result in uneven shard sizes. Hash sharding more evenly distributes records but is more complex to implement. Directory-based sharding groups related items on the same shard.

Implementing a sharded database often involves deploying multiple Utho servers. Utho offers robust Linux-based hosting solutions equipped with the LAMP stack, including high-performance options like Dedicated CPU services or flexible and cost-effective Shared CPU alternatives. Additionally, Utho's Managed Database service provides a hassle-free option for deploying and managing database clusters without handling infrastructure installation and maintenance.

{{< content "dbass-eos" >}}