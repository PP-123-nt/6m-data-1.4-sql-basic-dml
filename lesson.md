# 📚 Lesson 1.4: SQL Basic — DML

## Session Overview

| | |
|---|---|
| **Duration** | 3 hours |
| **Format** | Flipped Classroom + Hands-On SQL in DbGate |
| **Tools** | DuckDB + DbGate |
| **Database** | `db/unit-1-4.db` — table: `resale_flat_prices_2017` |
| **Dataset** | Singapore HDB Resale Flat Prices 2017 |

## Agenda

| Time | Part | Topic |
|------|------|-------|
| 0:00 – 0:55 | Part 1 | Selection, Filtering & Sorting — SELECT, WHERE, ORDER BY |
| 0:55 – 1:00 | Break | — |
| 1:00 – 1:55 | Part 2 | Aggregates & Grouping — COUNT, SUM, AVG; GROUP BY; HAVING |
| 1:55 – 2:00 | Break | — |
| 2:00 – 2:55 | Part 3 | Advanced Logic & Data Cleaning — CASE, CAST, date extraction |

---

## 🏃 Part 1: The Art of Asking — Selection, Filtering & Sorting (55 min)

### 🎯 Learning Objectives
Retrieve specific columns, apply mathematical operators, filter datasets, and organize results using sorting.

### Concept Overview

**Analogy:** Querying a database is like having a conversation with your data:
- `SELECT` says: *"Give me these columns."*
- `FROM` says: *"...from this table."*
- `WHERE` says: *"...but only rows matching this condition."*
- `ORDER BY` says: *"...sorted this way."*

### 🛠️ Workshop — Task 1: Basic Retrieval & Sorting

Open DbGate and create a new connection to `db/unit-1-4.db`.

> The table we will be using is `main.resale_flat_prices_2017`. It contains HDB resale flat prices based on registration date from Jan-2017 onwards.
>
> **Data Dictionary:**
>
> | Column Name | Data Type | Description |
> |-------------|-----------|-------------|
> | month | Text (Month) "YYYY-MM" | Transaction month |
> | town | Text | Town name |
> | flat_type | Text | Type of flat |
> | block | Text | Block number |
> | street_name | Text | Street name |
> | storey_range | Text | Storey range |
> | floor_area_sqm | Numeric | Floor area in sqm |
> | flat_model | Text | Flat model |
> | lease_commence_date | Text (Year) | Year lease commenced |
> | remaining_lease | Text | Remaining lease |
> | resale_price | Numeric ($) | Resale transaction price |

**Examples:**

See all columns:

```sql
SELECT
  *
FROM
  resale_flat_prices_2017;
```

Select specific columns and sort by price (ascending is default):

```sql
SELECT
  town, flat_type, resale_price
FROM
  resale_flat_prices_2017
ORDER BY
  resale_price;
```

Sort by price from highest to lowest:

```sql
SELECT
  *
FROM
  resale_flat_prices_2017
ORDER BY
  resale_price DESC;
```

Sort by town alphabetically, then by price (highest first):

```sql
SELECT
  town, street_name, resale_price
FROM
  resale_flat_prices_2017
ORDER BY
  town ASC,
  resale_price DESC;
```

👋 **Exercise 1:**
- Select any 3 columns from the table.
- Select flats from highest to lowest resale price in Punggol.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
  town, street_name, resale_price
FROM
  resale_flat_prices_2017
WHERE town = 'PUNGGOL'
ORDER BY
  resale_price DESC;
