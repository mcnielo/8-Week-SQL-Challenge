# SQL Challenge Case Study #8: Fresh Segments
<div align="center"><img src="https://8weeksqlchallenge.com/images/case-study-designs/8.png" alt="Image" width="500" height="520"></div>

***

## Introduction
Danny created Fresh Segments, a digital marketing agency that helps other businesses analyse trends in online ad click behaviour for their unique customer base.

Clients share their customer lists with the Fresh Segments team who then aggregate interest metrics and generate a single dataset worth of metrics for further analysis.

In particular - the composition and rankings for different interests are provided for each client showing the proportion of their customer list who interacted with online assets related to each interest for each month.

Danny has asked for your assistance to analyse aggregated metrics for an example client and provide some high level insights about the customer list and their interests

***

## Business Problem
Fresh Segments, a digital marketing agency, aids businesses in analyzing trends in online ad clicks for their unique customer groups. Clients provide their customer lists, which Fresh Segments uses to aggregate interest metrics and generate a consolidated dataset. This dataset includes interest composition, rankings, and customer interactions for each client, per interest and month. Danny seeks your assistance to analyze aggregated metrics for a client and offer high-level customer and interest insights.

***

## Datasets

### Table 1: interest_metrics

This table contains information about aggregated interest metrics for a specific major client of Fresh Segments which makes up a large proportion of their customer base.

````sql
    SELECT * FROM fresh_segments.interest_metrics LIMIT 10;
````
### Results:
| _month | _year | month_year | interest_id | composition | index_value | ranking | percentile_ranking |
| ------ | ----- | ---------- | ----------- | ----------- | ----------- | ------- | ------------------ |
| 7      | 2018  | 07-2018    | 32486       | 11.89       | 6.19        | 1       | 99.86              |
| 7      | 2018  | 07-2018    | 6106        | 9.93        | 5.31        | 2       | 99.73              |
| 7      | 2018  | 07-2018    | 18923       | 10.85       | 5.29        | 3       | 99.59              |
| 7      | 2018  | 07-2018    | 6344        | 10.32       | 5.1         | 4       | 99.45              |
| 7      | 2018  | 07-2018    | 100         | 10.77       | 5.04        | 5       | 99.31              |
| 7      | 2018  | 07-2018    | 69          | 10.82       | 5.03        | 6       | 99.18              |
| 7      | 2018  | 07-2018    | 79          | 11.21       | 4.97        | 7       | 99.04              |
| 7      | 2018  | 07-2018    | 6111        | 10.71       | 4.83        | 8       | 98.9               |
| 7      | 2018  | 07-2018    | 6214        | 9.71        | 4.83        | 8       | 98.9               |
| 7      | 2018  | 07-2018    | 19422       | 10.11       | 4.81        | 10      | 98.63              |

***

### Table 2: interest_map

This mapping table links the interest_id with their relevant interest information. You will need to join this table onto the previous interest_details table to obtain the interest_name as well as any details about the summary information.

````sql
    SELECT * FROM fresh_segments.interest_map LIMIT 10;
````
### Results:
| id  | interest_name             | interest_summary                                                                   | created_at               | last_modified            |
| --- | ------------------------- | ---------------------------------------------------------------------------------- | ------------------------ | ------------------------ |
| 1   | Fitness Enthusiasts       | Consumers using fitness tracking apps and websites.                                | 2016-05-26T14:57:59.000Z | 2018-05-23T11:30:12.000Z |
| 2   | Gamers                    | Consumers researching game reviews and cheat codes.                                | 2016-05-26T14:57:59.000Z | 2018-05-23T11:30:12.000Z |
| 3   | Car Enthusiasts           | Readers of automotive news and car reviews.                                        | 2016-05-26T14:57:59.000Z | 2018-05-23T11:30:12.000Z |
| 4   | Luxury Retail Researchers | Consumers researching luxury product reviews and gift ideas.                       | 2016-05-26T14:57:59.000Z | 2018-05-23T11:30:12.000Z |
| 5   | Brides & Wedding Planners | People researching wedding ideas and vendors.                                      | 2016-05-26T14:57:59.000Z | 2018-05-23T11:30:12.000Z |
| 6   | Vacation Planners         | Consumers reading reviews of vacation destinations and accommodations.             | 2016-05-26T14:57:59.000Z | 2018-05-23T11:30:13.000Z |
| 7   | Motorcycle Enthusiasts    | Readers of motorcycle news and reviews.                                            | 2016-05-26T14:57:59.000Z | 2018-05-23T11:30:13.000Z |
| 8   | Business News Readers     | Readers of online business news content.                                           | 2016-05-26T14:57:59.000Z | 2018-05-23T11:30:12.000Z |
| 12  | Thrift Store Shoppers     | Consumers shopping online for clothing at thrift stores and researching locations. | 2016-05-26T14:57:59.000Z | 2018-03-16T13:14:00.000Z |
| 13  | Advertising Professionals | People who read advertising industry news.                                         | 2016-05-26T14:57:59.000Z | 2018-05-23T11:30:12.000Z |

