## 📘 **Why This Cheatsheet?**

As a Data Engineer/Analyst interviewer, I see many freshers making the same mistakes: focusing only on **syntax** instead of **real problem-solving.** This cheatsheet is a **50+ Q&A collection** designed to:

- Cover **fundamentals + real-world scenarios**
- Teach you **interviewer mindset & expectations**
- Equip you with **full code & follow-ups**

---

# 🔹 **Section 1: Fundamental SQL Concepts**

---

## **Q1. What is the difference between WHERE and HAVING?**

| Aspect | WHERE | HAVING |
| --- | --- | --- |
| Filters on | Raw data (before GROUP BY) | Aggregated data (after GROUP BY) |
| Example usage | `WHERE status = 'active'` | `HAVING COUNT(*) > 10` |

**Example:**

```sql
sql
CopyEdit
SELECT department, COUNT(*)
FROM employees
WHERE status = 'active'
GROUP BY department
HAVING COUNT(*) > 5;

```

**Follow-up:**

*Can you use HAVING without GROUP BY?*

✅ Yes, HAVING works even without GROUP BY to filter aggregated results.

---

## **Q2. Types of JOINs and When to Use Them**

| Type | What It Does | Example Use Case |
| --- | --- | --- |
| INNER JOIN | Only matching rows from both tables | Customers who placed orders |
| LEFT JOIN | All from left + matched rows from right | All products, even if unsold |
| RIGHT JOIN | All from right + matched rows from left | All sales reps, even if no sales |
| FULL JOIN | All rows from both (match if possible) | Audit of users vs purchases |

**Follow-up:**

*When do you use EXISTS instead of JOIN?*

✅ EXISTS is faster for **checking existence** without returning full data.

---

## **Q3. What is a Primary Key vs Foreign Key?**

- **Primary Key:** Uniquely identifies each row (e.g., `customer_id`)
- **Foreign Key:** Links to another table’s primary key (e.g., `order.customer_id`)

✅ **Follow-up:** Why do we need Foreign Keys?

➡️ They **enforce referential integrity.**

---

## **Q4. Difference Between UNION and UNION ALL**

- **UNION:** Removes duplicates
- **UNION ALL:** Keeps duplicates

✅ **Tip:** Always prefer UNION ALL if you don’t need deduplication (faster).

---

## **Q5. What is Normalization? Why is it Important?**

- Process of **breaking data into smaller, related tables** to avoid redundancy.
- Ensures **data integrity + easier updates.**

✅ **Example:**

Split `customer_name` + `customer_orders` into 2 tables.

---

## **Q6. NULL Handling: How Do You Deal with NULLs in SQL?**

- Use `COALESCE()`, `IFNULL()`
- Filtering: `WHERE col IS NULL` (not `= NULL`)

✅ **Example:**

```sql
sql
CopyEdit
SELECT COALESCE(phone_number, 'Not Provided') FROM customers;

```

✅ **Follow-up:** What’s the difference between NULLIF and COALESCE?

- NULLIF returns NULL if **two inputs are equal**
- COALESCE returns the **first non-NULL**

---

---

# 🔹 **Section 2: Scenario-Based SQL Questions**

---

## **Q7. Find the Second Highest Salary**

```sql
sql
CopyEdit
SELECT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
  FROM employees
) AS ranked
WHERE rnk = 2;

```

✅ **Why this works:** Handles duplicates correctly.

---

## **Q8. Find Repeat Customers in Last 3 Months**

```sql
sql
CopyEdit
SELECT customer_id
FROM orders
WHERE order_date >= CURRENT_DATE - INTERVAL '3 months'
GROUP BY customer_id
HAVING COUNT(DISTINCT DATE_TRUNC('month', order_date)) > 1;

```

✅ **Real-World Use:** Loyalty programs, churn analysis.

---

## **Q9. Sales Funnel Analysis**

- Stage 1: Site Visit
- Stage 2: Added to Cart
- Stage 3: Purchased