``` 

> **What's Your Query?**
> "If our table had 100 columns and a million rows, what would happen to our computer's memory if we always used `SELECT *`?"
>
> `SELECT *` pull all 100 columns for every row into memory, even columns you never need. With million rows, extra unused columns multiply memory use and network/disk transfer massively — could mean gigabytes wasted, slow query, risk of running out of RAM or crashing client app. Better: select only needed columns, e.g. `SELECT flat_type, resale_price FROM ...` — cuts memory and speeds query way down.
> 
> **Assumptions**
>
> 100 columns, 1,000,000 rows = 100,000,000 cells total
> Average cell size ~20 bytes (mix of ints, floats, short strings — raw bytes, no overhead)
> **Raw data size**
> 100M cells × 20 bytes = 2,000,000,000 bytes ≈ 1.9 GB
>
> **Reality check — in-memory overhead**
> Raw bytes ≠ actual RAM used. In Python/pandas, each value carries object overhead:
>
> Pandas object dtype (strings): ~50-80 bytes/cell overhead alone
> If most columns are numeric (int64/float64): 8 bytes/cell, closer to raw estimate
> Plus index, dtype metadata, duplicated buffers during operations (copy on filter/sort)
Realistic range
>
> **All-numeric table:** ~1-2 GB
> Mixed with several string/object columns: 3-6 GB, since pandas object columns cost 5-10x raw size
> 
> **Takeaway:** even "just 1.9 GB" of raw data can balloon to several GB in memory depending on dtypes — and that's before doing any transform that copies the DataFrame. Selecting only needed columns cuts this proportionally (e.g. 5 columns instead of 100 → ~20x less memory).
>
---

### 🛠️ Workshop — Task 2: Transformations & Filtering

**Operators:**

| Operator | Description |
|----------|-------------|
| `+` | Addition |
| `-` | Subtraction |
| `*` | Multiplication |
| `/` | Division |
| `%` | Modulo |  

> **Modulo (`%`)** returns remainder after division, not quotient. Example: `10 % 3` = `1` (3 goes into 10 three times, remainder 1). Common use: check even/odd (`x % 2 = 0` means even), or group rows into buckets (`row_num % 5` cycles 0-4).

Example — calculate price in thousands and rename for clarity:

```sql
SELECT
  street_name,
  resale_price / 1000 AS price_k  -- Use AS to rename the column
FROM
  resale_flat_prices_2017
WHERE
  town = 'PUNGGOL'
  AND resale_price > 500000
ORDER BY
  resale_price DESC;
```

👋👋👋 **Group Exercise 2:**

- "I want to find a home for my parents. They need something larger than 100sqm, but my budget is strictly under $600,000. How would we write that rule?"
- Define your own filter for flats in a specific town, with a price range and floor area requirement etc. Then sort the results by resale price (highest first) and floor area (largest first).

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
  block, street_name, flat_type, floor_area_sqm, resale_price
FROM
  resale_flat_prices_2017
WHERE
  town = 'BISHAN' AND resale_price < 600000 AND floor_area_sqm > 100
ORDER BY
  resale_price DESC, floor_area_sqm DESC;
```

> **What's Your Query?**

**Comparison and Logical Operators:**

| Operator | Description |
|----------|-------------|
| `=` | Equal |
| `<>` | Not equal |
| `>` | Greater |
| `>=` | Greater or equal |
| `<` | Less |
| `<=` | Less or equal |
| `AND` | Logical AND |
| `OR` | Logical OR |
| `NOT` | Logical NOT |

Example:

```sql
SELECT
  *
FROM
  resale_flat_prices_2017
WHERE
  town = 'BUKIT MERAH';
```

👋 **Exercise 3 — basic filters:**
- Select flats with floor area greater than 100 sqm.
- Select flats with resale price between 400,000 and 500,000.
- Select flats with lease commence date later than year 2000 and floor area greater than 100 sqm.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT * FROM resale_flat_prices_2017
WHERE 
  town IN ('BUKIT MERAH', 'BUKIT TIMAH','PUNGGOL') 
  AND resale_price BETWEEN 400000 AND 500000 
  AND floor_area_sqm > 100 
  AND lease_commence_date > 2000
ORDER BY
  resale_price DESC, floor_area_sqm DESC;
