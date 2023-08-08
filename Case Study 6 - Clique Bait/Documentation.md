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

# Data Cleansing Steps

In a single query, pe#rform the following operations and generate a new table in the data_mart schema named clean_weekly_sales:

- Convert the week_date to a DATE format
- Add a week_number as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc
- Add a month_number with the calendar month for each week_date value as the 3rd column
- Add a calendar_year column as the 4th column containing either 2018, 2019 or 2020 values
- Add a new column called age_band after the original segment column using the following mapping on the number inside the segment value
- Add a new demographic column using the following mapping for the first letter in the segment values:
- Ensure all null string values with an "unknown" string value in the original segment column as well as the new age_band and demographic columns
- Generate a new avg_transaction column as the sales value divided by transactions rounded to 2 decimal places for each record

````sql
    DROP TABLE IF EXISTS clean_weekly_sales;
    CREATE TEMP TABLE clean_weekly_sales AS (
    SELECT
      TO_DATE(week_date, 'DD/MM/YY') AS week_date,
      DATE_PART('week', TO_DATE(week_date, 'DD/MM/YY')) AS week_number,
      DATE_PART('month', TO_DATE(week_date, 'DD/MM/YY')) AS month_number,
      DATE_PART('year', TO_DATE(week_date, 'DD/MM/YY')) AS year_number,
      region,
      platform,
      CASE
      	WHEN segment = 'null' THEN 'unknown'
      	ELSE segment END AS segment,
      CASE
      	WHEN RIGHT(segment,1) = '1' THEN 'Young Adults'
      	WHEN RIGHT(segment,1) = '2' THEN 'Middle Aged'
      	WHEN RIGHT(segment,1) IN ('3', '4') THEN 'Retirees'
      	ELSE 'unknown' END AS age_band,
      CASE
      	WHEN LEFT(segment,1) = 'C' THEN 'Couples'
      	WHEN LEFT(segment,1) = 'F' THEN 'Families'
      	ELSE 'unknown' END AS demographic,
      customer_type,
      transactions,
      ROUND((sales::numeric/transactions),2) AS avg_transactions,
      sales
    FROM data_mart.weekly_sales
    );
````

### Answer:
| week_date                | week_number | month_number | year_number | region | platform | segment | age_band     | demographic | customer_type | transactions | avg_transactions | sales    |
| ------------------------ | ----------- | ------------ | ----------- | ------ | -------- | ------- | ------------ | ----------- | ------------- | ------------ | ---------------- | -------- |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020        | ASIA   | Retail   | C3      | Retirees     | Couples     | New           | 120631       | 30.31            | 3656163  |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020        | ASIA   | Retail   | F1      | Young Adults | Families    | New           | 31574        | 31.56            | 996575   |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020        | USA    | Retail   | unknown | unknown      | unknown     | Guest         | 529151       | 31.20            | 16509610 |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020        | EUROPE | Retail   | C1      | Young Adults | Couples     | New           | 4517         | 31.42            | 141942   |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020        | AFRICA | Retail   | C2      | Middle Aged  | Couples     | New           | 58046        | 30.29            | 1758388  |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020        | CANADA | Shopify  | F2      | Middle Aged  | Families    | Existing      | 1336         | 182.54           | 243878   |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020        | AFRICA | Shopify  | F3      | Retirees     | Families    | Existing      | 2514         | 206.64           | 519502   |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020        | ASIA   | Shopify  | F1      | Young Adults | Families    | Existing      | 2158         | 172.11           | 371417   |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020        | AFRICA | Shopify  | F2      | Middle Aged  | Families    | New           | 318          | 155.84           | 49557    |
| 2020-08-31T00:00:00.000Z | 36          | 8            | 2020        | AFRICA | Retail   | C3      | Retirees     | Couples     | New           | 111032       | 35.02            | 3888162  |

***

## Data Exploration

**1. What day of the week is used for each week_date value?**

````sql
    SELECT DISTINCT(TO_CHAR(week_date, 'day')) AS week_day
    FROM clean_weekly_sales;
````

### Answer:
| week_day  |
| --------- |
| monday    |
***

**2. What range of week numbers are missing from the dataset?**

````sql
    WITH week_num_cte AS (
      SELECT GENERATE_SERIES(1,52) AS week_number
    )
      
    SELECT DISTINCT wn.week_number
    FROM week_num_cte AS wn
    LEFT JOIN clean_weekly_sales AS cw
      ON wn.week_number = cw.week_number
    WHERE cw.week_number IS NULL
    LIMIT 5;
````

### Answer:
| week_number |
| ----------- |
| 1           |
| 2           |
| 3           |
| 4           |
| 5           |

***

**3. How many total transactions were there for each year in the dataset?**

````sql
    SELECT year_number, SUM(transactions) AS transactions
    FROM clean_weekly_sales
    GROUP BY year_number
    ORDER BY year_number;
````

### Answer:
| year_number | transactions |
| ----------- | ------------ |
| 2018        | 346406460    |
| 2019        | 365639285    |
| 2020        | 375813651    |
***

**4. What is the total sales for each region for each month?**

````sql
    SELECT month_number, region, SUM(sales) AS total_sales
    FROM clean_weekly_sales
    GROUP BY month_number, region
    ORDER BY month_number, region
    LIMIT 5;
