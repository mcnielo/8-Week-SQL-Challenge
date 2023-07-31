# SQL Challenge Case Study #2: Pizza Runner
<div align="center"><img src="https://8weeksqlchallenge.com/images/case-study-designs/2.png" alt="Image" width="500" height="520"></div>

***

## Introduction
Danny, inspired by the popularity of pizza and the trend of 80s retro styling, came up with a brilliant idea to combine the two and create a unique business venture called Pizza Runner. With the goal of expanding his Pizza Empire, Danny realized he needed more than just pizza to attract seed funding. So, he decided to "Uberize" the concept by recruiting "runners" to deliver fresh pizza and developing a mobile app to accept customer orders. To bring his vision to life, Danny used his personal resources, including his own home as Pizza Runner Headquarters and maxing out his credit card to hire freelance developers.

***

## Business Problem
Danny is scaling up his Pizza Empire and aiming to revolutionize it through the launch of Pizza Runner. To make this vision a reality, Danny took the initial steps of recruiting dedicated "runners" to facilitate the delivery of fresh pizzas from the designated Pizza Runner Headquarters, which happens to be Danny's house. In addition, he made a significant financial investment by maxing out his credit card to hire freelance developers who developed a mobile app capable of accepting customer orders seamlessly. With these strategic moves, Danny is paving the way for an innovative and efficient pizza delivery service.

***

##  Data Cleaning & Manipulation

Upon inspection of the datasets, I noticed that some of the tables has null or missing data. We need to address this first before we can proceed with out analysis.

### Table 1: customer_orders

As you can on the table below, the 'exclusion' and 'extra' column contains NULL or missing values.

<img width="1037" alt="image" src="https://user-images.githubusercontent.com/81607668/129472585-badae450-52d2-442e-9d50-e4d0d8fce83a.png">

We first need to clean this by creating a temporary table and removing the null or missing values.

````sql
CREATE TEMP TABLE temp_customer_orders AS
	SELECT order_id, customer_id, pizza_id,
		CASE
			WHEN exclusions = '' OR exclusions LIKE 'null' THEN NULL
			ELSE exclusions
			END AS exclusions,
		CASE
        	WHEN extras = '' OR extras LIKE 'null' THEN NULL
			ELSE extras
			END AS extras, 
            order_time
FROM pizza_runner.customer_orders;
````

After cleaning the dataset, 'customer_orders' table should look like this:

order_id|customer_id|pizza_id|exclusions|extras|order_time             |
--------|-----------|--------|----------|------|-----------------------|
1|        101|       1|          |      |2020-01-01 18:05:02.000|
2|        101|       1|          |      |2020-01-01 19:00:52.000|
3|        102|       1|          |      |2020-01-02 23:51:23.000|
3|        102|       2|          |      |2020-01-02 23:51:23.000|
4|        103|       1|4         |      |2020-01-04 13:23:46.000|
4|        103|       1|4         |      |2020-01-04 13:23:46.000|
4|        103|       2|4         |      |2020-01-04 13:23:46.000|
5|        104|       1|          |1     |2020-01-08 21:00:29.000|
6|        101|       2|          |      |2020-01-08 21:03:13.000|
7|        105|       2|          |1     |2020-01-08 21:20:29.000|
8|        102|       1|          |      |2020-01-09 23:54:33.000|
9|        103|       1|4         |1, 5  |2020-01-10 11:22:59.000|
10|        104|       1|          |      |2020-01-11 18:34:49.000|
10|        104|       1|2, 6      |1, 4  |2020-01-11 18:34:49.000|


***

### Table 2: runner_orders

<img width="1037" alt="image" src="https://user-images.githubusercontent.com/81607668/129472585-badae450-52d2-442e-9d50-e4d0d8fce83a.png">

Similar to the dataset above, 'runner_orders' also contain several nulls or missing value. lets solve this by applying the same data cleaning techniques from above.
- Remove null values from 'pickup_time'.
- Remove null values in 'distance' and trim the "km" unit from the dataset.
- Same with the 'duration' data, remove null values and trim minutes from each data.
- Lastly, remove the null values from 'cancellation' data.