***


## Case Study Questions
***

## Data Exploration and Cleansing

**1. Update the fresh_segments.interest_metrics table by modifying the month_year column to be a date data type with the start of the month**

````sql
    ALTER TABLE fresh_segments.interest_metrics
    ADD COLUMN new_month_year DATE;
    UPDATE fresh_segments.interest_metrics
    SET new_month_year = TO_DATE(month_year, 'MM-YYYY');
    ALTER TABLE fresh_segments.interest_metrics
    DROP COLUMN month_year;
````

### Answer:
| _month | _year | interest_id | composition | index_value | ranking | percentile_ranking | new_month_year           |
| ------ | ----- | ----------- | ----------- | ----------- | ------- | ------------------ | ------------------------ |
| 7      | 2018  | 32486       | 11.89       | 6.19        | 1       | 99.86              | 2018-07-01T00:00:00.000Z |
| 7      | 2018  | 6106        | 9.93        | 5.31        | 2       | 99.73              | 2018-07-01T00:00:00.000Z |
| 7      | 2018  | 18923       | 10.85       | 5.29        | 3       | 99.59              | 2018-07-01T00:00:00.000Z |
| 7      | 2018  | 6344        | 10.32       | 5.1         | 4       | 99.45              | 2018-07-01T00:00:00.000Z |
| 7      | 2018  | 100         | 10.77       | 5.04        | 5       | 99.31              | 2018-07-01T00:00:00.000Z |
| 7      | 2018  | 69          | 10.82       | 5.03        | 6       | 99.18              | 2018-07-01T00:00:00.000Z |
| 7      | 2018  | 79          | 11.21       | 4.97        | 7       | 99.04              | 2018-07-01T00:00:00.000Z |
| 7      | 2018  | 6111        | 10.71       | 4.83        | 8       | 98.9               | 2018-07-01T00:00:00.000Z |
| 7      | 2018  | 6214        | 9.71        | 4.83        | 8       | 98.9               | 2018-07-01T00:00:00.000Z |
| 7      | 2018  | 19422       | 10.11       | 4.81        | 10      | 98.63              | 2018-07-01T00:00:00.000Z |
***

**2. What is count of records in the fresh_segments.interest_metrics for each month_year value sorted in chronological order (earliest to latest) with the null values appearing first?**

````sql
    SELECT 
    	new_month_year,
        COUNT(*)
    FROM fresh_segments.interest_metrics
    GROUP BY 1
    ORDER BY 1 NULLS FIRST;
````

### Answer:
| new_month_year           | count |
| ------------------------ | ----- |
|                          | 1194  |
| 2018-07-01T00:00:00.000Z | 729   |
| 2018-08-01T00:00:00.000Z | 767   |
| 2018-09-01T00:00:00.000Z | 780   |
| 2018-10-01T00:00:00.000Z | 857   |
| 2018-11-01T00:00:00.000Z | 928   |
| 2018-12-01T00:00:00.000Z | 995   |
| 2019-01-01T00:00:00.000Z | 973   |
| 2019-02-01T00:00:00.000Z | 1121  |
| 2019-03-01T00:00:00.000Z | 1136  |
| 2019-04-01T00:00:00.000Z | 1099  |
| 2019-05-01T00:00:00.000Z | 857   |
| 2019-06-01T00:00:00.000Z | 824   |
| 2019-07-01T00:00:00.000Z | 864   |
| 2019-08-01T00:00:00.000Z | 1149  |
***

**3. What do you think we should do with these null values in the fresh_segments.interest_metrics**

````sql
    DELETE FROM fresh_segments.interest_metrics
    WHERE interest_id IS NULL;
    SELECT 
      ROUND(100 * (SUM(CASE WHEN interest_id IS NULL THEN 1 END) * 1.0 /
        COUNT(*)),2) AS null_perc
    FROM fresh_segments.interest_metrics;
````

### Answer:
| null_perc |
| --------- |
|           |
***

**4. How many interest_id values exist in the fresh_segments.interest_metrics table but not in the fresh_segments.interest_map table? What about the other way around?**

````sql
    SELECT 
      COUNT(DISTINCT map.id) AS map_id_count,
      COUNT(DISTINCT metrics.interest_id) AS metrics_id_count,
      SUM(CASE WHEN map.id IS NULL THEN 1 END) AS not_in_metric,
      SUM(CASE WHEN metrics.interest_id IS NULL THEN 1 END) AS not_in_map
    FROM fresh_segments.interest_map map
    FULL OUTER JOIN fresh_segments.interest_metrics metrics
      ON metrics.interest_id::integer = map.id;
````

### Answer:
| map_id_count | metrics_id_count | not_in_metric | not_in_map |
| ------------ | ---------------- | ------------- | ---------- |
| 1209         | 1202             |               | 7          |
***

