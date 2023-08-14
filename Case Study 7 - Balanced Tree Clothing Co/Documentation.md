# SQL Challenge Case Study #7: Balanced Tree Clothing Co.
<div align="center"><img src="https://8weeksqlchallenge.com/images/case-study-designs/7.png" alt="Image" width="500" height="520"></div>

***

## Introduction
Balanced Tree Clothing Company prides themselves on providing an optimised range of clothing and lifestyle wear for the modern adventurer!

Danny, the CEO of this trendy fashion company has asked you to assist the team’s merchandising teams analyse their sales performance and generate a basic financial report to share with the wider business.

***

## Business Problem
Balanced Tree Clothing Company, known for curating a meticulously tailored collection of apparel and lifestyle attire for contemporary explorers, is facing a pivotal challenge.

As the visionary leader behind this fashionable enterprise, CEO Danny envisions a strategic analysis of the company's sales performance. He recognizes the significance of understanding their financial landscape and seeks your expertise to compile a fundamental financial report. This report will not only provide valuable insights to the internal merchandising teams but will also serve as a comprehensive document for the broader business audience.

Your mission involves delving into the sales data, unraveling patterns, and presenting a concise yet informative financial overview. By addressing this challenge, you will empower Balanced Tree Clothing Company to make informed decisions, refine their market approach, and continue outfitting the modern adventurer with unparalleled style and functionality.

***

## Datasets

### Table 1: product_details

balanced_tree.product_details includes all information about the entire range that Balanced Clothing sells in their store.

````sql
    SELECT * FROM balanced_tree.product_details LIMIT 10;
````
### Results:
| product_id | price | product_name                  | category_id | segment_id | style_id | category_name | segment_name | style_name     |
| ---------- | ----- | ----------------------------- | ----------- | ---------- | -------- | ------------- | ------------ | -------------- |
| c4a632     | 13    | Navy Oversized Jeans - Womens | 1           | 3          | 7        | Womens        | Jeans        | Navy Oversized |
| e83aa3     | 32    | Black Straight Jeans - Womens | 1           | 3          | 8        | Womens        | Jeans        | Black Straight |
| e31d39     | 10    | Cream Relaxed Jeans - Womens  | 1           | 3          | 9        | Womens        | Jeans        | Cream Relaxed  |
| d5e9a6     | 23    | Khaki Suit Jacket - Womens    | 1           | 4          | 10       | Womens        | Jacket       | Khaki Suit     |
| 72f5d4     | 19    | Indigo Rain Jacket - Womens   | 1           | 4          | 11       | Womens        | Jacket       | Indigo Rain    |
| 9ec847     | 54    | Grey Fashion Jacket - Womens  | 1           | 4          | 12       | Womens        | Jacket       | Grey Fashion   |
| 5d267b     | 40    | White Tee Shirt - Mens        | 2           | 5          | 13       | Mens          | Shirt        | White Tee      |
| c8d436     | 10    | Teal Button Up Shirt - Mens   | 2           | 5          | 14       | Mens          | Shirt        | Teal Button Up |
| 2a2353     | 57    | Blue Polo Shirt - Mens        | 2           | 5          | 15       | Mens          | Shirt        | Blue Polo      |
| f084eb     | 36    | Navy Solid Socks - Mens       | 2           | 6          | 16       | Mens          | Socks        | Navy Solid     |
***

### Table 2: sales  

balanced_tree.sales contains product level information for all the transactions made for Balanced Tree including quantity, price, percentage discount, member status, a transaction ID and also the transaction timestamp.

````sql
    SELECT * FROM balanced_tree.sales LIMIT 10;