````sql
CREATE TEMP TABLE temp_runner_orders AS (
	SELECT order_id,
		runner_id,
		CASE
			WHEN pickup_time LIKE 'null' THEN NULL
			ELSE pickup_time
		END::timestamp AS pickup_time,
		NULLIF(regexp_replace(distance, '[^0-9.]', '', 'g'), '')::NUMERIC AS distance,
		NULLIF(regexp_replace(duration, '[^0-9.]', '', 'g'), '')::NUMERIC AS duration,
		CASE
			WHEN cancellation LIKE 'null'
			OR cancellation LIKE 'NaN'
			OR cancellation LIKE '' THEN NULL
			ELSE cancellation
		END AS cancellation
	FROM pizza_runner.runner_orders
);
````
***

## Case Study Questions
***
## Pizza Metrics

**1. What is the total amount each customer spent at the restaurant?**

````sql
    SELECT COUNT(order_id) AS Ordered_Pizzas
    FROM temp_customer_orders;
````

### Answer:
| ordered_pizzas |
| -------------- |
| 14             |
***

**2. How many unique customer orders were made?**

````sql
    SELECT COUNT(DISTINCT order_id) AS unique_orders_made
    FROM pizza_runner.customer_orders;
````

### Answer:
| unique_orders_made |
| ------------------ |
| 10                 |

***

**3. How many successful orders were delivered by each runner?**

````sql
    SELECT runner_id, COUNT(order_id)
    FROM temp_runner_orders
    WHERE cancellation IS NULL
    GROUP BY runner_id ORDER BY runner_id;
````

### Answer:
| runner_id | count |
| --------- | ----- |
| 1         | 4     |
| 2         | 3     |
| 3         | 1     |


***

**4. How many of each type of pizza was delivered?**

````sql
    SELECT pn.pizza_name, COUNT(co.*) AS pizza_delivered
    FROM temp_customer_orders AS co
    JOIN pizza_runner.pizza_names AS pn ON co.pizza_id = pn.pizza_id
    JOIN temp_runner_orders AS ro ON co.order_id = ro.order_id
    WHERE ro.distance != 0
    GROUP BY pizza_name
    ORDER BY pizza_name;
````

### Answer:
| pizza_name | pizza_delivered |
| ---------- | --------------- |
| Meatlovers | 9               |
| Vegetarian | 3               |

***

**5. How many Vegetarian and Meatlovers were ordered by each customer?**

````sql
    SELECT co.customer_id,
    	SUM(
          CASE WHEN co.pizza_id = 1 THEN 1
          ELSE 0
          END) AS meat_lover,
        SUM(
          CASE WHEN co.pizza_id = 2 THEN 1
          ELSE 0
          END) AS vegetarian
    FROM temp_customer_orders AS co
    GROUP BY 1
    ORDER BY 1;
````

### Answer:
| customer_id | meat_lover | vegetarian |
| ----------- | ---------- | ---------- |
| 101         | 2          | 1          |
| 102         | 2          | 1          |
| 103         | 3          | 1          |
| 104         | 3          | 0          |
| 105         | 0          | 1          |

***

**6. What was the maximum number of pizzas delivered in a single order?**

```sql
    WITH order_count_cte AS(
      SELECT 
      	co.order_id,
      	COUNT(co.pizza_id) AS pizza_per_order
      FROM temp_customer_orders co
      JOIN temp_runner_orders ro ON co.order_id = ro.order_id
      WHERE ro.distance != 0
      GROUP BY 1
    )
    
    SELECT MAX(pizza_per_order) AS max_order
    FROM order_count_cte;
```

### Answer:
| max_order |
| --------- |
| 3         |

***

***7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**

````sql
    SELECT co.customer_id, 
    	SUM(
          CASE WHEN co.exclusions IS NOT NULL OR co.extras IS NOT NULL THEN 1
          ELSE 0
          END) AS change,
        SUM(
          CASE WHEN co.exclusions IS NULL AND co.extras IS NULL THEN 1
          ELSE 0
          END) AS no_change
    FROM temp_customer_orders co
    JOIN temp_runner_orders ro ON co.order_id = ro.order_id
    WHERE cancellation IS NULL
    GROUP BY co.customer_id
    ORDER BY co.customer_id;
````

### Answer:
| customer_id | change | no_change |
| ----------- | ------ | --------- |
| 101         | 0      | 2         |
| 102         | 0      | 3         |
| 103         | 3      | 0         |
| 104         | 2      | 1         |
| 105         | 1      | 0         |
***

