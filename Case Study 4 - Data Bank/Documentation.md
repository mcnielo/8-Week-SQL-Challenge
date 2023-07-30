# SQL Challenge Case Study #4: Data Bank
<div align="center"><img src="https://8weeksqlchallenge.com/images/case-study-designs/4.png" alt="Image" width="500" height="520"></div>

***

## Introduction
There is a new innovation in the financial industry called Neo-Banks: new aged digital only banks without physical branches.

Danny thought that there should be some sort of intersection between these new age banks, cryptocurrency and the data world…so he decides to launch a new initiative - Data Bank!

Data Bank runs just like any other digital bank - but it isn’t only for banking activities, they also have the world’s most secure distributed data storage platform!

Customers are allocated cloud data storage limits which are directly linked to how much money they have in their accounts. There are a few interesting caveats that go with this business model, and this is where the Data Bank team need your help!

The management team at Data Bank want to increase their total customer base - but also need some help tracking just how much data storage their customers will need.

This case study is all about calculating metrics, growth and helping the business analyse their data in a smart way to better forecast and plan for their future developments!

***

## Business Problem
Data Bank, a digital bank with a secure data storage platform, needs assistance in tracking and optimizing data storage allocation for their expanding customer base. They seek a data-driven approach to accurately forecast storage needs, optimize resource allocation, and ensure customer satisfaction.

***

## Datasets

### Table 1: regions

Just like popular cryptocurrency platforms - Data Bank is also run off a network of nodes where both money and data is stored across the globe. In a traditional banking sense - you can think of these nodes as bank branches or stores that exist around the world.

This regions table contains the **region_id** and their respective **region_name** values


````sql

````
### Results:


***

### Table 2: customer_nodes

Customers are randomly distributed across the nodes according to their region - this also specifies exactly which node contains both their cash and data.

This random distribution changes frequently to reduce the risk of hackers getting into Data Bank's system and stealing customer's money and data!

````sql

````
### Results:


***

### Table 3: customer_transactions

This table stores all customer deposits, withdrawals and purchases made using their Data Bank debit card.

````sql

````
### Results:


***

## Case Study Questions

***

## Customer Nodes Exploration

**1. How many unique nodes are there on the Data Bank system?**

```sql
    SELECT COUNT(DISTINCT n_nodes) AS n_unique
    FROM data_bank.customer_nodes;
```
### Results:
| n_unique |
| -------- |
| 5        |
***


**2. What is the number of nodes per region?**

```sql
    SELECT 
        region_id,
        COUNT(DISTINCT node_id) AS n_nodes
    FROM data_bank.customer_nodes 
    GROUP BY region_id;
```
### Results:
| region_id | n_nodes |
| --------- | ------- |
| 1         | 5       |
| 2         | 5       |
| 3         | 5       |
| 4         | 5       |
| 5         | 5       |
***


**3. What is the number of nodes per region??**

```sql
    SELECT 
        r.region_name,
        COUNT(DISTINCT cn.customer_id) AS n_cs
    FROM data_bank.customer_nodes cn
    JOIN data_bank.regions r ON cn.region_id = r.region_id 
    GROUP BY region_name
    ORDER BY region_name;
```
### Results:
| region_name | n_cs |
| ----------- | ---- |
| Africa      | 102  |
| America     | 105  |
| Asia        | 95   |
| Australia   | 110  |
| Europe      | 88   |
***

**4. How many days on average are customers reallocated to a different node?**

```sql
    WITH lag_cte AS (
    	SELECT
    		customer_id,
    		node_id,
    		start_date,
    		end_date,
    		LAG(node_id) OVER (PARTITION BY customer_id ORDER BY start_date) AS prev_node
    	FROM
    		data_bank.customer_nodes 
    	WHERE 
    		EXTRACT(YEAR FROM end_date) != '9999'
    	ORDER BY
    		customer_id,
    		start_date
    )
    
    SELECT ROUND(AVG(end_date - start_date), 1) AS AVG_DAYS
    FROM lag_cte
    WHERE prev_node != node_id;
```
### Results:
| avg_days |
| -------- |
| 14.6     |
***


**5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region?**