```


**Advanced Filters:**

```sql
-- IN – one of several values
SELECT * FROM resale_flat_prices_2017
WHERE town IN ('BUKIT MERAH', 'BUKIT TIMAH');

-- BETWEEN – numbers in a range
SELECT * FROM resale_flat_prices_2017
WHERE resale_price BETWEEN 400000 AND 500000;

-- LIKE – pattern matching
SELECT * FROM resale_flat_prices_2017
WHERE town LIKE 'B%';

-- DISTINCT – remove duplicates
SELECT DISTINCT town FROM resale_flat_prices_2017;
```

👋 **Exercise 4:**
- Return the unique flat types and flat models.
- Find all towns starting with "P".

✅✅✅ **Answer** ✅✅✅
```sql
SELECT DISTINCT town, flat_type, flat_model, 
FROM main.resale_flat_prices_2017
WHERE town LIKE 'P%'
ORDER BY flat_type, flat_model;
```


**Functions:**

| Function | Description |
|----------|-------------|
| `ABS()` | Absolute value of a number |
| `ROUND()` | Round to a number of decimal places |
| `LOWER()` | String in lowercase |
| `UPPER()` | String in uppercase |
| `LENGTH()` | Length of a string |
| `TRIM()` | Remove leading and trailing spaces |
| `CONCAT()` | Concatenate strings |

**Example:**

```sql
SELECT
  LOWER(town) AS town_lower,
  CONCAT(block, ' ', street_name) AS address
FROM
  resale_flat_prices_2017;
```

### 💬 Reflection

- Do SQL keywords need to be capitalized? Why do we use uppercase for them anyway?
- How would a real estate app or website use these filters when a user moves a slider on their screen?

---

## 🏃 Part 2: Finding the Big Picture — Aggregates & Grouping (55 min)

### 🎯 Learning Objectives
Summarise datasets using aggregate functions and use the `HAVING` clause to filter those summaries.

### Concept Overview

So far every query returned one row per flat. But questions like "what's the average price in each town?" aren't about individual flats — they're about whole towns. Aggregation is like emptying a drawer of receipts onto the table and reading out a single total. `GROUP BY town` first sorts the receipts into one pile per town, then reads out one total per pile. The catch: once you've grouped, the individual receipts are gone — you can only see the piles.

**Aggregate Functions:**

| Function | Description |
|----------|-------------|
| `COUNT()` | Number of rows |
| `SUM()` | Sum of values |
| `AVG()` | Average value |
| `MIN()` | Minimum value |
| `MAX()` | Maximum value |
| `FIRST()` | First value in a column |
| `LAST()` | Last value in a column |

### 🛠️ Workshop — Task 3: Basic Aggregates

How many transactions happened?

```sql
SELECT
  COUNT(*)
FROM
  resale_flat_prices_2017;
```

What is the most expensive flat ever sold in this dataset?

```sql
SELECT
  MAX(resale_price)
FROM
  resale_flat_prices_2017;
```

Average resale price overall:

```sql
SELECT
  AVG(resale_price) AS avg_price_overall
FROM
  resale_flat_prices_2017;
```

Average price per town:

```sql
SELECT
  town,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
GROUP BY
  town;
```

👋 **Exercise 5:**
- Select the average resale price of flats in Bishan.

✅✅✅ **Answer** ✅✅✅ -> Bishan average resale by flat type
```sql
SELECT
  flat_type,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
WHERE town = 'BISHAN'
GROUP BY
  flat_type; 
```

- Select the total resale value (price) of flats in Tampines.
```sql
SELECT
  town,
  SUM(resale_price) AS total_price
FROM
  resale_flat_prices_2017
WHERE town = 'TAMPINES'
GROUP BY
  town; 
```


---

### 🛠️ Workshop — Task 4: GROUP BY & HAVING

> **Question:** Look at this query. If you want to find the town where the average price is more than $600,000, can you use `WHERE` as a filter? Run this SQL and find out what's wrong.

```sql
-- This will fail — why?
SELECT
  town,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
