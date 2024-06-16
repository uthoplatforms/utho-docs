---
title: "How To Reset Your MySQL or MariaDB Root Password on Ubuntu 18.04"
date: "2022-09-16"
---

![](images/How-To-Reset-Your-MySQL-or-MariaDB-Root-Password-on-Ubuntu-18.04-1-1024x576.png)

**Introduction**

Password forgetting happens to even the best of us. If you have access to the server and a user account with sudo rights, you can still acquire access and reset the root password for your MySQL or MariaDB database.

**note:**  
As long as you connect from the system's root account, the default MySQL or MariaDB configuration on fresh Ubuntu 18.04 installations typically lets you access the database (with full administrator capabilities) without requiring a password. It might not be essential to reset the password in this case. Try using the sudo mysql command to access the database before changing the database root password. Only if the default setting for authentication was adjusted, and this results in an access denied issue, follow the steps in this tutorial.

This guide shows how to reset MySQL and MariaDB's root passwords on Ubuntu 18.04. Changing the root password depends on whether MySQL or MariaDB is installed and the distribution's default systemd settings or third-party packages. These instructions may work with different system or database server versions, but they've been tested with Ubuntu 18.04 and distribution-supplied packages.

**Prerequisites**

You'll need the following to recover your MySQL or MariaDB root password:

Using a sudo user or another method of gaining access to the server with root capabilities, you can connect to the Ubuntu 18.04 server that is running MySQL or MariaDB. Create a test server with an ordinary, non-root user who has sudo capabilities using the initial server setup lesson if you want to test out the recovery techniques in this guide without having them impact your production server. Install MySQL after that the best way to set up MySQL on Ubuntu 18.04.

## Step 1 — Identifying the Database Version and Stopping the Server

MySQL or MariaDB, a well-liked drop-in replacement that is 100% compatible with MySQL, are both supported by Ubuntu 18.04. Depending on which of these you have installed, you'll need to use various commands to recover the root password. To find out which database server you're running, follow the instructions in this section.

The command below will verify your version:

```
#mysql --version
```

The result will display "MariaDB" followed by the version number if MariaDB is being used:

```
MariaDB output  
mysql Ver 15.1 Distrib 10.1.47-MariaDB, for debian-linux-gnu (x86_64) using readline 5.2
```

If you are using MySQL, you will see output similar to this:

```
MySQL output  
mysql Ver 14.14 Distrib 5.7.32, for Linux (x86_64) using EditLine wrapper
```

Note the database you are using because it may affect the commands you should use in the remaining sections of this guide.

You must terminate the database server in order to modify the root password. You can use the next command to do so if you are using MariaDB:

```
#sudo systemctl stop mariadb
```

To turn off the database server for MySQL, use the following command:

```
#sudo systemctl stop mysql
```

You can reset the root password by restarting the database in safe mode after the database has been terminated.

## Step.2 Database Server Restarting Without Permission Checks

Running MySQL and MariaDB without permission checks gives root access without a password. Stop the database from loading grant tables, which store user privileges. You may want to disable networking to prevent other clients from connecting to the temporarily vulnerable server.

Starting the server without loading the grant tables varies every database server.

**Configuring MariaDB to Start Without Grant Tables**

We'll utilise the systemd unit file to define additional options for the MariaDB server daemon so that it can be started without the grant tables.

Run the command below to set the MYSQLD OPTS environment variable, which MariaDB uses when it starts up. When starting MariaDB, the —skip-grant-tables and —skip-networking options instruct it to do so without loading the grant tables or networking features:

```
#sudo systemctl set-environment MYSQLD_OPTS="--skip-grant-tables --skip-networking"
```

start the MariaDB server:

```
#sudo systemctl start mariadb
```

Although this command doesn't output anything, it will restart the database server using the updated environment variable settings.

With sudo systemctl status mariadb, you may confirm that it began.

Now you shouldn't need to enter a password to access to the database as the MariaDB root user:

```
#sudo mysql -u root
```

```
MariaDB prompt  
Type 'help;' or 'h' for help. Type 'c' to clear the current input statement.

MariaDB [(none)]>
```

You can modify the root password as indicated in Step 3 now that you have access to the database server.

**Setting Up MySQL to Launch Without Grant Tables**

You must modify the systemd settings of MySQL to allow additional command-line parameters to be passed to the server during starting if you want to start the server without its grant tables.

