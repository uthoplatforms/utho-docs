---
slug: Beginner guide nodejs sqlite
description: 'With npm and sqlite3, you can make your Node.js applications shine. This guide explains how you can install Node.js SQLite, create a SQLite database, create tables, and insert data.'
keywords: ['nodejs sqlite']
tags: ['nodejs', 'sqlite', 'database']
published: 2024-07-11
modified: 2024-07-11
modified_by:
  name: Utho
title: "Getting Started with Node.js SQLite"
title_meta: "Node.js SQLite3: A Beginner's Guide to Installation & Usage"
authors: ["Pawan Kumar"]
---

## Getting Started with Node.js and SQLite

SQLite is an excellent choice for standalone databases in applications that don't require a full client-server setup. Integrating SQLite3 with Node.js is straightforward and offers all the advantages of a SQL database persistence layer without requiring a DBA or DevOps team.

For a detailed overview, refer to the SQLite3 API documentation for demonstrations and guides on how to proceed.

**Prerequisite:**
To get started with Node.js and SQLite3, you must have installed Node.js and Node Package Manager (`npm`) on your machine. If you have not, install using the below commands:

    sudo apt install npm
    sudo apt install nodejs

Having installed Node.js, SQLite is now ready to be installed using `npm` for SQLite3.

## Install SQLite

Install SQLite support into Node.js using `npm` on your local development environment.

    sudo npm install sqlite3

## Create a Database

You can now create an SQLite database using Node.js. This example demonstrates a basic database application for tracking superheroes from the Marvel Cinematic Universe.

First, create a file called `sample.js` and import the `sqlite3` module into Node.js:

    {{< file "sample.js" js >}}
var sqlite3 = require('sqlite3');
{{</ file >}}

The following line creates a database named mcu.db in the current working directory. The sqlite3.Database() call can accept one, two, or three arguments. The second argument includes SQLite database flags, which can be sqlite3.OPEN_READONLY, sqlite3.OPEN_READWRITE, or sqlite3.OPEN_CREATE.

    {{< file "sample.js" js >}}
...

new sqlite3.Database('./mcu.db', sqlite3.OPEN_READWRITE, (err) ...);
{{</ file >}}
    {{< note respectIndent=false >}}
The following are the different SQLite flag combinations:

   - `OPEN_READONLY`: The database is opened in read-only mode. If the database does not already exist, an error is returned.

   - `OPEN_READWRITE`: The database is opened for reading and writing where the database must already exist, otherwise an error is returned.

   - `OPEN_CREATE`: The database is opened for reading and writing, and if the database does not exist, it is created.

   The default SQLite database flag is `sqlite3.OPEN_READWRITE` and `sqlite3.OPEN_CREATE`.
{{< /note >}}

The third argument in the `sqlite3.Database()` is a callback function that is called when the database is opened successfully or when an error occurred. This callback function has the error object, `err` as the first parameter. If an error occurred, the error object is not null, otherwise, it is null.
        {{< file "sample.js" js >}}

var sqlite3 = require('sqlite3');
var db;
new sqlite3.Database('./mcu.db', sqlite3.OPEN_READWRITE, (err) => {
    if (err && err.code == "SQLITE_CANTOPEN") {
        createDatabase();
        return;
        } else if (err) {
            console.log("Getting error " + err);
            exit(1);
    }
    runQueries(db);
});
{{< /file >}}

1. If the database exists, the `runQueries()` is executed. Now you need to create the `createDatabase()` function as shown below:
        {{< file "sample.js" >}}

function createDatabase() {
    var newdb = new sqlite3.Database('mcu.db', (err) => {
        if (err) {
            console.log("Getting error " + err);
            exit(1);
        }
        createTables(newdb);
    });
}
{{< /file >}}

The above code is similar to that of creating the database. However, this time the flags are missing; that means that the database is created if it does not exist yet. If it succeeds, the `createTables()` is executed to create the tables. If we get an error again, something more serious is going on, so the code exits.

## Create Tables and Insert Data

The code snippet below demonstrates the use of SQLite's exec() method to create tables and populate them with data. The `exec()` method executes all queries provided in a string. Once the tables are created and populated, the `runQueries()` method is called. This example creates tables for popular Marvel superheroes such as X-Men, Thanos, and others, along with a table for their superpowers.

        {{< file "sample.js" >}}
