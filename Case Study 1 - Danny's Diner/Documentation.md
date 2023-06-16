# SQL Challenge Case Study #1: Danny's Diner 
<img src="[Case Study 1 - Danny's Diner/CaseStudy1.png](https://github.com/mcnielo/8-Week-SQL-Challenge/blob/20b1bfb22ef04a72abfa451168cfbff0f750808f/Case%20Study%201%20-%20Danny's%20Diner/CaseStudy1.png)" alt="Image" width="500" height="520">

***

## Introduction
Danny, a passionate lover of Japanese food, took a leap of faith in early 2021 by opening a charming little restaurant called Danny's Diner. Specializing in his three favorite dishes, sushi, curry, and ramen, Danny embarked on this risky venture with enthusiasm and a desire to provide a unique dining experience.

***

## Business Problem
However, Danny's Diner is facing a challenge and requires assistance to navigate the restaurant industry successfully. Although the restaurant has collected basic data during its initial months of operation, Danny and his team lack the expertise to utilize this data effectively. They are unaware of how to leverage the available data to make informed business decisions and optimize their operations.

***

## Dataset Description

To address this challenge, Danny has provided three key datasets that contain valuable information for analysis and insights. These datasets are as follows:

### Table 1: sales
The sales table captures customer-level purchases, including the customer_id, order_date, and product_id of each menu item ordered. It provides details about when customers visited the restaurant and what menu items they ordered.

### Table 2: menu
The menu table maps the product_id to the corresponding product_name and price of each menu item. It helps in understanding the specific menu items available and their associated prices.

### Table 3: members
The members table records the join_date for each customer_id who joined the beta version of the Danny's Diner loyalty program. It provides insights into when customers became part of the loyalty program.

These datasets are stored within the "dannys_diner" database schema. It is important to reference this schema in SQL scripts to explore the data and answer the questions posed in this case study.

Through the analysis of this dataset, Danny aims to gain insights into his customers' visiting patterns, their spending habits, and their favorite menu items. By understanding his customers on a deeper level, Danny can personalize the dining experience and make informed decisions regarding the expansion of the existing customer loyalty program.

Note: Due to privacy concerns, the provided dataset contains a sample of customer data, but it is sufficient to write fully functioning SQL queries and generate basic datasets for analysis.

Now, armed with these datasets, it's time to dive into the data and help Danny gain valuable insights to run Danny's Diner more efficiently and deliver a personalized experience to his loyal customers.

***

## Case Study Questions

**1. What is the total amount each customer spent at the restaurant?**

````sql
SELECT s.customer_id, SUM(m.price) as TotalSale
FROM dannys_diner.sales s
JOIN dannys_diner.menu m ON s.product_id = m.product_id
GROUP BY customer_id
ORDER BY customer_id;
````

### Answer:
| customer_id | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

- Customer A spent $76.
- Customer B spent $74.
- Customer C spent $36.

***

**2. How many days has each customer visited the restaurant?**

````sql
SELECT 
SELECT customer_id, COUNT(DISTINCT order_date) as TotalVisit
FROM dannys_diner.sales
GROUP BY customer_id;
````

### Answer:
| customer_id | visit_count |
| ----------- | ----------- |
| A           | 4          |
| B           | 6          |
| C           | 2          |

- Customer A visited 4 times.
- Customer B visited 6 times.
- Customer C visited 2 times.

***

**3. What was the first item from the menu purchased by each customer?**

````sql
WITH sub AS (
	SELECT s.customer_id, s.order_date, m.product_name, DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS RANK
	FROM dannys_diner.sales s
  	JOIN dannys_diner.menu m ON s.product_id = m.product_id
)
SELECT customer_id, product_name
FROM sub
WHERE RANK = 1
GROUP BY customer_id, product_name;
````

### Answer:
| customer_id | product_name | 
| ----------- | ----------- |
| A           | curry        | 
| A           | sushi        | 
| B           | curry        | 
| C           | ramen        |

- Customer A placed an order for both curry and sushi simultaneously, making them the first items in the order.
- Customer B's first order is curry.
- Customer C's first order is ramen.

***

**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**

````sql
SELECT m.product_name, COUNT(s.product_id) as total
from dannys_diner.sales s
JOIN dannys_diner.menu m on s.product_id = m.product_id
GROUP BY product_name limit 1;
````

### Answer:
| most_purchased | product_name | 
| ----------- | ----------- |
| 8       | ramen |


- Ramen, being the most frequently ordered item on the menu, has been purchased a eight times. It's undeniably delicious!

***

**5. Which item was the most popular for each customer?**

````sql
WITH sub AS (
  SELECT s.customer_id, m.product_name, count(s.product_id) as count,
  DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY count(s.customer_id) DESC) AS Rank
  FROM dannys_diner.sales s
  JOIN dannys_diner.menu m ON s.product_id = m.product_id
  group by s.customer_id, m.product_name
)
SELECT customer_id, product_name, count
FROM sub
WHERE rank = 1;
````