```sql
sql
CopyEdit
SELECT
    COUNT(DISTINCT CASE WHEN stage = 'visit' THEN user_id END) AS visitors,
    COUNT(DISTINCT CASE WHEN stage = 'cart' THEN user_id END) AS cart_adds,
    COUNT(DISTINCT CASE WHEN stage = 'purchase' THEN user_id END) AS purchases
FROM user_activity;

```

✅ **Bonus:** Add drop-off % using simple math.

---

## **Q10. Find Products with No Sales**

```sql
sql
CopyEdit
SELECT p.product_id
FROM products p
LEFT JOIN orders o ON p.product_id = o.product_id
WHERE o.order_id IS NULL;

```

✅ **Key Concept:** Anti-join.

---

## **Q11. Calculate Rolling 7-Day Total Sales**

```sql
sql
CopyEdit
SELECT order_date,
       SUM(sales) OVER (ORDER BY order_date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS rolling_sales
FROM orders;

```

---

## **Q12. Detect Gaps in a Sequence**

```sql
sql
CopyEdit
SELECT id + 1 AS missing_id
FROM orders o
WHERE NOT EXISTS (
    SELECT 1 FROM orders WHERE id = o.id + 1
);

```

---

---

# 🔹 **Section 3: Tricky & Advanced Questions**

---

## **Q13. What is an Index? Why Do Queries Get Faster?**

✅ Index = **Pointer structure (B-tree)**

✅ Speeds up **row retrieval by skipping full scans.**

---

## **Q14. How Do You Optimize a Slow Query?**

Checklist:

- ✅ Use `EXPLAIN` to analyze
- ✅ Add proper indexes
- ✅ Avoid SELECT *
- ✅ Rewrite subqueries as JOINs
- ✅ Use partition filters if available

---

## **Q15. How Do You Pivot Data in SQL?**

```sql
sql
CopyEdit
SELECT user_id,
       SUM(CASE WHEN month = 'Jan' THEN sales ELSE 0 END) AS jan_sales,
       SUM(CASE WHEN month = 'Feb' THEN sales ELSE 0 END) AS feb_sales
FROM sales
GROUP BY user_id;

```

---

## **Q16. Remove Duplicate Rows Efficiently**

```sql
sql
CopyEdit
WITH ranked AS (
  SELECT *, ROW_NUMBER() OVER (PARTITION BY email ORDER BY created_at ASC) AS rn
  FROM users
)
DELETE FROM users WHERE user_id IN (SELECT user_id FROM ranked WHERE rn > 1);

```

---

## **Q17. What is a Window Function?**

✅ **Example:**

```sql
sql
CopyEdit
SELECT customer_id,
       SUM(order_amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS running_total
FROM orders;

```

➡️ **Key:** Computes across a "window" of rows.

---

## **Q18. Difference Between RANK(), DENSE_RANK(), ROW_NUMBER()**

| Function | Behavior |
| --- | --- |
| ROW_NUMBER() | Unique sequence (no ties) |
| RANK() | Skips ranks if duplicates (1,2,2,4) |
| DENSE_RANK() | No gaps (1,2,2,3) |

---

## **Q19. Full-Text Search Strategy**

Postgres:

```sql
sql
CopyEdit
SELECT * FROM articles
WHERE to_tsvector('english', content) @@ to_tsquery('data & science');

```

✅ Faster than LIKE '%...%'

---

---

# 🔹 **Section 4: End-to-End Practical Guide (Mini Cheat Sheet)**

| Topic | Example SQL |
| --- | --- |
| Index Usage | CREATE INDEX idx_name ON table(col); |
| Anti-Joins | LEFT JOIN ... WHERE right_table.id IS NULL |
| Window Functions | SUM() OVER (PARTITION BY user_id ORDER BY date) |
| Pivoting | SUM(CASE WHEN col='x' THEN val ELSE 0 END) |
| NULL Handling | COALESCE(column, 'default') |
| Performance Debugging | EXPLAIN ANALYZE SELECT ... |
| String Cleaning | TRIM(), UPPER(), REGEXP_REPLACE() |
| Date Bucketing | DATE_TRUNC('month', order_date) |

---

# 🔹 **Section 5: Final Pro Tips for Interviews**

## ✅ **Tip #1: Think Out Loud (Showcase Your Approach)**

