# SQL Challenge Case Study #3: Foodie-Fi
<div align="center"><img src="https://8weeksqlchallenge.com/images/case-study-designs/3.png" alt="Image" width="500" height="520"></div>

***

## Introduction
Danny identified a market gap and ventured into the subscription-based business model. His startup, Foodie-Fi, launched in 2020, offering a unique streaming service dedicated solely to food-related content, akin to Netflix but exclusively focused on cooking shows. Through monthly and annual subscriptions, customers gained unlimited on-demand access to a diverse range of exclusive food videos from around the world. With a data-driven approach, Danny aimed to make informed investment decisions and introduce new features based on data analysis. This case study emphasizes the utilization of subscription-style digital data to address crucial business inquiries.

***

## Business Problem
Danny and his friends embarked on the launch of Foodie-Fi, a startup that offers monthly and annual subscription plans, granting customers unrestricted on-demand access to a vast collection of exclusive food videos spanning the globe. This case study delves into the utilization of subscription-style digital data to address vital business inquiries pertaining to customer journey, payments, and overall business performance.

***

### Table 1: plans

When customers initially sign up for Foodie-Fi, they have the flexibility to choose their preferred plan.

The Basic plan offers limited access to streaming videos and is available on a monthly basis at a cost of $9.90.

The Pro plan, on the other hand, provides customers with unlimited watch time and the ability to download videos for offline viewing. Pro plans start at $19.90 per month or $199 for an annual subscription.

During the sign-up process, customers can enjoy a 7-day free trial, after which they will automatically be enrolled in the Pro monthly subscription plan unless they choose to cancel, switch to the Basic plan, or upgrade to an annual Pro plan at any point during the trial.

In the event that customers decide to cancel their Foodie-Fi service, they will have a churn plan record with a null price. However, their plan will remain active until the end of the current billing period.


````sql
    SELECT * FROM foodie_fi.plans;
````
### Results:
| plan_id | plan_name     | price  |
| ------- | ------------- | ------ |
| 0       | trial         | 0.00   |
| 1       | basic monthly | 9.90   |
| 2       | pro monthly   | 19.90  |
| 3       | pro annual    | 199.00 |
| 4       | churn         |        |

***

### Table 2: subsriptions

The start date of each customer's subscription plan is recorded accurately, indicating when their specific plan_id becomes active.

If customers choose to downgrade from a pro plan or cancel their subscription, the higher plan will continue until the current billing period ends. The start_date in the subscriptions table will reflect the date when the actual plan change takes place.

On the other hand, when customers upgrade their account from a basic plan to a pro or annual pro plan, the higher plan will be immediately activated.

In the event of customer churn, their access will be retained until the conclusion of their current billing period. However, the start_date will technically indicate the day they made the decision to cancel their service.

````sql
    SELECT
    	customer_id,
        plan_id,
        start_date
    FROM foodie_fi.subscriptions
    WHERE customer_id IN (1, 2, 3, 4, 10, 9, 8 ,7);
````
### Results:
| customer_id | plan_id | start_date               |
| ----------- | ------- | ------------------------ |
| 1           | 0       | 2020-08-01T00:00:00.000Z |
| 1           | 1       | 2020-08-08T00:00:00.000Z |
| 2           | 0       | 2020-09-20T00:00:00.000Z |
| 2           | 3       | 2020-09-27T00:00:00.000Z |
| 3           | 0       | 2020-01-13T00:00:00.000Z |
| 3           | 1       | 2020-01-20T00:00:00.000Z |
| 4           | 0       | 2020-01-17T00:00:00.000Z |
| 4           | 1       | 2020-01-24T00:00:00.000Z |
| 4           | 4       | 2020-04-21T00:00:00.000Z |
| 7           | 0       | 2020-02-05T00:00:00.000Z |
| 7           | 1       | 2020-02-12T00:00:00.000Z |
| 7           | 2       | 2020-05-22T00:00:00.000Z |
| 8           | 0       | 2020-06-11T00:00:00.000Z |
| 8           | 1       | 2020-06-18T00:00:00.000Z |
| 8           | 2       | 2020-08-03T00:00:00.000Z |
| 9           | 0       | 2020-12-07T00:00:00.000Z |
| 9           | 3       | 2020-12-14T00:00:00.000Z |
| 10          | 0       | 2020-09-19T00:00:00.000Z |
| 10          | 2       | 2020-09-26T00:00:00.000Z |