**5. Summarise the id values in the fresh_segments.interest_map by its total record count in this table**

````sql
    SELECT 
      id, 
      interest_name, 
      COUNT(*)
    FROM fresh_segments.interest_map map
    JOIN fresh_segments.interest_metrics metrics
      ON map.id = metrics.interest_id::integer
    GROUP BY id, interest_name
    ORDER BY count DESC, id LIMIT 10;
````

### Answer:
| id  | interest_name             | count |
| --- | ------------------------- | ----- |
| 4   | Luxury Retail Researchers | 14    |
| 5   | Brides & Wedding Planners | 14    |
| 6   | Vacation Planners         | 14    |
| 12  | Thrift Store Shoppers     | 14    |
| 15  | NBA Fans                  | 14    |
| 16  | NCAA Fans                 | 14    |
| 17  | MLB Fans                  | 14    |
| 18  | Nascar Fans               | 14    |
| 20  | Moviegoers                | 14    |
| 25  | Doctors                   | 14    |
***

**6. What sort of table join should we perform for our analysis and why? Check your logic by checking the rows where interest_id = 21246 in your joined output and include all columns from fresh_segments.interest_metrics and all columns from fresh_segments.interest_map except from the id column.**

````sql
    SELECT *
    FROM fresh_segments.interest_map map
    INNER JOIN fresh_segments.interest_metrics metrics
      ON map.id = metrics.interest_id::integer
    WHERE metrics.interest_id::integer = 21246   
      AND metrics._month IS NOT NULL;
````

### Answer:
| id    | interest_name                    | interest_summary                                      | created_at               | last_modified            | _month | _year | interest_id | composition | index_value | ranking | percentile_ranking | new_month_year           |
| ----- | -------------------------------- | ----------------------------------------------------- | ------------------------ | ------------------------ | ------ | ----- | ----------- | ----------- | ----------- | ------- | ------------------ | ------------------------ |
| 21246 | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 7      | 2018  | 21246       | 2.26        | 0.65        | 722     | 0.96               | 2018-07-01T00:00:00.000Z |
| 21246 | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 8      | 2018  | 21246       | 2.13        | 0.59        | 765     | 0.26               | 2018-08-01T00:00:00.000Z |
| 21246 | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 9      | 2018  | 21246       | 2.06        | 0.61        | 774     | 0.77               | 2018-09-01T00:00:00.000Z |
| 21246 | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 10     | 2018  | 21246       | 1.74        | 0.58        | 855     | 0.23               | 2018-10-01T00:00:00.000Z |
| 21246 | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 11     | 2018  | 21246       | 2.25        | 0.78        | 908     | 2.16               | 2018-11-01T00:00:00.000Z |
| 21246 | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 12     | 2018  | 21246       | 1.97        | 0.7         | 983     | 1.21               | 2018-12-01T00:00:00.000Z |
| 21246 | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 1      | 2019  | 21246       | 2.05        | 0.76        | 954     | 1.95               | 2019-01-01T00:00:00.000Z |
| 21246 | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 2      | 2019  | 21246       | 1.84        | 0.68        | 1109    | 1.07               | 2019-02-01T00:00:00.000Z |
| 21246 | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 3      | 2019  | 21246       | 1.75        | 0.67        | 1123    | 1.14               | 2019-03-01T00:00:00.000Z |
| 21246 | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. | 2018-06-11T17:50:04.000Z | 2018-06-11T17:50:04.000Z | 4      | 2019  | 21246       | 1.58        | 0.63        | 1092    | 0.64               | 2019-04-01T00:00:00.000Z |

***

**7. Are there any records in your joined table where the month_year value is before the created_at value from the fresh_segments.interest_map table? Do you think these values are valid and why?**

````sql
    SELECT 
      COUNT(*)
    FROM fresh_segments.interest_map map
    INNER JOIN fresh_segments.interest_metrics metrics
      ON map.id = metrics.interest_id::integer
    WHERE metrics.new_month_year < map.created_at::DATE;
````

### Answer:
| count |
| ----- |
| 188   |
***

## Interest Analysis


**1. Which interests have been present in all month_year dates in our dataset?**

````sql
    SELECT 
      COUNT(DISTINCT month_year) AS unique_month_year_count, 
      COUNT(DISTINCT interest_id) AS unique_interest_id_count
    FROM fresh_segments.interest_metrics;
````

### Answer:
| unique_month_year_count | unique_interest_id_count |
| ----------------------- | ------------------------ |
| 14                      | 1202                     |
***

**2. Using this same total_months measure - calculate the cumulative percentage of all records starting at 14 months - which total_months value passes the 90% cumulative percentage value?**