💬 Interviewers love candidates who **explain their logic.** Even if you don’t know the answer **exactly,** walk through:

- What **the question tests**
- What **concepts might apply** (joins, windows, indexes?)
- How you'd **test your query on sample data**

**Example Response:**

*"I don’t remember the exact syntax for that function, but here’s how I would approach it: I’d first write a base query to filter data, and then layer on a window function to rank the rows."*

---

## ✅ **Tip #2: Highlight Performance Awareness**

Even if your query works, **mention the performance angle:**

- "I'd check if there's an index on this column because filtering on unindexed columns can slow it down."
- "If the table is very large, I'd avoid using functions in the WHERE clause since it disables index use."

✅ **This shows you’re not just writing SQL—you’re thinking like a data engineer.**

---

## ✅ **Tip #3: Watch Out for Edge Cases**

Always **ask clarifying questions:**

- *Should I handle NULLs?*
- *Do we expect duplicate rows?*
- *How do we treat ties in ranking?*

**Pro Move:** If you handle edge cases **proactively,** it shows you can write **robust, production-ready queries.**

---

## ✅ **Tip #4: Use "Real-World" Examples in Your Answers**

Instead of just giving **theoretical answers,** tie them back to **business context.**

**Example:**

*"For finding the top 3 products by sales, I’ve used this logic in a project where we built a dashboard for the sales team."*

This makes your answer **feel practical and experienced.**

---

## ✅ **Tip #5: Learn to Read & Debug Query Plans**

Even at a junior level, **understanding EXPLAIN plans** makes you stand out.

👉 **What to mention:**

- *“I’d run EXPLAIN to check if it's using Index Scan or Seq Scan.”*
- *“If I see Nested Loop joins on large tables, that’s a red flag and I’d investigate.”*

**🔥 Bonus:** Mention specific tools: `pg_stat_statements` (Postgres), Query Profiler (BigQuery), Execution Plans (SQL Server).

---

## ✅ **Tip #6: Stay Calm with Complex Joins**

A common **trap** for freshers is getting overwhelmed by **multi-join queries.**

🧠 **Mindset Trick:** Always visualize **tables + relationships** like a **Venn diagram.** Ask:

- *Which table is my base?*
- *Do I want ALL rows (LEFT JOIN) or only matching (INNER JOIN)?*

---

## ✅ **Tip #7: Avoid "SELECT " in Interviews*

Even if **you’re prototyping,** always write **explicit columns.**

**Say this:**

*"For clarity and performance, I’d avoid SELECT * and specify only required columns."*

✅ Shows **good habits** and **professionalism.**

---

## ✅ **Tip #8: Master NULL Handling**

Freshers often **forget NULL traps.** Always test + handle:

- NULL in joins → use `IS NULL` checks
- Aggregations → `SUM(column)` skips NULLs
- Comparisons → `col = NULL` **never works** (use `IS NULL`)

---

## ✅ **Tip #9: Don't Just Memorize—Build Mental Models**

Instead of rote-learning:

- **Practice SQL** with **real datasets** (Kaggle, StrataScratch)
- Draw **table relationships**
- Explain concepts to a **peer (or to yourself!)**

✅ This deepens your **understanding** and boosts recall.

---

## ✅ **Tip #10: Learn to Say "It Depends" (With Confidence)**

Many interview questions **have trade-offs.**

**Example:**

Q: *Which is faster—EXISTS or JOIN?*

➡️ Strong Answer:

*"It depends. For checking mere existence, EXISTS can be faster because it short-circuits. But if I need full row data, JOIN is better."*

✅ **This shows maturity.**

# 🔹 **More Advanced SQL Interview Questions (Technical Focus)**

---

## **Q46. How Do You Find Duplicate Rows in a Table?**

✅ **Example: Find duplicate emails:**

```sql
sql
CopyEdit
SELECT email, COUNT(*)
FROM users
GROUP BY email
HAVING COUNT(*) > 1;

```

👉 **Follow-up:** *How would you delete duplicates but keep one?*

