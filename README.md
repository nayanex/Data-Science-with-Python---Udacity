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

`WHERE column >= 6 AND column <= 10`

we can instead write, equivalently:

`WHERE column BETWEEN 6 AND 10`

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

## NULLs and Aggregation

**NULLs** are a datatype that specifies where no data exists in SQL. 

You need to write `IS NULL` instead of `= NULL`, that's because `NULL` is not a value, but a **property** of the data.

## NULLs - Expert Tip

There are two common ways in which you are likely to encounter **NULL**s:

* **NULL**s frequently occur when performing a **LEFT** or **RIGHT JOIN**. When some rows in the left table of a left join are not matched with rows in the right table, those rows will contain some **NULL** values in the result set.
* **NULL**s can also occur from simply missing data in our database.

## First Aggregation - COUNT

### COUNT the Number of Rows in a Table

Finding all the rows in the b table.

```sql
SELECT COUNT(*)
FROM accounts;
```

But we could have just as easily chosen a column to drop into the aggregation function:

```sql
SELECT COUNT(accounts.id)
FROM accounts;
```

These two statements are equivalent, but this isn't always the case.

Other examples:

```sql
SELECT COUNT(*)
FROM orders
WHERE occurred_at >= '2016-12-01'
AND occurred_at < '2017-01-01'
```

Using alias

```sql
SELECT COUNT(*) AS order_count
FROM orders
WHERE occurred_at >= '2016-12-01'
AND occurred_at < '2017-01-01'
```

```sql
SELECT COUNT (*) AS account_count
FROM accounts
```

```sql
SELECT COUNT (id) AS account_id_count
FROM accounts
```

```sql
SELECT COUNT(primary_poc) AS account_primary_poc_count
FROM accounts
```

Notice that **COUNT** does not consider rows that have **NULL** values. Therefore, this can be useful for quickly identifying which rows have missing data. 

## SUM

Unlike **COUNT**, you can only use **SUM** on numeric columns. However, **SUM** will ignore **NULL** values, as do the other aggregation functions

```sql
SELECT SUM(standard_qty) AS standard,
       SUM(gloss_qty) AS gloss,
       SUM(poster_qty) AS poster
FROM orders
```

### Aggregation Reminder

An important thing to remember: **aggregators only aggregate vertically - the values of a column**. If you want to perform a calculation across rows, you would do this with **simple arithmetic**(aggregate across rows).


1. Find the total amount of **poster_qty** paper ordered in the **orders** table.

```sql
SELECT SUM(poster_qty) AS total_poster_sales
FROM orders
```

2. Find the total amount of **standard_qty** paper ordered in the **orders** table.

```sql
SELECT SUM(standard_qty) AS total_standard_sales
FROM orders
```

3. Find the total dollar amount of sales using the **total_amt_usd** in the **orders** table.

```sql
SELECT SUM(total_amt_usd) AS total_dollar_sales
FROM orders
```

4. Find the total amount spent on **standard_amt_usd** and **gloss_amt_usd** paper for each order in the orders table This should give a dollar amount for **each** order in the table.

```sql
SELECT standard_amt_usd + gloss_amt_usd AS total_standard_gloss
FROM orders;
```

5. Find the **standard_amt_usd** per unit of **standard_qty** paper. Your solution should use both aggregation and a mathematical operator.

```sql
SELECT SUM(standard_amt_usd)/SUM(standard_qty) AS standard_price_per_unit
FROM orders;
```

## MIN & MAX

### Expert Tip

Functionally, **MIN** and **MAX** are similar to **COUNT** in that they can be used on non-numerical columns. Depending on the column type, **MIN** will return the lowest number, earliest date, or non-numerical value as early in the alphabet as possible. As you might suspect, **MAX** does the opposite—it returns the highest number, the latest date, or the non-numerical value closest alphabetically to “Z.”

```sql
SELECT MIN(standard_qty) AS standard_min,
       MIN(gloss_qty) AS gloss_min,
       MIN(poster_qty) AS poster_min,
       MAX(standard_qty) AS standard_max,
       MAX(gloss_qty) AS gloss_max,
       MAX(poster_qty) AS poster_max
FROM
```

## AVG

Similar to other software **AVG** returns the mean of the data - that is the sum of all of the values in the column divided by the number of values in a column. This aggregate function again ignores the **NULL** values in both the numerator and the denominator.

If you want to count **NULL**s as zero, you will need to use **SUM** and **COUNT**. However, this is probably not a good idea if the **NULL** values truly just represent unknown values for a cell.

### MEDIAN - Expert Tip

One quick note that a median might be a more appropriate measure of center for this data, but finding the median happens to be a pretty difficult thing to get using SQL alone — so difficult that finding a median is occasionally asked as an interview question.

```sql
SELECT AVG(standard_qty) AS standard_avg,
       AVG(gloss_qty) AS gloss_avg,
       AVG(poster_qty) AS poster_avg
FROM orders
```

### Questions: MIN, MAX, & AVERAGE

1. When was the earliest order ever placed? You only need to return the date.

```sql
SELECT MIN(occurred_at) AS earliest_order
FROM orders
```

2. Try performing the same query as in question 1 without using an aggregation function.

```sql
SELECT occurred_at
FROM orders
ORDER BY occurred_at
LIMIT 1
```

3. When did the most recent (latest) **web_event** occur?