````sql
    WITH counted_months AS (
        SELECT
          interest_id,
          COUNT(interest_id) total_months,
          ROW_NUMBER() OVER(
            PARTITION BY COUNT(interest_id)
            ORDER BY
              COUNT(interest_id)
          ) AS rank
        FROM fresh_segments.interest_metrics AS im
        GROUP BY 1
        HAVING COUNT(interest_id) > 0
      )
    SELECT
      total_months,
      MAX(rank) AS number_of_interests,
      CAST(
        100 * SUM(MAX(rank)) OVER (
          ORDER BY
            total_months
        ) / SUM(MAX(rank)) OVER () AS numeric(10, 2)
      ) cum_top,
      CAST(
        100 - 100 * SUM(MAX(rank)) OVER (
          ORDER BY
            total_months
        ) / SUM(MAX(rank)) OVER () AS numeric(10, 2)
      ) cum_top_reversed
    FROM counted_months
    GROUP BY total_months
    ORDER BY 1;
````

### Answer:
| total_months | number_of_interests | cum_top | cum_top_reversed |
| ------------ | ------------------- | ------- | ---------------- |
| 1            | 13                  | 1.08    | 98.92            |
| 2            | 12                  | 2.08    | 97.92            |
| 3            | 15                  | 3.33    | 96.67            |
| 4            | 32                  | 5.99    | 94.01            |
| 5            | 38                  | 9.15    | 90.85            |
| 6            | 33                  | 11.90   | 88.10            |
| 7            | 90                  | 19.38   | 80.62            |
| 8            | 67                  | 24.96   | 75.04            |
| 9            | 95                  | 32.86   | 67.14            |
| 10           | 85                  | 39.93   | 60.07            |
| 11           | 95                  | 47.84   | 52.16            |
| 12           | 65                  | 53.24   | 46.76            |
| 13           | 82                  | 60.07   | 39.93            |
| 14           | 480                 | 100.00  | 0.00             |
***

**3. If we were to remove all interest_id values which are lower than the total_months value we found in the previous question - how many total data points would we be removing?**

````sql
    WITH interests AS (
        SELECT
          interest_id
        FROM fresh_segments.interest_metrics AS im
        GROUP BY 1
        HAVING COUNT(interest_id) < 6
      )
    SELECT
      COUNT(interest_id) AS number_of_interests
    FROM interests
    ORDER BY 1;
````

### Answer:
| number_of_interests |
| ------------------- |
| 110                 |
***

**4. Does this decision make sense to remove these data points from a business perspective? Use an example where there are all 14 months present to a removed interest example for your arguments - think about what it means to have less months present from a segment perspective.**

````sql
    SELECT
      im.month_year,
      COUNT(interest_id) AS number_of_excluded_interests,
      number_of_included_interests,
      ROUND(
        100 *(
          COUNT(interest_id) / number_of_included_interests :: numeric
        ),
        1
      ) AS percent_of_excluded
    FROM fresh_segments.interest_metrics AS im
      JOIN (
        SELECT
          month_year,
          COUNT(interest_id) AS number_of_included_interests
        FROM fresh_segments.interest_metrics AS im
        WHERE
          month_year IS NOT NULL
          AND interest_id :: int IN (
            SELECT
              interest_id :: int
            FROM fresh_segments.interest_metrics
            GROUP BY 1
            HAVING COUNT(interest_id) > 5
          )
        GROUP BY 1
      ) i ON im.month_year = i.month_year
    WHERE
      im.month_year IS NOT NULL
      AND interest_id :: int IN (
        SELECT
          interest_id :: int
        FROM fresh_segments.interest_metrics
        GROUP BY 1
        HAVING COUNT(interest_id) < 6
      )
    GROUP BY 1, 3
    ORDER BY 1;
````

### Answer:
| month_year | number_of_excluded_interests | number_of_included_interests | percent_of_excluded |
| ---------- | ---------------------------- | ---------------------------- | ------------------- |
| 01-2019    | 7                            | 966                          | 0.7                 |
| 02-2019    | 49                           | 1072                         | 4.6                 |
| 03-2019    | 58                           | 1078                         | 5.4                 |
| 04-2019    | 64                           | 1035                         | 6.2                 |
| 05-2019    | 30                           | 827                          | 3.6                 |
| 06-2019    | 20                           | 804                          | 2.5                 |
| 07-2018    | 20                           | 709                          | 2.8                 |
| 07-2019    | 28                           | 836                          | 3.3                 |
| 08-2018    | 15                           | 752                          | 2.0                 |
| 08-2019    | 87                           | 1062                         | 8.2                 |
| 09-2018    | 6                            | 774                          | 0.8                 |
| 10-2018    | 4                            | 853                          | 0.5                 |
| 11-2018    | 3                            | 925                          | 0.3                 |
| 12-2018    | 9                            | 986                          | 0.9                 |

***

**5. After removing these interests - how many unique interests are there for each month?**

````sql
    SELECT
      month_year,
      COUNT(interest_id) AS number_of_interests
    FROM fresh_segments.interest_metrics AS im
    WHERE
      month_year IS NOT NULL
      AND interest_id :: int IN (
        SELECT
          interest_id :: int
        FROM fresh_segments.interest_metrics
        GROUP BY 1
        HAVING COUNT(interest_id) > 5
      )
    GROUP BY 1
    ORDER BY 1;
