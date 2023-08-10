# SQL Challenge Case Study #6: Clique Bait
<div align="center"><img src="https://8weeksqlchallenge.com/images/case-study-designs/6.png" alt="Image" width="500" height="520"></div>

***

## Introduction
Step into the world of Clique Bait, an online seafood emporium that defies convention. Behind its virtual shelves stands Danny, a visionary CEO who once honed his skills in the realm of digital data analytics. Now, fueled by a desire to fuse his expertise with the depths of the seafood industry, Danny invites you to embark on a captivating case study.

Within these pages, your role unfolds—to align with Danny's mission, dissect his dataset, and ingeniously devise pathways to compute funnel fallout rates for the Clique Bait online store. Join us in unraveling the intricate threads of this narrative as we explore uncharted waters and chart the course to innovative solutions.

***

## Business Problem
Clique Bait, an online seafood store led by CEO Danny with a background in data analytics, aims to enhance the seafood industry. The challenge is to analyze the customer journey and devise innovative solutions to calculate and improve funnel fallout rates. This involves identifying where potential customers drop off during their online shopping experience and developing strategies to boost conversions. Your role is crucial in aligning data insights with seafood retail, driving growth and user satisfaction.

***

## Datasets

### Table 1: users

Customers who visit the Clique Bait website are tagged via their cookie_id.

````sql
    SELECT * FROM clique_bait.users LIMIT 10;
````
### Results:
| user_id | cookie_id | start_date               |
| ------- | --------- | ------------------------ |
| 1       | c4ca42    | 2020-02-04T00:00:00.000Z |
| 2       | c81e72    | 2020-01-18T00:00:00.000Z |
| 3       | eccbc8    | 2020-02-21T00:00:00.000Z |
| 4       | a87ff6    | 2020-02-22T00:00:00.000Z |
| 5       | e4da3b    | 2020-02-01T00:00:00.000Z |
| 6       | 167909    | 2020-01-25T00:00:00.000Z |
| 7       | 8f14e4    | 2020-02-09T00:00:00.000Z |
| 8       | c9f0f8    | 2020-02-12T00:00:00.000Z |
| 9       | 45c48c    | 2020-02-07T00:00:00.000Z |
| 10      | d3d944    | 2020-01-23T00:00:00.000Z |
***

### Table 2: events

Customer visits are logged in this events table at a cookie_id level and the event_type and page_id values can be used to join onto relevant satellite tables to obtain further information about each event.

The sequence_number is used to order the events within each visit.

````sql
    SELECT * FROM clique_bait.events LIMIT 10;
````
### Results:
| visit_id | cookie_id | page_id | event_type | sequence_number | event_time               |
| -------- | --------- | ------- | ---------- | --------------- | ------------------------ |
| ccf365   | c4ca42    | 1       | 1          | 1               | 2020-02-04T19:16:09.182Z |
| ccf365   | c4ca42    | 2       | 1          | 2               | 2020-02-04T19:16:17.358Z |
| ccf365   | c4ca42    | 6       | 1          | 3               | 2020-02-04T19:16:58.454Z |
| ccf365   | c4ca42    | 9       | 1          | 4               | 2020-02-04T19:16:58.609Z |
| ccf365   | c4ca42    | 9       | 2          | 5               | 2020-02-04T19:17:51.729Z |
| ccf365   | c4ca42    | 10      | 1          | 6               | 2020-02-04T19:18:11.605Z |
| ccf365   | c4ca42    | 10      | 2          | 7               | 2020-02-04T19:19:10.570Z |
| ccf365   | c4ca42    | 11      | 1          | 8               | 2020-02-04T19:19:46.911Z |
| ccf365   | c4ca42    | 11      | 2          | 9               | 2020-02-04T19:20:45.274Z |
| ccf365   | c4ca42    | 12      | 1          | 10              | 2020-02-04T19:20:52.307Z |
***

### Table 3: event_identifier

The event_identifier table shows the types of events which are captured by Clique Bait’s digital data systems.

````sql
    SELECT * FROM clique_bait.event_identifier;
````
### Results:
| event_type | event_name    |
| ---------- | ------------- |
| 1          | Page View     |
| 2          | Add to Cart   |
| 3          | Purchase      |
| 4          | Ad Impression |
| 5          | Ad Click      |
***

### Table 4: campaign_identifier

This table shows information for the 3 campaigns that Clique Bait has ran on their website so far in 2020.

````sql
    SELECT * FROM clique_bait.campaign_identifier;