**8. How many pizzas were delivered that had both exclusions and extras?**

```sql
    SELECT 
    	SUM(
          CASE WHEN co.exclusions IS NOT NULL AND co.extras IS NOT NULL THEN 1
          ELSE 0
          END) AS total_order
    FROM temp_customer_orders co
    JOIN temp_runner_orders ro ON co.order_id = ro.order_id
    WHERE cancellation IS NULL;
```

### Answer:
| total_order |
| ----------- |
| 1           |

***

**9. What was the total volume of pizzas ordered for each hour of the day?**

```sql
    SELECT EXTRACT(HOUR FROM order_time) AS hour, COUNT(order_id) AS count_pizza
    FROM temp_customer_orders
    GROUP BY EXTRACT(HOUR FROM order_time)
    ORDER BY 1;
```

### Answer:
| hour | count_pizza |
| ---- | ----------- |
| 11   | 1           |
| 13   | 3           |
| 18   | 3           |
| 19   | 1           |
| 21   | 3           |
| 23   | 3           |

***

**10. What was the volume of orders for each day of the week?**

```sql
    SELECT 
    	TO_CHAR(order_time, 'DAY') AS day_of_week,
    	COUNT(*) AS n_pizzas
    FROM temp_customer_orders
    GROUP BY 1
    ORDER BY 1;
```

### Answer:
| day_of_week | n_pizzas |
| ----------- | -------- |
| FRIDAY      | 1        |
| SATURDAY    | 5        |
| THURSDAY    | 3        |
| WEDNESDAY   | 5        |

***
## Runner and Customer Experience

**1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)**

```sql
    SELECT 
    	registration_date - ((registration_date - '2021-01-01') % 7) AS starting_week,
        COUNT(runner_id) AS Count
    FROM pizza_runner.runners
    GROUP BY 1
    ORDER BY 1;
```

### Answer:
| starting_week            | count |
| ------------------------ | ----- |
| 2021-01-01T00:00:00.000Z | 2     |
| 2021-01-08T00:00:00.000Z | 1     |
| 2021-01-15T00:00:00.000Z | 1     |

**2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?**

```sql
    WITH pizza_time AS (
      SELECT 
        ro.runner_id,
      	ro.order_id,
      	ro.pickup_time,
      	co.order_time,
      	(ro.pickup_time - co.order_time) AS arrival_time
      FROM temp_runner_orders ro
      JOIN temp_customer_orders co ON ro.order_id = co.order_id
      WHERE ro.cancellation IS NULL
    )
    
    SELECT 
    	runner_id,
    	EXTRACT('MINUTES' FROM AVG(arrival_time)) AS avg_arrival_time
    FROM pizza_time
    GROUP BY 1 
    ORDER BY 1;
```

### Answer:
| runner_id | avg_arrival_time |
| --------- | ---------------- |
| 1         | 15               |
| 2         | 23               |
| 3         | 10               |

**3. Is there any relationship between the number of pizzas and how long the order takes to prepare?**

```sql
    WITH prep_time AS (
      SELECT 
        ro.runner_id,
      	COUNT(ro.order_id) AS count,
      	ro.pickup_time,
      	co.order_time,
      	(ro.pickup_time - co.order_time) AS arrival_time
      FROM temp_runner_orders ro
      JOIN temp_customer_orders co ON ro.order_id = co.order_id
      GROUP BY 1, 3, 4
    )
    
    SELECT 
    	count,
        extract('minutes' FROM avg(arrival_time)) AS avg_arrival_time
    FROM prep_time
    GROUP BY 1
    ORDER BY 1;
```

### Answer:
| count | avg_arrival_time |
| ----- | ---------------- |
| 1     | 12               |
| 2     | 18               |
| 3     | 29               |

**4. What was the average distance travelled for each customer?**

```sql
    SELECT 
    	co.customer_id, 
        AVG(ro.distance)
    FROM temp_customer_orders co
    JOIN temp_runner_orders ro ON co.order_id = ro.order_id
    GROUP BY co.customer_id
    ORDER BY co.customer_id;
```