```sql
    WITH lag_cte AS (
    	SELECT
      		r.region_name,
    		cn.customer_id,
    		cn.node_id,
    		cn.start_date,
    		cn.end_date,
    		LAG(node_id) OVER (PARTITION BY cn.customer_id ORDER BY cn.start_date) AS prev_node
    	FROM
    		data_bank.customer_nodes cn
      	JOIN data_bank.regions r ON cn.region_id = r.region_id
    	WHERE 
    		EXTRACT(YEAR FROM end_date) != '9999'
    	ORDER BY
    		cn.customer_id,
    		cn.start_date
    ),
    
    percentile_cte AS (
        SELECT
            region_name,
            PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY end_date - start_date) AS "50th_perc",
            PERCENTILE_CONT(0.8) WITHIN GROUP(ORDER BY end_date - start_date) AS "80th_perc",
            PERCENTILE_CONT(0.95) WITHIN GROUP(ORDER BY end_date - start_date) AS "95th_perc"
        FROM
            lag_cte
        WHERE
            prev_node != node_id
        GROUP BY 
            region_name
    )
    
    SELECT
    	region_name,
    	CEIL("50th_perc") AS median,
    	CEIL("80th_perc") AS "80th_percentile",
    	CEIL("95th_perc") AS "95th_percentile"
    FROM
    	percentile_cte;
```
### Results:
| region_name | median | 80th_percentile | 95th_percentile |
| ----------- | ------ | --------------- | --------------- |
| Africa      | 15     | 23              | 28              |
| America     | 15     | 23              | 27              |
| Asia        | 14     | 23              | 27              |
| Australia   | 16     | 23              | 28              |
| Europe      | 15     | 24              | 28              |
***


## Customer Transactions

**1. What is the unique count and total amount for each transaction type?**

````sql
    SELECT
    	txn_type,
        COUNT(customer_id) AS txn_count,
        SUM(txn_amount) AS total_amount
    FROM data_bank.customer_transactions
    GROUP BY txn_type;
````

### Answer:
| txn_type   | txn_count | total_amount |
| ---------- | --------- | ------------ |
| purchase   | 1617      | 806537       |
| deposit    | 2671      | 1359168      |
| withdrawal | 1580      | 793003       |
***

**2. What is the average total historical deposit counts and amounts for all customers?**

````sql
    WITH deposits AS (
      SELECT
      	customer_id,
      	COUNT(customer_id) AS txn_count,
      	AVG(txn_amount) AS total_amount
      FROM data_bank.customer_transactions
      WHERE txn_type = 'deposit'
      GROUP BY customer_id
    )
    
    SELECT 
    	AVG(txn_count) AS avg_txn_count,
        AVG(total_amount) AS avg_total_amount
    FROM deposits;
````

### Answer:
| avg_txn_count      | avg_total_amount     |
| ------------------ | -------------------- |
| 5.3420000000000000 | 508.6127820956820957 |
***

**3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?**

````sql
    WITH monthly_transactions AS (
      SELECT
        customer_id,
        DATE_PART('month', txn_date) AS month,
        SUM(CASE WHEN txn_type = 'deposit' THEN 0 ELSE 1 END) AS deposit_count,
        SUM(CASE WHEN txn_type = 'purchase' THEN 0 ELSE 1 END) AS purchase_count,
        SUM(CASE WHEN txn_type = 'withdrawal' THEN 1 ELSE 0 END) AS withdrawal_count
      FROM data_bank.customer_transactions
      GROUP BY 1, 2
    )
    
    SELECT
    	month,
        COUNT(DISTINCT customer_id) AS customer_count
    FROM monthly_transactions
    WHERE deposit_count > 1 AND (purchase_count >= 1 OR withdrawal_count >= 1)
    GROUP BY month
    ORDER BY month;
````

### Answer:
| month | customer_count |
| ----- | -------------- |
| 1     | 170            |
| 2     | 277            |
| 3     | 292            |
| 4     | 103            |
***

**4. What is the closing balance for each customer at the end of the month?**

````sql
    WITH monthly_balances_cte AS (
      SELECT 
        customer_id, 
        (DATE_TRUNC('month', txn_date) + INTERVAL '1 MONTH - 1 DAY') AS closing_month, 
        SUM(CASE 
          WHEN txn_type = 'withdrawal' OR txn_type = 'purchase' THEN -txn_amount
          ELSE txn_amount END) AS transaction_balance
      FROM data_bank.customer_transactions
      GROUP BY 
        customer_id, txn_date 
    )
    
    
    , monthend_series_cte AS (
      SELECT
        DISTINCT customer_id,
        ('2020-01-31'::DATE + GENERATE_SERIES(0,3) * INTERVAL '1 MONTH') AS ending_month
      FROM data_bank.customer_transactions
    )
    
    
    , monthly_changes_cte AS (
      SELECT 
        monthend_series_cte.customer_id, 
        monthend_series_cte.ending_month,
        SUM(monthly_balances_cte.transaction_balance) OVER (
          PARTITION BY monthend_series_cte.customer_id, monthend_series_cte.ending_month
          ORDER BY monthend_series_cte.ending_month
        ) AS total_monthly_change,
        SUM(monthly_balances_cte.transaction_balance) OVER (
          PARTITION BY monthend_series_cte.customer_id 
          ORDER BY monthend_series_cte.ending_month
          ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
        ) AS ending_balance
      FROM monthend_series_cte
      LEFT JOIN monthly_balances_cte
        ON monthend_series_cte.ending_month = monthly_balances_cte.closing_month
        AND monthend_series_cte.customer_id = monthly_balances_cte.customer_id
    )
    
    
    
    SELECT 
    customer_id, 
      ending_month, 
      COALESCE(total_monthly_change, 0) AS total_monthly_change, 
      MIN(ending_balance) AS ending_balance
     FROM monthly_changes_cte
     GROUP BY 
      customer_id, ending_month, total_monthly_change
     ORDER BY 
      customer_id, ending_month
     LIMIT 10;