Run the following command to accomplish this:

```
#sudo systemctl edit mysql
```

This command opens MySQL's service overrides in the VI editor. These modify MySQL service defaults. Add the following to this empty file:

```
[Service]  
ExecStart=  
ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/run/mysqld/mysqld.pid --skip-grant-tables --skip-networking
```

The first ExecStart statement removes the default setting, while the second one gives systemd a new startup command with parameters to stop loading networking and grant tables.

CTRL-x will close the file, Y will save your changes, and ENTER will confirm the file name.

To apply these modifications, reload the systemd configuration:

```
#sudo systemctl daemon-reload
```

start the MySQL server:

```
#sudo systemctl start mysql
```

The database server will launch even though the programme doesn't produce any output. The grant tables and networking will not be enabled.

As the root user, connect to the database:

```
#sudo mysql -u root
```

see a database shell prompt:

```
MySQL prompt  
Type 'help;' or 'h' for help. Type 'c' to clear the current input statement.

mysql>
```

You can modify the root password now that you have access to the server.

## Step 3 — Changing the Root Password

The grant tables are not loaded, and networking support is not enabled, thus the database server is now operating in a restricted state. This enables password-free access to the server but prevents you from running commands that change data. Now that you have access to the server, you must load the grant tables in order to reset the root password.

Give the FLUSH PRIVILEGES command to the database server to instruct it to reload the grant tables:

```
mysql> #FLUSH PRIVILEGES;
```

Now you can modify the root password. Whether you're using MariaDB or MySQL will affect the approach you use.

**Password-changing MariaDB**

Execute the following command to change the root account's password if you're using MariaDB, making sure to replace new password with a secure new one that you'll remember:

```
mariadb> #UPDATE mysql.user SET password = PASSWORD('new_password') WHERE user = 'root';
```

This output will appear to show that the password has changed:

```
Output  
Query OK, 1 row affected (0.00 sec)
```

Execute the following two commands to ensure that MariaDB will use its default authentication mechanism for the new password you supplied to the root account. MariaDB permits the use of custom authentication techniques.

```
mariadb> #UPDATE mysql.user SET authentication_string = '' WHERE user = 'root';
```  
```
mariadb> #UPDATE mysql.user SET plugin = '' WHERE user = 'root';
```

Whenever you run a statement, you'll see the following results:

```
Output  
Query OK, 0 rows affected (0.01 sec)
```

The password has now been updated. In order to restart the database server in regular mode, type quit to close the MariaDB console and move on to Step 4.

**Changing the MySQL Password**

Execute the following command for MySQL to update the root user's password, substituting new password with a reliable password you can remember:

```
mysql> #UPDATE mysql.user SET authentication_string = PASSWORD('new_password') WHERE user = 'root';
```

The following output will show that the password change was successful:

```
Output  
Query OK, 1 row affected (0.00 sec)
```

Execute the following command to instruct MySQL to utilise its default authentication mechanism in order to authenticate the root user with the new password:

```
mysql> #UPDATE mysql.user SET plugin = 'mysql_native_password' WHERE user = 'root';
```

You should see output that is comparable to the earlier command:

```
Output  
Query OK, 1 row affected (0.00 sec)
```

The password has been updated. Enter the word exit to close the MySQL console.

Restarting the database in default operating mode will be helpful.

Bringing Back Your Database Server's Settings to Their Defaults

Reverting the changes you made will enable networking and load the grant tables, allowing you to restart the database server in normal mode. Again, the approach you choose will depend on whether you utilised MySQL or MariaDB.

Unset the MYSQLD OPTS environment variable for MariaDB that you previously set:

```
#sudo systemctl unset-environment MYSQLD_OPTS
```

restart the service using systemctl:

```
#sudo systemctl restart mariadb
```

Remove MySQL's modified systemd configuration:

```
#sudo systemctl revert mysql
```

You can expect to see results along these lines:

```
Output  
Removed /etc/systemd/system/mysql.service.d/override.conf.  
Removed /etc/systemd/system/mysql.service.d.
```

To effect the changes, reload the systemd configuration after that:

```
#sudo systemctl daemon-reload
```

restart the service:

```
#sudo systemctl restart mysql
```

Now that the database has been restarted, everything is as it should be. By using a password to log in as the root user, you may verify that the new password is functional.

```
#mysql -u root -p
```

A password prompt will appear. You will successfully access the database prompt after entering your new password.