````
### Results:
| campaign_id | products | campaign_name                     | start_date               | end_date                 |
| ----------- | -------- | --------------------------------- | ------------------------ | ------------------------ |
| 1           | 1-3      | BOGOF - Fishing For Compliments   | 2020-01-01T00:00:00.000Z | 2020-01-14T00:00:00.000Z |
| 2           | 4-5      | 25% Off - Living The Lux Life     | 2020-01-15T00:00:00.000Z | 2020-01-28T00:00:00.000Z |
| 3           | 6-8      | Half Off - Treat Your Shellf(ish) | 2020-02-01T00:00:00.000Z | 2020-03-31T00:00:00.000Z |
***

### Table 5: page_hierarchy

This table lists all of the pages on the Clique Bait website which are tagged and have data passing through from user interaction events.

````sql
    SELECT * FROM clique_bait.page_hierarchy LIMIT 10;
````
### Results:
| page_id | page_name      | product_category | product_id |
| ------- | -------------- | ---------------- | ---------- |
| 1       | Home Page      |                  |            |
| 2       | All Products   |                  |            |
| 3       | Salmon         | Fish             | 1          |
| 4       | Kingfish       | Fish             | 2          |
| 5       | Tuna           | Fish             | 3          |
| 6       | Russian Caviar | Luxury           | 4          |
| 7       | Black Truffle  | Luxury           | 5          |
| 8       | Abalone        | Shellfish        | 6          |
| 9       | Lobster        | Shellfish        | 7          |
| 10      | Crab           | Shellfish        | 8          |
***

## Case Study Questions
***

## Digital Analysis

**1. How many users are there?**

````sql
    SELECT COUNT(DISTINCT user_id) FROM clique_bait.users;
````

### Answer:
| count |
| ----- |
| 500   |
***

**2. How many cookies does each user have on average?**

````sql
    WITH cookies_cte AS (
      SELECT
          user_id,
          COUNT(cookie_id) AS cookie_count
      FROM clique_bait.users
      GROUP BY user_id
    )
    
    SELECT
        AVG(cookie_count) AS avg_cookies
    FROM cookies_cte;
````

### Answer:
| avg_cookies        |
| ------------------ |
| 3.5640000000000000 |
***

**3. What is the unique number of visits by all users per month?**

````sql
    SELECT
    	EXTRACT(MONTH from event_time) AS month,
        COUNT(DISTINCT visit_id) AS visit_count
    FROM clique_bait.events
    GROUP BY month;
````

### Answer:
| month | visit_count |
| ----- | ----------- |
| 1     | 876         |
| 2     | 1488        |
| 3     | 916         |
| 4     | 248         |
| 5     | 36          |
***

**4. What is the number of events for each event type?**

````sql
    SELECT
    	event_type,
        COUNT(*) AS event_count
    FROM clique_bait.events
    GROUP BY event_type
    ORDER BY event_type;
````

### Answer:
| event_type | event_count |
| ---------- | ----------- |
| 1          | 20928       |
| 2          | 8451        |
| 3          | 1777        |
| 4          | 876         |
| 5          | 702         |
***

**5. What is the percentage of visits which have a purchase event?**

````sql
    SELECT 
      100 * COUNT(DISTINCT e.visit_id)/
        (SELECT COUNT(DISTINCT visit_id) FROM clique_bait.events) AS percentage_purchase
    FROM clique_bait.events AS e
    WHERE event_type = 3;
````

### Answer:
| percentage_purchase |
| ------------------- |
| 49                  |
***

**6. What is the percentage of visits which view the checkout page but do not have a purchase event?**

````sql
    WITH checkout_purchase AS (
      SELECT
      	visit_id,
      	MAX(CASE WHEN event_type = 1 AND page_id = 12 THEN 1 ELSE 0 END) AS checkout,
      	MAX(CASE WHEN event_type = 3 THEN 1 ELSE 0 END) AS purchase
      FROM clique_bait.events
      GROUP BY visit_id
    )
    
    SELECT
    	ROUND(100 * (1-(SUM(purchase)::NUMERIC/SUM(checkout))),2) AS percentage_checkout
    FROM checkout_purchase;
````

### Answer:
| percentage_checkout |
| ------------------- |
| 15.50               |
***

**7. What are the top 3 pages by number of views?**

````sql
    SELECT
    	ph.page_name,
        COUNT(*) AS page_views
    FROM clique_bait.events e
    JOIN clique_bait.page_hierarchy ph
    ON e.page_id = ph.page_id
    WHERE e.event_type = 1
    GROUP BY ph.page_name
    ORDER BY page_views DESC
    LIMIT 3;