````

### Answer:
| customer_id | ending_month             | total_monthly_change | ending_balance |
| ----------- | ------------------------ | -------------------- | -------------- |
| 1           | 2020-01-31T00:00:00.000Z | 312                  | 312            |
| 1           | 2020-02-29T00:00:00.000Z | 0                    | 312            |
| 1           | 2020-03-31T00:00:00.000Z | -952                 | -964           |
| 1           | 2020-04-30T00:00:00.000Z | 0                    | -640           |
| 2           | 2020-01-31T00:00:00.000Z | 549                  | 549            |
| 2           | 2020-02-29T00:00:00.000Z | 0                    | 549            |
| 2           | 2020-03-31T00:00:00.000Z | 61                   | 610            |
| 2           | 2020-04-30T00:00:00.000Z | 0                    | 610            |
| 3           | 2020-01-31T00:00:00.000Z | 144                  | 144            |
| 3           | 2020-02-29T00:00:00.000Z | -965                 | -821           |

***

**5. What is the percentage of customers who increase their closing balance by more than 5%?**

````sql
    CREATE TEMP TABLE customer_monthly_balances AS (
      WITH monthly_balances_cte AS (
      SELECT 
        customer_id, 
        (DATE_TRUNC('month', txn_date) + INTERVAL '1 MONTH - 1 DAY') AS closing_month, 
        SUM(CASE 
          WHEN txn_type = 'withdrawal' OR txn_type = 'purchase' THEN -txn_amount
          ELSE txn_amount END) AS transaction_balance
      FROM data_bank.customer_transactions
      GROUP BY 
        customer_id, txn_date 
    ), monthend_series_cte AS (
      SELECT
        DISTINCT customer_id,
        ('2020-01-31'::DATE + GENERATE_SERIES(0,3) * INTERVAL '1 MONTH') AS ending_month
      FROM data_bank.customer_transactions
    ), monthly_changes_cte AS (
      SELECT 
        monthend_series_cte.customer_id, 
        monthend_series_cte.ending_month,
        SUM(monthly_balances_cte.transaction_balance) OVER (
          PARTITION BY monthend_series_cte.customer_id, monthend_series_cte.ending_month
          ORDER BY monthend_series_cte.ending_month
        ) AS total_monthly_change,
        SUM(monthly_balances_cte.transaction_balance) OVER (
          PARTITION BY monthend_series_cte.customer_id 
          ORDER BY monthend_series_cte.ending_month
          ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
        ) AS ending_balance
      FROM monthend_series_cte
      LEFT JOIN monthly_balances_cte
        ON monthend_series_cte.ending_month = monthly_balances_cte.closing_month
        AND monthend_series_cte.customer_id = monthly_balances_cte.customer_id 
    )
    
    SELECT 
      customer_id, 
      ending_month, 
      COALESCE(total_monthly_change, 0) AS total_monthly_change, 
      MIN(ending_balance) AS ending_balance
    FROM monthly_changes_cte
    GROUP BY 
      customer_id, ending_month, total_monthly_change
    ORDER BY 
      customer_id, ending_month
    );
````

````sql
    CREATE TEMP TABLE ranked_monthly_balances AS (
      SELECT 
        customer_id, 
        ending_month, 
        ending_balance,
        ROW_NUMBER() OVER (
          PARTITION BY customer_id 
          ORDER BY ending_month) AS sequence
      FROM customer_monthly_balances
    );

````

````sql
    WITH following_month_cte AS (
      SELECT
        customer_id, 
        ending_month, 
        ending_balance, 
        LEAD(ending_balance) OVER (
          PARTITION BY customer_id 
          ORDER BY ending_month) AS following_balance
      FROM ranked_monthly_balances
    )
    , variance_cte AS (
      SELECT 
        customer_id, 
        ending_month, 
        ROUND(100.0 * 
          (following_balance - ending_balance) / ending_balance,1) AS variance
      FROM following_month_cte  
      WHERE ending_month = '2020-01-31'
        AND following_balance::TEXT NOT LIKE '-%'
      GROUP BY 
        customer_id, ending_month, ending_balance, following_balance
      HAVING ROUND(100.0 * (following_balance - ending_balance) / ending_balance,1) > 5.0
    )
    
    SELECT 
      ROUND(100.0 * 
        COUNT(customer_id)
        / (SELECT COUNT(DISTINCT customer_id) 
        FROM ranked_monthly_balances),1) AS increase_5_percentage
    FROM variance_cte;

````
### Answer
| increase_5_percentage |
| --------------------- |
| 20.0                  |
***

## Data Allocation Challenge



## Resources and References:

I obtained my sources, dataset, and information from the following website: [Here](https://8weeksqlchallenge.com/case-study-4/).

***
