# **Project Assignment: "The Property Analyst"**

**Goal:** Identify the most "undervalued" town.

Work in two steps — everything you need was taught in this lesson:

**Step A — Find the undervalued towns.** Write a `GROUP BY town` query with `HAVING AVG(resale_price) < 460000` to list towns where the average price is under $460,000. Run it and note down the town names.

✅✅✅ **Answer** ✅✅✅ -> Yishun
```sql
SELECT 
  town, 
  AVG(resale_price) AS avg_price
FROM 
 resale_flat_prices_2017
GROUP BY 
 town
HAVING 
 avg_price < 460000   -- filters groups after aggregation
ORDER BY 
 avg_price DESC; 
```

<br>

**Step B — Find the biggest flats in those towns.** Write a second query that filters flats using the town names you found, with a plain `WHERE town IN ('…', '…', …)`, then sort by floor\_area\_sqm descending and keep the top 5.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT 
  *,
FROM 
 resale_flat_prices_2017
WHERE
 town IN ('YISHUN')
ORDER BY 
 floor_area_sqm DESC
LIMIT
 5;
```

<br>

**Level Up:** Create a new column called price\_per\_sqm and find which town has the lowest average price per square meter.

✅✅✅ **Answer** ✅✅✅
```sql
ALTER TABLE                   -- add new column
  resale_flat_prices_2017
ADD COLUMN
  price_per_sqm INTEGER;

UPDATE                        -- add formula to new column 
  resale_flat_prices_2017
SET
  price_per_sqm = resale_price / floor_area_sqm

SELECT                        -- find lowest average price/sqm
 town,
 AVG(price_per_sqm) AS ave_price_per_sqm
FROM 
 resale_flat_prices_2017
GROUP BY
 town
ORDER BY
 ave_price_per_sqm
LIMIT
 1;
```

<br>

**🚀 Stretch goal (Lesson 1.5 preview):** a *subquery* lets you nest both steps into one query — try it after the two-step way works.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT 
    town, 
    block, 
    street_name, 
    floor_area_sqm, 
    resale_price
FROM resale_flat_prices_2017
WHERE town IN (                           -- This 'Inner Query' finds our undervalued towns
    SELECT town
    FROM resale_flat_prices_2017
    GROUP BY town
    HAVING AVG(resale_price) < 460000
)
ORDER BY floor_area_sqm DESC
LIMIT 5;
```

<br>

---

Write the SQL DML statements for the following questions.

## Instructions

Write your SQL answers in the code blocks below each question. Check your work against the solution key at the bottom. To share your work with peers, post in **#peer-reviews** on Discord. For questions, post in **#questions**.

### Question 1

Select the minimum and maximum price per sqm of all the flats.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
  *
FROM
  resale_flat_prices_2017
ORDER BY price_per_sqm DESC
LIMIT 1;

SELECT
  *
FROM
  resale_flat_prices_2017
ORDER BY price_per_sqm
LIMIT 1;

-- max & min in 1 query with price_per_sqm info only
SELECT
    MIN(resale_price / floor_area_sqm) AS min_price_per_sqm, 
    MAX(resale_price / floor_area_sqm) AS max_price_per_sqm
FROM resale_flat_prices_2017;
```

### Question 2

Select the average price per sqm for flats in each town.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
  town,
  AVG(price_per_sqm) AS ave_price_per_sqm
FROM
  resale_flat_prices_2017
GROUP BY
  town,
ORDER BY
  ave_price_per_sqm DESC;
```

### Question 3

Categorize flats into price ranges and count how many flats fall into each category:

- Under $400,000: 'Budget'
- $400,000 to $700,000: 'Mid-Range'
- Above $700,000: 'Premium'
  Show the counts in descending order.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT 
 CASE
  WHEN resale_price > 700000 THEN 'Premium'
  WHEN resale_price < 400000 THEN 'Budget'
  ELSE 'Mid-Range'
 END AS price_category,
 COUNT(*) AS number_of_flats
FROM 
 resale_flat_prices_2017
GROUP BY
 price_category
ORDER BY
 number_of_flats DESC;
```

### Question 4

Count the number of flats sold in each town during the first quarter of 2017 (January to March).

✅✅✅ **Answer** ✅✅✅
```sql
SELECT 
 town,
 COUNT(*) AS flats_sold
FROM 
 resale_flat_prices_2017