### Answer:
| customer_id | avg                 |
| ----------- | ------------------- |
| 101         | 20.0000000000000000 |
| 102         | 16.7333333333333333 |
| 103         | 23.4000000000000000 |
| 104         | 10.0000000000000000 |
| 105         | 25.0000000000000000 |

**5. What was the difference between the longest and shortest delivery times for all orders?**

```sql
    SELECT MAX(duration) - MIN(duration) AS difference
    FROM temp_runner_orders;
```

### Answer:
| difference |
| ---------- |
| 30         |


**6. What was the average speed for each runner for each delivery and do you notice any trend for these values?**

```sql
    SELECT
    	ro.runner_id,
        co.customer_id,
        co.order_id,
        ro.duration,
        COUNT(co.order_id) AS pizza_count,
        ro.distance,
        (ro.duration/60) AS duration_hr,
        ROUND((ro.distance/ro.duration * 60)::numeric, 2) AS avg_speed
    FROM temp_runner_orders ro
    JOIN temp_customer_orders co ON ro.order_id = co.order_id
    WHERE distance != 0
    GROUP BY 1, 2, 3, 4, ro.distance, ro.duration
    ORDER BY co.order_id;
```

### Answer:
| runner_id | customer_id | order_id | duration | pizza_count | distance | duration_hr            | avg_speed |
| --------- | ----------- | -------- | -------- | ----------- | -------- | ---------------------- | --------- |
| 1         | 101         | 1        | 32       | 1           | 20       | 0.53333333333333333333 | 37.50     |
| 1         | 101         | 2        | 27       | 1           | 20       | 0.45000000000000000000 | 44.44     |
| 1         | 102         | 3        | 20       | 2           | 13.4     | 0.33333333333333333333 | 40.20     |
| 2         | 103         | 4        | 40       | 3           | 23.4     | 0.66666666666666666667 | 35.10     |
| 3         | 104         | 5        | 15       | 1           | 10       | 0.25000000000000000000 | 40.00     |
| 2         | 105         | 7        | 25       | 1           | 25       | 0.41666666666666666667 | 60.00     |
| 2         | 102         | 8        | 15       | 1           | 23.4     | 0.25000000000000000000 | 93.60     |
| 1         | 104         | 10       | 10       | 2           | 10       | 0.16666666666666666667 | 60.00     |

**7. What is the successful delivery percentage for each runner?**

```sql
    SELECT 
    	runner_id,
    	ROUND(100 * SUM(
          CASE WHEN distance IS NULL THEN 0
          ELSE 1
          END) / COUNT(*)) AS deliveries_percentage
    FROM temp_runner_orders
    GROUP BY runner_id
    ORDER BY runner_id;
```

### Answer:
| runner_id | deliveries_percentage |
| --------- | --------------------- |
| 1         | 100                   |
| 2         | 75                    |
| 3         | 50                    |

***
## Ingredients Optimization

***Need to create unnested array table using temp table for this.***
```sql
DROP TABLE IF EXISTS recipe_toppings;
CREATE TEMP TABLE recipe_toppings AS (
	SELECT
		pn.pizza_id,
		pn.pizza_name,
		UNNEST(string_to_array(pr.toppings, ','))::numeric AS each_topping
	FROM pizza_runner.pizza_names AS pn
	JOIN pizza_runner.pizza_recipes AS pr ON pn.pizza_id = pr.pizza_id
);
```

**1. What are the standard ingredients for each pizza?**

```sql
    WITH pizza_toppings_cte AS (
      SELECT
          rt.pizza_name,
          pt.topping_name
      FROM recipe_toppings rt
      JOIN pizza_runner.pizza_toppings pt ON rt.each_topping = pt.topping_id
      ORDER BY 1
    )
    
    SELECT
    	pizza_name,
        STRING_AGG(topping_name, ', ') AS all_toppings
    FROM pizza_toppings_cte
    GROUP BY 1;
```

### Answer:
| pizza_name | all_toppings                                                          |
| ---------- | --------------------------------------------------------------------- |
| Meatlovers | BBQ Sauce, Pepperoni, Cheese, Salami, Chicken, Bacon, Mushrooms, Beef |
| Vegetarian | Tomato Sauce, Cheese, Mushrooms, Onions, Peppers, Tomatoes            |

**2. What was the most commonly added extra?**