function createTables(newdb) {
    newdb.exec(`
    create table hero (
        hero_id int primary key not null,
        hero_name text not null,
        is_xman text not null,
        was_snapped text not null
    );
    insert into hero (hero_id, hero_name, is_xman, was_snapped)
        values (1, 'Spiderman', 'N', 'Y'),
               (2, 'Tony Stark', 'N', 'N'),
               (3, 'Jean Grey', 'Y', 'N');

    create table hero_power (
        hero_id int not null,
        hero_power text not null
    );

    insert into hero_power (hero_id, hero_power)
        values (1, 'Web Slinging'),
               (1, 'Super Strength'),
               (1, 'Total Nerd'),
               (2, 'Total Nerd'),
               (3, 'Telepathic Manipulation'),
               (3, 'Astral Projection');
        `, ()  => {
            runQueries(newdb);
    });
}
{{< /file >}}

## Query the Database

You can use one of several methods to fetch rows from the database. The data can be fetched row by row, looped over, or returned in a single array. In this case, the latter method is used. The following code returns characters whose superpowers are being "Total Nerds," and whether they are X-Men or were snapped by Thanos.
    {{< file "sample.js" >}}
function runQueries(db) {
    db.all(`
    select hero_name, is_xman, was_snapped from hero h
    inner join hero_power hp on h.hero_id = hp.hero_id
    where hero_power = ?`, "Total Nerd", (err, rows) => {
        rows.forEach(row => {
            console.log(row.hero_name + "\t" +
            row.is_xman + "\t" +
            row.was_snapped);
        });
    });
}
{{< /file >}}

The `all()` method of the sqlite3 returns an array of rows on success, or an error on failure.

{{< note respectIndent=false >}}
It's recommended to parameterize queries by supplying a list of substitution values or an object with properties. This method allows substitution using the `$propertyName` syntax, helping to prevent SQL injection attacks. For more information on this security vulnerability, refer to our guide SQL Injection Attack: What It Is and How to Prevent It.
{{< /note >}}

Below is the complete `sample.js` file:
    {{< file "sample.js" >}}
var sqlite3 = require('sqlite3');
let db= new sqlite3.Database('./mcu.db', sqlite3.OPEN_READWRITE | sqlite3.OPEN_CREATE, (err) => {
    if (err && err.code == "SQLITE_CANTOPEN") {
        createDatabase();
        return;
        } else if (err) {
            console.log("Getting error " + err);
            exit(1);
    }
    runQueries(db);
});

function createDatabase() {
    var newdb = new sqlite3.Database('mcu.db', (err) => {
        if (err) {
            console.log("Getting error " + err);
            exit(1);
        }
        createTables(newdb);
    });
}

function createTables(newdb) {
    newdb.exec(`
    create table hero (
        hero_id int primary key not null,
        hero_name text not null,
        is_xman text not null,
        was_snapped text not null
    );
    insert into hero (hero_id, hero_name, is_xman, was_snapped)
        values (1, 'Spiderman', 'N', 'Y'),
               (2, 'Tony Stark', 'N', 'N'),
               (3, 'Jean Grey', 'Y', 'N');

    create table hero_power (
        hero_id int not null,
        hero_power text not null
    );

    insert into hero_power (hero_id, hero_power)
        values (1, 'Web Slinging'),
               (1, 'Super Strength'),
               (1, 'Total Nerd'),
               (2, 'Total Nerd'),
               (3, 'Telepathic Manipulation'),
               (3, 'Astral Projection');
        `, ()  => {
            runQueries(newdb);
    });
}

function runQueries(db) {
    db.all(`select hero_name, is_xman, was_snapped from hero h
   inner join hero_power hp on h.hero_id = hp.hero_id
   where hero_power = ?`, "Total Nerd", (err, rows) => {
        rows.forEach(row => {
            console.log(row.hero_name + "\t" +row.is_xman + "\t" +row.was_snapped);
        });
    });
}
{{< /file >}}

When you execute `sample.js` file, the following result is generated:
    {{< output >}}
username@localhost:~$ node sample.js
Spiderman	N	Y
Tony Stark	N	N
{{< /output >}}
