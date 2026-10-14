# Lesson 1.4 — DML Quiz (5 MCQs)

**1. What is the main difference between DDL and DML?**

A. DDL works with data inside tables; DML defines the table structure\
**B. DDL defines the structure of tables (CREATE, ALTER, DROP); DML works with the data inside them (SELECT, WHERE, GROUP BY)**\
C. They are two names for the same thing\
D. DDL is only used in DuckDB; DML is only used in MySQL

**Answer: B**

---

**2. Which SQL clause is used to filter individual rows before any grouping happens?**

A. HAVING\
B. GROUP BY\
**C. WHERE**\
D. SELECT

**Answer: C**

---

**3. You want to filter groups based on an aggregated value, e.g. only towns where the average resale price is above 450,000. Which clause should you use?**

A. WHERE\
**B. HAVING**\
C. CASE\
D. CAST

**Answer: B**

---

**4. To find the number of flats sold in each town, which SQL structure is correct?**

**A. SELECT town, COUNT(*) FROM resale_flat_prices_2017 GROUP BY town;**\
B. SELECT town, COUNT(*) FROM resale_flat_prices_2017 WHERE town;\
C. SELECT COUNT(town) FROM resale_flat_prices_2017;\
D. SELECT town FROM resale_flat_prices_2017 HAVING COUNT(*);

**Answer: A**

---

**5. Why is `SELECT *` often avoided in production queries?**

A. It only works with aggregate functions\
B. It cannot be used with WHERE clauses\
**C. It retrieves more data than needed and results can change unexpectedly if columns are added or removed**\
D. It automatically deletes unused columns

**Answer: C**