````

### Answer:
| page_name    | page_views |
| ------------ | ---------- |
| All Products | 3174       |
| Checkout     | 2103       |
| Home Page    | 1782       |
***

**8. What is the number of views and cart adds for each product category?**

````sql
    SELECT
    	ph.product_category,
        SUM(CASE WHEN e.event_type = 1 THEN 1 ELSE 0 END) AS page_views,
        SUM(CASE WHEN e.event_type = 2 THEN 1 ELSE 0 END) AS add_cart
    FROM clique_bait.page_hierarchy ph
    JOIN clique_bait.events e
    ON ph.page_id = e.page_id
    WHERE ph.product_category IS NOT NULL
    GROUP BY ph.product_category
    ORDER BY page_views DESC;
````

### Answer:
| product_category | page_views | add_cart |
| ---------------- | ---------- | -------- |
| Shellfish        | 6204       | 3792     |
| Fish             | 4633       | 2789     |
| Luxury           | 3032       | 1870     |
***

**9. What are the top 3 products by purchases?**

````sql

````

### Answer:

***



## Product Funnel Analysis

Using a single SQL query - create a new output table which has the following details:

How many times was each product viewed?
How many times was each product added to cart?
How many times was each product added to a cart but not purchased (abandoned)?
How many times was each product purchased?

````sql
    WITH product_page_events AS (
      SELECT 
        e.visit_id,
        ph.product_id,
        ph.page_name AS product_name,
        ph.product_category,
        SUM(CASE WHEN e.event_type = 1 THEN 1 ELSE 0 END) AS page_view, 
        SUM(CASE WHEN e.event_type = 2 THEN 1 ELSE 0 END) AS cart_add 
      FROM clique_bait.events AS e
      JOIN clique_bait.page_hierarchy AS ph
        ON e.page_id = ph.page_id
      WHERE product_id IS NOT NULL
      GROUP BY e.visit_id, ph.product_id, ph.page_name, ph.product_category
    ),
    purchase_events AS ( 
      SELECT 
        DISTINCT visit_id
      FROM clique_bait.events
      WHERE event_type = 3
    ),
    combined_table AS (
      SELECT 
        ppe.visit_id, 
        ppe.product_id, 
        ppe.product_name, 
        ppe.product_category, 
        ppe.page_view, 
        ppe.cart_add,
        CASE WHEN pe.visit_id IS NOT NULL THEN 1 ELSE 0 END AS purchase
      FROM product_page_events AS ppe
      LEFT JOIN purchase_events AS pe
        ON ppe.visit_id = pe.visit_id
    ),
    product_info AS (
      SELECT 
        product_name, 
        product_category, 
        SUM(page_view) AS views,
        SUM(cart_add) AS cart_adds, 
        SUM(CASE WHEN cart_add = 1 AND purchase = 0 THEN 1 ELSE 0 END) AS abandoned,
        SUM(CASE WHEN cart_add = 1 AND purchase = 1 THEN 1 ELSE 0 END) AS purchases
      FROM combined_table
      GROUP BY product_id, product_name, product_category)

    SELECT *
    FROM product_info
    ORDER BY product_name;
````
| product_name   | product_category | views | cart_adds | abandoned | purchases |
| -------------- | ---------------- | ----- | --------- | --------- | --------- |
| Abalone        | Shellfish        | 1525  | 932       | 233       | 699       |
| Black Truffle  | Luxury           | 1469  | 924       | 217       | 707       |
| Crab           | Shellfish        | 1564  | 949       | 230       | 719       |
| Kingfish       | Fish             | 1559  | 920       | 213       | 707       |
| Lobster        | Shellfish        | 1547  | 968       | 214       | 754       |
| Oyster         | Shellfish        | 1568  | 943       | 217       | 726       |
| Russian Caviar | Luxury           | 1563  | 946       | 249       | 697       |
| Salmon         | Fish             | 1559  | 938       | 227       | 711       |
| Tuna           | Fish             | 1515  | 931       | 234       | 697       |


