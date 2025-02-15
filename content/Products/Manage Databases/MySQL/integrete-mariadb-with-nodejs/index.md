---
title: "Integrating Utho's MySQL Managed Database with Node.js Application"
date: "2022-09-20"
title_meta: "Guide to Integrate Utho's Managed Mysql DB with nodejs"
description: "This guide outlines the steps to configure your nodejs application to access Utho's Managed DB instance"
keywords: ["Utho", "MySQL", "database"]
tags: ["Managed Database", "MYSQL"]
icon: "mariadb"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Products/Manage Databases/MySQL/integrete-mariadb-with-nodejs']
tab: true
---

# Integrating Utho's MySQL Managed Database with Node.js Application

In this guide, we will walk through the process of integrating a MySQL managed database with a Node.js application. This step-by-step tutorial will cover everything from setting up the database to querying it and securing your credentials.

## Prerequisites

Before starting, ensure you have the following:

- **Node.js** installed on your system.
- Access to a **MySQL managed database** (whether hosted on VM or Deployed as Utho's Managed Databse).
- **npm (Node Package Manager)** installed to manage dependencies.

## Step 1: Setting Up the MySQL Managed Database

To integrate MySQL with your Node.js application, you first need to set up a MySQL database instance. Follow the steps below:

1. Log in to your Utho Cloud and deploy a Maraiadb Managed Instance provider's dashboard.
2. Create a new MySQL database instance if you are deploying this on a tradional VM/local machine.
3. If you are using, Utho's Managed Database you can skip this step or proceed to configure the database instance with:
   - A database name.
   - A username and password.
   - Set appropriate security groups/firewall rules to allow remote access.

After completing these steps, you should have the following connection details:
- **Host** (e.g., `your-managed-database-instance.hostname`)
- **Port** (default is `3306`)
- **Username** (e.g., `dbadmin`)
- **Password** (your set password)
- **Database name** (e.g., `mydatabase`)

## Step 2: Installing MySQL Library in Node.js

Next, you need to install a MySQL library for Node.js. We will use the `mysql2` library, which is one of the most popular libraries for this purpose.

Run the following command to install `mysql2`:

```bash
npm install mysql2
```

This will add mysql2 to your node_modules directory.

### Step 3: Creating a Connection to the Database
In your Node.js application, create a file (e.g., db.js) to manage the connection to your MySQL database.

Example configuration in db.js:

``` js
// db.js
const mysql = require('mysql2');

// Create a connection to the database
const connection = mysql.createConnection({
  host: 'your-database-instance.xyz.cloudprovider.com', // Replace with your host
  user: 'dbadmin', // Replace with your MySQL username
  password: 'yourpassword', // Replace with your MySQL password
  database: 'mydatabase', // Replace with your database name
  port: 3306 // Default MySQL port
});

// Connect to the MySQL database
connection.connect((err) => {
  if (err) {
    console.error('Error connecting to the database:', err.stack);
    return;
  }
  console.log('Connected to the database with ID:', connection.threadId);
});

module.exports = connection;

```

This script creates a connection to the MySQL database using the provided credentials.

##  Step 4: Querying the Database from Your Application
Once the connection is established, you can query the database from your Node.js application. For example, to retrieve all records from a **users** table:

```js
// app.js
const express = require('express');
const connection = require('./db');

const app = express();
const port = 3000;

// Endpoint to get all users from the database
app.get('/users', (req, res) => {
  connection.query('SELECT * FROM users', (err, results) => {
    if (err) {
      console.error('Error fetching data:', err);
      res.status(500).send('Internal Server Error');
      return;
    }
    res.json(results); // Send the query results as JSON
  });
});

// Start the server
app.listen(port, () => {
  console.log(`Server is running on http://localhost:${port}`);
});

```

This example uses Express.js to create a simple web server that fetches data from the users table.

## Step 5: Testing the Application
Now that your Node.js app is set up to interact with the MySQL database, test the application by running:

```bash
node app.js
```


Once the server is running, access the /users endpoint by opening a browser or using a tool like Postman:

```
http://localhost:3000/users
```

You should receive a JSON response with the data from the users table.

## Step 6: Error Handling and Connection Pooling
In a production environment, you should handle errors gracefully and use connection pooling to improve performance.

Example with connection pooling:

```js
// db.js with connection pooling
const mysql = require('mysql2');

// Create a connection pool
const pool = mysql.createPool({
  host: 'your-database-instance.xyz.cloudprovider.com',
  user: 'dbadmin',
  password: 'yourpassword',
  database: 'mydatabase',
  port: 3306,
  waitForConnections: true,
  connectionLimit: 10, // Maximum number of connections in the pool
  queueLimit: 0 // No queue limit
});

module.exports = pool;
```

Using connection pooling ensures that the application doesn’t repeatedly open and close connections, which is more efficient.

## Step 7: Securing Your Credentials
For security, avoid hardcoding sensitive information like database credentials in your code. Instead, use environment variables.

Install the **dotenv** package:

```bash
npm install dotenv
```

Create a **.env** file with your credentials:

```env
DB_HOST=your-database-instance.xyz.cloudprovider.com
DB_USER=dbadmin
DB_PASSWORD=yourpassword
DB_NAME=mydatabase
```

Modify **db.js** to load the credentials from the **.env** file:


```js
require('dotenv').config(); // Load environment variables

const mysql = require('mysql2');

const connection = mysql.createConnection({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  port: 3306
});

connection.connect((err) => {
  if (err) {
    console.error('Error connecting to the database:', err.stack);
    return;
  }
  console.log('Connected to the database with ID:', connection.threadId);
});

module.exports = connection;

```

Now, your database credentials are securely stored in the .env file.

# Conclusion
By following these steps, you’ve successfully integrated a MySQL managed database with a Node.js application. You can now retrieve and manipulate data in real-time. With these practices, your application will be more secure, efficient, and ready for production.