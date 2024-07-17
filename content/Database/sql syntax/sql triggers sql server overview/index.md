---
slug: sql triggers sql server overview
description: This guide explains SQL trigger statements, special database objects, and how to enforce referential integrity for primary/foreign key relationships with them.
keywords:
  - referential integrity
  - primary foreign keys
  - special database objects
  - trigger statements
published: 2024-07-12
modified_by:
  name: Utho
title_meta: An Introduction to SQL Triggers in SQL Server
title: "SQL Triggers in SQL Server: An Overview"
authors: ["Pawan Kumar"]
---

## What are Triggers?

*SQL triggers* are a special type of stored procedure that are executed when a data change takes place on a table. When they are declared, triggers are associated with a specific table and with a SQL data modification operation such as `INSERT`, `UPDATE`, or `DELETE`. Triggers can be implemented for different use cases, including:

- logging

- data validation

- calculating derived data

- enforcing referential integrity

For example, the basic syntax for creating a trigger that runs after a `DELETE` operation on a table is as follows:

```file
CREATE TRIGGER AfterTriggerName
ON TableName
AFTER DELETE
AS
BEGIN
    /* Series SQL code statements */
END;
```

This guide demonstrates how to utilize triggers in SQL Server. While there are syntax variations for MySQL and other database systems when creating triggers, the underlying concepts remain similar. For MySQL triggers, refer to the MySQL reference manual.

## In this Guide

Before delving into the practical aspects of using triggers in SQL Server, this guide first covers fundamental database concepts essential for understanding triggers:

- Primary and foreign keys are explained, illustrating how they establish relationships between tables through an example database schema.

- Referential integrity is defined, highlighting why triggers are sometimes employed to maintain referential integrity.

Once these foundational concepts are established, the guide proceeds to explore the operational aspects of triggers:

- It outlines the types of triggers.

- It explains the purpose and usage of the special database objects INSERTED and DELETED.

- The CREATE TRIGGER syntax is introduced.

- Examples demonstrating how to implement an INSTEAD OF trigger and an AFTER trigger are provided.

## Primary and Foreign Keys

To understand the examples of triggers later in this guide, it is important to understand the distinction between primary and foreign keys:

- In a relational database, a *primary key* is a table column that uniquely identifies each record in a table. Primary keys must contain unique values. They cannot contain NULL values. A table cannot have more than one primary key.

- A *foreign key* is a column that associates a record in a table with another record in a different table. The value of the foreign key matches the value of the primary key of the associated record. Foreign keys act as a cross-reference between tables.

## Primary/Foreign Keys Example

Consider a database that consists of `Customer`, `Order`, and `OrderItem` tables. The primary keys in the table schemas are denoted with `PK`, and the foreign keys are denoted with `FK`:

| Customer        | Order            | OrderItem               |
|-----------------|------------------|-------------------------|
| CustomerId (PK) | OrderId (PK)     | OrderItemId (PK)        |
| LastName        | CustomerId (FK)  | OrderItemDescription    |
| FirstName       | OrderItemId (FK) |

- In this schema, a Customer may have multiple Orders associated with them. The `CustomerId` foreign key of an Order associates it with a record in the Customer table. An Order can only be associated with a single Customer.

- Similarly, each Order is associated with a single OrderItem, via the `OrderItemId` foreign key. An OrderItem can be appear in multiple Orders.

The SQL Server syntax for creating these tables is as follows:

```file
CREATE TABLE Customer (
    CustomerId INT NOT NULL PRIMARY KEY,
    LastName   VARCHAR(50) NOT NULL,
    FirstName  VARCHAR(30) NOT NULL
);

CREATE TABLE Order (
    OrderId INT NOT NULL PRIMARY KEY,
    CustomerId INT FOREIGN KEY REFERENCES Customer(CustomerId),
    OrderItemId NOT NULL FOREIGN KEY REFERENCES OrderItem(OrderItemId)
);

CREATE TABLE OrderItem (
    OrderItemId INT NOT NULL PRIMARY KEY,
    OrderItemDescription VARCHAR(255)
);
```

