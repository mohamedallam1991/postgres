+++
slug = "crud-sql"
title = "CRUD SQL queries"
description = "Basic sql steps, to get up and running CRUD and batch CRUD"
weight = 3
+++



The following steps will help you get started as quickly as possible, manipulating SQL data.

## Intro

Lets create a customer or two, Assuming you have [installed]({{%relref "installation.md"%}}) and [configured]({{%relref "configuration.md"%}}), Its time to get the hang of CRUD on your fresh database.

So given we need to store customers in our database, we create `customers` table, in which we store informations like:
- First name
- Last name
- Email
- Age

In theory, First Name and Last Name or Email, are a text, in SQL, there is many text types, but to keep it simple we will use `VARCHAR`, think of it as a `string` type, but with a predefined max length we provide, 150 in our case is the maximum number of charachters for a first name or last name. And 50 charachters for the email.

As for age, its obviously a numeric type, we will use integer.

This is what we will do:

1. Create Table
2. With columns/fields in the table (name and data type)
3. create a customer read, update, delete.

## Create Table

Lets first create a table, with the predefined rows, customer which is auto increment id, set by

```SQL
-- CREATE TABLE tableName (
	-- column name followed by column data type or any conditions to the column
--);
CREATE TABLE customers (
	customer_id SERIAL PRIMARY KEY,
	first_name VARCHAR(150),
	last_name VARCHAR(50),
	email VARCHAR(50),
	age INT
);
```

<aside>

{{% alert theme="info" %}} 💡 To check the table `SELECT * FROM customers` it will return an empty table, with column names and types.{{%/alert%}}
</aside>




## Retrieve all data from customers

To retrieve all data of a column

```sql
-- SELECT * FROM tableName
SELECT * FROM customers
```

If you might have noticed, the customer_id is auto incremented and is set Postgres, it will keep auto-incrementing.

## Insert a record

Let’s insert a row of data.

```sql
-- INSERT INTO tableName (column1, column2, column3.....)
-- VALUES (value1, value2, value3.....)

INSERT INTO customers (first_name, last_name, email, age)
VALUES ('Mohamed', 'ALLAM', 'a@b.com', 30);
```

### Quotes in the data

If you have quotes in your insertion, simply double the one you want to insert, This indicates to Postgres to escape the quotes.

```sql
-- To escape quotes we have to double them so, John's, becomes John''s

INSERT INTO customers (first_name, last_name, email, age)
VALUES ('Mohamed''s', 'ALLAM', 'a@b.com', 30);
```

### Insert multiple records

We often want to insert multiple data at once, its easy.

```sql
-- INSERT INTO tableName (column1, column2, ....)
-- VALUES
--('value1','value2')
--('value1','value2')
--('value1','value2')

INSERT INTO customers (first_name, last_name)
VALUES
('Mohamed', 'Allam'),
('Jane','Doe'),
('John','Doe');
```

### Returning

If we want to return all the rows and data, we inserted we run:

```sql
-- INSERT INTO tableName (column1, column2...)
-- VALUES ('value1', 'value2') returning *;
INSERT INTO customers (first_name, last_name)
VALUES
('John','Doe') returning *;
```

If however we want to return just a specific row, we return the name of the column:

```sql
-- INSERT INTO tableName (column1, column2...)
-- VALUES ('value1', 'value2') returning columnName;
INSERT INTO customers (first_name, last_name)
VALUES
('John','Doe') returning customer_id;
```

## Update a record

### Update a row

```sql
-- UPDATE tablename
-- SET columnname = 'new value'
-- WHERE columnname = 'value'

UPDATE customers
SET email = 'yes@aol.com'
WHERE "customer_id" = 3
```

### Update one column and return the rows

```sql
-- UPDATE tablename
-- SET columnname = 'new value'
-- WHERE columnname = 'value'
-- RETURNING *;

UPDATE customers
SET email = 'yes@aol.com'
WHERE "customer_id" = 3
returning *;
```

### Update one column and return by column name

```sql
-- UPDATE tablename
-- SET columnname = 'new value'
-- WHERE columnname = 'value'
-- RETURNING columnName;

UPDATE customers
SET email = 'yes@aol.com'
WHERE "customer_id" = 3
returning customer_id;
```

## Update Multiple columns

```sql
-- UPDATE tablename
-- SET columnname = 'new value', anotherColumn = 'some new value'
-- WHERE columnname = 'value'
UPDATE customers
SET email = 'yes@aol.com', age = 40
WHERE first_name = 'Mohamed'
```

### Update Multiple columns and returning

```sql
-- UPDATE tablename
-- SET columnname = 'new value', anotherColumn = 'some new value'
-- WHERE columnname = 'value'
-- RETURNING *;
UPDATE customers
SET email = 'yes@aol.com', age = 40
WHERE first_name = 'Mohamed' returning *;
```

### Update Multiple columns and returning a column name

```sql
-- UPDATE tablename
-- SET columnname = 'new value', anotherColumn = 'some new value'
-- WHERE columnname = 'value'
-- RETURNING *;
UPDATE customers
SET email = 'yes@aol.com', age = 40
WHERE first_name = 'Mohamed' returning customer_id;
```

## Update all records in a database

If you update with no where clause, you would update all records in a database, so be careful

```sql
-- UPDATE tablename
-- SET columnname = 'new value'
UPDATE customers
SET age = '30'
```

## Delete a record

```sql
DELETE FROM customers
where customer_id = 3
```

## Delete all records

```sql
DELETE FROM customers
```

## UPSERT

So you might have guessed, upsert, is made up of insert and update. This instructs postgres to update row if it already exists, otherwise it will perform an insert.

```sql
-- INSERT INTO tableName (column_list)
-- VALUES (value_list)
-- ON CONFLICT target action;
```

The action is:

```sql
-- ON CONFLICT DO NOTHING
-- ON CONFLICT DO UPDATE SET column_1 = value_1
-- WHERE some_column = value
```

```sql
CREATE TABLE t_tags (
	id SERIAL PRIMARY KEY,
	tag text UNIQUE, --enforce unique case sensitive value
	update_date TIMESTAMP DEFAULT NOW()
);
```

```sql
INSERT INTO t_tags (tag) values
('pen'),
('pencil')
returning *;
```

```sql
INSERT INTO t_tags(tag)
VALUES ('pen')
ON CONFLICT (tag)
DO NOTHING;
```

<aside>
💡 If we do select *, we would see Ids and inserts to our table, then removes it.

</aside>

Mention if we check on the Ids

```sql
INSERT INTO t_tags(tag)
VALUES ('pen')
ON CONFLICT (tag)
DO UPDATE SET
tag = EXCLUDED.tag,
update_date = NOW(); -- just to show we excluded the update we made
```

```sql
INSERT INTO t_tags(tag)
VALUES ('pen')
ON CONFLICT (tag)
DO UPDATE SET
tag = EXCLUDED.tag,
update_date = NOW(); -- just to show we excluded the update we made
```