```sql
    WITH extras_cte AS (
    	SELECT
    		trim(UNNEST(string_to_array(extras, ',')))::numeric AS extras
    	FROM temp_customer_orders
    	GROUP BY extras
    ),
    
    most_common_extra AS (
    	SELECT
      		extras,
      		RANK() OVER (ORDER BY COUNT(extras) DESC) AS ranking
      	FROM extras_cte
     	GROUP BY 1
    )
    
    SELECT topping_name
    FROM pizza_runner.pizza_toppings
    WHERE topping_id = (SELECT extras FROM most_common_extra WHERE ranking = 1);
```

### Answer:
| topping_name |
| ------------ |
| Bacon        |

**3. What was the most common exclusion?**

```sql
    WITH exclusions_cte AS (
    	SELECT
    		trim(UNNEST(string_to_array(exclusions, ',')))::numeric AS exclusions
    	FROM temp_customer_orders
    	GROUP BY exclusions
    ),
    
    most_common_exclusions AS (
    	SELECT
      		exclusions,
      		RANK() OVER (ORDER BY COUNT(exclusions) DESC) AS ranking
      	FROM exclusions_cte
     	GROUP BY 1
    )
    
    SELECT topping_name
    FROM pizza_runner.pizza_toppings
    WHERE topping_id IN (SELECT exclusions FROM most_common_exclusions WHERE ranking = 1);
```

### Answer:
| topping_name |
| ------------ |
| BBQ Sauce    |
| Cheese       |
| Mushrooms    |

**4. Generate an order item for each record in the customers_orders table in the format of one of the following:**
- Meat Lovers
- Meat Lovers - Exclude Beef
- Meat Lovers - Extra Bacon
- Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

```sql
    -- Create a temp table and give customer orders a unique id using row_number
    DROP TABLE IF EXISTS id_customer_orders;
    CREATE TEMP TABLE id_customer_orders AS (
      SELECT
        row_number() OVER (ORDER BY order_id) AS row_id,
        order_id,
        customer_id,
        pizza_id,
        exclusions,
        extras,
        order_time
    FROM
      new_customer_orders
    );
    -- Create a temp table and unnest the exclusions array.
    DROP TABLE IF EXISTS get_exclusions;
    CREATE TEMP TABLE get_exclusions AS (
      SELECT
        row_id,
        order_id,
        trim(UNNEST(string_to_array(exclusions, ',')))::NUMERIC AS single_exclusions
      FROM id_customer_orders
      GROUP BY row_id, order_id, exclusions
    );
    -- Create a temp table and unnest the extras array.
    DROP TABLE IF EXISTS get_extras;
    CREATE TEMP TABLE get_extras AS (
      SELECT
        row_id,
        order_id,
        trim(UNNEST(string_to_array(extras, ',')))::numeric AS single_extras
      FROM id_customer_orders
      GROUP BY row_id, order_id, extras
    );

    WITH get_exlusions_and_extras AS (
      SELECT
        c.row_id,
        c.order_id,
        pn.pizza_name,
        CASE
          WHEN c.exclusions IS NULL AND c.extras IS NULL THEN NULL
          ELSE 
            (SELECT
              string_agg((SELECT topping_name FROM pizza_toppings WHERE topping_id = get_exc.single_exclusions), ', ')
            FROM
              get_exclusions AS get_exc
            WHERE order_id =c.order_id)
        END AS all_exclusions,
        CASE
          WHEN c.exclusions IS NULL AND c.extras IS NULL THEN NULL
          ELSE
            (SELECT
              string_agg((SELECT topping_name FROM pizza_toppings WHERE topping_id = get_ext.single_extras), ', ')
            FROM
              get_extras AS get_ext
            WHERE order_id =c.order_id)
        END AS all_extras
      FROM pizza_names AS pn
      JOIN id_customer_orders AS c
      ON c.pizza_id = pn.pizza_id
      LEFT JOIN get_exclusions AS get_exc
      ON get_exc.order_id = c.order_id AND c.exclusions IS NOT NULL
      LEFT JOIN get_extras AS get_ext
      ON get_ext.order_id = c.order_id AND c.extras IS NOT NULL
      GROUP BY 
        c.row_id,
        c.order_id,
        pn.pizza_name,
        c.exclusions,
        c.extras
      ORDER BY c.row_id
    )
    SELECT
      CASE
        WHEN all_exclusions IS NOT NULL AND all_extras IS NULL THEN concat(pizza_name, ' - ', 'Exclude: ', all_exclusions)
        WHEN all_exclusions IS NULL AND all_extras IS NOT NULL THEN concat(pizza_name, ' - ', 'Extra: ', all_extras)
        WHEN all_exclusions IS NOT NULL AND all_extras IS NOT NULL THEN concat(pizza_name, ' - ', 'Exclude: ', all_exclusions, ' - ', 'Extra: ', all_extras)
        ELSE pizza_name
      END AS pizza_type
    FROM get_exlusions_and_extras;
```

