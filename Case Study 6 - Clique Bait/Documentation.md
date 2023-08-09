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

**1. What is the total sales for the 4 weeks before and after 2020-06-15? What is the growth or reduction rate in actual values and percentage of sales?**

````sql
    SELECT DISTINCT week_number
    FROM clean_weekly_sales
    WHERE week_date = '2020-06-15' 
      AND year_number = '2020';

    WITH packaging_sales AS (
      SELECT 
        week_date, 
        week_number, 
        SUM(sales) AS total_sales
      FROM clean_weekly_sales
      WHERE (week_number BETWEEN 21 AND 28) 
        AND (year_number = 2020)
      GROUP BY week_date, week_number
    )
    , before_after_changes AS (
      SELECT 
        SUM(CASE 
          WHEN week_number BETWEEN 21 AND 24 THEN total_sales END) AS before_packaging_sales,
        SUM(CASE 
          WHEN week_number BETWEEN 25 AND 28 THEN total_sales END) AS after_packaging_sales
      FROM packaging_sales
    )
    
    SELECT 
      after_packaging_sales - before_packaging_sales AS sales_variance, 
      ROUND(100 * 
        (after_packaging_sales - before_packaging_sales) 
        / before_packaging_sales,2) AS variance_percentage
    FROM before_after_changes;

````

### Answer:
| sales_variance | variance_percentage |
| -------------- | ------------------- |
| -26884188      | -1.15               |
***

**2. What about the entire 12 weeks before and after?**

````sql
    WITH packaging_sales AS (
      SELECT 
        week_date, 
        week_number, 
        SUM(sales) AS total_sales
      FROM clean_weekly_sales
      WHERE (week_number BETWEEN 13 AND 37) 
        AND (year_number = 2020)
      GROUP BY week_date, week_number
    )
    , before_after_changes AS (
      SELECT 
        SUM(CASE 
          WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_packaging_sales,
        SUM(CASE 
          WHEN week_number BETWEEN 25 AND 37 THEN total_sales END) AS after_packaging_sales
      FROM packaging_sales
    )
    
    SELECT 
      after_packaging_sales - before_packaging_sales AS sales_variance, 
      ROUND(100 * 
        (after_packaging_sales - before_packaging_sales) 
        / before_packaging_sales,2) AS variance_percentage
    FROM before_after_changes;
````

### Answer:
| sales_variance | variance_percentage |
| -------------- | ------------------- |
| -152325394     | -2.14               |
***

**3. How do the sale metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?**

````sql
    WITH changes AS (
      SELECT 
        year_number,
        week_number, 
        SUM(sales) AS total_sales
      FROM clean_weekly_sales
      WHERE week_number BETWEEN 21 AND 28
      GROUP BY year_number, week_number
    )
    , before_after_changes AS (
      SELECT 
        year_number,
        SUM(CASE 
          WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_packaging_sales,
        SUM(CASE 
          WHEN week_number BETWEEN 25 AND 28 THEN total_sales END) AS after_packaging_sales
      FROM changes
      GROUP BY year_number
    )
    
    SELECT 
      year_number, 
      after_packaging_sales - before_packaging_sales AS sales_variance, 
      ROUND(100 * 
        (after_packaging_sales - before_packaging_sales) 
        / before_packaging_sales,2) AS variance_percentage
    FROM before_after_changes;
````

### Answer:
| year_number | sales_variance | variance_percentage |
| ----------- | -------------- | ------------------- |
| 2018        | 4102105        | 0.19                |
| 2019        | 2336594        | 0.10                |
| 2020        | -26884188      | -1.15               |
***

## Resources and References:

I obtained my sources, dataset, and information from the following website: [Here](https://8weeksqlchallenge.com/case-study-5/).

***
