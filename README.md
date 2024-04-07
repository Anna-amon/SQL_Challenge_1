# SQL_Challenge_1

A. Customer Nodes Exploration

How many unique nodes are there on the Data Bank system?

```sql
SELECT COUNT(node_ID) AS unique_nodes 
FROM data_bank.customer_nodes;
```

| unique_nodes |
|-------|
| 3500  |

What is the number of nodes per region?

How many customers are allocated to each region?

How many days on average are customers reallocated to a different node?

What is the median, 80th and 95th percentile for this same reallocation days metric for each region?