### Answer:
pizza_type                                                       |
-----------------------------------------------------------------|
Meatlovers                                                       |
Meatlovers                                                       |
Meatlovers                                                       |
Vegetarian                                                       |
Meatlovers - Exclude: Cheese                                     |
Meatlovers - Exclude: Cheese                                     |
Vegetarian - Exclude: Cheese                                     |
Meatlovers - Extra: Bacon                                        |
Vegetarian                                                       |
Vegetarian - Extra: Bacon                                        |
Meatlovers                                                       |
Meatlovers - Exclude: Cheese - Extra: Bacon, Chicken             |
Meatlovers                                                       |
Meatlovers - Exclude: BBQ Sauce, Mushrooms - Extra: Bacon, Cheese|

**5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients**
- For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"
This query uses the '**get_exclusions**' and '**get_extras**' temp tables from the previous question.
```sql
    DROP TABLE IF EXISTS get_toppings;
    CREATE TEMP TABLE get_toppings AS (
      SELECT
        row_id,
        order_id,
        trim(UNNEST(string_to_array(toppings, ',')))::numeric AS single_toppings
      FROM id_customer_orders AS c
      JOIN pizza_recipes AS pr
      ON c.pizza_id = pr.pizza_id
      GROUP BY row_id, order_id, toppings
    );

    DROP TABLE IF EXISTS ingredients; 
    CREATE TEMP TABLE ingredients AS (
      SELECT
        row_id,
        order_id,
        pizza_name,
        concat(all_toppings, ',', all_extras) AS all_ingredients
      FROM
        (SELECT
          c.row_id,
          c.order_id,
          pn.pizza_name,
          (SELECT
            trim(string_agg((SELECT topping_name FROM pizza_toppings WHERE topping_id = get_top.single_toppings), ','))
          FROM
            get_toppings AS get_top
          WHERE get_top.row_id = c.row_id
          AND get_top.single_toppings NOT IN (
            (SELECT 
              single_exclusions
            FROM get_exclusions
            WHERE c.row_id = row_id)
          ))AS all_toppings,
          (SELECT
            trim(string_agg((SELECT topping_name FROM pizza_toppings WHERE topping_id = get_extra.single_extras), ','))
          FROM
            get_extras AS get_extra
          WHERE get_extra.row_id = c.row_id
          ) AS all_extras
        FROM pizza_names AS pn
        JOIN id_customer_orders AS c
        ON c.pizza_id = pn.pizza_id
        ORDER BY c.row_id) AS tmp);

    SELECT
      row_id,
      pizza_name,
      string_agg(new_ing, ',') AS toppings
    FROM
      (SELECT
        row_id,
        pizza_name,
        CASE
          WHEN count(each_ing) > 1 THEN concat('2x', each_ing)
          when each_ing != '' THEN each_ing
        END AS new_ing
      FROM
        (SELECT 
          row_id,
          pizza_name,
          UNNEST(string_to_array(all_ingredients, ',')) AS each_ing
        FROM ingredients) AS tmp
      GROUP BY 
        row_id,
        pizza_name,
        each_ing) AS tmp2
    WHERE new_ing IS NOT null
    GROUP BY 
      row_id,
      pizza_name;
```

