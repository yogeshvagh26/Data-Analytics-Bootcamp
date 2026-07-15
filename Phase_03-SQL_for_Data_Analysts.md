Welcome back. 

If **Excel is the universal language of business, SQL (Structured Query Language) is the native tongue of data. You cannot call yourself a Data Analyst without SQL. Period.**

In Phase 2, we learned to manipulate a million rows in Excel. In the real world, datasets are billions of rows. Excel would crash. Your laptop would catch fire. SQL speaks directly to the database engine, processing massive datasets efficiently without ever loading them into your computer's memory.

**The Pareto Principle for SQL:** 80% of your daily work involves **reading data ( `SELECT` statements).** Within that, 80% of your problems are solved by mastering **Filtering ( `WHERE` ), Grouping ( `GROUP BY` ), Joining ( `JOIN` ), and Window Functions ( `OVER()` ).** We will ruthlessly prioritize these. We will touch on `INSERT`, `UPDATE`, and `DELETE` (DML), but as an Analyst, you are usually given *read-only* access. Your job is to extract and transform, not to administer the database.

Grab a text editor or your favorite SQL IDE (I recommend DBeaver or SQLite Browser for practice). Let's build your core competency.


# Phase 3: SQL for Data Analysts

## 1. Database Basics: The Foundation

### Concept: 

A database is an organized collection of structured information, or data, typically stored electronically in a computer system.

* **Relational Database:** Data is stored in *tables* (like Excel sheets) that relate to each other via keys. Examples: PostgreSQL, MySQL, SQL Server, Oracle.

### Normalization:

This is the process of organizing data to reduce redundancy and improve integrity.

* **1st Normal Form (1NF):** Each cell has a single value (no lists). Each record is unique.

* **2nd Normal Form (2NF):** Must be in 1NF, and all non-key columns depend on the *entire* primary key (relevant when you have composite keys).

* **3rd Normal Form (3NF):** Must be in 2NF, and columns must depend *only* on the primary key. (e.g., In an Orders table, you store `CustomerID`. You do not store `CustomerName` because that depends on `CustomerID`, not `OrderID`).


### Why is it important? 

If a customer changes their address, and you have that address stored in 5,000 order rows, you must update 5,000 rows. In a normalized DB, you update 1 row in the `Customers` table.

### Primary Key (PK): 

A unique identifier for a single row in a table. (e.g., `CustomerID` in a Customers table). Cannot be NULL.

### Foreign Key (FK): 

A column in one table that points to the Primary Key of another table, creating a *relationship*. (e.g., `CustomerID` in the Orders table).

### Relationships:

* **One-to-Many:** One Customer can have many Orders. (Most common).

* **One-to-One:** One User has one Profile.

* **Many-to-Many:** Students can enroll in many Classes; Classes can have many Students. (Requires a "junction table").


### CRUD Operations:

* **Create ( `INSERT` )**

* **Read ( `SELECT` )** - *This is your 90% focus.*

* **Update ( `UPDATE` )**

* **Delete ( `DELETE` )**


### Common Mistakes Beginners Make:

* Not understanding normalization and ending up with "spaghetti" databases where the same data is duplicated.

* Confusing Primary Keys with Foreign Keys.

---

## 2. The SELECT Command & Core Filtering

### Concept: 

The `SELECT` statement retrieves data from a database.

### Syntax:

```sql
    SELECT column1, column2, ...
    FROM table_name
    WHERE condition
    ORDER BY column ASC/DESC
    LIMIT number;
```

### Detailed Breakdown:

* **SELECT:** Tells the database *which columns you want*.