### Answer:
| customer_id | product_name | order_count |
| ----------- | ---------- |------------  |
| A           | ramen        |  3   |
| B           | sushi        |  2   |
| B           | curry        |  2   |
| B           | ramen        |  2   |
| C           | ramen        |  3   |

- Customer A and C's favourite item is ramen.
- Customer B enjoys all items on the menu. He/she is a true foodie, sounds like me.

***

**6. Which item was purchased first by the customer after they became a member?**

```sql
WITH sub AS (
  SELECT mb.customer_id, s.product_id, ROW_NUMBER() OVER (PARTITION BY mb.customer_id ORDER BY s.order_date) AS rn
  FROM dannys_diner.members mb
  JOIN dannys_diner.sales s ON mb.customer_id = s.customer_id
  WHERE s.order_date > mb.join_date
)

SELECT customer_id, product_name
FROM sub
JOIN dannys_diner.menu m ON sub.product_id = m.product_id
WHERE rn = 1
ORDER BY customer_id ASC ;
```

### Answer:
| customer_id | product_name |
| ----------- | ---------- |
| A           | ramen        |
| B           | sushi        |

- Customer A's first order as a member is ramen.
- Customer B's first order as a member is sushi.

***

**7. Which item was purchased just before the customer became a member?**

````sql
WITH sub AS (
  SELECT mb.customer_id, s.product_id, ROW_NUMBER() OVER (PARTITION BY mb.customer_id ORDER BY s.order_date DESC) AS rn
  FROM dannys_diner.members mb
  JOIN dannys_diner.sales s ON mb.customer_id = s.customer_id
  AND s.order_date < mb.join_date
)

SELECT sub.customer_id, m.product_name
FROM sub
JOIN dannys_diner.menu m ON sub.product_id = m.product_id
WHERE rn = 1
ORDER BY customer_id;
````

### Answer:
| customer_id | product_name |
| ----------- | ---------- |
| A           | sushi        |
| B           | sushi        |

- Both customers' last order before becoming members are sushi.

***

**8. What is the total items and amount spent for each member before they became a member?**

```sql
SELECT s.customer_id, COUNT(s.product_id) AS total_amount, SUM(m.price) AS total_price
FROM dannys_diner.sales s
JOIN dannys_diner.members mb ON s.customer_id = mb.customer_id AND s.order_date < mb.join_date 
JOIN dannys_diner.menu m ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id;
```

### Answer:
| customer_id | total_items | total_sales |
| ----------- | ---------- |----------  |
| A           | 2 |  25       |
| B           | 3 |  40       |

Before becoming members,
- Customer A spent $25 on 2 items.
- Customer B spent $40 on 3 items.

***

**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier — how many points would each customer have?**

```sql
WITH sub AS (
 SELECT m.product_id,
  CASE
  	WHEN m.product_id = 1 THEN price * 20
  	ELSE price * 10 END AS points
  FROM dannys_diner.menu m
)
SELECT s.customer_id, SUM(sb.points) AS points
FROM dannys_diner.sales s
JOIN sub sb ON sb.product_id = s.product_id
GROUP BY customer_id
ORDER BY customer_id;
```

### Answer:
| customer_id | total_points | 
| ----------- | ---------- |
| A           | 860 |
| B           | 940 |
| C           | 360 |

- Total points for Customer A is $860.
- Total points for Customer B is $940.
- Total points for Customer C is $360.

***

**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi — how many points do customer A and B have at the end of January?**

```sql
WITH sub AS (
 SELECT customer_id,
  	join_date, 
  	join_date + 6 AS criteria, 
  	DATE_TRUNC('month', '2021-01-31'::DATE)
  	+ interval '1 month' 
  	- interval '1 day' AS last_date
 FROM dannys_diner.members
)

SELECT s.customer_id,
SUM(
 CASE
  WHEN m.product_name = 'sushi' THEN 20 * m.price
  WHEN s.order_date BETWEEN sub.join_date AND sub.criteria THEN 20 * m.price
  ELSE 10 * m.price END) AS points
FROM dannys_diner.sales s
JOIN sub 
	ON s.customer_id = sub.customer_id
	AND s.order_date <= sub.last_date
JOIN dannys_diner.menu m
	ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY customer_id;
```

### Answer:
| customer_id | total_points | 
| ----------- | ---------- |
| A           | 1370 |
| B           | 820 |

- Total points for Customer A is 1,370.
- Total points for Customer B is 820.

***

## Resources and References:

I obtained my sources, dataset, and information from the following website: [Here](https://8weeksqlchallenge.com/case-study-1/).

***