WHERE
 month IN('2017-01', '2017-02', '2017-03')
GROUP BY
 town
ORDER BY
 flats_sold DESC;
```

---
---

## **✅ Solutions**

*Attempt all questions before expanding the solutions below.*

<details>
<summary>Click to reveal — Project: The Property Analyst</summary>

### Part 1: The Core Solution (Two Steps)

**Step A — find the undervalued towns:**

```sql
SELECT town
FROM resale_flat_prices_2017
GROUP BY town
HAVING AVG(resale_price) < 450000;
```

Run it and note the town names it returns.

**Step B — find the biggest flats in those towns** (substitute the names Step A gave you):

```sql
SELECT town, block, street_name, floor_area_sqm, resale_price
FROM resale_flat_prices_2017
WHERE town IN ('WOODLANDS', 'CHOA CHU KANG')  -- use YOUR Step A results
ORDER BY floor_area_sqm DESC
LIMIT 5;
```

### 🚀 Stretch goal (Lesson 1.5 preview)

A *subquery* lets you nest both steps into one query — try it after the two-step way works. It allows us to first identify the "undervalued" list and then look inside that list for the biggest homes, all in one go.

```sql
-- The Stretch Solution Query
SELECT 
    town, 
    block, 
    street_name, 
    floor_area_sqm, 
    resale_price
FROM resale_flat_prices_2017
WHERE town IN (
    -- This 'Inner Query' finds our undervalued towns
    SELECT town
    FROM resale_flat_prices_2017
    GROUP BY town
    HAVING AVG(resale_price) < 450000
)
ORDER BY floor_area_sqm DESC
LIMIT 5;
```
The Breakdown:

- The Inner Query (The "Filter"): We use GROUP BY town and HAVING to find which towns have an average price below $450k. These are our "undervalued" targets (e.g., Woodlands, Choa Chu Kang).

- The Outer Query (The "Search"): We ask SQL to look at the entire table but only show rows where the town is in our target list.

- The Result: Finally, we ORDER BY floor_area_sqm DESC to put the biggest units at the top and LIMIT 5 to keep it concise.

</details>

<details>
<summary>Click to reveal — Level Up Solution</summary>

### Part 2: The "Level Up" Solution
This challenge tests the ability to perform math across columns and then aggregate that math.

```sql
-- Finding the town with the lowest average price per square meter
SELECT 
    town, 
    AVG(resale_price / floor_area_sqm) AS avg_price_per_sqm
FROM resale_flat_prices_2017
GROUP BY town
ORDER BY avg_price_per_sqm ASC
LIMIT 1;
```

The Breakdown:

- The Calculation: For every single row, we divide resale_price by floor_area_sqm.

- The Aggregation: We then take the AVG() of those calculated results for each town.

- The Winner: By ordering ASC (ascending) and using LIMIT 1, we isolate the single town that gives you the most space for every dollar spent.

</details>

<details>
<summary>Click to reveal — Questions 1–4 Solutions</summary>

### Question 1

```sql
SELECT 
    MIN(resale_price / floor_area_sqm) AS min_price_per_sqm, 
    MAX(resale_price / floor_area_sqm) AS max_price_per_sqm
FROM resale_flat_prices_2017;
```

### Question 2

```sql
SELECT 
    town, 
    AVG(resale_price / floor_area_sqm) AS avg_price_per_sqm
FROM resale_flat_prices_2017
GROUP BY town
ORDER BY avg_price_per_sqm DESC;
```

### Question 3

```sql
SELECT 
    CASE 
        WHEN resale_price < 400000 THEN 'Budget'
        WHEN resale_price <= 700000 THEN 'Mid-Range'
        ELSE 'Premium' 
    END AS price_category,
    COUNT(*) AS number_of_flats
FROM resale_flat_prices_2017
GROUP BY price_category
ORDER BY number_of_flats DESC;
```

### Question 4

```sql
SELECT 
    town, 
    COUNT(*) AS flats_sold
FROM resale_flat_prices_2017
WHERE month IN ('2017-01', '2017-02', '2017-03')
GROUP BY town
ORDER BY flats_sold DESC;
```

</details>

---

**Post-class reading:**
- [DuckDB Documentation on Aggregate Functions](https://duckdb.org/docs/sql/aggregates)
- *SQL Performance Tuning for Beginners* — Why `SELECT *` is often avoided in production