````

### Answer:
| month_year | number_of_interests |
| ---------- | ------------------- |
| 01-2019    | 966                 |
| 02-2019    | 1072                |
| 03-2019    | 1078                |
| 04-2019    | 1035                |
| 05-2019    | 827                 |
| 06-2019    | 804                 |
| 07-2018    | 709                 |
| 07-2019    | 836                 |
| 08-2018    | 752                 |
| 08-2019    | 1062                |
| 09-2018    | 774                 |
| 10-2018    | 853                 |
| 11-2018    | 925                 |
| 12-2018    | 986                 |
***

## Segment Analysis

**1. Using our filtered dataset by removing the interests with less than 6 months worth of data, which are the top 10 and bottom 10 interests which have the largest composition values in any month_year? Only use the maximum composition value for each interest but you must keep the corresponding month_year**

````sql
    SELECT
      interests.month_year,
      interests.interest_name,
      interests.composition,
      i_max_new.composition AS max_composition,
      i_max_new.month_year AS max_composition_month
    FROM
      (
        (
          WITH max_interests AS (
            SELECT
              month_year,
              interest_name,
              composition,
              RANK() OVER (
                PARTITION BY interest_name
                ORDER BY
                  composition DESC
              ) AS max_rank
            FROM fresh_segments.interest_metrics AS im
            JOIN fresh_segments.interest_map AS m ON m.id = im.interest_id :: int
            WHERE
              month_year IS NOT NULL
              AND interest_id :: int in (
                SELECT
                  interest_id :: int
                FROM fresh_segments.interest_metrics
                GROUP BY 1
                HAVING COUNT(interest_id) > 5
              )
            GROUP BY 1, 2, 3
          )
          SELECT
            month_year,
            interest_name,
            composition
          FROM max_interests
          WHERE max_rank = 1
          GROUP BY 1, 2, 3
          ORDER BY 3 DESC
          LIMIT 10
        )
        UNION
          (
            WITH min_interests AS (
              SELECT
                month_year,
                interest_name,
                composition,
                RANK() OVER (
                  PARTITION BY interest_name
                  ORDER BY
                    composition
                ) AS min_rank
              FROM fresh_segments.interest_metrics AS im
              JOIN fresh_segments.interest_map AS m ON m.id = im.interest_id :: int
              WHERE
                month_year IS NOT NULL
                AND interest_id :: int in (
                  SELECT
                    interest_id :: int
                  FROM fresh_segments.interest_metrics
                  GROUP BY 1
                  HAVING COUNT(interest_id) > 5
                )
              GROUP BY 1, 2, 3
            )
            SELECT
              month_year,
              interest_name,
              composition
            FROM min_interests
            WHERE min_rank = 1
            GROUP BY 1, 2, 3
            ORDER BY 3
            LIMIT 10
          )
      ) AS interests
      JOIN (
        WITH max_interests AS (
          SELECT
            month_year,
            interest_name,
            composition,
            RANK() OVER (
              PARTITION BY interest_name
              ORDER BY
                composition DESC
            ) AS max_rank
          FROM fresh_segments.interest_metrics AS im
          JOIN fresh_segments.interest_map AS m ON m.id = im.interest_id :: int
          WHERE
            month_year IS NOT NULL
            AND interest_id :: int in (
              SELECT
                interest_id :: int
              FROM fresh_segments.interest_metrics
              GROUP BY 1
              HAVING COUNT(interest_id) > 5
            )
          GROUP BY 1, 2, 3
        )
        SELECT
          month_year,
          interest_name,
          composition
        FROM max_interests
        WHERE max_rank = 1
        GROUP BY 1, 2, 3
        ORDER BY 3 DESC
      ) i_max_new on interests.interest_name = i_max_new.interest_name
    ORDER BY 3 DESC;
````