````sql
    WITH product_page_events AS (
      SELECT 
        e.visit_id,
        ph.product_id,
        ph.page_name AS product_name,
        ph.product_category,
        SUM(CASE WHEN e.event_type = 1 THEN 1 ELSE 0 END) AS page_view, 
        SUM(CASE WHEN e.event_type = 2 THEN 1 ELSE 0 END) AS cart_add 
      FROM clique_bait.events AS e
      JOIN clique_bait.page_hierarchy AS ph
        ON e.page_id = ph.page_id
      WHERE product_id IS NOT NULL
      GROUP BY e.visit_id, ph.product_id, ph.page_name, ph.product_category
    ),
    purchase_events AS (
      SELECT 
        DISTINCT visit_id
      FROM clique_bait.events
      WHERE event_type = 3 
    ),
    combined_table AS (
      SELECT 
        ppe.visit_id, 
        ppe.product_id, 
        ppe.product_name, 
        ppe.product_category, 
        ppe.page_view, 
        ppe.cart_add,
        CASE WHEN pe.visit_id IS NOT NULL THEN 1 ELSE 0 END AS purchase
      FROM product_page_events AS ppe
      LEFT JOIN purchase_events AS pe
        ON ppe.visit_id = pe.visit_id
    ),
    product_category AS (
      SELECT 
        product_category, 
        SUM(page_view) AS views,
        SUM(cart_add) AS cart_adds, 
        SUM(CASE WHEN cart_add = 1 AND purchase = 0 THEN 1 ELSE 0 END) AS abandoned,
        SUM(CASE WHEN cart_add = 1 AND purchase = 1 THEN 1 ELSE 0 END) AS purchases
      FROM combined_table
      GROUP BY product_category)
    
    SELECT *
    FROM product_category;
````

| product_category | views | cart_adds | abandoned | purchases |
| ---------------- | ----- | --------- | --------- | --------- |
| Luxury           | 3032  | 1870      | 466       | 1404      |
| Shellfish        | 6204  | 3792      | 894       | 2898      |
| Fish             | 4633  | 2789      | 674       | 2115      |

***

### Creating the table



***

**1. Which product had the most views, cart adds and purchases?**

````sql
    SELECT *
    FROM product_info
    ORDER BY views DESC
    LIMIT 1;

    SELECT *
    FROM product_info
    ORDER BY cart_adds DESC
    LIMIT 1;

    SELECT *
    FROM product_info
    ORDER BY purchases DESC
    LIMIT 1;
````

### Answer:
### Views
| product_name | product_category | views | cart_adds | abandoned | purchases |
| ------------ | ---------------- | ----- | --------- | --------- | --------- |
| Oyster       | Shellfish        | 1568  | 943       | 217       | 726       |

### Cart Adds
| product_name | product_category | views | cart_adds | abandoned | purchases |
| ------------ | ---------------- | ----- | --------- | --------- | --------- |
| Lobster      | Shellfish        | 1547  | 968       | 214       | 754       |

### Purchases
| product_name | product_category | views | cart_adds | abandoned | purchases |
| ------------ | ---------------- | ----- | --------- | --------- | --------- |
| Lobster      | Shellfish        | 1547  | 968       | 214       | 754       |
***

**2. Which product was most likely to be abandoned?**

````sql
    SELECT *
    FROM product_info
    ORDER BY abandoned DESC
    LIMIT 1;
````

### Answer:
| product_name   | product_category | views | cart_adds | abandoned | purchases |
| -------------- | ---------------- | ----- | --------- | --------- | --------- |
| Russian Caviar | Luxury           | 1563  | 946       | 249       | 697       |
***

**3. Which product had the highest view to purchase percentage?**

````sql
    SELECT 
        product_name, 
      product_category, 
      ROUND(100 * purchases/views,2) AS purchase_per_view_percentage
    FROM product_info
    ORDER BY purchase_per_view_percentage DESC
    LIMIT 1;
````

### Answer:
| product_name | product_category | purchase_per_view_percentage |
| ------------ | ---------------- | ---------------------------- |
| Lobster      | Shellfish        | 48.74                        |
***

**4. What is the average conversion rate from view to cart add?**

````sql
      ROUND(100*AVG(cart_adds/views),2) AS avg_view_to_cart_add_conversion
    FROM product_info
    LIMIT 1;
````

### Answer:
| avg_view_to_cart_add_conversion |
| ------------------------------- |
| 60.95                           | 

***

**5. What is the average conversion rate from cart add to purchase?**

````sql
      ROUND(100*AVG(purchases/cart_adds),2) AS avg_cart_add_to_purchases_conversion_rate
    FROM product_info
    LIMIT 1;
````

### Answer:
| avg_cart_add_to_purchases_conversion_rate |
| ----------------------------------------- |
| 75.93                                     |

***

## Campaign Analysis

Generate a table that has 1 single row for every unique visit_id record and has the following columns:

