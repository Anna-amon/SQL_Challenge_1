# SQL_Challenge_1

## A. Customer Nodes Exploration

**How many unique nodes are there on the Data Bank system?**

```sql
    SELECT *
    FROM data_bank.customer_nodes
    limit 10;
```

| customer_id | region_id | node_id | start_date               | end_date                 |
| ----------- | --------- | ------- | ------------------------ | ------------------------ |
| 1           | 3         | 4       | 2020-01-02T00:00:00.000Z | 2020-01-03T00:00:00.000Z |
| 2           | 3         | 5       | 2020-01-03T00:00:00.000Z | 2020-01-17T00:00:00.000Z |
| 3           | 5         | 4       | 2020-01-27T00:00:00.000Z | 2020-02-18T00:00:00.000Z |
| 4           | 5         | 4       | 2020-01-07T00:00:00.000Z | 2020-01-19T00:00:00.000Z |
| 5           | 3         | 3       | 2020-01-15T00:00:00.000Z | 2020-01-23T00:00:00.000Z |
| 6           | 1         | 1       | 2020-01-11T00:00:00.000Z | 2020-02-06T00:00:00.000Z |
| 7           | 2         | 5       | 2020-01-20T00:00:00.000Z | 2020-02-04T00:00:00.000Z |
| 8           | 1         | 2       | 2020-01-15T00:00:00.000Z | 2020-01-28T00:00:00.000Z |
| 9           | 4         | 5       | 2020-01-21T00:00:00.000Z | 2020-01-25T00:00:00.000Z |
| 10          | 3         | 4       | 2020-01-13T00:00:00.000Z | 2020-01-14T00:00:00.000Z |

---

**What is the number of nodes per region?**

```sql
    SELECT COUNT(DISTINCT node_id) AS unique_nodes
    FROM data_bank.customer_nodes;

| unique_nodes |
| ------------ |
| 5            |
```

---
**How many customers are allocated to each region?**

```sql
    SELECT *
    FROM data_bank.regions
    limit 10;
```

| region_id | region_name |
| --------- | ----------- |
| 1         | Australia   |
| 2         | America     |
| 3         | Africa      |
| 4         | Asia        |
| 5         | Europe      |

```sql
    SELECT r.region_name, c.region_id, COUNT(c.node_id) AS node_count
    FROM data_bank.customer_nodes AS c
    INNER JOIN data_bank.regions AS r ON c.region_id = r.region_id
    GROUP BY r.region_name, c.region_id
    ORDER BY node_count DESC;
```

| region_name | region_id | node_count |
| ----------- | --------- | ---------- |
| Australia   | 1         | 770        |
| America     | 2         | 735        |
| Africa      | 3         | 714        |
| Asia        | 4         | 665        |
| Europe      | 5         | 616        |


```sql
    SELECT r.region_name, c.region_id, COUNT(DISTINCT c.customer_id) AS customer_id
    FROM data_bank.customer_nodes AS c
    INNER JOIN data_bank.regions AS r ON c.region_id = r.region_id
    GROUP BY r.region_name, c.region_id
    ORDER BY customer_id DESC;
```

| region_name | region_id | customer_id |
| ----------- | --------- | ----------- |
| Australia   | 1         | 110         |
| America     | 2         | 105         |
| Africa      | 3         | 102         |
| Asia        | 4         | 95          |
| Europe      | 5         | 88          |

---

**How many days on average are customers reallocated to a different node?**
```sql
    SELECT AVG(end_date - start_date) AS DateDiff
    FROM data_bank.customer_nodes;
```

| datediff            |
| ------------------- |
| 416373.411714285714 |


```sql
    SELECT *
    FROM data_bank.customer_nodes
    ORDER BY end_date DESC
    limit 10;
```

| customer_id | region_id | node_id | start_date               | end_date                 |
| ----------- | --------- | ------- | ------------------------ | ------------------------ |
| 8           | 1         | 3       | 2020-04-26T00:00:00.000Z | 9999-12-31T00:00:00.000Z |
| 9           | 4         | 3       | 2020-04-03T00:00:00.000Z | 9999-12-31T00:00:00.000Z |
| 5           | 3         | 5       | 2020-03-06T00:00:00.000Z | 9999-12-31T00:00:00.000Z |
| 6           | 1         | 5       | 2020-05-30T00:00:00.000Z | 9999-12-31T00:00:00.000Z |
| 1           | 3         | 2       | 2020-03-17T00:00:00.000Z | 9999-12-31T00:00:00.000Z |
| 7           | 2         | 5       | 2020-04-08T00:00:00.000Z | 9999-12-31T00:00:00.000Z |
| 2           | 3         | 4       | 2020-03-14T00:00:00.000Z | 9999-12-31T00:00:00.000Z |
| 3           | 5         | 2       | 2020-04-25T00:00:00.000Z | 9999-12-31T00:00:00.000Z |
| 4           | 5         | 3       | 2020-04-02T00:00:00.000Z | 9999-12-31T00:00:00.000Z |
| 10          | 3         | 2       | 2020-03-19T00:00:00.000Z | 9999-12-31T00:00:00.000Z |


```sql
    SELECT AVG(end_date - start_date) AS DateDiff
    FROM data_bank.customer_nodes
    WHERE end_date != '9999-12-31';
```

| datediff            |
| ------------------- |
| 14.6340000000000000 |

---
**What is the median, 80th, and 95th percentile for this same reallocation days metric for each region?**
```sql
    WITH DateDiffs AS (
        SELECT region_id, (end_date - start_date) AS DateDiff
        FROM data_bank.customer_nodes
        WHERE end_date != '9999-12-31'
    )
    
    SELECT region_id, 
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY DateDiff) AS MedianDateDiff,
    PERCENTILE_CONT(0.8) WITHIN GROUP (ORDER BY DateDiff) AS Percentile_eighty,
    PERCENTILE_CONT(0.95) WITHIN GROUP (ORDER BY DateDiff) AS Percentile_ninetyfive
    FROM DateDiffs
    GROUP BY region_id
    ORDER BY region_id;
```

| region_id | mediandatediff | percentile_eighty | percentile_ninetyfive |
| --------- | -------------- | ----------------- | --------------------- |
| 1         | 15             | 23                | 28                    |
| 2         | 15             | 23                | 28                    |
| 3         | 15             | 24                | 28                    |
| 4         | 15             | 23                | 28                    |
| 5         | 15             | 24                | 28                    |