```sql
sql
CopyEdit
WITH ranked AS (
    SELECT id, ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) AS rn
    FROM users
)
DELETE FROM users WHERE id IN (SELECT id FROM ranked WHERE rn > 1);

```

---

## **Q47. What Is the Difference Between DELETE and TRUNCATE?**

| Operation | DELETE | TRUNCATE |
| --- | --- | --- |
| Logging | Row-by-row | Minimal logging |
| Rollback | Can rollback | Can't rollback (depends on DB) |
| Speed | Slower (logs each row) | Faster (deallocates data pages) |
| Triggers | Fires triggers | Doesn’t fire triggers |

✅ **Follow-up:** When would you prefer TRUNCATE?

➡️ When you need to **quickly clear out a table** but don’t need fine-grained control.

---

## **Q48. How Do You Handle Dividing by Zero in SQL?**

✅ Use `NULLIF()` to prevent errors:

```sql
sql
CopyEdit
SELECT sales / NULLIF(quantity, 0) AS avg_price
FROM orders;

```

---

## **Q49. Explain the Difference Between Subquery and CTE**

| Feature | Subquery | CTE |
| --- | --- | --- |
| Readability | Less readable if nested | Much more readable, especially recursive |
| Reusable | No | Yes (can refer multiple times) |
| Materialized | Inline optimization | May be materialized (depends on DB) |

✅ **Example:**

```sql
sql
CopyEdit
WITH recent_orders AS (
    SELECT * FROM orders WHERE order_date > '2024-01-01'
)
SELECT * FROM recent_orders WHERE total > 100;

```

---

## **Q50. Find the Highest Salary Per Department & Employee Name**

✅ **Using JOIN:**

```sql
sql
CopyEdit
SELECT e.department, e.name, e.salary
FROM employees e
INNER JOIN (
    SELECT department, MAX(salary) AS max_salary
    FROM employees
    GROUP BY department
) AS dept_max
ON e.department = dept_max.department AND e.salary = dept_max.max_salary;

```

✅ **Follow-up:** Could you do this with a window function?

Yes:

```sql
sql
CopyEdit
SELECT department, name, salary
FROM (
    SELECT *, RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rnk
    FROM employees
) AS ranked
WHERE rnk = 1;

```

---

## **Q51. Explain the Use of CASE Statements**

**Example: Categorize sales:**

```sql
sql
CopyEdit
SELECT order_id,
    CASE
        WHEN total_amount > 1000 THEN 'High'
        WHEN total_amount BETWEEN 500 AND 1000 THEN 'Medium'
        ELSE 'Low'
    END AS sales_category
FROM orders;

```

✅ **Use Case:** Data bucketing, dashboards.

---

## **Q52. Find Customers Who Never Placed an Order**

✅ **Anti-Join Example:**

```sql
sql
CopyEdit
SELECT c.customer_id, c.name
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;

```

---

## **Q53. How Do You Compare Performance Between Two SQL Queries?**

✅ Steps:

1️⃣ Run both with **EXPLAIN ANALYZE**

2️⃣ Check:

- **Seq Scan vs Index Scan**
- Join types (Nested Loop, Hash Join)
- Estimated vs actual rows
    
    3️⃣ Track **execution time** and **rows processed**
    

---

## **Q54. How Do You Identify and Fix Data Skew?**

✅ **Detection:**

```sql
sql
CopyEdit
SELECT join_key, COUNT(*) FROM big_table GROUP BY join_key ORDER BY COUNT(*) DESC LIMIT 5;

```

✅ **Fix:**

- Add **salting key** to distribute
- Pre-aggregate before joining
- Re-partition the data (especially in Spark)

---

## **Q55. What's the Difference Between Temporary and Persistent Tables?**

| Feature | Temp Table | Persistent Table |
| --- | --- | --- |
| Lifetime | Only in session | Saved in DB |
| Storage | In-memory / temp disk | Full storage |
| Visibility | Only to session | Global |

✅ **Example:**

```sql
sql
CopyEdit
CREATE TEMP TABLE recent_orders AS SELECT * FROM orders WHERE order_date > '2024-01-01';

```

---

## **Q56. How to Find Consecutive Login Days (User Streaks)**