- user_id
- visit_id
- visit_start_time: the earliest event_time for each visit
- page_views: count of page views for each visit
- cart_adds: count of product cart add events for each visit
- purchase: 1/0 flag if a purchase event exists for each visit
- campaign_name: map the visit to a campaign if the visit_start_time falls between the start_date and end_date
- impression: count of ad impressions for each visit
- click: count of ad clicks for each visit
- (Optional column) cart_products: a comma separated text value with products added to the cart sorted by the order they were added to the cart (hint: use the sequence_number)

### Solution

````sql
    SELECT 
      u.user_id, e.visit_id, 
      MIN(e.event_time) AS visit_start_time,
      SUM(CASE WHEN e.event_type = 1 THEN 1 ELSE 0 END) AS page_views,
      SUM(CASE WHEN e.event_type = 2 THEN 1 ELSE 0 END) AS cart_adds,
      SUM(CASE WHEN e.event_type = 3 THEN 1 ELSE 0 END) AS purchase,
      c.campaign_name,
      SUM(CASE WHEN e.event_type = 4 THEN 1 ELSE 0 END) AS impression, 
      SUM(CASE WHEN e.event_type = 5 THEN 1 ELSE 0 END) AS click, 
      STRING_AGG(CASE WHEN p.product_id IS NOT NULL AND e.event_type = 2 THEN p.page_name ELSE NULL END, 
        ', ' ORDER BY e.sequence_number) AS cart_products
    FROM clique_bait.users AS u
    INNER JOIN clique_bait.events AS e
      ON u.cookie_id = e.cookie_id
    LEFT JOIN clique_bait.campaign_identifier AS c
      ON e.event_time BETWEEN c.start_date AND c.end_date
    LEFT JOIN clique_bait.page_hierarchy AS p
      ON e.page_id = p.page_id
    GROUP BY u.user_id, e.visit_id, c.campaign_name
    LIMIT 10;
````

### Results

| user_id | visit_id | visit_start_time         | page_views | cart_adds | purchase | campaign_name                     | impression | click | cart_products                                                               |
| ------- | -------- | ------------------------ | ---------- | --------- | -------- | --------------------------------- | ---------- | ----- | --------------------------------------------------------------------------- |
| 1       | 02a5d5   | 2020-02-26T16:57:26.260Z | 4          | 0         | 0        | Half Off - Treat Your Shellf(ish) | 0          | 0     |                                                                             |
| 1       | 0826dc   | 2020-02-26T05:58:37.918Z | 1          | 0         | 0        | Half Off - Treat Your Shellf(ish) | 0          | 0     |                                                                             |
| 1       | 0fc437   | 2020-02-04T17:49:49.602Z | 10         | 6         | 1        | Half Off - Treat Your Shellf(ish) | 1          | 1     | Tuna, Russian Caviar, Black Truffle, Abalone, Crab, Oyster                  |
| 1       | 30b94d   | 2020-03-15T13:12:54.023Z | 9          | 7         | 1        | Half Off - Treat Your Shellf(ish) | 1          | 1     | Salmon, Kingfish, Tuna, Russian Caviar, Abalone, Lobster, Crab              |
| 1       | 41355d   | 2020-03-25T00:11:17.860Z | 6          | 1         | 0        | Half Off - Treat Your Shellf(ish) | 0          | 0     | Lobster                                                                     |
| 1       | ccf365   | 2020-02-04T19:16:09.182Z | 7          | 3         | 1        | Half Off - Treat Your Shellf(ish) | 0          | 0     | Lobster, Crab, Oyster                                                       |
| 1       | eaffde   | 2020-03-25T20:06:32.342Z | 10         | 8         | 1        | Half Off - Treat Your Shellf(ish) | 1          | 1     | Salmon, Tuna, Russian Caviar, Black Truffle, Abalone, Lobster, Crab, Oyster |
| 1       | f7c798   | 2020-03-15T02:23:26.312Z | 9          | 3         | 1        | Half Off - Treat Your Shellf(ish) | 0          | 0     | Russian Caviar, Crab, Oyster                                                |
| 2       | 0635fb   | 2020-02-16T06:42:42.735Z | 9          | 4         | 1        | Half Off - Treat Your Shellf(ish) | 0          | 0     | Salmon, Kingfish, Abalone, Crab                                             |
| 2       | 1f1198   | 2020-02-01T21:51:55.078Z | 1          | 0         | 0        | Half Off - Treat Your Shellf(ish) | 0          | 0     |                                                                             |


***

## Resources and References:

I obtained my sources, dataset, and information from the following website: [Here](https://8weeksqlchallenge.com/case-study-6/).

***