### Answer:
row_id|pizza_name|toppings                                                        |
------|----------|----------------------------------------------------------------|
1|Meatlovers|Bacon,BBQ Sauce,Beef,Cheese,Chicken,Mushrooms,Pepperoni,Salami  |
2|Meatlovers|Bacon,BBQ Sauce,Beef,Cheese,Chicken,Mushrooms,Pepperoni,Salami  |
3|Meatlovers|Bacon,BBQ Sauce,Beef,Cheese,Chicken,Mushrooms,Pepperoni,Salami  |
4|Vegetarian|Cheese,Mushrooms,Onions,Peppers,Tomato Sauce,Tomatoes           |
5|Meatlovers|Bacon,BBQ Sauce,Beef,Chicken,Mushrooms,Pepperoni,Salami         |
6|Meatlovers|Bacon,BBQ Sauce,Beef,Chicken,Mushrooms,Pepperoni,Salami         |
7|Vegetarian|Mushrooms,Onions,Peppers,Tomato Sauce,Tomatoes                  |
8|Meatlovers|2xBacon,BBQ Sauce,Beef,Cheese,Chicken,Mushrooms,Pepperoni,Salami|
9|Vegetarian|Cheese,Mushrooms,Onions,Peppers,Tomato Sauce,Tomatoes           |
10|Vegetarian|Bacon,Cheese,Mushrooms,Onions,Peppers,Tomato Sauce,Tomatoes     |
11|Meatlovers|Bacon,BBQ Sauce,Beef,Cheese,Chicken,Mushrooms,Pepperoni,Salami  |
12|Meatlovers|2xBacon,BBQ Sauce,Beef,2xChicken,Mushrooms,Pepperoni,Salami     |
13|Meatlovers|Bacon,BBQ Sauce,Beef,Cheese,Chicken,Mushrooms,Pepperoni,Salami  |
14|Meatlovers|2xBacon,Beef,2xCheese,Chicken,Pepperoni,Salami                  |

**6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?**

```sql
    WITH get_each_ingredient AS (
      SELECT 
        row_id,
        order_id,
        pizza_name,
        UNNEST(string_to_array(all_ingredients, ',')) AS each_ing
      FROM ingredients
    )
    SELECT
      each_ing,
      count(each_ing) AS n_ingredients
    from
      get_each_ingredient AS gei
    JOIN temp_runner_orders AS r
    ON r.order_id = gei.order_id
    WHERE 
      each_ing <> ''
    AND 
      r.cancellation IS NULL
    GROUP BY each_ing
    ORDER BY n_ingredients DESC;
```

### Answer:
each_ing    |n_ingredients|
------------|-------------|
Bacon       |           12|
Mushrooms   |           11|
Cheese      |           10|
Chicken     |            9|
Salami      |            9|
Pepperoni   |            9|
Beef        |            9|
BBQ Sauce   |            8|
Tomato Sauce|            3|
Tomatoes    |            3|
Peppers     |            3|
Onions      |            3|

***

## Pricing and Ratings

**1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?**

```sql
    WITH pizza_income_cte AS (
        SELECT 
            SUM(
              CASE WHEN co.pizza_id = 1 THEN 12
              ELSE 0
              END) AS m_profit,
            SUM(
              CASE WHEN co.pizza_id = 2 THEN 10
              ELSE 0
              END) AS v_profit
        FROM temp_customer_orders co
        JOIN temp_runner_orders ro ON co.order_id = ro.order_id
      WHERE cancellation IS NULL
    )
    
    SELECT
    	(m_profit + v_profit) AS profit
    from pizza_income_cte;
```

### Answer:
| profit |
| ------ |
| 138    |

**2. What if there was an additional $1 charge for any pizza extras?**
Add cheese is $1 extra

```sql
    CREATE TEMP TABLE get_extras AS (
      SELECT
      	order_id,
      	COUNT(each_extra) AS total_extra
      FROM (
        		SELECT
        			order_id,
        			UNNEST(STRING_TO_ARRAY(extras, ',')) AS each_extra
        		FROM temp_customer_orders
        	) AS temp
      GROUP BY 1
    );

    WITH extra_charge AS (
        SELECT 
      		co.order_id,
      		co.pizza_id,
            SUM(
              CASE WHEN co.pizza_id = 1 THEN 12
              ELSE 0
              END) AS m_profit,
            SUM(
              CASE WHEN co.pizza_id = 2 THEN 10
              ELSE 0
              END) AS v_profit,
      		ge.total_extra
        FROM temp_customer_orders co
        JOIN temp_runner_orders ro ON co.order_id = ro.order_id
      	LEFT JOIN get_extras ge ON ge.order_id = co.order_id
      WHERE cancellation IS NULL
      GROUP BY 1, 2, 5
    )
    
    SELECT
    	sum(total_extra) + sum(m_profit) + sum(v_profit) AS total
    FROM extra_charge;
```