WHERE
  avg_price > 600000
ORDER BY
  avg_price DESC;
```

`WHERE` filters individual rows **before** they are grouped. To filter on aggregated results, use `HAVING` — the *post-grouping filter*.

💡 SQL execution order: 
1. FROM / JOIN
2. WHERE
3. GROUP BY
4. AGGREGATE (SUM/AVG/COUNT/MIN/MAX)
5. HAVING
6. SELECT
7. ORDER BY
8. LIMIT

💡💡💡 **Example** 💡💡💡
```sql
SELECT 
 town, 
 AVG(resale_price) AS avg_price,
 CAST(AVG(resale_price) AS INTEGER) AS avg_price_int,   -- convert to INTEGER
 CASE                                                   -- add price category column 
  WHEN avg_price_int >= 800000 THEN 'High'
  WHEN avg_price_int >= 650000 THEN 'Mid'
  ELSE 'Budget'
 END AS ave_price_category
FROM 
 resale_flat_prices_2017
WHERE 
 flat_type = '4 ROOM'         -- filters rows first
GROUP BY 
 town
HAVING 
 AVG(resale_price) > 500000   -- filters groups after aggregation
ORDER BY 
 avg_price DESC               -- can use the alias
LIMIT 10;
```


**WHERE vs. HAVING side-by-side:**

```sql
-- Filter on individual rows BEFORE grouping
SELECT
  town,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
WHERE
  resale_price > 600000
GROUP BY
  town;
```

```sql
-- Filter on the aggregated result AFTER grouping
SELECT
  town,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
GROUP BY
  town
HAVING
  AVG(resale_price) > 600000;
```

Filter to show only towns with high average prices:

```sql
SELECT
  town,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
GROUP BY
  town
HAVING
  avg_price > 600000
ORDER BY
  avg_price DESC;
```
💡 HAVING accepts alias defined earlier in SELECT statement.

Group by multiple columns:

```sql
SELECT
  town,
  lease_commence_date,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
GROUP BY
  town, lease_commence_date;
```

👋👋👋 **Group Exercise 6:**
- Select the average resale price by flat type.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
  flat_type,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
GROUP BY
  flat_type;
```
  
- Select the average resale price by flat type and flat model.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
  flat_type,
  flat_model,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
GROUP BY
  flat_type, flat_model;
```
  
- Select the average resale price by town and lease commence date, only for lease commence dates after year 2010, sorted by town (descending) and lease commence date (descending).

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
  town,
  lease_commence_date,
  AVG(resale_price) AS avg_price
FROM
  resale_flat_prices_2017
WHERE
  lease_commence_date > 2010
GROUP BY
  town, lease_commence_date
ORDER BY
  town DESC, lease_commence_date DESC;
```



👋👋👋 **Group Exercise 7 — HAVING:**
- Count transactions per town, then show only towns with more than 5,000 transactions.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
  town,
  COUNT(*) AS transaction_count
FROM
  resale_flat_prices_2017
GROUP BY
  town
HAVING
  transaction_count > 5000
ORDER BY 
    transaction_count DESC;
```

- Show the average floor area per flat type, but only for flat types whose average floor area is above 90 sqm.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
  flat_type,
  AVG(floor_area_sqm) AS avg_area_sqm
FROM
  resale_flat_prices_2017
GROUP BY
  flat_type
HAVING
  avg_area_sqm > 90
ORDER BY
  avg_area_sqm DESC;
```

- Find towns where the maximum resale price exceeds $900,000, sorted highest first. (Hint: filter on `MAX(resale_price)` in `HAVING`.)

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
  town,
  MAX(resale_price) AS max_resale_price
FROM
  resale_flat_prices_2017
GROUP BY
  town
HAVING
  max_resale_price > 900000
ORDER BY
  max_resale_price DESC;