````
### Results:
| prod_id | qty | price | discount | member | txn_id | start_txn_time           |
| ------- | --- | ----- | -------- | ------ | ------ | ------------------------ |
| c4a632  | 4   | 13    | 17       | true   | 54f307 | 2021-02-13T01:59:43.296Z |
| 5d267b  | 4   | 40    | 17       | true   | 54f307 | 2021-02-13T01:59:43.296Z |
| b9a74d  | 4   | 17    | 17       | true   | 54f307 | 2021-02-13T01:59:43.296Z |
| 2feb6b  | 2   | 29    | 17       | true   | 54f307 | 2021-02-13T01:59:43.296Z |
| c4a632  | 5   | 13    | 21       | true   | 26cc98 | 2021-01-19T01:39:00.345Z |
| e31d39  | 2   | 10    | 21       | true   | 26cc98 | 2021-01-19T01:39:00.345Z |
| 72f5d4  | 3   | 19    | 21       | true   | 26cc98 | 2021-01-19T01:39:00.345Z |
| 2a2353  | 3   | 57    | 21       | true   | 26cc98 | 2021-01-19T01:39:00.345Z |
| f084eb  | 3   | 36    | 21       | true   | 26cc98 | 2021-01-19T01:39:00.345Z |
| c4a632  | 1   | 13    | 21       | false  | ef648d | 2021-01-27T02:18:17.164Z |
***

### Table 3: product_hierarchy

Thes tables are used only for the bonus question where we will use them to recreate the balanced_tree.product_details table.

````sql
    SELECT * FROM balanced_tree.product_hierarchy LIMIT 10;
````
### Results:
| id  | parent_id | level_text     | level_name |
| --- | --------- | -------------- | ---------- |
| 1   |           | Womens         | Category   |
| 2   |           | Mens           | Category   |
| 3   | 1         | Jeans          | Segment    |
| 4   | 1         | Jacket         | Segment    |
| 5   | 2         | Shirt          | Segment    |
| 6   | 2         | Socks          | Segment    |
| 7   | 3         | Navy Oversized | Style      |
| 8   | 3         | Black Straight | Style      |
| 9   | 3         | Cream Relaxed  | Style      |
| 10  | 4         | Khaki Suit     | Style      |
***

### Table 4: product_prices

````sql
    SELECT * FROM balanced_tree.product_prices LIMIT 10;
````
### Results:
| id  | product_id | price |
| --- | ---------- | ----- |
| 7   | c4a632     | 13    |
| 8   | e83aa3     | 32    |
| 9   | e31d39     | 10    |
| 10  | d5e9a6     | 23    |
| 11  | 72f5d4     | 19    |
| 12  | 9ec847     | 54    |
| 13  | 5d267b     | 40    |
| 14  | c8d436     | 10    |
| 15  | 2a2353     | 57    |
| 16  | f084eb     | 36    |
***


## Case Study Questions
***

## High Level Sales Analysis

**1. What was the total quantity sold for all products?**

````sql
    SELECT
    	pd.product_name,
        SUM(s.qty) AS total_quantity
    FROM balanced_tree.sales as s
    JOIN balanced_tree.product_details as pd
    ON s.prod_id = pd.product_id
    GROUP BY pd.product_name;
````

### Answer:
| product_name                     | total_quantity |
| -------------------------------- | -------------- |
| White Tee Shirt - Mens           | 3800           |
| Navy Solid Socks - Mens          | 3792           |
| Grey Fashion Jacket - Womens     | 3876           |
| Navy Oversized Jeans - Womens    | 3856           |
| Pink Fluro Polkadot Socks - Mens | 3770           |
| Khaki Suit Jacket - Womens       | 3752           |
| Black Straight Jeans - Womens    | 3786           |
| White Striped Socks - Mens       | 3655           |
| Blue Polo Shirt - Mens           | 3819           |
| Indigo Rain Jacket - Womens      | 3757           |
| Cream Relaxed Jeans - Womens     | 3707           |
| Teal Button Up Shirt - Mens      | 3646           |
***

**2. What is the total generated revenue for all products before discounts?**

````sql
    SELECT
    	pd.product_name,
        SUM(s.price) * SUM(s.qty) AS total_revenue
    FROM balanced_tree.sales as s
    JOIN balanced_tree.product_details as pd
    ON s.prod_id = pd.product_id
    GROUP BY pd.product_name;
````