### Answer:
| total |
| ----- |
| 142   |

**3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.**

```sql
    DROP TABLE IF EXISTS runner_rating_system;
    CREATE TABLE runner_rating_system (
      "rating_id" INTEGER,
      "customer_id" INTEGER,
      "order_id" INTEGER,
      "runner_id" INTEGER,
      "rating" INTEGER
    );
    INSERT INTO runner_rating_system (
        "rating_id",
        "customer_id",
        "order_id",
        "runner_id",
        "rating"
      )
    VALUES ('1', '101', '1', '1', '3'),
      ('2', '103', '4', '2', '4'),
      ('3', '102', '5', '3', '5'),
      ('4', '102', '8', '2', '2'),
      ('5', '104', '10', '1', '5');
```

### Answer:
rating_id|customer_id|order_id|runner_id|rating|
---------|-----------|--------|---------|------|
1|        101|       1|        1|     3|
2|        103|       4|        2|     4|
3|        102|       5|        3|     5|
4|        102|       8|        2|     2|
5|        104|      10|        1|     5|


**4. Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?**
- customer_id
- order_id
- runner_id
- rating
- order_time
- pickup_time
- Time between order and pickup
- Delivery duration
- Average speed
- Total number of pizzas

```sql
SELECT co.customer_id,
	co.order_id,
	ro.runner_id,
	rrs.rating,
	co.order_time,
	ro.pickup_time,
	(
		ro.pickup_time::timestamp - co.order_time::timestamp
	) AS time_diff,
	ro.duration,
	round(60 * ro.distance / ro.duration, 2) AS avg_speed,
	count(ro.pickup_time) AS total_delivered
FROM temp_customer_orders AS co
	JOIN temp_runner_orders AS ro ON ro.order_id = co.order_id
	LEFT JOIN runner_rating_system AS rrs ON ro.order_id = rrs.order_id
WHERE ro.cancellation IS NULL
GROUP BY co.customer_id,
	co.order_id,
	ro.runner_id,
	rrs.rating,
	co.order_time,
	ro.pickup_time,
	time_diff,
	ro.duration,
	avg_speed
ORDER BY co.order_id
```

### Answer:
customer_id|order_id|runner_id|rating|order_time             |pickup_time            |time_diff|duration|avg_speed|total_delivered|
-----------|--------|---------|------|-----------------------|-----------------------|---------|--------|---------|---------------|
101|       1|        1|     3|2020-01-01 18:05:02.000|2020-01-01 18:15:34.000| 00:10:32|      32|    37.50|              1|
101|       2|        1|      |2020-01-01 19:00:52.000|2020-01-01 19:10:54.000| 00:10:02|      27|    44.44|              1|
102|       3|        1|      |2020-01-02 23:51:23.000|2020-01-03 00:12:37.000| 00:21:14|      20|    40.20|              2|
103|       4|        2|     4|2020-01-04 13:23:46.000|2020-01-04 13:53:03.000| 00:29:17|      40|    35.10|              3|
104|       5|        3|     5|2020-01-08 21:00:29.000|2020-01-08 21:10:57.000| 00:10:28|      15|    40.00|              1|
105|       7|        2|      |2020-01-08 21:20:29.000|2020-01-08 21:30:45.000| 00:10:16|      25|    60.00|              1|
102|       8|        2|     2|2020-01-09 23:54:33.000|2020-01-10 00:15:02.000| 00:20:29|      15|    93.60|              1|
104|      10|        1|     5|2020-01-11 18:34:49.000|2020-01-11 18:50:20.000| 00:15:31|      10|    60.00|              2|

**5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?**

```sql
WITH total_payout AS (
	SELECT
		(sum(distance*2) * .30) AS payout
	FROM temp_runner_orders
	WHERE cancellation IS NULL
)
SELECT
	total_income - payout AS profit
from
	total_payout,
	pizza_income;

```

### Answer:
profit|
------|
50.880|




## Resources and References:

I obtained my sources, dataset, and information from the following website: [Here](https://8weeksqlchallenge.com/case-study-2/).

***