```sql
SELECT MAX(occurred_at) AS latest_web_event
FROM web_events
```

4. Try to perform the result of the previous query without using an aggregation function.

```sql
SELECT occurred_at
FROM web_events
ORDER BY occurred_at DESC
LIMIT 1 
```

5. Find the mean (**AVERAGE**) amount spent per order on each paper type, as well as the mean amount of each paper type purchased per order. Your final answer should have 6 values - one for each paper type for the average number of sales, as well as the average amount.

```sql
SELECT AVG(standard_qty) mean_standard, 
       AVG(gloss_qty) mean_gloss, 
       AVG(poster_qty) mean_poster, 
       AVG(standard_amt_usd) mean_standard_usd, 
       AVG(gloss_amt_usd) mean_gloss_usd, 
       AVG(poster_amt_usd) mean_poster_usd
FROM orders;
```

6. Via the video, you might be interested in how to calculate the MEDIAN. Though this is more advanced than what we have covered so far try finding - what is the MEDIAN **total_usd** spent on all **orders**?

![media-formula](img/media-formula.png)

```sql
-- we can hard code a solution in the following way.--
SELECT *
FROM (SELECT total_amt_usd
   FROM orders
   ORDER BY total_amt_usd
   LIMIT 3457) AS Table1
ORDER BY total_amt_usd DESC
LIMIT 2;
```

even better:

```sql
SELECT AVG(total_amt_usd)
FROM (
        SELECT *
        FROM (
            SELECT total_amt_usd
            FROM orders
            ORDER BY total_amt_usd
            LIMIT 3457
            ) AS FirstHalf
        ORDER BY total_amt_usd DESC
        LIMIT 2
    ) AS Table2
```

Since there are 6912 orders - we want the average of the 3457 and 3456 order amounts when ordered. This is the average of 2483.16 and **2482.55**. This gives the median of 2482.855. This obviously isn't an ideal way to compute. If we obtain new orders, we would have to change the limit. SQL didn't even calculate the median for us. The above used a SUBQUERY, but you could use any method to find the two necessary values, and then you just need the average of them.

## GROUP BY

The key takeaways here:

* **GROUP BY** can be used to aggregate data within subsets of the data. For example, grouping for different accounts, different regions, or different sales representatives.
* Any column in the **SELECT** statement that is not within an aggregator must be in the **GROUP BY** clause.
* The **GROUP BY** always goes between **WHERE** and **ORDER BY**.

### GROUP BY - Expert Tip

```sql
SELECT account_id,
       SUM(standard_qty) AS standard,
       SUM(gloss_qty) AS gloss,
       SUM(poster_qty) AS poster
FROM orders
GROUP BY account_id
ORDER BY account_id
```

Before we dive deeper into aggregations using **GROUP BY** statements, it is worth noting that SQL evaluates the aggregations before the **LIMIT** clause. If you don’t group by any columns, you’ll get a 1-row result—no problem there. If you group by a column with enough unique values that it exceeds the **LIMIT** number, the aggregates will be calculated, and then some rows will simply be omitted from the results.

This is actually a nice way to do things because you know you’re going to get the correct aggregates. If SQL cuts the table down to 100 rows, then performed the aggregations, your results would be substantially different.

### Questions: GROUP BY

One part that can be difficult to recognize is when it might be easiest to use an aggregate or one of the other SQL functionalities. Try some of the below to see if you can differentiate to find the easiest solution.

1. Which **account** (by name) placed the earliest order? Your solution should have the **account name** and the **date** of the order.

```sql
SELECT ac.name, od.occurred_at
FROM accounts AS ac
JOIN orders AS od
ON ac.id = od.account_id
ORDER BY od.occurred_at
```

2. Find the total sales in **usd** for each account. You should include two columns - the total sales for each company's orders in **usd** and the company **name**.

```sql
SELECT ac.name, SUM(od.total)
FROM accounts AS ac
JOIN orders AS od
ON ac.id = od.account_id
GROUP BY ac.name
```

3. Via what **channel** did the most recent (latest) **web_event** occur, which **account** was associated with this **web_event**? Your query should return only three values - the **date**, **channel**, and **account name.**

```sql
SELECT we.channel, ac.name, we.occurred_at
FROM web_events AS we
JOIN accounts AS ac
ON ac.id = we.account_id
ORDER BY we.occurred_at DESC
LIMIT 1
```


4. Find the total number of times each type of **channel** from the **web_events** was used. Your final table should have two columns - the **channel** and the number of times the channel was used.

```sql
SELECT we.channel, COUNT(*)
FROM web_events AS we
GROUP BY we.channel
```

5. Who was the **primary contact** associated with the earliest **web_event**?

```sql
SELECT ac.primary_poc, we.occurred_at
FROM web_events AS we
JOIN accounts AS ac
ON ac.id = we.account_id
ORDER BY we.occurred_at
LIMIT 1
```

6. What was the smallest order placed by each **account** in terms of **total usd**. Provide only two columns - the account **name** and the **total usd**. Order from smallest dollar amounts to largest.

```sql
SELECT ac.name, MIN(od.total_amt_usd)
FROM accounts AS ac
JOIN orders AS od
ON ac.id = od.account_id
GROUP BY ac.name
ORDER BY od.total_amt_usd
```

7. Find the number of **sales reps** in each region. Your final table should have two columns - the **region** and the number of **sales_reps**. Order from the fewest reps to most reps.

```sql
```