* **`*` (Asterisk):** Selects all columns. (*Prof. Sterling's Note: Avoid `SELECT *` in production; it slows queries down*).

* **WHERE:** Filters rows based on conditions.

* **ORDER BY:** Sorts the result.

* **LIMIT (or `TOP` in SQL Server, `FETCH` in Oracle):** Restricts the number of rows returned.

* **DISTINCT:** Removes duplicate rows.


### Example:

```sql
SELECT
    first_name,
    last_name,
    hire_date
FROM employees
WHERE department = 'Sales'
ORDER BY hire_date DESC
LIMIT 10;
```

### Filtering Operators in WHERE:

* **Comparison:** `=`, `>`, `<`, `>=`, `<=`, `<>` (not equal).

* **Logical:** `AND`, `OR`, `NOT`.

* **Range:** `BETWEEN ... AND ...`.

* **List:** `IN ('A', 'B', 'C')`.

* **Pattern:** `LIKE '%gmail.com'` (Wildcards: `%` for any chars, `_` for single char).

* **Nulls:** `IS NULL` or `IS NOT NULL`. (*Crucial: `NULL` is not `= NULL`*).

---

**Business Example:** "Get me the top 5 highest-spending customers who joined in 2023 and are not from the 'Trial' segment."

```sql

    SELECT
        customer_id,
        total_spend
    FROM customers
    WHERE join_date BETWEEN '2023-01-01' AND '2023-12-31'
    AND segment != 'Trial'
    ORDER BY total_spend DESC
    LIMIT 5;

```

**Common Mistakes:**

* Using `=` to find NULLs (`WHERE middle_name = NULL` is wrong; use `IS NULL`).

* Case sensitivity in string comparisons (depends on the DB collation; in PostgreSQL it's case-sensitive, in SQL Server it usually isn't). Use `UPPER()` or `LOWER()` to standardize if unsure.

---

## 3. Aggregate Functions & GROUP BY (The Business Report)

### Concept: 

We move from looking at individual rows to summarizing massive datasets. This is how you create KPIs.

### Core Aggregate Functions:

* `COUNT(*)` / `COUNT(DISTINCT column)`

* `SUM(column)`

* `AVG(column)`

* `MIN(column)`

* `MAX(column)`


### Syntax & Logic:

`GROUP BY` groups rows that have the same values in specified columns into summary rows.

`HAVING` is the `WHERE` clause for groups (filters after aggregation).

---

### Real-World Business Scenario: Monthly Sales by Product

Write a query to find total revenue, average order value, and number of orders per product category for the month of June.

```sql
SELECT
    product_category,
    COUNT(order_id) AS total_orders,
    SUM(sales_amount) AS total_revenue,
    AVG(sales_amount) AS average_order_value
FROM orders
WHERE order_date BETWEEN '2024-06-01' AND '2024-06-30'
GROUP BY product_category
HAVING SUM(sales_amount) > 10000  -- Only show categories making over \$10k
ORDER BY total_revenue DESC;
```

---

### Advantages & Disadvantages:

* **Advantage:** Turns millions of rows into a one-page summary.

* **Disadvantage:** You lose detail. You know the average, but you don't know the outlier customer.

### Best Practice: 

Always use meaningful aliases (`AS total_revenue`).

---

### Common Mistakes:

* Forgetting to include non-aggregated columns in the `GROUP BY`.

    * *Wrong:* `SELECT product_category, region, SUM(sales)... GROUP BY product_category;` (This will error because `region` isn't in the group).
    
* Using `WHERE` to filter aggregates (e.g., `WHERE SUM(sales) > 1000`). Remember: `WHERE` filters raw rows; `HAVING` filters aggregated groups.

---

## 4. JOINS (The Art of Combining Tables)

### Concept: 

The most critical SQL skill. Rarely is all the data you need in a single table. `JOIN` combines rows from two or more tables based on a related column.

### The Types (80/20 Focus):

1. **INNER JOIN:** Returns only matching rows from both tables. (If Order has `CustomerID` 1, 2, 3, and Customers has 1, 2, only 1 & 2 return). Use this 80% of the time.

2. **LEFT JOIN (or LEFT OUTER JOIN):** Returns ALL rows from the left table, and matching rows from the right. If no match, returns NULL on the right.

3. **RIGHT JOIN:** Opposite of LEFT. (I rarely use this; I just flip the tables and use LEFT JOIN).

4. **FULL OUTER JOIN:** All rows from both, matching where possible.

5. **CROSS JOIN:** Cartesian product (Every row in A matches every row in B). Dangerous.


### Visualization (The Mental Model):

* **INNER JOIN:** The intersection of a Venn diagram.

* **LEFT JOIN:** The entire left circle + the intersection.

---


### Business Example: Customer Order History
We want to see all customers and their order totals. We need customers who haven't ordered yet (to show $0) and those who have.

```sql

    SELECT
        c.customer_id,
        c.customer_name,
        COALESCE(SUM(o.sales_amount), 0) AS lifetime_value
    FROM customers c
    LEFT JOIN orders o ON c.customer_id = o.customer_id
    GROUP BY c.customer_id, c.customer_name
    ORDER BY lifetime_value DESC;

```

*(Note: `COALESCE` replaces NULL with 0 so customers without orders don't show NULL).*

### Advanced Join with Multiple Conditions:
Sometimes you need to join on more than one column (e.g., `ON t1.id = t2.id AND t1.date = t2.date`).

### Common Mistakes:

* Forgetting the `ON` clause and accidentally creating a `CROSS JOIN` (a Cartesian product), causing a massive, slow result set.

* Using `LEFT JOIN` but filtering the right table in the `WHERE` clause (e.g., `WHERE right_table.column = 'x'`). This effectively turns your `LEFT JOIN` into an `INNER JOIN`. If you need the right table filtered, put the condition in the `ON` clause.

---


## 5. Subqueries & CTEs (Organizing Complexity)

### Concept: 

Sometimes you need to ask a question within a question.

* **Subquery:** A query nested inside another query.

* **CTE (Common Table Expression - `WITH` clause):** A temporary named result set that you can reference within your main query. This is far more readable than nested subqueries.


### When to use:

* When you need to calculate an aggregate, and then use that aggregate for further comparisons.

### Business Scenario: "Above Average Employees"

Find employees who earn more than the average salary of their department.

*(You cannot solve this with a simple `WHERE salary > AVG(salary)` because aggregate functions can't be used directly in `WHERE` without a group).*

**Solution using CTE (Best Practice):**

```sql

    WITH dept_avg AS (
        SELECT
            department_id,
            AVG(salary) AS avg_salary
        FROM employees
        GROUP BY department_id
    )
    SELECT
        e.employee_name,
        e.salary,
        d.avg_salary
    FROM employees e
    JOIN dept_avg d ON e.department_id = d.department_id
    WHERE e.salary > d.avg_salary;

```

### Correlated Subqueries (Advanced): 

A subquery that references the outer query.

* **Example:**

  `SELECT * FROM products p WHERE price > (SELECT AVG(price) FROM products WHERE category = p.category);`
  
* **Note:** While powerful, correlated subqueries are slow on large data. Prefer CTEs and `JOIN`s 90% of the time.

---

## 6. Window Functions (The 20% That Sets You Apart)

### Concept: 

This is where you go from "Data Clerk" to "Data Wizard." Window functions perform calculations across a set of rows that are related to the current row, *without collapsing them into a single row* (unlike `GROUP BY`). The current row keeps its identity.

### The Critical Functions:

1. **`ROW_NUMBER()`:** Assigns a unique sequential integer to each row within a partition.

2. **`RANK()` & `DENSE_RANK()`:** Similar, but handle ties differently.

3. **`LAG()` & `LEAD()`:** Access data from a previous row or a following row in the same result set.

4. **`SUM() OVER()`:** Running totals.


### Syntax Structure:

```sql
function() OVER (
    PARTITION BY column1  -- Groups the data (like GROUP BY but doesn't collapse)
    ORDER BY column2      -- Defines the order within the partition
    ROWS BETWEEN ...      -- Defines the frame (optional, very powerful)
)
```

**Business Scenario 1: Ranking Sales Reps**

"We want to rank our sales reps by total sales, per region, to see who the top performer is in each region."

```sql

    SELECT
        region,
        sales_rep,
        total_sales,
        RANK() OVER (PARTITION BY region ORDER BY total_sales DESC) AS rank_in_region
    FROM sales_performance;

```

**Business Scenario 2: The "MoM" Growth Rate (LAG)**

"We need to calculate the month-over-month percentage change in revenue."

```sql

    WITH monthly_revenue AS (
        SELECT
            DATE_TRUNC('month', order_date) AS month,
            SUM(sales) AS revenue
        FROM orders
        GROUP BY month
    )
    SELECT
        month,
        revenue,
        LAG(revenue, 1) OVER (ORDER BY month) AS previous_month_revenue,
        (revenue - LAG(revenue, 1) OVER (ORDER BY month)) / LAG(revenue, 1) OVER (ORDER BY month) * 100
    AS pct_change
    FROM monthly_revenue;

```

**Business Scenario 3: Running Total (Cumulative Sum)**

"We need to see the cumulative sales throughout 2024 to forecast cash flow."

```sql
SELECT
    order_date,
    daily_sales,
    SUM(daily_sales) OVER (ORDER BY order_date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
FROM daily_sales_data;
```

### Common Mistakes:

* Forgetting the `PARTITION BY` (this treats the *entire* dataset as one partition).

* Mixing up `RANK` and `DENSE_RANK`. (Rank skips numbers on ties; Dense does not).

---

## 7. Views, Indexes, and Optimization (Performance Tuning)

### Concept: 

A database must be fast. If your query takes 10 minutes to run, the business will hate you.

* **Views:** A "saved SQL query" that looks like a table. It does *not* store data (unless materialized). Use it to simplify complex joins for junior analysts.

* **Indexes:** A data structure (like a book's index) that improves the speed of data retrieval on a specific column.

### When to use Indexes:

* On columns used frequently in `WHERE`, `JOIN`, or `ORDER BY`.

* **Disadvantage:** Indexes slow down `INSERT` / `UPDATE` / `DELETE` operations because the index must be updated too.

### Execution Plan (The "EXPLAIN" command):

This is your secret weapon. Adding `EXPLAIN` before your query shows you exactly how the database engine executes it. You look for "Seq Scan" (Sequential Scan - bad for large tables) and look for "Index Scan" (good).


### Optimization Checklist (The 80/20):

1. **Avoid `SELECT *`:** Only pull columns you need. Saves memory.

2. **Use `EXISTS` instead of `IN`:** For large subqueries, `EXISTS` stops scanning as soon as it finds a match, whereas `IN` builds the full subquery result set.

    * **Best Practice:**
      `SELECT * FROM customers c WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.id);`

3. **Filter early:** Reduce the dataset before doing expensive `JOIN`s (use subqueries/CTEs to reduce rows first).

4. **Avoid functions in `WHERE` clauses on indexed columns:**

    * **Bad:** `WHERE DATE(order_date) = '2024-01-01'` (kills the index).

    * **Good:** `WHERE order_date >= '2024-01-01' AND order_date < '2024-01-02'`.

**Interview Question: Your query is running slowly. Walk me through your debugging steps.**

**Answer:**

1. I run `EXPLAIN` to view the execution plan.

2. I check for a "Seq Scan" on a large table.

3. I verify if an index exists on the filtered/joined column. If not, I create one.


4. I refactor the query to eliminate any functions on indexed columns.

5. I break it into a CTE to pre-filter rows before the main join.

---

## 8. DDL, DML, TCL, and Stored Procedures (The Analyst's Context)

* **DDL (Data Definition Language):** `CREATE`, `ALTER`, `DROP`. (You likely won't have permissions for this).

* **DML (Data Manipulation Language):** `INSERT`, `UPDATE`, `DELETE`. (You might use `INSERT INTO ... SELECT` to create temporary tables for analysis).

* **TCL (Transaction Control):** `COMMIT`, `ROLLBACK`. (Used when updating data to save or revert changes).

* **Stored Procedures:** Saved SQL code that can be reused. A Data Analyst might call a procedure like `sp_GenerateQuarterlyReport` to run a complex pipeline.

---

## Mini Project: "The E-Commerce Customer 360"

**Background:** An online retailer wants a complete profile of their customers to launch a loyalty program. They have 3 tables:

* `customers`: `customer_id`, `signup_date`, `email`, `country`.

* `orders`: `order_id`, `customer_id`, `order_date`, `total_amount`, `status` (shipped/pending/cancelled).

* `order_items`: `order_item_id`, `order_id`, `product_sku`, `quantity`, `unit_price`.

### Your Tasks (Write SQL Queries for each):

1. **Intro:** Write a query to get the total number of customers who signed up in the last 30 days.

2. **Aggregation:** Write a query to find the Average Order Value (AOV) per country.

3. **Join:** Write a query to list all customers who have placed more than 5 orders. Include their name (email) and total spend.

4. **Window Function:** Rank the customers by their total lifetime spend (`SUM(total_amount)`). Show the Top 5.

5. **Data Quality:** Write a query to check for "orphaned" `order_items` (where `order_id` in `order_items` does not exist in the `orders` table). This indicates data integrity issues.

---

## Major Project: "The SaaS Churn Analysis"

**Scenario:** You are the sole Data Analyst for "Streamly," a subscription-based video streaming service. They are bleeding subscribers. The VP of Product has given you a database dump (PostgreSQL) with the following tables:

* `subscribers`: `subscriber_id`, `signup_date`, `plan_type` (Basic/Pro/Premium), `country`.

* `subscription_periods`: `period_id`, `subscriber_id`, `start_date`, `end_date` (NULL if active), `monthly_price`.

* `usage_logs`: `log_id`, `subscriber_id`, `login_date`, `minutes_watched`.

* `support_tickets`: `ticket_id`, `subscriber_id`, `created_date`, `resolution_time_hours`.


### The Business Questions (Your Deliverables):

1. **Define Churn Rate:** Write a query that calculates the Monthly Churn Rate for the past 6 months. (Definition: Number of subscribers whose `end_date` occurred in that month / Total active subscribers at the start of that month).

    * **Hint:** You will need to use `DATE_TRUNC` and `COUNT(DISTINCT)`.

2. **Cohort Analysis:** Write a SQL query (using CTEs) to create a retention cohort.

    * **Rows:** Signup Month (e.g., Jan 2024).

    * **Columns:** Month 0, Month 1, Month 2, Month 3.

    * **Values:** Percentage of subscribers retained (still active) after X months.

3. **Churn Driver Diagnosis (Window Functions):**

    * For subscribers who churned, find the average `minutes_watched` in the 30 days *before* their churn date versus the general population average.

    * Find the average `resolution_time_hours` for support tickets created in the 60 days before churn vs those who stayed.

    * Write a query that extracts the "last login date" for each subscriber and calculates the gap between the last login and the churn date.
    
4. **Recommendation Query (The Final Insight):**

    * Write a query that outputs a list of at-risk subscribers (those who have not logged in for 14 days, are active, and have a 'Basic' plan).

    * Your output should include: `subscriber_id`, `email`, `last_login_date`, and `days_since_last_login`.

5. **Optimization Challenge:**

    * One of your queries runs slowly. Apply an index. Write the `CREATE INDEX` command for the columns that would speed up the churn calculation (`subscription_periods.start_date` and `end_date`).

---

## Advanced SQL Interview Questions (The Grinder)

1. **Question:** "You have a table `Employee` with `ID`, `Name`, `Salary`, and `ManagerID`. Write a SQL query to find employees who earn more than their managers."

    * **Answer:** Use a Self-Join.

    ```sql
    SELECT e.Name
    FROM Employee e
    INNER JOIN Employee m ON e.ManagerID = m.ID
    WHERE e.Salary > m.Salary;
    ```

2. **Question:** "Explain the difference between `WHERE` and `HAVING`."

    * **Answer:** `WHERE` filters rows before aggregation. `HAVING` filters groups after aggregation. You cannot use aggregate functions in `WHERE`, but you can in `HAVING`.

3. **Question:** "What is the difference between `UNION` and `UNION ALL`?"

    * **Answer:** `UNION` removes duplicate rows (slower), `UNION ALL` keeps all rows (faster). Use `UNION ALL` unless you absolutely need unique values.
    
4. **Question:** "You have a table with timestamps. How do you group by date only (ignoring time)?"

    * **Answer:** Use `CAST(timestamp AS DATE)` or `DATE(timestamp)` or `DATE_TRUNC('day', timestamp)`.

5. **Scenario:** "We have a table of transactions. We want to find the *second highest salary* (or transaction amount)."
    * **Answer:** Use `OFFSET` or a subquery.

    ```sql
    SELECT DISTINCT salary
    FROM employees
    ORDER BY salary DESC
    LIMIT 1 OFFSET 1;
    ```

6. **Advanced Scenario:** "Find the top 3 most sold products in each category, sorted by sales volume."
    * **Answer:** Use `ROW_NUMBER()` with `PARTITION BY category` and `ORDER BY sales_volume DESC`. Wrap it in a CTE and filter `WHERE row_num <= 3`.

---

## Phase 3 Summary / Cheat Sheet

* **Order of Execution (Crucial to know!):**

  `FROM` $\rightarrow$ `WHERE` $\rightarrow$ `GROUP BY` $\rightarrow$ `HAVING` $\rightarrow$ `SELECT` $\rightarrow$ `ORDER BY` $\rightarrow$ `LIMIT`.

  *(This is why aliases defined in `SELECT` cannot be used in `WHERE` or `GROUP BY` — they don't exist yet!)*

* **The 5 Commandments of SQL for Analysts:**

  1. Thou shalt use `LEFT JOIN` if thou needest data from the left table regardless of a match.

  2. Thou shalt use `WITH` (CTEs) for readability. (If a query has more than 3 subqueries nested, rewrite it with CTEs).
    
  3. Thou shalt wrap all business logic inside `COALESCE` to handle NULLs.

  4. Thou shalt always format dates using standard ISO format ( `YYYY-MM-DD` ) to avoid regional confusion.

  5. Thou shalt `EXPLAIN` thy slow queries before asking for help.

---

## Assignment: Phase 3 (Graded)

1. **Setup:** Download and install SQLite Browser or use an online SQL sandbox (like DB Fiddle). Create a simple table called `Sales` with columns: `SaleID`, `SaleDate`, `Product`, `Region`, `Salesperson`, `Amount`.

2. **Insert:** Insert 10 dummy rows with varying data.

3. **Write Queries:**

    * Select all sales in the 'East' region.

    * Calculate the total sales per product.

    * Find the salesperson with the maximum sales amount.

    * Use a CTE to calculate the average sales amount per region, and then join back to the main table to flag which sales are above the average for their region ( `CASE WHEN Amount > Avg THEN 'Above' ELSE 'Below' END` ).

---

## Recommended Industry Resources (SQL)

* **Books:** *SQL Cookbook* by Anthony Molinaro (Read this like a dictionary). *T-SQL Fundamentals* by Itzik Ben-Gan (For SQL Server users, but the logic applies everywhere).

* **Practice Platforms:**

    * **LeetCode** (Database Section) - Do the "Medium" and "Hard" problems.

    * **HackerRank** (SQL Section) - Excellent for syntax drilling.

    * **SQLZoo** - For absolute beginners to get the feel.

* **YouTube:** DatawithBaraa has excellent practical SQL case studies.

* **Documentation:** Read the official PostgreSQL or MySQL documentation. Knowing how to read docs is a superpower.

---

## Professor's Final Note on Phase 3:

This is the most technically dense phase so far. Do not just *read* this lecture. Open your SQL environment and *type every single query I have written here*. Modify the table names. Break them on purpose to see the error messages. Error messages are your friends, they tell you exactly where you went wrong.

**Remember the 80/20 rule:** If you master `SELECT...FROM...JOIN...WHERE...GROUP BY...HAVING...ORDER BY` and Window Functions ( `ROW_NUMBER` , `LAG` , `LEAD` ), you are more than capable of handling 80% of the SQL requirements in a Data Analyst role.

The "SaaS Churn Analysis" major project is your Golden Ticket. It is exactly the kind of take-home assignment a hiring manager will give you. Complete it, save the SQL scripts, and add it to your GitHub repository.

---


<br/><br/><br/>

### Happy Learning!😊