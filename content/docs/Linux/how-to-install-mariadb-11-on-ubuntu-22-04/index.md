---
title: "How to install MariaDB 11 on Ubuntu 22.04"
date: "2023-05-03"
---

![How to install MariaDB 11 on Ubuntu 22.04](images/How-to-install-MariaDB-11-on-Ubuntu-22.04-1024x576.png)

## Introduction

In this article, you will learn how to install MariaDB 11 on Ubuntu 22.04.

This is to install MariaDB 11 on Ubuntu 22.04. MariaDB is a database management system that is a fork of [MySQL](https://www.mysql.com/). It is extremely similar to MySQL, which is a database management system. Several different applications, including data warehousing, e-commerce, enterprise-level features, and logging programmes, all make use of the MariaDB database.

MariaDB will let you to fulfil all of your burden in an effective manner; it can function in any cloud database and can function at any scale, whether it be little or huge.

A database is a repository for information that can be easily retrieved and applied in the context in which it is required. When compared to recording information on a piece of paper or in a Word document, storing all of your information in a database allows it to be organized into tables, making it simple to retrieve each individual entry in a manner that is both systematic and accurate.

##### Add MariaDB 11 APT repository

```
# apt update

```

```
# curl -LsS https://downloads.mariadb.com/MariaDB/mariadb\_repo\_setup | bash -s -- --mariadb-server-version=11.0

```

**You need to update the system after adding the repo.**

```
# apt update

```

##### **Install MariaDB 11 on Ubuntu 22.04**

```
# apt install mariadb-server mariadb-client -y

```

**You can use the command to check the version of MariaDB that is currently installed;**

```
# mariadb -V

```

![How to install MariaDB 11 on Ubuntu 22.04](images/image-1027.png)

**Now, using the following command, check the current status of mariadb.**

```
# systemctl status mariadb

```

![How to install MariaDB 11 on Ubuntu 22.04](images/image-1028.png)

##### Run the mariadb-secure-installation script, which assists you in protecting your MariaDB database server.

```
# mysql\_secure\_installation

```

**Enter current password for root (enter for none): Press Enter**

![press enter](images/image-989.png)

**Switch to unix\_socket authentication \[Y/n\] : Press y**

![y](images/image-990.png)

**Change the root password? \[Y/n\] : Press y**

![y](images/image-991.png)

**Remove anonymous users? \[Y/n\] : Press y**

![y](images/image-992.png)

**If you choose you wish to let root login remotely, then press the y key; otherwise, press the n key.**

![n](images/image-993.png)

**Remove test database and access to it? \[Y/n\] Press y**

![How to install MariaDB 11 on Ubuntu 22.04](images/image-995.png)

**Reload privilege tables now? \[Y/n\] : Press y**

![How to install MariaDB 11 on Ubuntu 22.04](images/image-996.png)

**A username and password combination should be required in order to access the MariaDB shell.**

```
# mysql -u root -p

```

![login](images/image-1029.png)

**To exit, run the following command**

```
# exit

```

## Conclusion

Hopefully, now you have learned how to install MariaDB 11 on Ubuntu 22.04.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