***

## Case Study Questions
***
## Customer Journey
Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer's onboarding journey.

Try to keep it as short as possible - you may also want to run some sort of join to make your explanations a bit easier!

```sql
    DROP TABLE IF EXISTS customer_journey;
    CREATE TEMP TABLE customer_journey AS (
      SELECT
      	s.customer_id,
      	s.plan_id,
      	p.plan_name,
      	p.price,
      	s.start_date
      FROM foodie_fi.subscriptions s
      JOIN foodie_fi.plans p ON s.plan_id = p.plan_id
    );

    SELECT
    	customer_id,
        plan_name,
        start_date
    FROM customer_journey
    WHERE customer_id IN (1, 2, 3, 4, 10, 9, 8 ,7)
    ORDER BY customer_id, plan_id ASC;

```
### Results:
| customer_id | plan_name     | start_date               |
| ----------- | ------------- | ------------------------ |
| 1           | trial         | 2020-08-01T00:00:00.000Z |
| 1           | basic monthly | 2020-08-08T00:00:00.000Z |
| 2           | trial         | 2020-09-20T00:00:00.000Z |
| 2           | pro annual    | 2020-09-27T00:00:00.000Z |
| 3           | trial         | 2020-01-13T00:00:00.000Z |
| 3           | basic monthly | 2020-01-20T00:00:00.000Z |
| 4           | trial         | 2020-01-17T00:00:00.000Z |
| 4           | basic monthly | 2020-01-24T00:00:00.000Z |
| 4           | churn         | 2020-04-21T00:00:00.000Z |
| 7           | trial         | 2020-02-05T00:00:00.000Z |
| 7           | basic monthly | 2020-02-12T00:00:00.000Z |
| 7           | pro monthly   | 2020-05-22T00:00:00.000Z |
| 8           | trial         | 2020-06-11T00:00:00.000Z |
| 8           | basic monthly | 2020-06-18T00:00:00.000Z |
| 8           | pro monthly   | 2020-08-03T00:00:00.000Z |
| 9           | trial         | 2020-12-07T00:00:00.000Z |
| 9           | pro annual    | 2020-12-14T00:00:00.000Z |
| 10          | trial         | 2020-09-19T00:00:00.000Z |
| 10          | pro monthly   | 2020-09-26T00:00:00.000Z |


- Customer 1 started with a trial plan on 2020-08-01 and then switched to a basic monthly plan on 2020-08-08.
- Customer 2 began with a trial plan on 2020-09-20 and later upgraded to a pro annual plan on 2020-09-27.
- Customer 3 started with a trial plan on 2020-01-13 and transitioned to a basic monthly plan on 2020-01-20.
- Customer 4 had a trial plan on 2020-01-17, followed by a basic monthly plan on 2020-01-24. However, this customer churned on 2020-04-21.
- Customer 7 began with a trial plan on 2020-02-05, switched to a basic monthly plan on 2020-02-12, and then upgraded to a pro monthly plan on 2020-05-22.
- Customer 8 had a trial plan on 2020-06-11, transitioned to a basic monthly plan on 2020-06-18, and later upgraded to a pro monthly plan on 2020-08-03.
- Customer 9 started with a trial plan on 2020-12-07 and upgraded to a pro annual plan on 2020-12-14.
- Customer 10 began with a trial plan on 2020-09-19 and then switched to a pro monthly plan on 2020-09-26.