### Answer:
| product_name                     | total_revenue |
| -------------------------------- | ------------- |
| White Tee Shirt - Mens           | 192736000     |
| Navy Solid Socks - Mens          | 174871872     |
| Grey Fashion Jacket - Womens     | 266862600     |
| Navy Oversized Jeans - Womens    | 63863072      |
| Pink Fluro Polkadot Socks - Mens | 137537140     |
| Khaki Suit Jacket - Womens       | 107611112     |
| Black Straight Jeans - Womens    | 150955392     |
| White Striped Socks - Mens       | 77233805      |
| Blue Polo Shirt - Mens           | 276022044     |
| Indigo Rain Jacket - Womens      | 89228750      |
| Cream Relaxed Jeans - Womens     | 46078010      |
| Teal Button Up Shirt - Mens      | 45283320      |
***

**3. What was the total discount amount for all products?**

````sql
    SELECT
    	pd.product_name,
        SUM(s.price * s.qty * s.discount/100) AS total_discounted_price
    FROM balanced_tree.sales as s
    JOIN balanced_tree.product_details as pd
    ON s.prod_id = pd.product_id
    GROUP BY pd.product_name;
````

### Answer:
| product_name                     | total_discounted_price |
| -------------------------------- | ---------------------- |
| White Tee Shirt - Mens           | 17968                  |
| Navy Solid Socks - Mens          | 16059                  |
| Grey Fashion Jacket - Womens     | 24781                  |
| Navy Oversized Jeans - Womens    | 5538                   |
| Pink Fluro Polkadot Socks - Mens | 12344                  |
| Khaki Suit Jacket - Womens       | 9660                   |
| Black Straight Jeans - Womens    | 14156                  |
| White Striped Socks - Mens       | 6877                   |
| Blue Polo Shirt - Mens           | 26189                  |
| Indigo Rain Jacket - Womens      | 8010                   |
| Cream Relaxed Jeans - Womens     | 3979                   |
| Teal Button Up Shirt - Mens      | 3925                   |
***


## Transaction Analysis 

***

**1. How many unique transactions were there?**

````sql
    SELECT COUNT(DISTINCT txn_id) AS transaction_count
    FROM balanced_tree.sales;
````

### Answer:
| transaction_count |
| ----------------- |
| 2500              |
***

**2. What is the average unique products purchased in each transaction?**

````sql
    SELECT ROUND(AVG(total_quantity)) AS avg_unique_products
    FROM (
      SELECT 
        txn_id, 
        SUM(qty) AS total_quantity
      FROM balanced_tree.sales
      GROUP BY txn_id
    ) AS total_quantities;
````

### Answer:
| avg_unique_products |
| ------------------- |
| 18                  |
***

**3. What are the 25th, 50th and 75th percentile values for the revenue per transaction?**

````sql
    WITH revenue_cte AS (
      SELECT 
        txn_id, 
        SUM(price * qty) AS revenue
      FROM balanced_tree.sales
      GROUP BY txn_id
    )
    
    SELECT
      PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY revenue) AS median_25th,
        PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY revenue) AS median_50th,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY revenue) AS median_75th
    FROM revenue_cte;
````

### Answer:
| median_25th | median_50th | median_75th |
| ----------- | ----------- | ----------- |
| 375.75      | 509.5       | 647         |
***

**4. What is the average discount value per transaction?**

````sql
    SELECT ROUND(AVG(discount_amt)) AS avg_discount
    FROM (
      SELECT 
    	  txn_id,
        SUM(qty * price * discount/100) AS discount_amt
      FROM balanced_tree.sales
      GROUP BY txn_id
    ) AS discounted_value;
````

### Answer:
| avg_discount |
| ------------ |
| 60           |
***

**5. What is the percentage split of all transactions for members vs non-members?**

````sql
    WITH transactions_cte AS (
      SELECT
        member,
        COUNT(DISTINCT txn_id) AS transactions
      FROM balanced_tree.sales
      GROUP BY member
    )
    
    SELECT
    	member,
      transactions,
      ROUND(100 * transactions
        /(SELECT SUM(transactions) 
          FROM transactions_cte)) AS percentage
    FROM transactions_cte
    GROUP BY member, transactions;
