---
title: "Integrating Utho's Managed MySQL with a Flask Application"
date: "2022-09-20"
title_meta: "Guide to Integrate Utho's Managed Mysql DB with Flask"
description: "This guide outlines the steps to configure your Flask application to access Utho's Managed MySQL db instance"
keywords: ["Utho", "MySQL", "database"]
tags: ["Managed Database", "MYSQL"]
icon: "mariadb"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/Products/Manage Databases/MySQL/integrating-flask-with-managed-mysql"]
tab: true
---

# Integrating Uthos Managed MySQL with a Flask Application

## Prerequisites

Before starting, ensure you have the following:

- **A Flask application.**
- **A Uthos account and a provisioned MySQL database.**
- **MySQL client installed** on your local machine.
- **Python 3 and `pip` installed.**
- **Basic knowledge of Flask and SQLAlchemy** (or any ORM you choose).

## Step 1: Configure the Uthos Managed MySQL Database

1. **Access the Uthos Console:** Log in to your Uthos account.
2. **Create a MySQL Database:** Navigate to the Managed Databases section and provision a new MySQL instance.
3. **Obtain Connection Details:** Note down the hostname, port, username, and password for the database instance. These details will be required to connect your Flask app.
4. **Allow IP Whitelisting:** Ensure that your development machine's IP address is whitelisted to access the database.

## Step 2: Install Required Python Packages

Install the necessary Python packages for MySQL and Flask:

```bash
pip install Flask flask-sqlalchemy mysql-connector-python
```

## Step 3: Configure Flask Application

Update your Flask application to include the database configuration. Use the connection details obtained from Uthos.

### Example Code

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# Replace the placeholders with your Uthos MySQL credentials
app.config['SQLALCHEMY_DATABASE_URI'] = (
    'mysql+mysqlconnector://<username>:<password>@<host>:<port>/<database>'
)
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

# Example model
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(80), nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)

@app.route('/')
def index():
    return "Hello, Uthos!"

if __name__ == '__main__':
    app.run(debug=True)
```

## Step 4: Test the Connection

1. **Run the Flask application:**

```bash
python app.py
```

2. **Open your browser** and navigate to `http://127.0.0.1:5000/` to ensure the app is running.
3. **Use the Flask shell** to test database connectivity:

```bash
flask shell
>>> from app import db
>>> db.create_all()  # Creates the tables defined in your models
```

## Step 5: Deploying Your Flask App

When deploying your Flask application to a server, ensure the server's IP address is whitelisted in the Uthos database settings. Additionally, use environment variables to securely store your database credentials.

### Example Using Environment Variables

Update the Flask app configuration:

```python
import os

app.config['SQLALCHEMY_DATABASE_URI'] = (
    f"mysql+mysqlconnector://{os.getenv('DB_USER')}:{os.getenv('DB_PASSWORD')}@"
    f"{os.getenv('DB_HOST')}:{os.getenv('DB_PORT')}/{os.getenv('DB_NAME')}"
)
```

Set the environment variables in your deployment environment:

```bash
export DB_USER=<username>
export DB_PASSWORD=<password>
export DB_HOST=<host>
export DB_PORT=<port>
export DB_NAME=<database>
```

## Step 6: Monitoring and Maintenance

- **Monitor Database Performance:** Use the Uthos console to monitor database metrics.
- **Automated Backups:** Enable automated backups in the Uthos database settings for data safety.
- **Scaling:** Upgrade your database instance from the Uthos console as your application grows.

## Conclusion

By following these steps, you can successfully integrate a Uthos Managed MySQL database with your Flask application. This setup ensures a robust and scalable backend for your application.

