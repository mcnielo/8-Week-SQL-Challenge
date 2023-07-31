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

````
### Results:


***

## Case Study Questions

***

## Data Cleansing Steps

**1. How many unique nodes are there on the Data Bank system?**

```sql

```
### Results:

***




## Data Exploration

**1. What day of the week is used for each week_date value?**

````sql

````

### Answer:

***

**2. What range of week numbers are missing from the dataset?**

````sql

````

### Answer:

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