## Data Analysis Questions

**1. How many customers has Foodie-Fi ever had?**

````sql
    SELECT COUNT(DISTINCT customer_id) AS Total_Customers
    FROM foodie_fi.subscriptions;
````

### Answer:
| total_customers |
| --------------- |
| 1000            |
***

**2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value?**

````sql
    SELECT
    	TO_CHAR(start_date, 'Month') AS Month,
    	COUNT(DISTINCT customer_id) AS Trial_Per_Month
    FROM foodie_fi.subscriptions
    WHERE plan_id = 0
    GROUP BY Month
    ORDER BY TO_DATE(TO_CHAR(start_date, 'Month'), 'Month');
````

### Answer:
| month     | trial_per_month |
| --------- | --------------- |
| January   | 88              |
| February  | 68              |
| March     | 94              |
| April     | 81              |
| May       | 88              |
| June      | 79              |
| July      | 89              |
| August    | 88              |
| September | 87              |
| October   | 79              |
| November  | 75              |
| December  | 84              |

***

**3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name?**

````sql
    SELECT
    	COUNT(plan_name) AS plans_count,
        plan_name
    FROM customer_journey
    WHERE start_date >= '2020-01-01'
    GROUP BY plan_name;
````

### Answer:
| plans_count | plan_name     |
| ----------- | ------------- |
| 258         | pro annual    |
| 1000        | trial         |
| 307         | churn         |
| 539         | pro monthly   |
| 546         | basic monthly |


***

**4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?**

````sql
    DROP TABLE IF EXISTS churn_count;
    CREATE TEMP TABLE churn_count AS (
      SELECT COUNT(DISTINCT customer_id) AS churn_count
      FROM foodie_fi.subscriptions
      WHERE plan_id = 4
    );

    DROP TABLE IF EXISTS customer_count;
    CREATE TEMP TABLE customer_count AS (
      SELECT COUNT(DISTINCT customer_id) AS customer_count
      FROM foodie_fi.subscriptions
    );

    SELECT ROUND((cc.churn_count::NUMERIC / cus.customer_count::NUMERIC)*100, 1) AS percentage
    FROM churn_count cc, customer_count cus;
````

### Answer:
| percentage |
| ---------- |
| 30.7       |


***

**5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?**

````sql
    DROP TABLE IF EXISTS trial_only;
    CREATE TEMP table trial_only AS (
    	WITH set_row_number as (
    		SELECT DISTINCT customer_id,
    			plan_name,
    			plan_id,
    			row_number() OVER (
    				PARTITION BY customer_id
    				ORDER BY plan_id
    			) AS rn
    		FROM customer_journey
    		ORDER BY customer_id,
    			plan_id
    	),
    	get_row_number AS (
    		SELECT rn,
    			plan_name
    		from set_row_number
    		WHERE rn < 3
    	)
    	SELECT sum(
    			CASE
    				WHEN rn = 2
    				AND plan_name = 'churn' THEN 1
    				ELSE 0
    			end
    		) AS trial_users_only
    	from get_row_number
    );

    SELECT customer_count,
    	trial_users_only,
    	round(
    		(
    			trial_users_only::numeric / customer_count::numeric * 100
    		),
    		1
    	) AS trial_churn_perc
    FROM trial_only,
    	customer_count;

    SELECT churn_count,
    	trial_users_only,
    	round(
    		(trial_users_only::numeric / churn_count::numeric * 100),
    		1
    	) AS trial_churn_perc
    FROM trial_only,
    	churn_count;
````

### Answer: Result from total customers
| customer_count | trial_users_only | trial_churn_perc |
| -------------- | ---------------- | ---------------- |
| 1000           | 92               | 9.2              |

### Answer: Result from total number of churn
| churn_count | trial_users_only | trial_churn_perc |
| ----------- | ---------------- | ---------------- |
| 307         | 92               | 30.0             |

***

**6. What is the number and percentage of customer plans after their initial free trial?**