````

### Answer:
| member | transactions | percentage |
| ------ | ------------ | ---------- |
| false  | 995          | 40         |
| true   | 1505         | 60         |
***

**6. What is the average revenue for member transactions and non-member transactions?**

````sql
    WITH revenue_cte AS (
      SELECT
        member,
      	txn_id,
        SUM(price * qty) AS revenue
      FROM balanced_tree.sales
      GROUP BY member, txn_id
    )
    
    SELECT
    	member,
      ROUND(AVG(revenue),2) AS avg_revenue
    FROM revenue_cte
    GROUP BY member;
````

### Answer:
| member | avg_revenue |
| ------ | ----------- |
| false  | 515.04      |
| true   | 516.27      |
***


## Product Analysis

**1. What are the top 3 products by total revenue before discount?**

````sql
    SELECT 
      product.product_id,
      product.product_name, 
      SUM(sales.qty) * SUM(sales.price) AS total_revenue
    FROM balanced_tree.sales
    INNER JOIN balanced_tree.product_details AS product
    	ON sales.prod_id = product.product_id
    GROUP BY product.product_id, product.product_name
    ORDER BY total_revenue DESC
    LIMIT 3;
````

### Answer:
| product_id | product_name                 | total_revenue |
| ---------- | ---------------------------- | ------------- |
| 2a2353     | Blue Polo Shirt - Mens       | 276022044     |
| 9ec847     | Grey Fashion Jacket - Womens | 266862600     |
| 5d267b     | White Tee Shirt - Mens       | 192736000     |
***

**2. What is the total quantity, revenue and discount for each segment?**

````sql
    SELECT 
      product.segment_id,
      product.segment_name, 
      SUM(sales.qty) AS total_quantity,
      SUM(sales.qty * sales.price) AS total_revenue,
      SUM((sales.qty * sales.price) * sales.discount/100) AS total_discount
    FROM balanced_tree.sales
    INNER JOIN balanced_tree.product_details AS product
    	ON sales.prod_id = product.product_id
    GROUP BY product.segment_id, product.segment_name;
````

### Answer:
| segment_id | segment_name | total_quantity | total_revenue | total_discount |
| ---------- | ------------ | -------------- | ------------- | -------------- |
| 4          | Jacket       | 11385          | 366983        | 42451          |
| 6          | Socks        | 11217          | 307977        | 35280          |
| 5          | Shirt        | 11265          | 406143        | 48082          |
| 3          | Jeans        | 11349          | 208350        | 23673          |
***

**3. What is the top selling product for each segment?**

````sql
    WITH top_selling_cte AS ( 
      SELECT 
        product.segment_id,
        product.segment_name, 
        product.product_id,
        product.product_name,
        SUM(sales.qty) AS total_quantity,
        RANK() OVER (
          PARTITION BY segment_id 
          ORDER BY SUM(sales.qty) DESC) AS ranking
      FROM balanced_tree.sales
      INNER JOIN balanced_tree.product_details AS product
        ON sales.prod_id = product.product_id
      GROUP BY 
        product.segment_id, product.segment_name, product.product_id, product.product_name
    )
    
    SELECT 
      segment_id,
      segment_name, 
      product_id,
      product_name,
      total_quantity
    FROM top_selling_cte
    WHERE ranking = 1;
````

### Answer:
| segment_id | segment_name | product_id | product_name                  | total_quantity |
| ---------- | ------------ | ---------- | ----------------------------- | -------------- |
| 3          | Jeans        | c4a632     | Navy Oversized Jeans - Womens | 3856           |
| 4          | Jacket       | 9ec847     | Grey Fashion Jacket - Womens  | 3876           |
| 5          | Shirt        | 2a2353     | Blue Polo Shirt - Mens        | 3819           |
| 6          | Socks        | f084eb     | Navy Solid Socks - Mens       | 3792           |
***

**4. What is the total quantity, revenue and discount for each category?**