```

### 💬 Reflection

- What happens if you forget the `GROUP BY` but use an `AVG()`?
- If you are a government planner, how does `GROUP BY town` help you decide where to build the next MRT station or school?

---

## 🏃 Part 3: Advanced Logic & Data Cleaning (55 min)

### 🎯 Learning Objectives
Transform data using `CASE` statements for conditional categorisation and `CAST` for type conversion including date extraction.

### Concept Overview

"Data isn't always ready for analysis. A `month` column might be a text string like `'2017-01'` instead of a date object. **`CAST` (or the `::` shorthand) acts as our translator to fix types**. Meanwhile, **`CASE` allows us to create new labels** — like tagging a flat as 'Large' or 'Small' based on its type — making our data much easier to read."

### 🛠️ Workshop — Task 5: Categorizing with CASE

**Price Categories:**

```sql
SELECT
  town,
  resale_price,
  CASE
    WHEN resale_price > 1000000 THEN 'Million Dollar Club'
    WHEN resale_price > 500000 THEN 'Mid-Range'
    ELSE 'Entry-Level'
  END AS price_category
FROM
  resale_flat_prices_2017;
```

Categorize flat sizes:

```sql
SELECT
  town,
  flat_type,
  CASE
    WHEN flat_type IN ('1 ROOM', '2 ROOM', '3 ROOM') THEN 'Small'
    WHEN flat_type = '4 ROOM' THEN 'Medium'
    ELSE 'Large'
  END AS flat_size
FROM
  resale_flat_prices_2017;
```

👋👋👋 **Group Exercise 8:**
- Design your own "budget/mid/high-end" categories based on resale_price.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
  town,
  flat_type,
  resale_price,
  CASE
    WHEN resale_price > 1000000 THEN 'High-End'
    WHEN resale_price > 700000 THEN 'Mid-Range'
    ELSE 'Budget'
  END AS price_category
FROM
  resale_flat_prices_2017;
```

- Design a "old vs new" label based on lease_commence_date.

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
  town,
  remaining_lease,
  resale_price,
  CASE
    WHEN lease_commence_date > 2015 THEN 'New Flat'
    ELSE 'Old Flat'
  END AS lease_commence_date
FROM
  resale_flat_prices_2017;
```

---

### 🛠️ Workshop — Task 6: Dates and Casting

**Casting Numbers:**

```sql
SELECT
  town,
  resale_price,
  CAST(resale_price AS INTEGER) AS resale_price_int
FROM
  resale_flat_prices_2017;
```

Or using shorthand:

```sql
SELECT
  town,
  resale_price,
  resale_price::INTEGER AS resale_price_int
FROM
  resale_flat_prices_2017;
```
⚠️⚠️⚠️ **CAST in a SELECT only changes the result of a query. It never change the stored table type to INT. CAST query produced a new column, resale_price_int, and the original sits next to it remain unchanged.** ⚠️⚠️⚠️

<br>

⚠️⚠️⚠️ **Use ALTER TABLE and ALTER COLUMN to update stored table type to INTEGER permanently**⚠️⚠️⚠️
```sql
ALTER TABLE main.resale_flat_prices_2017
ALTER COLUMN resale_price TYPE INTEGER;
```

<br>

**Convert text to a real date and extract the year:**

```sql
-- ANSI SQL style (maximum portability)
SELECT
    month,
    CAST(month || '-01' AS DATE) AS transaction_date,
    EXTRACT(YEAR FROM CAST(month || '-01' AS DATE)) AS sale_year
FROM resale_flat_prices_2017;
```

```sql
-- DuckDB/PostgreSQL style
SELECT
    month,
    (month || '-01')::DATE AS transaction_date,
    date_part('year', (month || '-01')::DATE) AS sale_year