```sql
    SELECT
    	plan_name,
        COUNT(plan_name) AS plan_count,
        ROUND(
          (COUNT(plan_name)::numeric / customer_count::numeric) * 100, 2
          ) AS plan_percentage
    FROM
    	(
          SELECT
          	DISTINCT customer_id,
          	plan_name,
          	plan_id, 
          	ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY plan_id) AS rn
          FROM customer_journey
          ORDER BY customer_id, plan_id
        ) AS a,
        customer_count
    WHERE rn = 2
    GROUP BY plan_name, customer_count;
```

### Answer:
| plan_name     | plan_count | plan_percentage |
| ------------- | ---------- | --------------- |
| pro annual    | 37         | 3.70            |
| pro monthly   | 325        | 32.50           |
| basic monthly | 546        | 54.60           |
| churn         | 92         | 9.20            |

***

***7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?**

````sql
    SELECT
    	COUNT(customer_id) AS count,
    	plan_name,
        plan_id,
        ROUND(
          (COUNT(plan_name)::numeric / customer_count::numeric) * 100, 2
          ) AS plan_percentage
    FROM
    	(
          SELECT
          	DISTINCT customer_id,
          	plan_name,
          	plan_id, 
          	start_date,
          	ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY plan_id DESC) AS rn
          FROM customer_journey
          WHERE start_date <= '2020-12-31' OR start_date BETWEEN '2020-12-25' AND '2020-12-31'
          GROUP BY customer_id, plan_name, plan_id, start_date
        ) AS tmp,
        customer_count
    WHERE rn = 1
    GROUP BY customer_count, plan_name, plan_id
    ORDER BY plan_id;
````

### Answer:
| count | plan_name     | plan_id | plan_percentage |
| ----- | ------------- | ------- | --------------- |
| 19    | trial         | 0       | 1.90            |
| 224   | basic monthly | 1       | 22.40           |
| 326   | pro monthly   | 2       | 32.60           |
| 195   | pro annual    | 3       | 19.50           |
| 236   | churn         | 4       | 23.60           |
***

**8. How many customers have upgraded to an annual plan in 2020?**

```sql
    SELECT 
    	COUNT(customer_id) AS annual_count
    FROM foodie_fi.subscriptions
    WHERE plan_id = 3 AND start_date <= '2021-01-01';
```

### Answer:
| annual_count |
| ------------ |
| 195          |

***

**9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?**

```sql

    DROP TABLE IF EXISTS get_join_date;
    CREATE TEMP TABLE get_join_date AS (
    	SELECT DISTINCT customer_id,
    		min(start_date) AS join_date
    	FROM customer_journey
    	GROUP BY customer_id
    	ORDER BY customer_id
    );

    DROP TABLE IF EXISTS get_aplan_date;
    CREATE TEMP TABLE get_aplan_date AS (
    	SELECT DISTINCT customer_id,
    		max(start_date) AS aplan_date
    	FROM customer_journey
    	WHERE plan_id = 3
    	GROUP BY customer_id
    	ORDER BY customer_id
    );

    SELECT round(avg(ad.aplan_date - jd.join_date), 2) AS avg_days
    FROM get_join_date AS jd
    	JOIN get_aplan_date AS ad ON jd.customer_id = ad.customer_id;
    
```

### Answer:
| avg_days |
| -------- |
| 104.62   |

***

**10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)?**

```sql
    SELECT 
    	CASE
    		WHEN date_periods = 1 THEN (date_periods - 1) || ' - ' || (date_periods * 30) || ' days' 
    		ELSE ((date_periods - 1) * 30 | 1) || ' - ' || (date_periods * 30) || ' days'
    	END AS time_period,
    	count(customer_id) AS customer_count,
    	round(avg(aplan_date - join_date), 2) AS avg_days
    FROM (
    		SELECT jd.customer_id,
    			ad.aplan_date,
    			jd.join_date,
    			((ad.aplan_date - jd.join_date) / 30 | 1) AS date_periods
    		FROM get_join_date AS jd
    			JOIN get_aplan_date AS ad ON jd.customer_id = ad.customer_id
    	) AS tmp
    GROUP BY date_periods
    ORDER BY date_periods;
```

