## Database Normalization

When creating a database, it is really important to think about how data will be stored. This is known as **normalization**, and it is a huge part of most SQL classes. If you are in charge of setting up a new database, it is important to have a thorough understanding of database normalization.

There are essentially three ideas that are aimed at database normalization:

* Are the tables storing logical groupings of the data?
* Can I make changes in a single location, rather than in many tables for the same information?
* Can I access and manipulate data quickly and efficiently?

This is discussed in detail [here](https://www.itprotoday.com/sql-server/sql-design-why-you-need-database-normalization).

## Introduction to JOINs

```sql
-- Inner Join --
SELECT orders.*,
       accounts.*
FROM orders 
JOIN accounts
ON orders.account_id = accounts.id;
```

The **SELECT** clause indicates which column(s) of data you'd like to see in the output (For Example, `orders.*` gives us all the columns in the orders table in the output). The **FROM** clause indicates the first table from which we're pulling data, and the **JOIN** indicates the second table. The **ON** clause specifies the column on which you'd like to merge the two tables together. 

```sql
-- pull individual elements from either the orders or accounts table --
SELECT accounts.name, orders.occurred_at
FROM orders
JOIN accounts
ON orders.account_id = accounts.id;
```

The below query pulls all the columns from both the accounts and orders table.

```sql
SELECT *
FROM orders
JOIN accounts
ON orders.account_id = accounts.id;
```

## Entity Relationship Diagrams

An **entity-relationship diagram (ERD)** is a common way to view data in a database. It is also a key element to understanding how we can pull data from multiple tables.

It will be beneficial to have an idea of what the ERD looks like for Parch & Posey.

![Entity Relationship Diagram](img/erd.png)

## Keys

### Primary Key (PK)

A **primary key** is a unique column in a particular table. This is the first column in each of our tables. Here, those columns are all called id, but that doesn't necessarily have to be the name. **It is common that the primary key is the first column in our tables in most databases**.

### Foreign Key (FK)

A **foreign key** is a column in one table that is a primary key in a different table. We can see in the Parch & Posey ERD that the foreign keys are:

1. region_id
2. account_id
3. sales_rep_id

Each of these is linked to the primary key of another table. An example is shown in the image below:

![View of Primary Key & Foreign Key Relationship](img/primary-foreign-key-relationship.png)

## Primary - Foreign Key Link

In the above image you can see that:

1. The **region_id** is the foreign key.
2. The region_id is **linked** to id - this is the primary-foreign key link that connects these two tables.
3. The crow's foot shows that the **FK** can actually appear in many rows in the **sales_reps** table.
4. While the single line is telling us that the **PK** shows that id appears only once per row in this table.

![Quiz ERD](img/quiz-erd.png)

## JOIN More than Two Tables

Look at the three tables below.

![ERD for Web Events, Accounts & Orders Tables](img/join-3-tables.png)

The code below pulls all of the data from all of the joined tables.

```sql
-- Alternatively you could also use the following SELECT statement to query specific columns
-- SELECT web_events.channel, accounts.name, orders.total --
SELECT *
FROM web_events
JOIN accounts
ON web_events.account_id = accounts.id
JOIN orders
ON accounts.id = orders.account_id
```

our **JOIN** holds a table, and **ON** is a link for our **PK** to equal the **FK**.

## Alias

We can simply write our alias directly after the column name (in the **SELECT**) or table name (in the **FROM** or **JOIN**) by writing the alias directly following the column or table we would like to alias. This will allow you to create clear column names even if calculations are used to create the column, and you can be more efficient with your code by aliasing table names.

Before, you saw something like:

Examples:

```sql
SELECT o.*, a.*
FROM orders o
JOIN accounts a
ON o.account_id = a.id
```

```sql
SELECT col1 + col2 AS total, col3
```

Frequently, you might also see these statements without the **AS** statement and they would still produce the **exact same results**:

```sql
SELECT col1 + col2 total, col3
```

## Aliases for Columns in Resulting Table

While aliasing tables is the most common use case. It can also be used to alias the columns selected to have the resulting table reflect a more readable name.

Example:

```sql
Select t1.column1 aliasname, t2.column2 aliasname2
FROM tablename AS t1
JOIN tablename2 AS t2
```

> If you have two or more columns in your SELECT that have the same name after the table name such as accounts.name and sales_reps.name you will need to alias them. Otherwise it will only show one of the columns. You can alias them like accounts.name AS AcountName, sales_rep.name AS SalesRepName

## QUESTIONS

1. Provide a table for all **web_events** associated with the account **name** of `Walmart`. There should be three columns. Be sure to include the `primary_poc`, time of the event, and the `channel` for each event. Additionally, you might choose to add a fourth column to assure only `Walmart` events were chosen.

```sql
SELECT we.occurred_at, we.channel, ac.primary_poc, ac.name
FROM web_events we
JOIN accounts ac
ON we.account_id = ac.id
WHERE ac.name = 'Walmart'
```

2. Provide a table that provides the **region** for each **sales_rep** along with their associated **accounts**. Your final table should include three columns: the region **name**, the sales rep **name**, and the account **name**. Sort the accounts alphabetically (A-Z) according to the account name.

```sql
SELECT re.name AS region_name, sr.name AS sales_rep_name, ac.name AS acc_name
FROM sales_reps sr
JOIN region re 
ON sr.region_id = re.id
JOIN accounts ac
ON ac.sales_rep_id = sr.id
ORDER BY ac.name
```

3. Provide the **name** for each region for every **order**, as well as the account **name** and the **unit price** they paid (total_amt_usd/total) for the order. Your final table should have 3 columns: **region name**, **account name**, and **unit price**. A few accounts have 0 for **total**, so I divided by (total + 0.01) to assure not dividing by zero.

```sql
SELECT re.name AS region_name, ac.name AS acc_name, od.total_amt_usd/(od.total+0.01) AS unit_price
FROM sales_reps sr
JOIN region re 
ON sr.region_id = re.id
JOIN accounts ac
ON ac.sales_rep_id = sr.id
JOIN orders od
ON od.account_id = ac.id
```

## OTHER TYPE OF JOINS

![outer-joins](img/outer-joins.png)

Notice each of these new **JOIN** statements pulls all the same rows as an **INNER JOIN**, which you saw by just using **JOIN**, but they also potentially pull some additional rows.

If there is not matching information in the **JOIN**ed table, then you will have columns with empty cells. These empty cells introduce a new data type called **NULL**.

**LEFT JOIN**
```sql
SELECT a.id, a.name, o.total
FROM orders o
LEFT JOIN accounts a
ON o.account_id = a.id
```

**RIGHT JOIN**
```sql
SELECT a.id, a.name, o.total
FROM orders o
RIGHT JOIN accounts a
ON o.a
```

### Quick Note

You might see the SQL syntax of

`LEFT OUTER JOIN`

OR

`RIGHT OUTER JOIN`

These are the exact same commands as the **LEFT JOIN** and **RIGHT JOIN** 

### OUTER JOINS

The last type of join is a full outer join. This will return the inner join result set, as well as any unmatched rows from either of the two tables being joined.

Again this returns rows that **do not match** one another from the two tables. The use cases for a full outer join are **very rare**.

Similar to the above, you might see the language **FULL OUTER JOIN**, which is the same as **OUTER JOIN**.


## JOINs and Filtering

```sql
SELECT orders.*, accounts.*
FROM orders
LEFT JOIN accounts
ON orders.account_id = accounts.id 
AND accounts.sales_rep_id = 321500
```

A simple rule to remember is that, when the database executes this query, it executes the join and everything in the **ON** clause first. Think of this as building the new result set. 

## QUESTIONS

1. Provide a table that provides the **region** for each **sales_rep** along with their associated **accounts**. This time only for the `Midwest` region. Your final table should include three columns: the region **name**, the sales rep **name**, and the account **name**. Sort the accounts alphabetically (A-Z) according to the account name.

```sql
SELECT re.name AS region_name, sr.name AS sales_rep_name, ac.name AS acc_name
FROM sales_reps sr
JOIN region re
ON sr.region_id = re.id
JOIN accounts ac
ON ac.sales_rep_id = sr.id
AND re.name = 'Midwest'
ORDER BY ac.name
```

2. Provide a table that provides the **region** for each **sales_rep** along with their associated **accounts**. This time only for accounts where the sales rep has a first name starting with `S` and in the `Midwest` region. Your final table should include three columns: the region **name**, the sales rep **name**, and the account **name**. Sort the accounts alphabetically (A-Z) according to the account name.

```sql
SELECT re.name AS region_name, sr.name AS sales_rep_name, ac.name AS acc_name
FROM sales_reps sr
JOIN region re
ON sr.region_id = re.id
JOIN accounts ac
ON ac.sales_rep_id = sr.id
AND sr.name LIKE 'S%'
AND re.name = 'Midwest'
ORDER BY ac.name
```

3. Provide a table that provides the **region** for each **sales_rep** along with their associated **accounts**. This time only for accounts where the sales rep has a **last** name starting with `K` and in the `Midwest` region. Your final table should include three columns: the region **name**, the sales rep **name**, and the account **name**. Sort the accounts alphabetically (A-Z) according to the account name.

```sql
SELECT re.name AS region_name, sr.name AS sales_rep_name, ac.name AS acc_name
FROM sales_reps sr
JOIN region re
ON sr.region_id = re.id
JOIN accounts ac
ON ac.sales_rep_id = sr.id
AND sr.name LIKE '% K%'
AND re.name = 'Midwest'
ORDER BY ac.name
```

4. Provide the **name** for each region for every **order**, as well as the account **name** and the **unit price** they paid (total_amt_usd/total) for the order. However, you should only provide the results if the **standard order quantity** exceeds `100`. Your final table should have 3 columns: **region name**, **account name**, and **unit price**. In order to avoid a division by zero error, adding .01 to the denominator here is helpful total_amt_usd/(total+0.01).

```sql
SELECT re.name AS region_name, ac.name AS acc_name, od.total_amt_usd/(od.total + 0.01) AS unit_price
FROM sales_reps sr
JOIN region re
ON sr.region_id = re.id
JOIN accounts ac
ON ac.sales_rep_id = sr.id
JOIN orders od
ON od.account_id = ac.id
AND od.standard_qty > 100
```

5. Provide the **name** for each region for every order, as well as the account name and the unit price they paid (total_amt_usd/total) for the order. However, you should only provide the results if the standard order quantity exceeds 100 and the poster order quantity exceeds 50. Your final table should have 3 columns: region name, account name, and unit price. Sort for the smallest unit price first. In order to avoid a division by zero error, adding .01 to the denominator here is helpful (total_amt_usd/(total+0.01).

```sql
SELECT re.name AS region_name, ac.name AS acc_name, od.total_amt_usd/(od.total + 0.01) AS unit_price
FROM sales_reps sr
JOIN region re
ON sr.region_id = re.id
JOIN accounts ac
ON ac.sales_rep_id = sr.id
JOIN orders od
ON od.account_id = ac.id
AND od.standard_qty > 100
AND od.poster_qty > 50
ORDER BY unit_price
```

6. Provide the name for each region for every **order**, as well as the account **name** and the **unit price** they paid (total_amt_usd/total) for the order. However, you should only provide the results if the **standard order quantity** exceeds `100` and the **poster order quantity** exceeds `50`. Your final table should have 3 columns: **region name**, **account name**, and **unit price**. Sort for the largest **unit price** first. In order to avoid a division by zero error, adding .01 to the denominator here is helpful (total_amt_usd/(total+0.01).

```sql
SELECT re.name AS region_name, ac.name AS acc_name, od.total_amt_usd/(od.total + 0.01) AS unit_price
FROM sales_reps sr
JOIN region re
ON sr.region_id = re.id
JOIN accounts ac
ON ac.sales_rep_id = sr.id
JOIN orders od
ON od.account_id = ac.id
AND od.standard_qty > 100
AND od.poster_qty > 50
ORDER BY unit_price DESC
```

7. What are the different **channels** used by **account id** `1001`? Your final table should have only 2 columns: **account name** and the different **channels**. You can try **SELECT DISTINCT** to narrow down the results to only the unique values.

```sql
SELECT DISTINCT we.channel, ac.name AS acc_name
FROM web_events we
JOIN accounts ac
ON we.account_id = ac.id
AND ac.id = '1001'
```

8. Find all the orders that occurred in `2015`. Your final table should have 4 columns: **occurred_at**, **account name**, **order total**, and order **total_amt_usd**.

```sql
SELECT o.occurred_at, a.name, o.total, o.total_amt_usd
FROM accounts a
JOIN orders o
ON o.account_id = a.id
WHERE o.occurred_at BETWEEN '01-01-2015' AND '01-01-2016'
ORDER BY o.occurred_at DESC;
```

You will notice that using **BETWEEN** is tricky for dates! While **BETWEEN** is generally inclusive of endpoints, it assumes the time is at 00:00:00 (i.e. midnight) for dates. This is the reason why we set the right-side endpoint of the period at '01-01-2016'.

## RECAP

There are a few more advanced JOINs that we did not cover here, and they are used in very specific use cases. [UNION and UNION ALL](https://www.w3schools.com/sql/sql_union.asp), [CROSS JOIN](http://www.w3resource.com/sql/joins/cross-join.php), and the tricky [SELF JOIN](https://www.w3schools.com/sql/sql_join_self.asp). These are more advanced than this course will cover, but it is useful to be aware that they exist, as they are useful in special cases.

## BETWEEN Operator

Sometimes we can make a cleaner statement using **BETWEEN** than we can use **AND**. Particularly this is true when we are using the same column for different parts of our **AND** statement. In the previous video, we probably should have used **BETWEEN**.

Instead of writing :

`WHERE column >= 6 AND column <= 10``

we can instead write, equivalently:

`WHERE column BETWEEN 6 AND 10``

## SQL Aggregations

- Deal with NULL values
- Create aggregations in your SQL Queries including
    * COUNT
    * SUM
    * MIN & MAX
    * AVG
    * GROUP BY
    * DISTINCT
    * HAVING
- Create DATE functions
- Implement CASE statements