````sql
    SELECT 
      product.category_id,
      product.category_name, 
      SUM(sales.qty) AS total_quantity,
      SUM(sales.qty * sales.price) AS total_revenue,
      SUM((sales.qty * sales.price) * sales.discount/100) AS total_discount
    FROM balanced_tree.sales
    INNER JOIN balanced_tree.product_details AS product
    	ON sales.prod_id = product.product_id
    GROUP BY product.category_id, product.category_name
    ORDER BY product.category_id;
````

### Answer:
| category_id | category_name | total_quantity | total_revenue | total_discount |
| ----------- | ------------- | -------------- | ------------- | -------------- |
| 1           | Womens        | 22734          | 575333        | 66124          |
| 2           | Mens          | 22482          | 714120        | 83362          |
***

**5. What is the top selling product for each category?**

````sql
    WITH top_selling_cte AS ( 
      SELECT 
        product.category_id,
        product.category_name, 
        product.product_id,
        product.product_name,
        SUM(sales.qty) AS total_quantity,
        RANK() OVER (
          PARTITION BY product.category_id 
          ORDER BY SUM(sales.qty) DESC) AS ranking
      FROM balanced_tree.sales
      INNER JOIN balanced_tree.product_details AS product
        ON sales.prod_id = product.product_id
      GROUP BY 
        product.category_id, product.category_name, product.product_id, product.product_name
    )
    
    SELECT 
      category_id,
      category_name, 
      product_id,
      product_name,
      total_quantity
    FROM top_selling_cte
    WHERE ranking = 1;
````

### Answer:
| category_id | category_name | product_id | product_name                 | total_quantity |
| ----------- | ------------- | ---------- | ---------------------------- | -------------- |
| 1           | Womens        | 9ec847     | Grey Fashion Jacket - Womens | 3876           |
| 2           | Mens          | 2a2353     | Blue Polo Shirt - Mens       | 3819           |
***

**6. What is the percentage split of revenue by product for each segment?**

````sql
    SELECT
      segment_name,
      product_name,
      ROUND(
        100 *(
          SUM(qty * s.price) :: numeric / SUM(SUM(qty * s.price)) OVER(PARTITION BY segment_name)
        ),
        1
      ) AS percent_of_revenue
    FROM
      sales AS s
      JOIN product_details AS pd ON s.prod_id = pd.product_id
    GROUP BY
      segment_name,
      product_name
    ORDER BY
      1, 3 DESC;
````

### Answer:
| segment_name | product_name                     | percent_of_revenue |
| ------------ | -------------------------------- | ------------------ |
| Jacket       | Grey Fashion Jacket - Womens     | 57.0               |
| Jacket       | Khaki Suit Jacket - Womens       | 23.5               |
| Jacket       | Indigo Rain Jacket - Womens      | 19.5               |
| Jeans        | Black Straight Jeans - Womens    | 58.1               |
| Jeans        | Navy Oversized Jeans - Womens    | 24.1               |
| Jeans        | Cream Relaxed Jeans - Womens     | 17.8               |
| Shirt        | Blue Polo Shirt - Mens           | 53.6               |
| Shirt        | White Tee Shirt - Mens           | 37.4               |
| Shirt        | Teal Button Up Shirt - Mens      | 9.0                |
| Socks        | Navy Solid Socks - Mens          | 44.3               |
| Socks        | Pink Fluro Polkadot Socks - Mens | 35.5               |
| Socks        | White Striped Socks - Mens       | 20.2               |
***

**7. What is the percentage split of revenue by segment for each category?**

````sql
    SELECT
      segment_name,
      category_name,
      ROUND(
        100 *(
          SUM(qty * s.price) :: numeric / SUM(SUM(qty * s.price)) OVER(PARTITION BY category_name)
        ),
        1
      ) AS percent_of_revenue
    FROM
      sales AS s
      JOIN product_details AS pd ON s.prod_id = pd.product_id
    GROUP BY
      segment_name,
      category_name
    ORDER BY
      1;
````