### Answer:
| month_year | interest_name                     | composition | max_composition | max_composition_month |
| ---------- | --------------------------------- | ----------- | --------------- | --------------------- |
| 12-2018    | Work Comes First Travelers        | 21.2        | 21.2            | 12-2018               |
| 07-2018    | Gym Equipment Owners              | 18.82       | 18.82           | 07-2018               |
| 07-2018    | Furniture Shoppers                | 17.44       | 17.44           | 07-2018               |
| 07-2018    | Luxury Retail Shoppers            | 17.19       | 17.19           | 07-2018               |
| 10-2018    | Luxury Boutique Hotel Researchers | 15.15       | 15.15           | 10-2018               |
| 12-2018    | Luxury Bedding Shoppers           | 15.05       | 15.05           | 12-2018               |
| 07-2018    | Shoe Shoppers                     | 14.91       | 14.91           | 07-2018               |
| 07-2018    | Cosmetics and Beauty Shoppers     | 14.23       | 14.23           | 07-2018               |
| 07-2018    | Luxury Hotel Guests               | 14.1        | 14.1            | 07-2018               |
| 07-2018    | Luxury Retail Researchers         | 13.97       | 13.97           | 07-2018               |
| 05-2019    | LED Lighting Shoppers             | 1.53        | 5.96            | 07-2018               |
| 05-2019    | Crochet Enthusiasts               | 1.53        | 2.97            | 11-2018               |
| 06-2019    | Online Directory Searchers        | 1.53        | 3.77            | 07-2018               |
| 05-2019    | Beer Aficionados                  | 1.52        | 6.53            | 07-2018               |
| 05-2019    | Gastrointestinal Researchers      | 1.52        | 6.29            | 07-2018               |
| 06-2019    | Disney Fans                       | 1.52        | 2.95            | 10-2018               |
| 05-2019    | Philadelphia 76ers Fans           | 1.52        | 2.77            | 08-2019               |
| 04-2019    | United Nations Donors             | 1.52        | 2.68            | 02-2019               |
| 06-2019    | New York Giants Fans              | 1.52        | 2.68            | 08-2019               |
| 05-2019    | Mowing Equipment Shoppers         | 1.51        | 2.57            | 03-2019               |

***

**2. Which 5 interests had the lowest average ranking value?**

````sql
    WITH ranking AS (
        SELECT
          interest_name,
          AVG(ranking) :: numeric(10, 2) AS avg_ranking,
          RANK() OVER (
            ORDER BY
              AVG(ranking) DESC
          ) AS rank
        FROM fresh_segments.interest_metrics AS im
        JOIN fresh_segments.interest_map AS m ON m.id = im.interest_id :: int
        WHERE
          month_year IS NOT NULL
          AND interest_id :: int IN (
            SELECT
              interest_id :: int
            FROM fresh_segments.interest_metrics
            GROUP BY 1
            HAVING COUNT(interest_id) > 5
          )
        GROUP BY 1
      )
    SELECT
      interest_name,
      avg_ranking
    FROM ranking
    WHERE rank between 0 AND 5;
````

### Answer:
| interest_name                                      | avg_ranking |
| -------------------------------------------------- | ----------- |
| League of Legends Video Game Fans                  | 1037.29     |
| Computer Processor and Data Center Decision Makers | 974.13      |
| Astrology Enthusiasts                              | 968.50      |
| Medieval History Enthusiasts                       | 961.71      |
| Budget Mobile Phone Researchers                    | 961.00      |
***

**3. Which 5 interests had the largest standard deviation in their percentile_ranking value?**

````sql
    WITH ranking AS (
        SELECT
          id,
          interest_name,
          STDDEV(percentile_ranking) :: numeric(10, 2) AS standard_deviation,
          RANK() OVER (
            ORDER BY
              STDDEV(percentile_ranking) DESC
          ) AS rank
        FROM fresh_segments.interest_metrics AS im
        JOIN fresh_segments.interest_map AS m ON m.id = im.interest_id :: int
        WHERE
          month_year IS NOT NULL
          AND interest_id :: int IN (
            SELECT
              interest_id :: int
            FROM fresh_segments.interest_metrics
            GROUP BY 1
            HAVING COUNT(interest_id) > 5
          )
        GROUP BY 1, 2
      )
    SELECT
      interest_name,
      standard_deviation
    FROM ranking
    WHERE rank between 0 AND 5;
````

### Answer:
| interest_name                          | standard_deviation |
| -------------------------------------- | ------------------ |
| Techies                                | 30.18              |
| Entertainment Industry Decision Makers | 28.97              |
| Oregon Trip Planners                   | 28.32              |
| Personalized Gift Shoppers             | 26.24              |
| Tampa and St Petersburg Trip Planners  | 25.61              |
***

**4. For the 5 interests found in the previous question - what was minimum and maximum percentile_ranking values for each interest and its corresponding year_month value? Can you describe what is happening for these 5 interests?**

````sql
    WITH ranking AS (
        SELECT
          month_year,
          id,
          interest_name,
          percentile_ranking,
          RANK() OVER (
            PARTITION BY id
            ORDER BY
              percentile_ranking
          ) AS min_rank,
          RANK() OVER (
            PARTITION BY id
            ORDER BY
              percentile_ranking DESC
          ) AS max_rank
        FROM fresh_segments.interest_metrics AS im
        JOIN fresh_segments.interest_map AS m ON m.id = im.interest_id :: int
        WHERE
          month_year IS NOT NULL
          AND interest_id :: int IN (
            SELECT
              interest_id :: int
            FROM fresh_segments.interest_metrics
            GROUP BY 1
            HAVING COUNT(interest_id) > 5
          )
          AND id IN (
            WITH ranking AS (
              SELECT
                id,
                interest_name,
                STDDEV(percentile_ranking) :: numeric(10, 2) AS standard_deviation,
                RANK() OVER (
                  ORDER BY
                    STDDEV(percentile_ranking) DESC
                ) AS rank
              FROM fresh_segments.interest_metrics AS im
              JOIN fresh_segments.interest_map AS m ON m.id = im.interest_id :: int
              WHERE month_year IS NOT NULL AND interest_id :: int IN (
                  SELECT
                    interest_id :: int
                  FROM fresh_segments.interest_metrics
                  GROUP BY 1
                  HAVING COUNT(interest_id) > 5
                )
              GROUP BY 1, 2
            )
            SELECT
              id
            FROM ranking
            WHERE rank between 0 AND 5
          )
        GROUP BY 1, 2, 3, 4
      )
    SELECT
      month_year,
      interest_name,
      percentile_ranking
    FROM ranking
    WHERE min_rank = 1 OR max_rank = 1
    GROUP BY 1, 2, 3
    ORDER BY 2, 3 DESC;
