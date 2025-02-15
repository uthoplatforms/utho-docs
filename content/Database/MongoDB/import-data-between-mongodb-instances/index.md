---
title: "How to Import Data between MongoDB instances"
date: "2022-11-20"
title_meta: "Import data between Mongodb Instances"
description: "Learn how to migrate data between your MongoDB instances."
keywords: ["install MongoDB Ubuntu 20.04", "MongoDB setup Ubuntu 20.04", "Ubuntu 20.04 MongoDB installation guide", "NoSQL database Ubuntu", "Ubuntu MongoDB tutorial", "MongoDB installation steps Ubuntu 20.04", "database management Ubuntu", "MongoDB Ubuntu 20.04 instructions"]
tags: ["MongoDB ", "importing", "backup", "migrate"]
icon: "mongodb"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Database/MongoDB/import-data-between-mongodb-instances']
---

# How to Import Data Between MongoDB Instances

This guide walks you through importing data from one MongoDB instance to another. This can be helpful for migrations, backups, or syncing data. The steps are simple and user-friendly, even for beginners.

## Prerequisites

Make sure you have:

- **Source MongoDB instance**: Where you’re exporting the data from.
- **Target MongoDB instance**: Where you’re importing the data to.
- **MongoDB Database Tools installed**, which include `mongodump` and `mongorestore`.
- **Access to both MongoDB instances**, either locally or remotely.

---

## Step 1: Export Data Using `mongodump`

`mongodump` is used to export data from the source MongoDB instance into a BSON format.

### Command:

```bash
mongodump --host <source_host> --port <source_port> --username <username> --password <password> \
  --authenticationDatabase <auth_db> --db <database_name> --out <output_directory>
```

### Example:

```bash
mongodump --host 127.0.0.1 --port 27017 --db mydatabase --out ./backup
```

### Explanation:
- `--host`: IP or hostname of the source MongoDB instance.
- `--port`: Port number of the MongoDB instance.
- `--username` and `--password`: Credentials for authentication (skip if not needed).
- `--authenticationDatabase`: Database used for authentication.
- `--db`: Name of the database to export.
- `--out`: Folder where the exported data will be saved.

This creates a folder (`./backup`) containing your database and collection files in BSON format.

---

## Step 2: Transfer the Backup Files

If your source and target MongoDB instances are on different machines, you need to move the backup files to the target server.

### Using `scp`:

```bash
scp -r ./backup user@target_host:/path/to/target_directory
```

### Using `rsync`:

```bash
rsync -av ./backup/ user@target_host:/path/to/target_directory
```

---

## Step 3: Import Data Using `mongorestore`

`mongorestore` imports the BSON dump into the target MongoDB instance.

### Command:

```bash
mongorestore --host <target_host> --port <target_port> --username <username> --password <password> \
  --authenticationDatabase <auth_db> --db <database_name> <path_to_dump>
```

### Example:

```bash
mongorestore --host 127.0.0.1 --port 27017 --db mydatabase ./backup/mydatabase
```

### Explanation:
- `--host`: IP or hostname of the target MongoDB instance.
- `--port`: Port number of the MongoDB instance.
- `--username` and `--password`: Credentials for authentication (skip if not needed).
- `--authenticationDatabase`: Database used for authentication.
- `--db`: Name of the database to import into.
- `<path_to_dump>`: Path to the dump folder or specific BSON file.

---

## Step 4: Verify the Import

After importing, verify the data in the target MongoDB instance:

1. Connect to the target instance using the CLI or a GUI like MongoDB Compass.
2. Check the imported database and collections.

### Example:

```bash
mongo --host 127.0.0.1 --port 27017
> use mydatabase
> db.collection_name.find()
```

---

## Bonus Tips for Advanced Users

- **Export/Import Specific Collections:**

  Export:
  ```bash
  mongodump --db mydatabase --collection users --out ./backup
  ```

  Import:
  ```bash
  mongorestore --db mydatabase ./backup/mydatabase/users.bson
  ```

- **Overwrite Existing Data:** Use `--drop` with `mongorestore` to clear existing data before importing.

  ```bash
  mongorestore --drop --db mydatabase ./backup/mydatabase
  ```

- **Compress Files:** Use `--gzip` to compress backup files during export and import.

  Export:
  ```bash
  mongodump --gzip --db mydatabase --out ./backup
  ```

  Import:
  ```bash
  mongorestore --gzip --db mydatabase ./backup
  ```

---

## Conclusion

Following these steps, you can easily transfer data between MongoDB instances. Customize commands as needed for your setup and enjoy seamless data migrations!