FROM resale_flat_prices_2017;
```

⚠️⚠️⚠️ **date_part is a built-in DuckDB function. You don’t import or define it, and it’s available in every query. It’s part of DuckDB’s date and time functions. What it does: it extracts one piece (year, month, day, and so on) from a date or timestamp** ⚠️⚠️⚠️ 
```sql
date_part('year', DATE '2017-03-15')   -- 2017
date_part('month', DATE '2017-03-15')  -- 3
date_part('day', DATE '2017-03-15')    -- 15
```

<br><br>



> **Question:** "If the `month` column is text `'2017-01'`, can we add 1 month to it directly? Why do we need to `CAST` it to a `DATE` type first?"

👋👋👋 **Group Exercise 9 — CAST:**
- Show `resale_price` rounded down to the nearest thousand by casting to `INTEGER` (e.g. `resale_price / 1000` cast to `INTEGER`, then `* 1000`).

✅✅✅ **Answer** ✅✅✅ add floor function to round down (eg. $425,888 to $425,000)
```sql
SELECT
  *,
  CAST(floor(resale_price / 1000) AS INTEGER) * 1000 AS resale_price_rounded   
FROM resale_flat_prices_2017;
```

✅✅✅ **Answer** ✅✅✅ without floor function round to the nearest instead (eg. $425,888 w/o floor function is $426,000)
```sql
SELECT
  *,
  CAST((resale_price / 1000) AS INTEGER) * 1000 AS resale_price_rounded
FROM resale_flat_prices_2017;
```

- Convert the text `month` column to a real `DATE` and extract the transaction month number (1–12).

✅✅✅ **Answer** ✅✅✅
```sql
SELECT
    month,
    (month || '-01')::DATE AS transaction_date,   -- CAST to convert month type to DATE as new column transaction_date
    date_part('month', (month || '-01')::DATE) AS sale_month
FROM resale_flat_prices_2017;
```


- Cast `floor_area_sqm` to `INTEGER` and show it next to the original value — what happens to the decimals?

✅✅✅ **Answer** ✅✅✅ - floor_area_sqm_int will be rouded eg. 67.4 to 67, 92.9 to 93
```sql
SELECT
    floor_area_sqm,
    floor_area_sqm::INTEGER AS floor_area_sqm_int,
FROM resale_flat_prices_2017;
```



**Optional — Changing the Table Schema:**

Add a new column and fill it:

```sql
ALTER TABLE
  resale_flat_prices_2017
ADD COLUMN
  transaction_date DATE;

UPDATE
  resale_flat_prices_2017
SET
  transaction_date = CONCAT(month, '-01')::DATE;
```

Extract the transaction year:

```sql
SELECT
  *,
  date_part('year', transaction_date) AS transaction_year
FROM
  resale_flat_prices_2017;
```

### 💬 Reflection

- What does the `::` mean in DuckDB/PostgreSQL? How does it relate to `CAST()`?

✅✅✅ **Answer** ✅✅✅
The :: is the cast operator: a shorthand for CAST(). Both convert a value from one data type to another, and they give the same result.
```sql
SELECT CAST(resale_price AS INTEGER);   -- standard SQL
SELECT resale_price::INTEGER;           -- DuckDB / PostgreSQL shorthand
```

- Why is it important to standardize date formats when merging data from two different countries?

✅✅✅ **Answer** ✅✅✅
Same written date can mean two different dates depending on the country, and a database can’t tell which one was intended. If you merge without standardizing, you get wrong data that looks perfectly valid.

---

## 🎯 Wrap-Up

**Key Takeaways:**
1. **SELECT → FROM → WHERE → AGGREGATE → GROUP BY → HAVING → ORDER BY → LIMIT** — this is the logical execution order. Understanding it prevents most beginner SQL errors.
2. Aggregate functions collapse rows — always pair them with `GROUP BY` for meaningful results.
3. `CASE` and `CAST` are your cleaning tools — categorise messy text and convert mistyped columns directly in your query.

**Next Steps:**
- Complete the [Assignment](./assignment.md) — SQL questions + a capstone challenge finding undervalued towns.
- Next lesson: Lesson 1.5 goes advanced — JOINs, window functions, and CTEs for complex multi-table analytics.