### Answer:
| segment_name | category_name | percent_of_revenue |
| ------------ | ------------- | ------------------ |
| Jacket       | Womens        | 63.8               |
| Jeans        | Womens        | 36.2               |
| Shirt        | Mens          | 56.9               |
| Socks        | Mens          | 43.1               |
***

**8. What is the percentage split of total revenue by category?**

````sql
    SELECT
      category_name,
      ROUND(
        100 *(
          SUM(qty * s.price) :: numeric / SUM(SUM(qty * s.price)) OVER()
        ),
        1
      ) AS percent_of_revenue
    FROM
      sales AS s
      JOIN product_details AS pd ON s.prod_id = pd.product_id
    GROUP BY
      category_name
    ORDER BY
      1;
````

### Answer:
| category_name | percent_of_revenue |
| ------------- | ------------------ |
| Mens          | 55.4               |
| Womens        | 44.6               |
***

**9. What is the total transaction “penetration” for each product? (hint: penetration = number of transactions where at least 1 quantity of a product was purchased divided by total number of transactions)**

````sql
    SELECT
      product_name,
      ROUND(
        100 *(COUNT(product_name) :: numeric / number_of_txn),
        2
      ) AS percent_of_penetration
    FROM
      sales AS s
      JOIN product_details AS pd ON s.prod_id = pd.product_id,
      LATERAL(
        SELECT
          COUNT(distinct txn_id) AS number_of_txn
        FROM
          sales
      ) ss
    GROUP BY
      product_name,
      number_of_txn
    ORDER BY
      2 DESC;
````

### Answer:
| product_name                     | percent_of_penetration |
| -------------------------------- | ---------------------- |
| Navy Solid Socks - Mens          | 51.24                  |
| Grey Fashion Jacket - Womens     | 51.00                  |
| Navy Oversized Jeans - Womens    | 50.96                  |
| White Tee Shirt - Mens           | 50.72                  |
| Blue Polo Shirt - Mens           | 50.72                  |
| Pink Fluro Polkadot Socks - Mens | 50.32                  |
| Indigo Rain Jacket - Womens      | 50.00                  |
| Khaki Suit Jacket - Womens       | 49.88                  |
| Black Straight Jeans - Womens    | 49.84                  |
| White Striped Socks - Mens       | 49.72                  |
| Cream Relaxed Jeans - Womens     | 49.72                  |
| Teal Button Up Shirt - Mens      | 49.68                  |
***

**9. What is the most common combination of at least 1 quantity of any 3 products in a 1 single transaction?**

````sql
    SELECT
      product_1,
      product_2,
      product_3,
      times_bought_together
    FROM
      (
        with products AS(
          SELECT
            txn_id,
            product_name
          FROM
            sales AS s
            JOIN product_details AS pd ON s.prod_id = pd.product_id
        )
        SELECT
          p.product_name AS product_1,
          p1.product_name AS product_2,
          p2.product_name AS product_3,
          COUNT(*) AS times_bought_together,
          ROW_NUMBER() OVER(
            ORDER BY
              COUNT(*) DESC
          ) AS rank
        FROM
          products AS p
          JOIN products AS p1 ON p.txn_id = p1.txn_id
          AND p.product_name != p1.product_name
          AND p.product_name < p1.product_name
          JOIN products AS p2 ON p.txn_id = p2.txn_id
          AND p.product_name != p2.product_name
          AND p1.product_name != p2.product_name
          AND p.product_name < p2.product_name
          AND p1.product_name < p2.product_name
        GROUP BY
          p.product_name,
          p1.product_name,
          p2.product_name
      ) pp
    WHERE
      RANK = 1;
````

### Answer:
| product_1                    | product_2                   | product_3              | times_bought_together |
| ---------------------------- | --------------------------- | ---------------------- | --------------------- |
| Grey Fashion Jacket - Womens | Teal Button Up Shirt - Mens | White Tee Shirt - Mens | 352                   |
***

## Reporting Challenge

## Resources and References:

I obtained my sources, dataset, and information from the following website: [Here](https://8weeksqlchallenge.com/case-study-7v /).

***