### Answer:
| time_period    | customer_count | avg_days |
| -------------- | -------------- | -------- |
| 0 - 30 days    | 73             | 20.60    |
| 61 - 90 days   | 68             | 85.78    |
| 121 - 150 days | 78             | 145.83   |
| 181 - 210 days | 31             | 194.71   |
| 241 - 270 days | 6              | 261.83   |
| 301 - 330 days | 2              | 336.50   |

***

**10. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?**

```sql
    SELECT count(customer_id) AS cust_downgrade_count
    from (
    		SELECT customer_id,
    			plan_name,
    			lead(plan_name) OVER (
    				PARTITION BY customer_id
    				ORDER BY start_date
    			) AS downgrade
    		FROM customer_journey
    		WHERE extract(
    				YEAR
    				FROM start_date
    			) = '2020'
    	) AS tmp
    WHERE plan_name = 'pro monthly'
    	AND downgrade = 'basic monthly';
```

### Answer:
| cust_downgrade_count |
| -------------------- |
| 0                    |

***
## Challenge Payment Question

**The Foodie-Fi team wants you to create a new payments table for the year 2020 that includes amounts paid by each customer in the subscriptions table with the following requirements:**

- Monthly payments always occur on the same day of month as the original start_date of any monthly paid plan.
- Upgrades from basic to monthly or pro plans are reduced by the current paid amount in that month and start immediately.
- Upgrades from pro monthly to pro annual are paid at the end of the current billing period and also starts at the end of the month period.
- Once a customer churns they will no longer make payments.


```sql
    DROP TABLE IF EXISTS customer_payments;
    CREATE TEMP TABLE customer_payments AS (
    	SELECT customer_id,
    		plan_id,
    		plan_name,
    		start_date,
    		CASE
    			WHEN plan_id = 1 THEN 9.90
    			WHEN plan_id = 2 THEN 19.90
    			WHEN plan_id = 3 THEN 199.00
    			ELSE 0
    		END AS amount,
    		lead(plan_name) OVER (
    			PARTITION BY customer_id
    			ORDER BY start_date
    		) AS next_plan
    	FROM customer_journey
    	WHERE plan_id <> 0
    		AND start_date BETWEEN '2020-01-01' AND '2020-12-31'
    );

    SELECT customer_id,
    	plan_id,
    	plan_name,
    	payment_date,
    	CASE
    		WHEN rn1 > rn2 
    		AND lag(plan_id) OVER (
    			PARTITION BY customer_id
    			ORDER BY payment_date
    		) < plan_id 
    		AND EXTRACT(
    			MONTH
    			FROM lag(payment_date) OVER (
    					PARTITION BY customer_id
    					ORDER BY payment_date
    				)
    		) = extract(
    			MONTH
    			FROM payment_date
    		) 
    		THEN amount - lag(amount) OVER (
    			PARTITION BY customer_id
    			ORDER BY payment_date
    		)
    		ELSE amount
    	END AS amount,
    	row_number() OVER (PARTITION BY customer_id) AS payment_ord
    from (
    		SELECT customer_id,
    			plan_id,
    			plan_name,
    			generate_series(start_date, end_date, '1 month')::date AS payment_date,
    			amount,
    			row_number() OVER (
    				PARTITION BY customer_id
    				ORDER BY start_date
    			) AS rn1,
    			row_number() OVER (
    				PARTITION BY customer_id
    				ORDER BY start_date desc
    			) AS rn2
    		from (
    				SELECT customer_id,
    					plan_id,
    					plan_name,
    					amount,
    					start_date,
    					CASE
    						
    						WHEN next_plan IS NULL
    						AND plan_id != 3 THEN '2020-12-31' 
    						WHEN plan_id = 2
    						AND next_plan = 'pro annual' THEN (
    							lead(start_date) OVER (
    								PARTITION BY customer_id
    								ORDER BY start_date
    							) - interval '1 month'
    						) 
    						WHEN next_plan = 'churn'
    						OR next_plan = 'pro monthly'
    						OR next_plan = 'pro annual' THEN lead(start_date) OVER (
    							PARTITION BY customer_id
    							ORDER BY start_date
    						) 
    						WHEN plan_id = 3 THEN start_date
    					END AS end_date,
    					next_plan
    				FROM customer_payments
    			) AS tmp1
    		WHERE plan_id != 4
    	) AS tmp2 
    WHERE customer_id IN (1, 2, 13, 15, 16, 18, 19)
    ORDER BY customer_id;
```

