---
slug: sql security
description: 'SQL database security relies on user management, permissions, groups, and roles. This guide discusses each of these aspects of SQL database security with examples.'
keywords: ['groups', 'roles', 'permissions', 'grant permission', 'revoke permission']
tags: ['MySQL']
published: 2024-07-12
modified_by:
  name: Utho
title: "SQL Database Security: User Management"
title_meta: "SQL Security and User Management"
authors: ["Pawan Kumar"]
---

User management and permissions are crucial for SQL database security. Typically, SQL security schemes include one or more users, their authentication, and their permissions. When a user attempts to perform an operation on a SQL object—such as a table, index, or stored procedure—the database engine verifies their permissions. The primary goal of assigning SQL roles and permissions is to grant users access only to the resources necessary for their job. In this guide, you will learn how to create and assign roles and permissions to users of relational database systems.

## Users and Groups

To grant access rights and permissions, a relational database management system (RDBMS) requires user identities. These rights and permissions can be assigned to individual users or groups of users. If multiple users share similar access requirements and restrictions, you can define a group and add those users as members. This way, the authentication and validation process for a given SQL object applies to the group instead of each user individually, assuming no specific restrictions have been set for individual users. If both a user and their group have access restrictions on a SQL object, the database enforces the most restrictive access rights from either the user or the group.

## Roles

Users of relational database systems are typically assigned *roles*. Different users might need to perform different tasks on the same database. For instance, one user might handle data entry, another might act as the database administrator, and an end-user may only need to retrieve data. Typically, users with the same role within an organization require similar database access. Each role can have its own data access permission levels. Once a role is created and the appropriate permissions are applied, individual users can be added to that role. All users assigned to a particular role inherit its permissions.

## Permissions

There are two different types of permissions that can be assigned to roles, users, and groups: *statement permissions* and *object permissions*. Statement permissions grant access to execute specific statements against a database. For example, a user could be granted access to create a stored procedure, but not be granted the right to create tables. Object permissions, on the other hand, grant the user the right to access a database object such as a table, a view, or to execute a stored procedure.

## Implementation of Users, Groups, Roles, and Permissions

When it comes to the management of users, groups, roles, and permissions, the concepts stated in the previous sections are quite uniform across SQL-based database management systems. What may differ are the names of commands and the syntax used by different SQL database implementations.

{{< note respectIndent=false >}}
The examples below use Microsoft SQL Server syntax. All commands should be executed from the command line. The examples also assume that all server security hardening has already been implemented.
{{< /note >}}

To demonstrate SQL security principles, this guide uses an example database that is used by a school. The school's database has tables for students and courses taken by each student. The definition of the `Student` table contains columns for the student's `SSNumber`, `Firstname`, and `Lastname`, and the definition of the `CourseTaken` table contains columns for `SSNumber`, `CourseId`, `NumericGrade`, and `YearTaken`.

The example further assumes that four employees in the school administer the school database. Their respective roles are defined as follows:

| Name | Database Role |
|:-:|:-:|
| Tom | Database Administrator |
| John | Database Data Entry |
| Mary | Database Query and Reports |
| Joan | Database Query and Reports |

In the example below, assume that Tom, the database administrator (DBA), has created the school database via the `CREATE DATABASE` command:

    CREATE DATABASE School;

Next, Tom creates database user login definitions for all four employees (including themselves) via the `CREATE USER` command:

    Use School;
    CREATE USER Tom WITH PASSWORD = 'Tompassword';
    CREATE USER John WITH PASSWORD = 'Johnpassword';
    CREATE USER Mary WITH PASSWORD = 'Marypassword';
    CREATE USER Joan WITH PASSWORD = 'Joanpassword';

    CREATE USER Tom IDENTIFIED BY 'TomPassword';

After creating user login definitions, Tom creates generic roles that will later be assigned to each employee, by using the `CREATE ROLE` command:

    USE School;
    CREATE ROLE DBAdmin;
    CREATE ROLE DataEntry;
    CREATE ROLE QueryReports;

Now that the roles exist, Tom assigns the roles to the appropriate users with the `ALTER ROLE` command as follows:

    USE School
    ALTER ROLE DBAdmin ADD MEMBER Tom;
    ALTER ROLE DataEntry ADD MEMBER John;
    ALTER ROLE QueryReports ADD MEMBER Mary;
    ALTER ROLE QueryReports ADD MEMBER Joan;

