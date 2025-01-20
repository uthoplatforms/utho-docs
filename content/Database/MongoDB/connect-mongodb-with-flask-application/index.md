---
title: "How to Connect Flask Application with MongoDB on CentOS"
date: "2022-06-22"
title_meta: "Guide for Node.js Application with MongoDBin Centos "
description: "Learn how to connect a Flask application with MongoDB. This guide provides a step-by-step tutorial to set up a basic Flask application to interact with MongoDB.
"
keywords: ["Flask", "python", "MongoDB", "CentOS", "Mongodb"]

tags: ["Flask", "python", "MongoDB", "CentOS"]
icon: "mongodb"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Database/MongoDB/connect-mongodb-with-flask-application']
tab: true

---

# Connecting MongoDB with a Python Flask Application

## Prerequisites

Before starting, ensure you have the following:

- **Flask installed** in your Python environment.
- **MongoDB installed locally** or a running MongoDB server.
- **Python 3 installed** with `pip`.
- **Basic knowledge of Flask and Python.**

## Step 1: Install Required Python Packages

Install the necessary Python packages to connect Flask to MongoDB:

```bash
pip install Flask pymongo dnspython
```

`pymongo` is the official MongoDB driver for Python, and `dnspython` helps with DNS resolution for MongoDB URIs.

## Step 2: Set Up a MongoDB Database

1. **Start MongoDB:** Ensure your MongoDB server is running. If you installed MongoDB locally, use the following command to start it:

   ```bash
   mongod
   ```

2. **Create a Database:** Use MongoDB Compass or the CLI to create a new database, for example, `mydatabase`.
3. **Create a Collection:** Inside `mydatabase`, create a collection, for example, `users`.

## Step 3: Configure Flask Application

Create a file named `app.py` and include the following code to connect your Flask application to MongoDB:

```python
from flask import Flask, jsonify, request
from pymongo import MongoClient

app = Flask(__name__)

# Replace with your MongoDB URI
MONGO_URI = "mongodb://127.0.0.1:27017"
client = MongoClient(MONGO_URI)

db = client['mydatabase']
users_collection = db['users']

@app.route('/add_user', methods=['POST'])
def add_user():
    user_data = request.json
    result = users_collection.insert_one(user_data)
    return jsonify({"message": "User added", "user_id": str(result.inserted_id)})

@app.route('/get_user/<name>', methods=['GET'])
def get_user(name):
    user = users_collection.find_one({"name": name})
    if user:
        user["_id"] = str(user["_id"])
        return jsonify(user)
    return jsonify({"error": "User not found"}), 404

if __name__ == '__main__':
    app.run(debug=True)
```

## Step 4: Run the Application

Run your Flask application using the following command:

```bash
python app.py
```

### Testing Endpoints

1. **Add a User:** Use a tool like Postman or `curl` to test the `/add_user` endpoint:

   ```bash
   curl -X POST -H "Content-Type: application/json" \
   -d '{"name": "John Doe", "email": "john.doe@example.com"}' \
   http://127.0.0.1:5000/add_user
   ```

2. **Get a User:** Test the `/get_user/<name>` endpoint:

   ```bash
   curl http://127.0.0.1:5000/get_user/John%20Doe
   ```

## Step 5: Use Environment Variables for Configuration

Avoid hardcoding sensitive information. Use environment variables for the MongoDB URI.

### Using `python-dotenv`

Install `python-dotenv`:

```bash
pip install python-dotenv
```

Create a `.env` file:

```env
MONGO_URI=mongodb://127.0.0.1:27017
```

Update `app.py`:

```python
from dotenv import load_dotenv
import os

load_dotenv()

MONGO_URI = os.getenv("MONGO_URI")
client = MongoClient(MONGO_URI)
```

## Step 6: Advanced Usage

- **Indexes:** Create indexes in your collections to improve query performance.
- **Authentication:** Use a MongoDB URI with username and password for authenticated databases.
- **Connection Pooling:** Configure connection pooling in `MongoClient` for better performance.

## Conclusion

By following these steps, you can connect a MongoDB database to your Flask application. This setup is compatible with a variety of Flask extensions and forms the foundation for building scalable web applications.

