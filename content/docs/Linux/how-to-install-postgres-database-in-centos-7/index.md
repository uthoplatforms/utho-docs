---
title: "HOW TO INSTALL POSTGRES DATABASE IN CENTOS 7"
date: "2022-01-09"
---

![](images/HOW-TO-INSTALL-POSTGRES-DATABASE-IN-CENTOS-7_utho.jpg)

**Introduction:**  
Postgres, is an open-source relational database management system also known as PostgreSQL.It was originally named POSTGRES, referring to its origins as a successor to the Ingres database developed at the University of California, Berkeley

Perquisite: 

1. Server with root privilege
2. packages should be updated  
    
3. SSH should be working  
    

Installation :  
**Step 1:** We can install postgress sql using below command.

```
**# yum install postgresql-server postgresql-contrib**
```

**Step 2:** One we will done with the installation we will initialize the database using below command.

```
**# postgresql-setup initdb** 
```

**Step 3:** We will start and enable the postgress service using below command

```
**## systemctl start postgresql** 
```

```
**## systemctl enable postgresql**
```

**Step 4**: Afterward, we need to setup the database.  
Firstly, we will reset the password of postgres user  using the below command

```
**## passwd postgres**
```

**Step 5:** Once the password has been reset, we will login to the postgres user using the below command.

**```
## su – postgres** 
```

**Step 7:** Now, we will initialize the bash shell for database login using below command.

```
**## su --shell /bin/bash postgres**
```

**Step 8**: again we will move to the postgres user using below command

```
**## su – postgres
```**

**step 9:** Now we will login the postgres database

```
**## psql postgres**
```

**step 9:** We can create a test database using the below command in postgres

```
**## createdb testDB** 
```

Thank You :)
