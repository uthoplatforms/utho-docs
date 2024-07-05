---
slug: Mastering Apache Airflow: A Step-by-Step Guide to Establishing Connections and Variables
description: "Unlock Airflow's power with Variables and Connections via the CLI: Create, encrypt, and manage with ease."
og_description: "In this Apache Airflow tutorial, you'll dive into Airflow Variables and Connections. Discover how to swiftly create variables using the Airflow CLI, enabling encryption and source control. Plus, learn the essentials of setting up Connections through a Bash script and the Airflow CLI. These techniques are easily integrated into your Python-based Airflow data pipelines."
keywords: ['apache airflow tutorial', 'apache airflow features']
published: 2024-03-30
modified_by:
 name: Pawan Kumar
title: "Unleash Airflow Magic: Crafting Connections and Variables"
authors: ["Pawan Kumar"]
---

## What is Apache Airflow?

Airflow, an open-source platform, empowers you to automate, orchestrate, and monitor workflows and data pipelines seamlessly. Its standout feature lies in the ability to craft and execute workflows using code. By embracing code-powered workflows, you gain advantages such as version control, collaborative editing, and efficient debugging.

In Airflow's terminology, workflows are referred to as Directed Acyclic Graphs (DAGs). These DAGs outline the sequence of tasks to be executed, alongside the interdependencies between tasks. With Airflow, you can tackle various tasks like executing ETL processes (extract, load, and transform data), automating emails with CSV attachments, and orchestrating Machine Learning (ML) workflows effortlessly.

Connecting your Airflow data sources to a central data warehouse ensures that your data analysts can access all necessary data, preventing data silos within your organization. Additionally, transparent and reproducible code-driven workflows help minimize bottlenecks, as anyone with access to the workflow's code can debug it.

Airflow offers a Python Application Programming Interface (API) that allows you to code your Directed Acyclic Graphs (DAGs) and call any connection scripts you've created seamlessly.

## In this Guide

This tutorial offers a beginner-friendly introduction to two core concepts in Apache Airflow: Variables and Connections. You'll discover how to apply these concepts in your DAGs (Directed Acyclic Graphs) and data pipelines, laying the groundwork for more advanced Python scripting.

Here's what you'll learn in this Apache Airflow tutorial:

Storing Airflow values in variables using the Airflow command-line interface (CLI)
Automating connections to your data sources with a straightforward script and the Airflow CLI

## Airflow Essentials: Understanding Variables and Connections

In Airflow, accessing data from external sources such as databases and APIs is essential. This is where Airflow Connections come into play. They enable you to establish connections to your data sources, serving as the foundation for your Airflow DAGs. With Connections, you define your data's sources, staging areas, and destinations.

Additionally, Airflow Variables are handy for storing reusable values like URIs, database usernames, and configurations, among others, necessary for your DAGs. These variables are conveniently stored in Airflow's metadata database, ensuring easy access and management.

### Mastering Airflow: Exploring the Command-Line Interface (CLI)

The Airflow Command-Line Interface (CLI) is a powerful tool for managing your DAGs and handling Airflow objects such as connections and variables. It allows you to create, edit, and delete these objects effortlessly. By integrating CLI commands into scripts, you can automate your frequently used Airflow operations. This guide will teach you how to harness the potential of the Airflow CLI to automate the creation of your Airflow variables and connections.

## Efficient Automation: Streamlining Creation of Airflow Variables and Connections

### Create Your DAG Variables

Utilizing a JSON file to load Airflow variables offers a faster and more reproducible approach compared to using the Airflow graphical user interface (GUI). In this section, we'll illustrate a straightforward example to guide you through the process of creating and storing Airflow variables using the Airflow CLI.

1. With a text editor, craft a new JSON file to house key-value pairs of reusable data necessary for your DAGs. In this example, the file will contain connection details for a MySQL database.

    {{< file "~/example_vars.json">}}
{
    "my_prod_db": "dbname",
    "my_prod_db_user": "username",
    "my_prod_db_pass": "securepassword",
    "my_prod_db_uri": "mysql://192.0.2.0:3306/"
}
    {{</ file >}}


1. Execute the following command to load all your variables. Make sure to replace the path with the location of your example_vars.json file.

        airflow variables --import /home/username/example_vars.json

1. Run the following command to load all your variables. Ensure to substitute the path with the location of your example_vars.json file.

        airflow variables -g my_prod_db

    Airflow returns the value of the `my_prod_db` variable.
    {{< output >}}
dbname
    {{</ output >}}

    {{< note respectIndent=false >}}
    Airflow stores passwords for connections and variable values in plain text within the metadata database.
    {{< /note >}}

### Crafting Your Connection Script

You can utilize the Airflow CLI to establish connections to external systems needed for your DAGs. This section demonstrates how to create a basic connection using a reusable bash script, which you can customize for your specific Airflow Connections. Below is an example featuring a connection for a MySQL database.

1. Generate a new file called connection.sh. Replace the placeholders with your actual values or extend the script to create the necessary Connections for your DAGs.

{{< file "connection.sh">}}
#!/usr/bin/env bash

airflow connections -d --conn_id db_conn

airflow connections -a --conn_id db_conn --conn_type mysql --conn_host 'mysql://192.0.2.0:3306/' --conn_schema 'dbname' --conn_login 'username' --conn_port '3306' --conn_password 'securepassword'
    {{</ file >}}

The third line of the script removes any previously created connections to ensure idempotence. This ensures that the script can be executed multiple times with the same expected outcome.

1. Make sure your Connections script is executable:

       chmod u+x /home/username/connection.sh

1. Load your connections by running your finalized script:

        bash /home/username/connection.sh

1. Utilize the Airflow CLI to confirm the creation of your new Connection. Replace db_conn with the name of your Connection.

        airflow connections --list | grep 'db_conn'

## Best Practice: Handling Sensitive Variables

When storing sensitive connection variables in a JSON file or automating Airflow Connections with a script, it's crucial to establish a workflow for encrypting and decrypting sensitive values. Airflow stores connection passwords in plain text within the metadata database, posing a security risk. A dedicated workflow for handling sensitive connection data ensures that these values remain secure and are never exposed in raw string format. Below is an outline for a workflow you can adopt to safeguard your sensitive variables.

- **Encrypt**: To safeguard sensitive values, tools like Ansible Vault offer encryption capabilities before storing them in a remote repository, such as GitHub. HashiCorp Vault is another widely used tool for securely storing sensitive information. Additionally, the Python Crypto package provides encryption support for passwords.

- **Decrypt**: To execute any automation scripts containing encrypted variable values, you must include a decryption step before running them. Airflow requires decrypted values to execute your DAGs properly. Both Ansible Vault and HashiCorp Vault offer mechanisms to provide decrypted variable values to Airflow.

- **Load**: After decrypting your values, proceed to execute your scripts.

- **Encrypt**: Once your automated infrastructure has loaded both Airflow Variables and Connections, proceed to encrypt your sensitive values. By doing so, sensitive data is only accessible through an encrypted Airflow Database that only Airflow can access.

## Conclusion

Automating the setup of your Airflow Variables and Connections is essential for transparent data handling, enabling fast experimentation, prototyping, and analysis. Centralizing all relevant data connections in a secure repository sets the stage for organizational collaboration. This streamlined approach minimizes time spent on the extract and load stages of workflows, allowing more focus on the transformation step.