The workflow demonstrated in this section reflects the user management steps a DBA might need to take when configuring a newly   created database.

## Granting Permissions

The `GRANT` statement is used to assign permissions to a user or to a role. You can also use the `GRANT` statement to assign specific statement permissions to a user or to a role. Some of the statement permissions that can be granted are: `CREATE DATABASE`, `CREATE DEFAULT`, `CREATE PROCEDURE`, `CREATE RULE`, `CREATE TABLE`, `CREATE VIEW`, `DUMP DATABASE`, and `DUMP TRANSACTION`.

For example, to grant the `CREATE PROCEDURE` statement permission to a user or a role, use the following command:

    GRANT CREATE PROCEDURE TO <User or Role>;

Continuing along with this guide's school database example, you can grant various permissions to the database roles you created in the previous section. Tom first grants required privileges to the `DBAdmin Role` (Tom's role), via the `GRANT` command, as follows:

    USE School;
    GRANT CREATE DATABASE TO DBAdmin;
    GRANT CREATE RULE TO DBAdmin;
    GRANT CREATE TABLE TO DBAdmin;
    GRANT CREATE VIEW TO DBAdmin;
    GRANT DUMP DATABASE TO DBAdmin;
    GRANT DUMP TRANSACTION TO DBAdmin;

Now, Tom can create the two tables in the school's database as follows:

    USE School;
    CREATE TABLE Student (
      SSNumber CHAR(9) NOT NULL,
      LastName VARCHAR(30) NOT NULL,
      FirstName VARCHAR(20) NOT NULL
    );

    CREATE TABLE CourseTaken (
      SSNumber CHAR(9) NOT NULL,
      CourseId CHAR(6) NOT NULL,
      NumericGrade TINYINT NOT NULL,
      YearTaken SMALLINT NOT NULL
    );

Tom grants necessary database entry permissions (`INSERT`, `UPDATE`, `DELETE`) on both database tables, to employee John  (`DBEntry` role), as follows:

    USE School;
    GRANT INSERT, UPDATE, DELETE ON Student TO DBEntry;
    GRANT INSERT, UPDATE, DELETE ON CourseTaken TO DBEntry;

{{< note respectIndent=false >}}
After executing the above `GRANT` commands, John is permitted to `INSERT`, `UPDATE`, and `DELETE` data in the two database tables, but is not permitted to read (`SELECT`) from it.
{{< /note >}}

Tom grants necessary database read permission (`SELECT`) on both database tables, to employees Mary and Joan, via the `QueryReports` role, as follows:

    USE School;
    GRANT SELECT ON Student TO QueryReports;
    GRANT SELECT ON CourseTaken TO QueryReports;

{{< note respectIndent=false >}}
After executing the above `GRANT` commands, Mary and Joan can only read the database tables (via the `SELECT` statement), but cannot manipulate the data (via the `INSERT`, `UPDATE`, or `DELETE` statements).
{{< /note >}}

## Revoking Permissions

Revoking permissions is the converse of granting permissions on database objects. You can revoke permissions from a table, view, table-valued function, stored procedure, and many other types of database objects.

Continuing with the school database example, assume that John switches his role at the school from performing data entry to querying reports. Due to this change, John should no longer have the ability to manipulate data (`INSERT`, `UPDATE`, `DELETE`) in the school tables. John should also be granted the ability to read data from the table (via `SELECT`). Tom, the database administrator, needs to execute the following commands to revoke and grant the appropriate permissions to John:

    USE School;
    REVOKE INSERT, UPDATE, DELETE ON Students FROM John;
    REVOKE INSERT, UPDATE, DELETE ON CourseTaken FROM John;
    GRANT SELECT ON Student TO John;
    GRANT SELECT ON CourseTaken TO John;

Alternatively, a simpler approach is to remove John from the `DBEntry` role and add him to the `QueryReports` role:

    USE School;
    ALTER ROLE DBEntry DROP MEMBER John;
    ALTER ROLE QueryReports ADD MEMBER John;

## Conclusion

User management, permissions, and roles are essential to SQL database security. When multiple users require the same database access and permissions, create a new group and add users to that group. To control access based on the tasks users should be allowed to perform, use database roles.

In SQL databases, every action must pass through a validity check to determine if the user has the required permissions to complete the action. The integrity of a SQL database relies on secure and well-designed user management.

Now that you are familiar with SQL user management, you can explore other aspects of the SQL language, such as joins, data types, and grouping and totaling.

