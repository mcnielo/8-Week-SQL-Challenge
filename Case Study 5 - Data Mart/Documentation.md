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
    WHERE cw.week_number IS NULL;
````

### Answer:
| week_number |
| ----------- |
| 1           |
| 2           |
| 3           |
| 4           |
| 5           |
| 6           |
| 7           |
| 8           |
| 9           |
| 10          |
| 11          |
| 12          |
| 37          |
| 38          |
| 39          |
| 40          |
| 41          |
| 42          |
| 43          |
| 44          |
| 45          |
| 46          |
| 47          |
| 48          |
| 49          |
| 50          |
| 51          |
| 52          |

***

**3. How many total transactions were there for each year in the dataset?**

````sql

````

### Answer:

***

**4. What is the total sales for each region for each month?**

````sql

````

### Answer:

***

**5. What is the total count of transactions for each platform?**

````sql

````

### Answer:

***

**6. What is the percentage of sales for Retail vs Shopify for each month?**

````sql

````

### Answer:

***

**7. What is the percentage of sales by demographic for each year in the dataset?**

````sql

````

### Answer:

***

**8. Which age_band and demographic values contribute the most to Retail sales?**

````sql

````

### Answer:

***

**9. Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?**

````sql

````

### Answer:

***



## Before & After Analysis

**1. What is the total sales for the 4 weeks before and after 2020-06-15? What is the growth or reduction rate in actual values and percentage of sales?**

````sql

````

### Answer:

***

**2. What about the entire 12 weeks before and after?**

````sql

````

### Answer:

***

**3. How do the sale metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?**

````sql

````

### Answer:

***

## Resources and References:

I obtained my sources, dataset, and information from the following website: [Here](https://8weeksqlchallenge.com/case-study-4/).

***
