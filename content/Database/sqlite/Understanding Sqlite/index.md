---
slug: Understanding Sqlite
description: 'This guide discusses the advantages and disadvantages of SQLite, one of the most popular of the serverless SQL databases, and also common use cases for SQLite.'
keywords: ['what is sqlite']
published: 2024-07-11
modified_by:
  name: Utho
title: "What is SQLite?"
title_meta: "A SQLite Overview"
tags: ["database"]
authors: ["Pawan Kumar"]
---

There are two primary types of databases: client/server relational databases (such as MySQL or PostgreSQL), and NoSQL databases (like MongoDB or CouchDB). However, there exists a third category known as serverless SQL databases, which bridges the gap between the two.

SQLite is the most prominent serverless SQL database. Its popularity stems from its compatibility with numerous operating systems and programming languages.

The key distinction between SQLite and traditional SQL databases is that SQLite does not require a separate database server. Instead, the database is maintained within files that the application directly accesses. This approach allows SQL queries to be processed directly within the application, eliminating the need for a dedicated server process.

## The Advantages of SQLite

SQLite operates independently and requires no setup prior to use. An application can be packaged with an existing SQLite database, or it can create one from scratch upon its initial launch. This feature makes SQLite particularly suitable for embedded and mobile applications where network connectivity may be unreliable or absent. Moreover, applications utilizing SQLite do not need to ensure that a database server is running before starting.

Because SQLite utilizes a standardized database file format that is consistent across different languages and platforms, database files can be moved between systems without compatibility issues. SQLite achieves this cross-compatibility through its use of a common library for database access across various languages and operating systems.

## The Disadvantages of SQLite

If a database is stored locally, multiple machines are not able to access the stored data to read or update it. On the other hand, SQLite does support concurrent reads and writes to a database on from multiple processes on the same machine. When a writes occurs, it locks the entire database until the write completes.

Since your application does all the heavy lifting, its responsiveness can slow down when running complex queries.

SQLite supports many advanced SQL capabilities, but not all of them. There are no stored procedures; and data types may be more limited than those of traditional server-based SQL databases.

Keep in mind, as well, that because the database is maintained inside the same process as the application, a compromised application could gain access to all the data stored in the SQLite database. An abrupt termination of the application might also cause the database to be left in an inconsistent state.

## What Are the Right Use Cases?

SQLite is an excellent option when you need to provide relational data to applications that operate in offline environments. For instance, a mobile application requiring access to dealer locations even when offline could include a SQLite database bundled with the application. This database can be periodically updated through downloads that replace the entire file.

Another compelling use case is for storing data locally or caching data for later synchronization with a remote system. For example, a game might employ a local SQLite database to maintain state information about a player's progress.

SQLite can also be suitable for utility web services that do not require scalability. Developers can build applications or web services using SQLite databases without the need to set up a separate database server or manage access credentials.

## Creating a SQLite Database

SQLite eliminates the need to connect from a client to a database server for database creation. Instead, most applications utilizing SQLite create their databases within the application itself. Stand-alone programs also exist that enable users to create SQLite database files using a traditional graphical user interface (GUI). One of the most widely used tools for this purpose is DB Browser for SQLite.

## Using SQLite in Applications

From a developer's perspective, using SQLite as a persistence layer is similar to working with any other SQL database. There are drivers available for various programming languages: Java, and libraries for Swift, C++, and Python.

The typical workflow for an application using SQLite involves checking for the existence of a database. If one does not exist, the application creates it. Then, your application code can perform operations such as creating, reading, updating, and deleting data as required.