✅ Use LAG & DATEDIFF:

```sql
sql
CopyEdit
WITH logins AS (
    SELECT user_id, login_date,
           LAG(login_date) OVER (PARTITION BY user_id ORDER BY login_date) AS prev_date
    FROM user_activity
)
SELECT user_id, COUNT(*)
FROM logins
WHERE DATEDIFF(login_date, prev_date) = 1
GROUP BY user_id;

```

---

## **Q57. Explain NULL Handling in Aggregations**

- `COUNT(column)` **excludes NULLs**
- `SUM()`, `AVG()`, etc. skip NULLs too
- To include NULL, use `COUNT(*)` or `COALESCE()`

✅ **Example:**

```sql
sql
CopyEdit
SELECT SUM(COALESCE(sales, 0)) FROM orders;

```

---

## **Q58. How to Efficiently Get a Random Row from a Table?**

👉 Instead of `ORDER BY RANDOM()` (slow), do:

```sql
sql
CopyEdit
SELECT * FROM table
WHERE id >= FLOOR(RANDOM() * (SELECT MAX(id) FROM table))
LIMIT 1;

```

✅ **Follow-up:** Does this work if IDs have gaps?

Not perfectly—best for **dense ID sequences.**

---

## **Q59. Explain the Difference Between Scalar vs Aggregate Functions**

| Function Type | Example | What It Does |
| --- | --- | --- |
| Scalar Function | UPPER(), ROUND(), LENGTH() | Operates on **one row at a time** |
| Aggregate Function | COUNT(), SUM(), AVG() | Operates across **multiple rows** |

---

## **Q60. What is the Difference Between Analytical and Aggregate Functions?**

✅ Aggregate: Collapses multiple rows → one result

✅ Analytical (window): Keeps **row-level detail + adds metrics**

Example Analytical:

```sql
sql
CopyEdit
SELECT order_id, customer_id, SUM(sales) OVER (PARTITION BY customer_id) AS total_sales
FROM orders;

```

## **Q61. How Do You Update Data in One Table Based on Another Table?**

✅ Using JOIN:

```sql
sql
CopyEdit
UPDATE customers c
SET loyalty_tier = o.new_tier
FROM orders o
WHERE c.customer_id = o.customer_id;

```

✅ **Alternative (for MySQL):**

```sql
sql
CopyEdit
UPDATE customers c
INNER JOIN orders o ON c.customer_id = o.customer_id
SET c.loyalty_tier = o.new_tier;

```

**Follow-up:**

*What if your DB doesn’t allow JOINs in UPDATE?*

➡️ Use correlated subqueries.

---

## **Q62. What’s a Common Pitfall with GROUP BY in SQL?**

👉 **Mistake:** Grouping only by **some columns** but selecting **others without aggregates.**

```sql
sql
CopyEdit
SELECT department, employee_name, COUNT(*)
FROM employees
GROUP BY department;

```

❌ This **fails** in strict SQL (Postgres) because `employee_name` isn’t in GROUP BY or aggregated.

✅ Correct:

```sql
sql
CopyEdit
GROUP BY department, employee_name;

```

OR use **aggregates** (e.g., MAX(employee_name)).

---

## **Q63. What’s a Self Join? Give an Example**

✅ **Used when a table relates to itself.**

Example: Employee-Manager hierarchy.

```sql
sql
CopyEdit
SELECT e1.name AS employee, e2.name AS manager
FROM employees e1
LEFT JOIN employees e2 ON e1.manager_id = e2.employee_id;

```

---

## **Q64. How Do You Find the Nth Highest Salary Without Using RANK()?**

✅ Using subquery:

```sql
sql
CopyEdit
SELECT DISTINCT salary
FROM employees e1
WHERE N - 1 = (
    SELECT COUNT(DISTINCT salary)
    FROM employees e2
    WHERE e2.salary > e1.salary
);

```

✅ **Follow-up:** Why is DENSE_RANK better?

➡️ Simpler & scales better on big data.

---

## **Q65. How Do You Handle Duplicate Column Names After JOIN?**

👉 Always **alias**:

```sql
sql
CopyEdit
SELECT c.customer_id AS c_id, o.customer_id AS o_id
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id;

```

✅ **Follow-up:** What happens if you don’t alias and use `*`?

➡️ It may cause **ambiguous column errors.**

---

## **Q66. How to Select Only Even Rows (Row Number)?**

```sql
sql
CopyEdit
WITH numbered AS (
    SELECT *, ROW_NUMBER() OVER (ORDER BY id) AS rn
    FROM employees
)
SELECT * FROM numbered WHERE rn % 2 = 0;

```

✅ Use Case: Pagination, sampling.

---

## **Q67. Explain the Concept of Denormalization**

👉 **Denormalization** is **intentionally adding redundancy** to improve **read performance.**

✅ Example: Instead of joining `orders` with `customers` every time, add `customer_name` directly into `orders`.

**Follow-up:** When is it OK?

➡️ For reporting/analytics systems where **read speed > update frequency.**

---

## **Q68. What’s a Surrogate Key vs Natural Key?**

| Key Type | Example | Pros |
| --- | --- | --- |
| Natural Key | SSN, email | Human-readable, meaningful |
| Surrogate Key | Auto-increment ID | Consistent, safe if natural key changes |

✅ Best practice: Use **surrogate keys** to avoid dependency on real-world values.

---

## **Q69. Explain ACID Properties of SQL Databases**

| Property | What It Ensures |
| --- | --- |
| Atomicity | All-or-nothing transactions |
| Consistency | Data integrity after transactions |
| Isolation | Transactions don’t interfere mid-way |
| Durability | Data survives system crashes |

✅ **Follow-up:** Why does this matter?

➡️ Ensures **reliable, predictable data.**

---

## **Q70. How to Get a Cumulative Count of Logins Per User Over Time?**

```sql
sql
CopyEdit
SELECT user_id, login_date,
       COUNT(*) OVER (PARTITION BY user_id ORDER BY login_date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS cumulative_logins
FROM logins;

```

✅ **Use Case:** Usage tracking, loyalty metrics.

---

## **Q71. What’s the Fastest Way to Get the Count of Rows in a Huge Table?**

👉 **Trick Answer:**

- `SELECT COUNT(*) FROM big_table;` works but is **slow.**

✅ **Faster:** Use DB **metadata tables** (if DB supports it):

- **Postgres:**

```sql
sql
CopyEdit
SELECT reltuples::BIGINT AS estimate FROM pg_class WHERE relname = 'big_table';

```

- **MySQL:**

```sql
sql
CopyEdit
SHOW TABLE STATUS WHERE Name = 'big_table';

```

✅ **Caveat:** These are **estimates, not exact.**

---

## **Q72. How Do You Perform Conditional Aggregation?**

✅ Example: Count VIP customers only.

```sql
sql
CopyEdit
SELECT
    COUNT(*) AS total_customers,
    COUNT(CASE WHEN vip = true THEN 1 END) AS vip_customers
FROM customers;

```

---

## **Q73. What’s the Difference Between COUNT(*) and COUNT(column)?**

| Function | What It Does |
| --- | --- |
| COUNT(*) | Counts **all rows** |
| COUNT(column) | Counts rows where column is NOT NULL |

✅ **Common Mistake:** Assuming they are always the same.

---

## **Q74. How Do You Optimize Joins on Very Large Tables?**

✅ Best practices:

- Filter **early** before join
- Ensure **indexes** on join columns
- Avoid functions in ON clause
- Consider **partitioned joins**

---

## **Q75. How Do You Calculate Month-over-Month Growth in SQL?**

✅ Use LAG to compare current month vs previous month:

```sql
sql
CopyEdit
WITH monthly_sales AS (
    SELECT DATE_TRUNC('month', order_date) AS month, SUM(sales) AS total
    FROM orders
    GROUP BY month
)
SELECT month, total,
       LAG(total) OVER (ORDER BY month) AS prev_total,
       (total - LAG(total) OVER (ORDER BY month)) / NULLIF(LAG(total) OVER (ORDER BY month), 0) * 100 AS growth_pct
FROM monthly_sales;

```
