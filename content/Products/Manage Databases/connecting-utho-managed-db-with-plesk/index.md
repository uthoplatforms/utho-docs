---
weight: 20
title: "Integrating Utho's Managed Database to Plesk"
title_meta: "Integrating Utho's Managed Database to Plesk"
description: "Easily connect your Utho's Postgres Managed Databse with PgAdmin"
keywords: ["cloud", "managed database", "plesk", "mysql", "postgres"]
tags: ["utho platform", "cloud", "managed database", "postgres"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/Products/Manage Databases/connecting-utho-managed-db-with-plesk"]
icon: "node"
tab: true
homecard: true

---



# Integrating Utho's Managed Database to Plesk

This guide will walk you through integrating Utho's managed database with your Plesk server. Using a managed database service provides a reliable, scalable, and secure solution for your web applications.

## Prerequisites

- A Plesk server with admin access
- Utho's managed database service credentials (URL, username, password, and database name)


### 1. Log in to Plesk

To begin, log in to your Plesk server with your administrator credentials. Open the Plesk control panel in your browser:

```
https://your-plesk-server:8443
```

Enter your admin username and password to access the Plesk dashboard.

### 2. Accessing the Databases Section

Once logged in, navigate to the **Websites & Domains** tab. From the left-hand sidebar, locate and click on the **Databases** section under your domain.

### 3. Add a New Database Connection

In the **Databases** section, you will need to add a new database. To do so:

1. Click on the **Add Database** button.
2. In the **Database name** field, enter a name for the database (you can use the name provided by Utho or create a new one).
3. In the **Database user** section, either select an existing user or create a new one. Make sure to provide a strong password.
4. For the **Database type**, select **MySQL** or **PostgreSQL**, depending on your Managed database configuration.

### 4. Configuring Remote Access to the Database

Utho's managed database is typically hosted externally. To allow your Plesk server to connect to it, you need to enable remote database access. Here's how:

1. Under **Database settings**, click on **Add Remote Access**.
2. Enter the IP address or hostname of your Utho-managed database.
3. Specify the database port (for MySQL, it’s usually `3306`; for PostgreSQL, it’s typically `5432`).
4. Allow remote access from your Plesk server to Utho’s database.

Ensure that Utho has provided the correct credentials for the database connection.

### 5. Update Connection Details

After creating the database and enabling remote access, you’ll need to update your application configuration to use the Utho-managed database. You can do this in your application’s settings file or environment variables.

For example, in a Django application:

```python
# settings.py

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',  # or 'postgresql' for PostgreSQL
        'NAME': 'your_utho_database_name',
        'USER': 'your_utho_user',
        'PASSWORD': 'your_utho_password',
        'HOST': 'your_utho_database_host',
        'PORT': '3306',  # or '5432' for PostgreSQL
    }
}
```

Update the host, database name, user, and password with the details provided by Utho.

### 6. Test the Connection

Once the database is added and the settings are configured, test the connection to ensure everything is set up correctly. You can test it by accessing your website or application, or using the **phpMyAdmin** or **pgAdmin** interface within Plesk.

### 7. Secure Your Database

To enhance security, consider the following best practices:

- Use SSL encryption for database connections offered by Utho.
- Enable firewalls and restrict access to only authorized IPs.
- Regularly back up your database.

### 8. Monitoring and Maintenance

After the integration, it’s important to monitor the performance of the database. Utho’s offers a dashboard for performance monitoring, but you can also monitor through Plesk’s database tools or by using third-party services.

### Conclusion

With these steps, you have successfully added Utho's managed database to your Plesk server. This setup ensures that your database is managed, secure, and scalable, allowing you to focus on developing your application.

If you encounter any issues, please feel free to raise a ticket