### Answer:
| customer_id | plan_id | plan_name     | payment_date             | amount | payment_ord |
| ----------- | ------- | ------------- | ------------------------ | ------ | ----------- |
| 1           | 1       | basic monthly | 2020-08-08T00:00:00.000Z | 9.90   | 1           |
| 1           | 1       | basic monthly | 2020-09-08T00:00:00.000Z | 9.90   | 2           |
| 1           | 1       | basic monthly | 2020-10-08T00:00:00.000Z | 9.90   | 3           |
| 1           | 1       | basic monthly | 2020-11-08T00:00:00.000Z | 9.90   | 4           |
| 1           | 1       | basic monthly | 2020-12-08T00:00:00.000Z | 9.90   | 5           |
| 2           | 3       | pro annual    | 2020-09-27T00:00:00.000Z | 199.00 | 1           |
| 13          | 1       | basic monthly | 2020-12-22T00:00:00.000Z | 9.90   | 1           |
| 15          | 2       | pro monthly   | 2020-03-24T00:00:00.000Z | 19.90  | 1           |
| 15          | 2       | pro monthly   | 2020-04-24T00:00:00.000Z | 19.90  | 2           |
| 16          | 1       | basic monthly | 2020-06-07T00:00:00.000Z | 9.90   | 1           |
| 16          | 1       | basic monthly | 2020-07-07T00:00:00.000Z | 9.90   | 2           |
| 16          | 1       | basic monthly | 2020-08-07T00:00:00.000Z | 9.90   | 3           |
| 16          | 1       | basic monthly | 2020-09-07T00:00:00.000Z | 9.90   | 4           |
| 16          | 1       | basic monthly | 2020-10-07T00:00:00.000Z | 9.90   | 5           |
| 16          | 3       | pro annual    | 2020-10-21T00:00:00.000Z | 189.10 | 6           |
| 18          | 2       | pro monthly   | 2020-07-13T00:00:00.000Z | 19.90  | 1           |
| 18          | 2       | pro monthly   | 2020-08-13T00:00:00.000Z | 19.90  | 2           |
| 18          | 2       | pro monthly   | 2020-09-13T00:00:00.000Z | 19.90  | 3           |
| 18          | 2       | pro monthly   | 2020-10-13T00:00:00.000Z | 19.90  | 4           |
| 18          | 2       | pro monthly   | 2020-11-13T00:00:00.000Z | 19.90  | 5           |
| 18          | 2       | pro monthly   | 2020-12-13T00:00:00.000Z | 19.90  | 6           |
| 19          | 2       | pro monthly   | 2020-06-29T00:00:00.000Z | 19.90  | 1           |
| 19          | 2       | pro monthly   | 2020-07-29T00:00:00.000Z | 19.90  | 2           |
| 19          | 3       | pro annual    | 2020-08-29T00:00:00.000Z | 199.00 | 3           |


## Resources and References:

I obtained my sources, dataset, and information from the following website: [Here](https://8weeksqlchallenge.com/case-study-3/).

***
