---
title: "How To Create a New User and Grant Permissions in MySQL"
date: "2022-09-18"
---

![How To Create a New User and Grant Permissions in MySQL](images/Create-a-New-User-in-MySQL-and-Learn-How-to-Grant-Permissions-1-1024x576.png)

**Introduction**

In this article we will learn How To Create a New User and Grant Permissions in MySQL..

  
[MySQL](https://en.wikipedia.org/wiki/MySQL) is open-source RDBMS. As of this writing, it's the most popular open-source database in the world and part of the LAMP stack (Linux, Apache, MySQL, and PHP).

This guide explains how to create a [MySQL](https://utho.com/docs/tutorial/how-do-we-install-mysql-workbench-on-ubuntu-18-04/) user and grant permissions.

Follo the below steps to Create a New User and Grant Permissions in MySQL..

**Prerequisites**  
You'll need MySQL to follow this guide. This guide assumes the database is on a VPS running Ubuntu 20.04, but the principles it outlines should apply regardless of how you access your database.

If you don't have a MySQL database and want to set one up yourself, see our How To Install MySQL guide. The methods for creating a new MySQL user and granting permissions are generally the same regardless of your server's operating system.

## Making a New User

MySQL creates a root user account for database management during installation. This user has full control over the MySQL server's databases, tables, users, etc. This account should only be used for administrative purposes. This step shows how to use the root MySQL user to create a new user account.

In Ubuntu systems running MySQL 5.7 (and later), the root MySQL user authenticates with the auth socket plugin by default. This plugin requires that the operating system user who invokes MySQL match the command's MySQL user. This means you must use sudo with the mysql command to access the root MySQL user.

```
#sudo mysql
```

**Note:** If your root MySQL user has a password, use a different command to access the MySQL shell. The following will run your MySQL client with regular user privileges; you'll only gain administrator privileges by entering a password.

```
 #mysql -u root -p
```

From the MySQL prompt, you can create a new user with CREATE USER. The syntax is as follows:

```
mysql> #CREATE USER 'username'@'host' IDENTIFIED WITH authentication_plugin BY 'password';
```

After CREATE USER, set a username. It's followed by a @ sign and the hostname from which the user will connect. If you only plan to access this user locally, use localhost. Username and host should be enclosed in single quotes to avoid errors.

You can choose an authentication plugin for your user. The auth socket plugin provides strong security without requiring a password from valid users. But it prevents remote connections, which can complicate external programme interactions with MySQL.

You can omit WITH authentication plugin to use MySQL's default plugin, caching sha2 password. MySQL recommends this plugin for users who want to log in with a password due to its strong security.

Run the command to create a caching sha2 password user. Replace sammy with your preferred username and password.

```
mysql> #CREATE USER 'microhost'@'localhost' IDENTIFIED BY 'password';
```

**Note**:There's a known issue with some versions of PHP and caching sha2 password. If you plan to use this database with phpMyAdmin, create a user that uses the older, but still secure, mysql native password plugin instead.

```
mysql> #CREATE USER 'microhost'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```

If you're unsure, create a user that authenticates with caching sha2 plugin and then ALTER it with this command.

```
mysql> #ALTER USER 'microhost'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```

You can grant a new user privileges after creating them.

## Providing Authorization to a User

Syntax for granting user privileges:

```
mysql> # GRANT PRIVILEGE ON database.table TO 'username'@'host';
```

PRIVILEGE in this example syntax defines what actions the user can perform on the specified database and table. You can grant multiple privileges to the same user with one command. You can also grant a user global privileges by entering \* for database and table names. SQL asterisks represent "all" databases or tables.

For example, the following command grants a user global privileges to CREATE, ALTER, and DROP databases, tables, and users, as well as INSERT, UPDATE, and DELETE data from any table on the server. The user can also query data with SELECT, create foreign keys with REFERENCES, and perform FLUSH operations with RELOAD. You should only give users the permissions they need, so feel free to adjust your own user's privileges.

MySQL documentation lists all available privileges.

Run this GRANT statement, substituting your MySQL user's name for microhost:

```
mysql> #GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'microhost'@'localhost' WITH GRANT OPTION;
```

With GRANT OPTION is also included. This lets your MySQL user grant permissions to other users.

**Warning**: Some users may want to grant their MySQL user ALL PRIVILEGES, which will give them broad superuser privileges like root's.

```
mysql> #GRANT ALL PRIVILEGES ON *.* TO 'microhost'@'localhost' WITH GRANT OPTION;
```

Such broad privileges should not be granted lightly, as this MySQL user will have access to every server database.

Many guides suggest running FLUSH PRIVILEGES after a CREATE USER or GRANT statement to reload the grant tables and ensure new privileges take effect.

```
mysql> #FLUSH PRIVILEGES;
```

According to the MySQL documentation, when you modify the grant tables indirectly with an account management statement like GRANT, the database reloads the grant tables immediately into memory, so we don't need FLUSH PRIVILEGES. Using it won't harm the system.

Revoking permission is similar to granting it:

```
mysql> #REVOKE type_of_permission ON database_name.table_name FROM 'username'@'host';
```

To revoke permissions, use FROM instead of TO.

Run SHOW GRANTS to see a user's current permissions.

```
mysql> #SHOW GRANTS FOR 'username'@'host';
```

Just as DROP deletes databases, it deletes users.

```
mysql> #DROP USER 'username'@'localhost';
```

After creating a MySQL user and granting privileges, exit the client:

```
mysql> #exit
```

To log in as your new MySQL user, type:

```
mysql> #mysql -u microhost -p
```

The -p flag prompts MySQL for your password to authenticate.

Hopefully you have understood all the steps carefully to Create a New User and Grant Permissions in MySQL ..

Must read:- [https://utho.com/docs/tutorials/how-to-install-mysql-with-phpmyadmin-on-ubuntu-14-04/](https://utho.com/docs/tutorials/how-to-install-mysql-with-phpmyadmin-on-ubuntu-14-04/)

**Thankyou**