The primary key of each table ensures uniqueness for each record within the table. However, this schema does not guarantee uniqueness for values in other columns. For instance, there could be multiple OrderItems with the same OrderItemDescription. If uniqueness for these values is required, a trigger can enforce this condition. The guide later demonstrates how to achieve this in the INSTEAD OF Trigger Example section.

## Referential Integrity


Triggers can also be employed to enforce referential integrity within a database. An example of this application is detailed in the AFTER Trigger Example section later in this guide.

Referential integrity ensures the consistency of primary and foreign key relationships between tables. For instance:

1. In the Primary/Foreign Key Example section, a Customer may be linked to one or multiple Orders.

2.  For each of a Customer's Orders, the `CustomerId` foreign key of the Order references the `CustomerId` primary key of the Customer.

3.  If a Customer is deleted, then the `CustomerId` foreign key for those Orders no longer references a record in the database. In this circumstance, referential integrity is violated.

For SQL Server databases, referential integrity can be ensured by setting a *constraint*. A constraint tells the database what to do when an update or delete operation would violate referential integrity. There are four possible constraints that can be set:

- `NO ACTION`: The database raises an error and does not complete the delete or update operation. This is the default constraint for SQL Server.

- `CASCADE`: When a record is deleted, records in another table that reference it via a foreign key are also deleted. If there are any records in a third table reference those cascade-deleted records in the second table, then the cascade is also propagated to that third table and those records are deleted. This cascade chain can continue in this manner.

- `SET NULL`: When a record is deleted, the foreign key of any other records that reference it is set to `NULL`.

- `SET DEFAULT`: When a record is deleted, the foreign key of any other records that reference it is set to the default value for the column.

Although constraints can be used to ensure referential integrity, it is sometimes useful to use a trigger to maintain integrity instead. In particular, a trigger can execute statements that work around limitations of the constraints listed above. For example:

- The `CASCADE` constraint is limited to cascading changes to a single referencing table.

- In other words, if there are two child tables that both directly reference the same parent table with a foreign key, then `CASCADE` cannot propagate changes to both children.

- In this scenario, a trigger can be used instead to update or delete records in the child tables when the parent table is changed.

## Types of Triggers

Two types of triggers are available for SQL Server:

-   `INSTEAD OF` trigger:

    The `INSTEAD OF` trigger allows you to bypass `INSERT`, `UPDATE`, or `DELETE` Data Manipulation Language (DML) statements and execute other statements instead. An `INSTEAD OF` trigger always overrides the triggering action. One `INSTEAD OF` trigger can be defined per `INSERT`, `UPDATE`, or `DELETE` action for a given table.

    {{< note >}}
    MySQL does not support an INSTEAD OF trigger. Instead, the BEFORE trigger can be used to achieve similar functionality, albeit with some differences in logic execution for MySQL databases.
    {{< /note >}}

-   `AFTER` trigger:

    The `AFTER` trigger is fired after the execution of a DML action. An `AFTER` trigger is only run if the action that triggered it succeeds. `AFTER` triggers cannot be defined on database Views. One or more `AFTER` triggers per `INSERT`, `UPDATE`, or `DELETE` action can be defined on a table, but having more than one can increase your database code complexity.

## Special Database Objects Associated With Triggers

Triggers use two special database objects, `INSERTED` and `DELETED`, to access rows affected by database changes. These database objects can be referenced as tables within the scope of a trigger's code. The `INSERTED` and `DELETED` objects have the same columns as the affected table.

The `INSERTED` table contains all the new values from the action that caused the trigger to run. The `DELETED` table contains old, removed values from the action. The `INSERTED` and `DELETED` tables are available for different triggers as follows:

- Triggers for `INSERT` actions: The `INSERTED` table determines which rows were added to the affected table.

- Triggers for `DELETE` actions: The `DELETED` table determines which rows were removed from the affected table.

- Triggers for `UPDATE` actions: The `INSERTED` table is used to view the new or updated values of the affected table. The `DELETED` table is used to view the values prior to the `UPDATE` action.

## Create Trigger Statements