````

### Answer:
| month_number | region  | total_sales |
| ------------ | ------- | ----------- |
| 3            | AFRICA  | 567767480   |
| 3            | ASIA    | 529770793   |
| 3            | CANADA  | 144634329   |
| 3            | EUROPE  | 35337093    |
| 3            | OCEANIA | 783282888   |
***

**5. What is the total count of transactions for each platform?**

````sql
    SELECT platform, SUM(transactions) AS total_transactions
    FROM clean_weekly_sales
    GROUP BY platform
    ORDER BY platform;
````

### Answer:
| platform | total_transactions |
| -------- | ------------------ |
| Retail   | 1081934227         |
| Shopify  | 5925169            |
***

**6. What is the percentage of sales for Retail vs Shopify for each month?**

````sql
    WITH monthly_cte AS (
      SELECT
      	year_number,
      	month_number,
      	platform,
      	SUM(sales) AS total_sales
      FROM clean_weekly_sales
      GROUP BY 1, 2, 3
    )
    
      
    SELECT 
      year_number, 
      month_number, 
      ROUND(100 * MAX 
        (CASE 
          WHEN platform = 'Retail' THEN total_sales ELSE NULL END) 
        / SUM(total_sales),2) AS retail_percentage,
      ROUND(100 * MAX 
        (CASE 
          WHEN platform = 'Shopify' THEN total_sales ELSE NULL END)
        / SUM(total_sales),2) AS shopify_percentage
    FROM monthly_cte
    GROUP BY year_number, month_number
    ORDER BY year_number, month_number
    LIMIT 7;
````

### Answer:
| year_number | month_number | retail_percentage | shopify_percentage |
| ----------- | ------------ | ----------------- | ------------------ |
| 2018        | 3            | 97.92             | 2.08               |
| 2018        | 4            | 97.93             | 2.07               |
| 2018        | 5            | 97.73             | 2.27               |
| 2018        | 6            | 97.76             | 2.24               |
| 2018        | 7            | 97.75             | 2.25               |
| 2018        | 8            | 97.71             | 2.29               |
| 2018        | 9            | 97.68             | 2.32               |
***

**7. What is the percentage of sales by demographic for each year in the dataset?**

````sql
    WITH yearly_cte AS (
      SELECT
      	year_number,
      	demographic,
      	SUM(sales) AS total_sales
      FROM clean_weekly_sales
      GROUP BY 1, 2
    )
    
      
    SELECT 
      year_number, 
      ROUND(100 * MAX 
        (CASE 
          WHEN demographic = 'Families' THEN total_sales ELSE NULL END) 
        / SUM(total_sales),2) AS family_percentage,
      ROUND(100 * MAX 
        (CASE 
          WHEN demographic = 'Couples' THEN total_sales ELSE NULL END)
        / SUM(total_sales),2) AS couple_percentage,
      ROUND(100 * MAX 
        (CASE 
          WHEN demographic = 'unknown' THEN total_sales ELSE NULL END)
        / SUM(total_sales),2) AS unknown_percentage
    FROM yearly_cte
    GROUP BY year_number;
````

### Answer:
| year_number | family_percentage | couple_percentage | unknown_percentage |
| ----------- | ----------------- | ----------------- | ------------------ |
| 2019        | 32.47             | 27.28             | 40.25              |
| 2018        | 31.99             | 26.38             | 41.63              |
| 2020        | 32.73             | 28.72             | 38.55              |
***

**8. Which age_band and demographic values contribute the most to Retail sales?**

````sql
    SELECT
    	age_band,
        demographic,
        SUM(sales) AS total_sales,
        ROUND(100 * SUM(sales)::NUMERIC / SUM(SUM(sales)) OVER (), 1) AS percentage
    FROM clean_weekly_sales
    WHERE platform = 'Retail'
    GROUP BY 1, 2
    ORDER BY 3 DESC;
````

### Answer:
| age_band     | demographic | total_sales | percentage |
| ------------ | ----------- | ----------- | ---------- |
| unknown      | unknown     | 16067285533 | 40.5       |
| Retirees     | Families    | 6634686916  | 16.7       |
| Retirees     | Couples     | 6370580014  | 16.1       |
| Middle Aged  | Families    | 4354091554  | 11.0       |
| Young Adults | Couples     | 2602922797  | 6.6        |
| Middle Aged  | Couples     | 1854160330  | 4.7        |
| Young Adults | Families    | 1770889293  | 4.5        |
***

**9. Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?**

````sql
    SELECT 
      year_number, 
      platform, 
      ROUND(AVG(avg_transactions),0) AS avg_transaction_row, 
      SUM(sales) / sum(transactions) AS avg_transaction_group
    FROM clean_weekly_sales
    GROUP BY 1, 2
    ORDER BY 1, 2;
````

### Answer:
| year_number | platform | avg_transaction_row | avg_transaction_group |
| ----------- | -------- | ------------------- | --------------------- |
| 2018        | Retail   | 43                  | 36                    |
| 2018        | Shopify  | 188                 | 192                   |
| 2019        | Retail   | 42                  | 36                    |
| 2019        | Shopify  | 178                 | 183                   |
| 2020        | Retail   | 41                  | 36                    |
| 2020        | Shopify  | 175                 | 179                   |
***



## Before & After Analysis

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