````

### Answer:
| month_year | interest_name                          | percentile_ranking |
| ---------- | -------------------------------------- | ------------------ |
| 07-2018    | Entertainment Industry Decision Makers | 86.15              |
| 08-2019    | Entertainment Industry Decision Makers | 11.23              |
| 11-2018    | Oregon Trip Planners                   | 82.44              |
| 07-2019    | Oregon Trip Planners                   | 2.2                |
| 03-2019    | Personalized Gift Shoppers             | 73.15              |
| 06-2019    | Personalized Gift Shoppers             | 5.7                |
| 07-2018    | Tampa and St Petersburg Trip Planners  | 75.03              |
| 03-2019    | Tampa and St Petersburg Trip Planners  | 4.84               |
| 07-2018    | Techies                                | 86.69              |
| 08-2019    | Techies                                | 7.92               |

***

**5. How would you describe our customers in this segment based off their composition and ranking values? What sort of products or services should we show to these customers and what should we avoid?**

````sql

````

### Answer:

***

## Index Analysis

The index_value is a measure which can be used to reverse calculate the average composition for Fresh Segments’ clients.

Average composition can be calculated by dividing the composition column by the index_value column rounded to 2 decimal places.

**1. What is the top selling product for each category?**

````sql
    SET
      SEARCH_PATH = fresh_segments;
    WITH ranking AS (
        SELECT
          month_year,
          id,
          interest_name,
          avg_composition,
          RANK() OVER (
            PARTITION BY month_year
            ORDER BY
              avg_composition DESC
          ) AS max_rank
        FROM
          interest_metrics AS im
          JOIN interest_map AS m ON m.id = im.interest_id :: int,
          LATERAL(
            SELECT
              (composition / index_value) :: numeric(10, 2) AS avg_composition
          ) ac
        WHERE
          month_year IS NOT NULL
          AND interest_id :: int IN (
            SELECT
              interest_id :: int
            FROM
              interest_metrics
            GROUP BY
              1
            HAVING
              COUNT(interest_id) > 5
          )
        GROUP BY
          1,
          2,
          3,
          4
      )
    SELECT
      month_year,
      interest_name,
      avg_composition
    FROM
      ranking
    WHERE
      max_rank between 1
      AND 10
    ORDER BY
      1,
      3 DESC
    LIMIT 10;
````

### Answer:
| month_year | interest_name                                        | avg_composition |
| ---------- | ---------------------------------------------------- | --------------- |
| 01-2019    | Work Comes First Travelers                           | 7.66            |
| 01-2019    | Solar Energy Researchers                             | 7.05            |
| 01-2019    | Readers of Honduran Content                          | 6.67            |
| 01-2019    | Nursing and Physicians Assistant Journal Researchers | 6.46            |
| 01-2019    | Luxury Bedding Shoppers                              | 6.46            |
| 01-2019    | Alabama Trip Planners                                | 6.44            |
| 01-2019    | New Years Eve Party Ticket Purchasers                | 6.16            |
| 01-2019    | Teen Girl Clothing Shoppers                          | 5.96            |
| 01-2019    | Christmas Celebration Researchers                    | 5.65            |
| 01-2019    | Chelsea Fans                                         | 5.48            |

***

**2. For all of these top 10 interests - which interest appears the most often?**

````sql
    SET
      SEARCH_PATH = fresh_segments;
    WITH ranking AS (
        SELECT
          month_year,
          id,
          interest_name,
          avg_composition,
          RANK() OVER (
            PARTITION BY month_year
            ORDER BY
              avg_composition DESC
          ) AS max_rank
        FROM
          interest_metrics AS im
          JOIN interest_map AS m on m.id = im.interest_id :: int,
          LATERAL(
            SELECT
              (composition / index_value) :: numeric(10, 2) AS avg_composition
          ) ac
        WHERE
          month_year IS NOT NULL
          AND interest_id :: int IN (
            SELECT
              interest_id :: int
            FROM
              interest_metrics
            GROUP BY
              1
            HAVING
              COUNT(interest_id) > 5
          )
        GROUP BY
          1,
          2,
          3,
          4
      )
    SELECT
      interest_name,
      COUNT(interest_name) AS months_in_top_1
    FROM
      ranking
    WHERE
      max_rank = 1
    GROUP BY
      1
    ORDER BY
      2 DESC;
