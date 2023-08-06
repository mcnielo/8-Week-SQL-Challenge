# SQL Challenge Case Study #5: Data Mart
<div align="center"><img src="https://8weeksqlchallenge.com/images/case-study-designs/5.png" alt="Image" width="500" height="520"></div>

***

## Introduction
Danny, the owner of Data Mart, is seeking assistance in analyzing the sales performance of his online supermarket, specializing in fresh produce. In June 2020, Data Mart implemented large-scale sustainable packaging methods for all its products from the farm to the customer. Danny wants to determine the measurable effect of this change on the company's sales performance and its various business areas. The key questions he needs help answering are:

- What was the quantifiable impact of the sustainability changes introduced in June 2020 on Data Mart's sales performance?
- Which platforms, regions, segments, and customer types were most affected by this change?
- How can the company approach future updates related to sustainability to minimize any negative impact on sales?

***

## Business Problem
Danny, the owner of Data Mart, is looking for support to analyze the sales performance of his online supermarket, which specializes in fresh produce. Data Mart made significant changes in June 2020 by implementing sustainable packaging methods for all its products from the farm to the customer. The main objectives are to determine the measurable impact of these changes on the company's sales performance, identify the platforms, regions, segments, and customer types most affected by the shift, and develop strategies for future sustainability updates that minimize any adverse effects on sales.

***

## Datasets

### Table 1: weekly_sales

For this case study there is only a single table: data_mart.weekly_sales

The columns are pretty self-explanatory based on the column names but here are some further details about the dataset:

- Data Mart has international operations using a multi-region strategy
- Data Mart has both, a retail and online platform in the form of a Shopify store front to serve their customers
- Customer segment and customer_type data relates to personal age and demographics information that is shared with Data Mart
transactions is the count of unique purchases made through Data Mart and sales is the actual dollar amount of purchases
- Each record in the dataset is related to a specific aggregated slice of the underlying sales data rolled up into a week_date value which represents the start of the sales week.


````sql
    SELECT *
    FROM data_mart.weekly_sales
    LIMIT 10;
````
### Results:
| week_date | region | platform | segment | customer_type | transactions | sales    |
| --------- | ------ | -------- | ------- | ------------- | ------------ | -------- |
| 31/8/20   | ASIA   | Retail   | C3      | New           | 120631       | 3656163  |
| 31/8/20   | ASIA   | Retail   | F1      | New           | 31574        | 996575   |
| 31/8/20   | USA    | Retail   | null    | Guest         | 529151       | 16509610 |
| 31/8/20   | EUROPE | Retail   | C1      | New           | 4517         | 141942   |
| 31/8/20   | AFRICA | Retail   | C2      | New           | 58046        | 1758388  |
| 31/8/20   | CANADA | Shopify  | F2      | Existing      | 1336         | 243878   |
| 31/8/20   | AFRICA | Shopify  | F3      | Existing      | 2514         | 519502   |
| 31/8/20   | ASIA   | Shopify  | F1      | Existing      | 2158         | 371417   |
| 31/8/20   | AFRICA | Shopify  | F2      | New           | 318          | 49557    |
| 31/8/20   | AFRICA | Retail   | C3      | New           | 111032       | 3888162  |
***

## Case Study Questions

***

## Data Cleansing Steps

In a single query, perform the following operations and generate a new table in the data_mart schema named clean_weekly_sales:

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