The basic SQL Server syntax for creating an `AFTER` trigger is as follows:

```file
CREATE TRIGGER <AfterTriggerName>
ON <TableName>
AFTER {[INSERT],[UPDATE],[DELETE]}
/* Either INSERT, UPDATE, or DELETE specified */
AS
BEGIN
    /* Series SQL code statements */
END;
```

The basic SQL Server syntax for creating an `INSTEAD OF` trigger is as follows:

```file
CREATE TRIGGER <InsteadOfTriggerName>
ON <TableName>
INSTEAD OF {[INSERT],[UPDATE],[DELETE]}
/* Either INSERT, UPDATE, or DELETE specified */
AS
BEGIN
    /* Series SQL code statements */
END;
```

## AFTER Trigger Example

This example demonstrates how to enforce referential integrity for the tables described in the Primary/Foreign Keys Example section. Specifically, the following trigger code deletes a Customer's Orders whenever a Customer record is deleted. This trigger activates when one or more records are deleted from the Customer table:

```file
CREATE TRIGGER AfterCustomerDeleteTrigger
ON             Customer
AFTER DELETE
AS
BEGIN
    DELETE FROM   Order
    WHERE DELETED.CustomerId = Order.CustomerId
END;
```

- The name of the new trigger is defined on line 1 as `AfterCustomerDeleteTrigger`.

- Lines 2 and 3 associate the trigger with the `Customer` table and with the `AFTER DELETE` operation.

- Lines 6 and 7 delete the associated Order records. The special database object `DELETED` is used to obtain the `customerId` of the deleted Customer.

## INSTEAD OF Trigger Example

This example demonstrates how to enforce validation for new records created in the tables outlined in the Primary/Foreign Keys Example section. Specifically, the following trigger code ensures that each record in the OrderItem table has a unique OrderItemDescription value. This trigger is activated when one or more records are inserted into the OrderItem table:

```file
CREATE TRIGGER InsteadOfOrderItemInsertTrigger
ON OrderItem
INSTEAD OF INSERT
AS
BEGIN
    DECLARE @OrderItemId INT,
            @OrderItemDescription VARCHAR(255)

    SELECT @OrderItemId = INSERTED.OrderItemId,
            @OrderItemDescription = INSERTED.OrderItemDescription
    FROM INSERTED

    IF (EXISTS(SELECT  OrderItemDescription
        FROM OrderItem
        WHERE OrderItemDescription = @OrderItemDescription))
    BEGIN
        ROLLBACK
    END
    ELSE
    BEGIN
        INSERT INTO OrderItem
        VALUES (@OrderItemId, @OrderItemDescription)
    END
END;
```

- The name of the new trigger is defined on line 1 as `InsteadOfOrderItemInsertTrigger`.

- Lines 2 and 3 associate the trigger with the `OrderItem` table and with the `INSTEAD OF INSERT` operation. Whenever an `INSERT` statement would be executed on the `OrderItem` table, this trigger is executed instead. The normal `INSERT` action is *not* executed.

- Lines 6-11 retrieve the new `OrderItemId` and `OrderItemDescription` values that would have been inserted by the `INSERT` action. The special database object `INSERTED` is used to obtain these values.

- Lines 13-15 check if the new `OrderItemDescription` value already exists for a record in the `OrderItem` table.

- Lines 16-18 prevent a change to the database if the new `OrderItemDescription` already exists.

- Lines 19-23 insert the new `OrderItemId` and `OrderItemDescription` into the `OrderItem` table if the `OrderItemDescription` does not exist in the table yet. These lines are needed because the original `INSERT` action that caused this `INSTEAD OF` trigger to run is not actually executed.

## Conclusion

Triggers in SQL Server are code segments that execute either instead of or after an `INSERT`, `UPDATE`, or `DELETE` operation on a table. Triggers are linked to a specific table upon their definition. Within a trigger's scope, special database objects like `INSERTED` and `DELETED` provide access to the newly inserted or deleted data. Triggers are versatile and can be used for various purposes such as logging, data validation, computing derived data, and maintaining referential integrity.