````

### Answer:
| interest_name                 | months_in_top_1 |
| ----------------------------- | --------------- |
| Work Comes First Travelers    | 6               |
| Las Vegas Trip Planners       | 4               |
| Alabama Trip Planners         | 1               |
| Cosmetics and Beauty Shoppers | 1               |
| Readers of Honduran Content   | 1               |
| Solar Energy Researchers      | 1               |
***

**3. What is the average of the average composition for the top 10 interests for each month?**

````sql
    SET
      SEARCH_PATH = fresh_segments;
    SELECT
      month_year,
      AVG(avg_composition) :: numeric(10, 2) AS average_rating
    FROM
      (
        WITH ranking AS (
          SELECT
            month_year,
            id,
            interest_name,
            avg_composition,
            RANK() OVER (
              PARTITION BY month_year
              ORDER BY
                avg_composition DESC
            ) AS max_rank
          FROM
            interest_metrics AS im
            JOIN interest_map AS m ON m.id = im.interest_id :: int,
            LATERAL(
              SELECT
                (composition / index_value) :: numeric(10, 2) AS avg_composition
            ) ac
          WHERE
            month_year IS NOT NULL
            AND interest_id :: int IN (
              SELECT
                interest_id :: int
              FROM
                interest_metrics
              GROUP BY
                1
              HAVING
                COUNT(interest_id) > 5
            )
          GROUP BY
            1,
            2,
            3,
            4
        )
        SELECT
          month_year,
          interest_name,
          avg_composition
        FROM
          ranking
        WHERE
          max_rank between 1
          AND 10
      ) r
    GROUP BY
      1
    ORDER BY
      1;
````

### Answer:
| month_year | average_rating |
| ---------- | -------------- |
| 01-2019    | 6.32           |
| 02-2019    | 6.58           |
| 03-2019    | 6.12           |
| 04-2019    | 5.75           |
| 05-2019    | 3.54           |
| 06-2019    | 2.43           |
| 07-2018    | 6.04           |
| 07-2019    | 2.77           |
| 08-2018    | 5.95           |
| 08-2019    | 2.63           |
| 09-2018    | 6.90           |
| 10-2018    | 7.07           |
| 11-2018    | 6.62           |
| 12-2018    | 6.65           |
***

**4. What is the 3 month rolling average of the max average composition value from September 2018 to August 2019 and include the previous top ranking interests in the same output shown below.**

````sql
    SET
      SEARCH_PATH = fresh_segments;
    SELECT
      *
    FROM
      (
        WITH ranking AS (
          SELECT
            month_year,
            id,
            interest_name,
            avg_composition,
            RANK() OVER (
              PARTITION BY month_year
              ORDER BY
                avg_composition DESC
            ) AS max_rank
          FROM
            interest_metrics AS im
            JOIN interest_map AS m ON m.id = im.interest_id :: int,
            LATERAL(
              SELECT
                (composition / index_value) :: numeric(10, 2) AS avg_composition
            ) ac
          WHERE
            month_year IS NOT NULL
            AND interest_id :: int IN (
              SELECT
                interest_id :: int
              FROM
                interest_metrics
              GROUP BY
                1
              HAVING
                COUNT(interest_id) > 5
            )
          GROUP BY
            1,
            2,
            3,
            4
        )
        SELECT
          month_year,
          interest_name,
          avg_composition AS max_index_composition,
          (
            AVG(avg_composition) OVER(
              ORDER BY
                month_year ROWS BETWEEN 2 PRECEDING
                AND CURRENT ROW
            )
          ) :: numeric(10, 2) AS _3_month_moving_avg,
          CONCAT(
            LAG(interest_name) OVER (
              ORDER BY
                month_year
            ),
            ': ',
            LAG(avg_composition) OVER (
              ORDER BY
                month_year
            )
          ) AS _1_month_ago,
          CONCAT(
            LAG(interest_name, 2) OVER (
              ORDER BY
                month_year
            ),
            ': ',
            LAG(avg_composition, 2) OVER (
              ORDER BY
                month_year
            )
          ) AS _2_month_ago
        FROM
          ranking
        WHERE
          max_rank = 1
      ) r
    WHERE
      month_year > '2018-08-01'
    ORDER BY
      1;
````

### Answer:
There are no results to be displayed.
***

**5. Provide a possible reason why the max average composition might change from month to month? Could it signal something is not quite right with the overall business model for Fresh Segments?**

````sql

````

### Answer:

***

## Resources and References:

I obtained my sources, dataset, and information from the following website: [Here](https://8weeksqlchallenge.com/case-study-7v /).

***
