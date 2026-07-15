Welcome back.

You have mastered the three pillars of the analyst: **Excel** (the UI), **SQL** (the extraction), and **Statistics** (the intuition). Now, we forge the master key: **Python**.

Here is the 80/20 truth about Python for Data Analysts: **You are not a software engineer.** You are not building a web application. You are building a **pipeline** from raw, ugly data to a clean, insightful report—that you can run with a single click every morning.

**The Pareto Principle for Python:** 80% of your Python code for data analytics will use exactly **20% of the language.** You will live inside **Pandas** (the Excel/Table of Python), occasionally calling **NumPy** for math, and writing just enough **Python logic** (loops, functions, conditionals) to glue it all together.

We will ruthlessly focus on **Pandas** for data wrangling and EDA. We will treat `matplotlib` and `seaborn` as visualization tools to support the analysis.

Let's build your automation engine.

---

# Phase 5: Python for Data Analytics

## Part 1: Python Basics – The "Pareto Syntax"

### Concept: 

The bare minimum Python you need to survive as an analyst.

**Why is it important?** You cannot run Pandas without knowing how variables, functions, and loops work. But you don't need to write complex algorithms. You just need to read code, tweak it, and write simple logic.


### The Core Toolkit (80% of your syntax):


#### 1. Variables & Data Types:

* **Numeric:** `int` (`1`), `float` (`3.14`).
* **String:** `text = "Sales Data"` (Enclose in quotes).
* **Boolean:** `True` / `False` (Must be capitalized).
* **List:** `my_list = [1, 2, 3]` (Ordered, mutable, can hold anything).
* **Dictionary (dict):** `my_dict = {"region": "East", "sales": 1000}` (Key-value pairs. Crucial for configuration).

#### 2. Operators:

* **Arithmetic:** `+`, `-`, `*`, `/` (float division), `//` (integer division), `**` (power).
* **Comparison:** `==` (equal), `!=` (not equal), `>`, `<`, `>=`, `<=`.
* **Logical:** `and`, `or`, `not`.

#### 3. Control Flow (The Deciders):

* `if` / `elif` / `else` : Used everywhere for data cleaning (e.g., categorizing customers).

    ```python
        if revenue > 10000:
            segment = "VIP"
        elif revenue > 5000:
            segment = "Gold"
        else:
            segment = "Standard"
    ```

#### 4. Loops (The Iterators):

* `for` loop: Iterating over lists/dicts. *Pareto Note:* In Pandas, you almost **never** loop over rows (it's slow). You use vectorized operations. But loops are essential for file management.

    ```python
        for i in range(5): # 0 to 4
            print(i)
    ```

#### 5. Functions (The Reusability Engine):

* **Syntax:** `def function_name(input_variable):` ... `return output`
* **Why?** If you copy-paste the same code 3 times, you are doing it wrong. Write a function.

    ```python
        def calculate_discount(price):
            if price > 100:
                return price * 0.9  # 10% off
            return price
    ```

#### 6. OOP Basics (Just the concept):

As a Data Analyst, you are a *user* of objects, not a builder of classes.

* **Concept:** A `DataFrame` in Pandas is an object. It has ***attributes*** (like `df.shape`) and ***methods*** (like `df.dropna()`). That is all the OOP you need to understand. You call `df.do_something()`.

#### 7. Modules & Virtual Environments (The Professional Touch):

* **Import:** `import pandas as pd`, `import numpy as np`. (Aliases are industry standard).
* **Virtual Environments (`venv`):** This isolates your project's packages. If Project A needs Pandas 1.0 and Project B needs Pandas 2.0, a `venv` prevents them from clashing.
    * **Command:** `python -m venv my_project_env` $\rightarrow$ Activate it $\rightarrow$ `pip install pandas numpy`.

#### 8. Jupyter Notebook:

This is your **"interactive scratchpad."** It allows you to write code in "cells," run them one at a time, and see the output (including charts) immediately below. **This is where 90% of Data Analysts build their EDA.**


---

## Part 2: NumPy – The Mathematical Engine

### Concept: 

NumPy (Numerical Python) provides the "array" data structure. Pandas is built *on top of* NumPy.

### Why is it important? 

It enables **Vectorization**—performing operations on entire arrays without writing explicit loops. This makes Python blindingly fast.

### Syntax & Examples:

```python

    import numpy as np

    # Creating an array
    prices = np.array([100, 200, 300])

    # Vectorized operation (multiplies every element by 0.9 without a loop)
    discounted = prices * 0.9

    # Basic stats
    np.mean(prices)    # 200
    np.std(prices)     # 81.65
    np.median(prices)  # 200

```


### Business Scenario:

You have 1 million sales transactions. You need to apply a tiered discount logic. Using NumPy's `np.where()` is significantly faster than a `for` loop.

```python
    # If sales > 1000, give 10% off, else 0% off
    discount_rate = np.where(sales_amount > 1000, 0.10, 0)
```

### Common Mistakes: 

Using pure Python loops on large datasets. If you find yourself writing `for i in range(len(df)):`, stop. There is almost always a vectorized Pandas/NumPy alternative.

---

## Part 3: Pandas – Your Professional Home

### Concept: 

Pandas provides the `DataFrame`—a 2-dimensional, labeled data structure (think: a supercharged Excel spreadsheet in Python).




### Why is it important?

This is your primary tool. You will spend 80% of your Python time in Pandas.

**A. The `Series` and `DataFrame`**

* **Series:** A single column. `pd.Series([1,2,3])`.
* **DataFrame:** A table of columns (multiple Series sharing an index). `pd.DataFrame({'col1':, 'col2': [3,4]})`.

**B. Reading Data (The First Step)**

```python

    import pandas as pd

    # CSV
    df = pd.read_csv('sales_data.csv')

    # Excel
    df = pd.read_excel('sales_data.xlsx', sheet_name='Sheet1')

    # SQL (requires sqlalchemy or connection string)
    # df = pd.read_sql("SELECT * FROM sales", con=connection)

    # Quick peek
    df.head()       # First 5 rows
    df.info()       # Column names, non-null counts, data types (CRUCIAL!)
    df.describe()   # Descriptive stats for numeric columns (Mean, Std, Min, Max, Quartiles)
    df.shape        # (rows, columns)

```

### C. Cleaning Data (The 80% Grind)

This is where we automate the Excel/Power Query cleaning we did manually.

#### 1. Handling Missing Values (`NaN` - Not a Number):

* **Detect:** `df.isnull().sum()` (Shows count of nulls per column).

* **Drop:** `df.dropna()` (Drops any row with a null). *Use with caution.*

* **Fill:** `df.fillna(value)` - The business solution.

    * *Numeric:* `df['salary'].fillna(df['salary'].median(), inplace=True)`.
    * *Categorical:* `df['department'].fillna('Unknown', inplace=True)`.

#### 2. Handling Duplicates:

* **Detect:** `df.duplicated().sum()`
* **Drop:** `df.drop_duplicates(subset=['customer_id'], keep='first', inplace=True)`.

#### 3. Renaming Columns (Keep it clean):

`df.rename(columns={'Old_Name': 'New_Name'}, inplace=True)`

#### 4. Data Type Conversion (Crucial for Date/Number operations):

* `df['date'] = pd.to_datetime(df['date'])`
* `df['sales'] = pd.to_numeric(df['sales'], errors='coerce')` (Coerce forces invalid entries to `NaN`).

#### 5. String Operations (Cleaning text):

* `df['email_domain'] = df['email'].str.split('@').str[1]`
* `df['customer_name'] = df['customer_name'].str.strip().str.upper()`


### D. Grouping & Aggregation (Replacing Pivot Tables)

```python

    # Equivalent to a Pivot Table: Total Sales by Region and Product
    summary = df.groupby(['region', 'product_category']).agg(
        total_sales = ('sales_amount', 'sum'),
        avg_order_value = ('sales_amount', 'mean'),
        order_count = ('order_id', 'count')
    ).reset_index()  # reset_index() turns the group keys back into columns.

```

### E. Merging & Joining (Replacing SQL JOINS)

```python

    # LEFT JOIN customer info onto orders
    orders_df = pd.merge(orders_df, customers_df, on='customer_id', how='left')

    # INNER JOIN
    orders_df = pd.merge(orders_df, products_df, on='product_id', how='inner')

    # Concatenating (stacking rows)
    master_df = pd.concat([df_q1, df_q2, df_q3], axis=0)  # axis=0 is rows, axis=1 is columns

```

### F. Pivoting & Reshaping (Excel Pivot in Python)

```python
# Create a cross-tabulation table
pivot_table = pd.pivot_table(df, 
                             values='sales_amount',
                             index='region',
                             columns='month',
                             aggfunc='sum',
                             fill_value=0)
```

### G. DateTime Magic (Time Series)

* **Extracting:** `df['year'] = df['date'].dt.year`, `df['month_name'] = df['date'].dt.month_name()`.

* **Filtering by date:** `df[(df['date'] >= '2024-01-01') & (df['date'] < '2024-02-01')]`.

* **Resampling (Monthly aggregation):** `df.set_index('date').resample('M')['sales'].sum()`. (This is a forecasting essential).

### H. Performance Optimization (Pareto Tip):

* **Avoid loops:** Use vectorized operations.
* **Use `query()`:** `df.query("region == 'East' and sales > 1000")` is sometimes faster and more readable than bracket indexing for complex conditions.
* **Downcast dtypes:** If a column has numbers up to 100, use `df['col'] = pd.to_numeric(df['col'], downcast='integer')` to save memory.

---

## Part 4: Advanced Data Cleaning – Feature Engineering

### Concept: 

Feature engineering is creating new variables from raw data to make the analysis more powerful.

### Why is it important? 

The raw data is never in the perfect format. You must derive business logic.

#### 1. Handling Outliers (Z-score method):

```python
    from scipy import stats

    # Calculate Z-scores
    z_scores = np.abs(stats.zscore(df['sales_amount']))

    # Keep only rows where Z-score < 3 (removes extreme outliers)
    df_clean = df[(z_scores < 3)]
```

#### 2. Encoding Categorical Variables (Turning text into numbers for models):

* **One-Hot Encoding:** `pd.get_dummies(df, columns=['department'], drop_first=True)`. (Creates binary columns for 'HR', 'Sales', etc.).

#### 3. Feature Creation (The Business Value):

```python
    # Profit Margin
    df['profit_margin'] = (df['revenue'] - df['cost']) / df['revenue']

    # Customer Lifetime Value (Aggregating)
    customer_clv = df.groupby('customer_id')['revenue'].sum().reset_index(name='lifetime_value')

    # Days since last order
    df['order_date'] = pd.to_datetime(df['order_date'])
    df['days_since_order'] = (pd.Timestamp('today') - df['order_date']).dt.days
```

---

## Part 5: Exploratory Data Analysis (The Payoff)

### Concept: 

EDA is the process of investigating data to discover patterns, spot anomalies, and test hypotheses using summary statistics and graphical representations.

### Why is it important? 

This is where you transition from "data janitor" to "business consultant." EDA is the story you are about to tell.

### The EDA Workflow (Pythonic):

#### 1. Univariate Analysis (Look at one variable at a time):

```python
    # Numeric: Distribution
    df['sales_amount'].describe()  # Mean, Std, Min, Max, Q1, Q2, Q3

    # Categorical: Frequency
    df['region'].value_counts()
```

#### 2. Bivariate Analysis (Relationships):

```python
    # Correlation Matrix
    correlation_matrix = df[['sales', 'marketing_spend', 'customer_age']].corr()
    print(correlation_matrix)

    # Correlation Heatmap (Visual)
    import seaborn as sns
    import matplotlib.pyplot as plt

    sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm')
    plt.show()
```

#### 3. Trend & Pattern Detection:

```python
    # Monthly trend
    monthly_trend = df.groupby(df['date'].dt.to_period('M'))['sales'].sum()
    monthly_trend.plot(kind='line', title='Monthly Sales Trend')
    plt.show()
```

#### 4. Segmentation (Finding the "Why"):

```python
    # Compare High-Value vs Low-Value customers
    df['segment'] = np.where(df['lifetime_value'] > df['lifetime_value'].median(), 'High', 'Low')
    df.groupby('segment')[['avg_order_size', 'days_active']].describe()
```

### Common Mistakes Beginners Make:

* **Not visualizing first:** Running statistics without looking at a distribution. Always plot a histogram before calculating a mean.

* **Confusing `df.copy()`:** If you do `df2 = df`, and modify `df2`, you modify `df` (it passes by reference). Always use `df2 = df.copy()` to avoid corrupting your original raw data.

---

## Visualization Integration (The 20% for Charts)

While Phase 6 (Dashboards) will cover BI tools, you must know how to create quick plots in Python to explore data.

* **Matplotlib (Foundation):** `plt.plot()`, `plt.bar()`.

* **Seaborn (Statistical Charts):** `sns.boxplot()`, `sns.histplot()`, `sns.scatterplot()`. It automatically handles statistical aggregations.

### Business Example:

```python
    import matplotlib.pyplot as plt
    import seaborn as sns

    # Setting the style
    sns.set_style("whitegrid")

    # Boxplot to see outliers by region
    plt.figure(figsize=(10,6))
    sns.boxplot(x='region', y='sales_amount', data=df)
    plt.title('Sales Distribution by Region - Outliers Visible')
    plt.show()
```

---

## Mini Project: "The Monthly Sales Reconciliation Script"

**Background:** Every month, the finance team sends you a messy CSV (`sales_raw.csv`) and expects a clean summary report by 9 AM. You are tired of manually doing this in Excel.

### Your Python Script (Your Deliverable):

**1. Load:** Read the CSV into Pandas.

**2. Clean:**
* Convert `order_Date` to datetime.

* Remove rows where `sales_Amount` is negative (data entry errors).

* Remove duplicates on `order_ID`.

* Fill missing `Region` with `"Unknown"`.


**3. Derive:** Create a new column `Quarter` (e.g., `df['date'].dt.quarter`).

**4. Aggregate:** Group by `Region` and `Quarter`. Calculate total revenue, total orders, and average order value.

**5. Save:** Export the clean aggregated table to `clean_summary.xlsx`.

**6. Add a print statement:**
`print("Report generated successfully for month: " + str(pd.Timestamp.now()))`

---


## Major Project: "The E-Commerce Customer Churn EDA Toolkit"

**Scenario:** You are hired by "ShopSphere," a mid-sized e-commerce platform. They have 50,000 customers. They have provided you with a messy dataset (`shopsphere_data.csv`) containing:

* `Customer_ID`, `Signup_Date`, `Last_Login_Date`, `Total_Orders`, `Total_Spend`, `Avg_Discount_Used`, `Days_Since_Last_Purchase`, `Support_Tickets_Logged`, `Response_Time_Hours`, `Product_Categories_Browsed`, `Is_Churned` (Yes/No - manually flagged by the sales team).

**The Business Question:** *"Who is going to churn next month, and what can we do to stop it?"*

### Your Deliverables (This is your Python Portfolio Piece):

**1. Data Loading & Initial Inspection:**

* Load the CSV. Run `df.info()`, `df.describe()`, and `df.head()`.

* Identify any obvious data quality issues (e.g., negative `Total_Spend`, missing `Last_Login_Dates`).

**2. Advanced Data Cleaning (The Automation):**

* **Dates:** Convert `Signup_Date` and `Last_Login_Date` to datetime. Create `customer_Tenure_Days` = (Today - `Signup_Date`).

* **Nulls:** Impute the median `Response_Time_Hours` for rows where it is missing.

* **Categorical:** Convert `Is_Churned` to a boolean (True/False) using `map` or `np.where`.

* **Outliers:** Use the IQR method to cap extreme values in `Total_Spend` at the 99th percentile (to prevent a single billionaire from skewing your analysis).


**3. Feature Engineering (The Insight Generation):**
Create these critical business features:

* **Engagement_Score:** `Days_Since_Last_Purchase` / `Customer_Tenure_Days` (smaller means more engaged).

* **Support_Burden:** Average support tickets per month of tenure.

* **Discount_Sensitivity:** If `Avg_Discount_Used` > 0.2 (20%), flag as "Discount_Hunter" (these often churn when discounts stop).


**4. Exploratory Analysis (The Diagnosis):**

* **Univariate:** Calculate the overall churn rate. (`df['Is_Churned'].mean()` gives you the %).

* **Bivariate (Segments):**

    * Group by `Is_Churned`. Compare the average `Total_Spend`, `Tenure`, and `Days_Since_Last_Purchase` for churned vs. non-churned. *(If `Days_Since_Last_Purchase` is 30 days for churned and 5 days for non-churned, you have a leading indicator).*
* **Correlation Heatmap:** Generate a heatmap of numeric variables. Specifically, check the correlation between `Days_Since_Last_Purchase` and `Is_Churned`.
* **Visualization:**

    * **Plot 1:** Histogram showing the distribution of `Customer_Tenure_Days` for churned vs. non-churned. *(If you see a spike in churn at 90 days, you know your 3-month free trial period is the problem).*

    * **Plot 2:** Bar chart showing churn rate by `Product_Categories_Browsed`. *(If customers who browse only 1 category churn at 80%, you know you need to cross-sell).*

    * **Plot 3:** Boxplot of `Support_Tickets_Logged` segmented by `Is_Churned`. *(If churned customers logged 5 tickets on average vs. 1 ticket for loyal customers, your customer service is failing).*


**5. Actionable Insights & Reporting:**

* **The "At-Risk" Segment:** Write a Pandas query to filter active customers (`Is_Churned` = False) who meet the following criteria:
    * `Days_Since_Last_Purchase` > 30 days.

    * `Support_Tickets_Logged` > 3.

    * `Total_Spend` < Median `Total_Spend`.

    * *Export this list to `at_risk_customers.csv` for the retention team to call.*
* **Summary Report:** In a Jupyter Notebook Markdown cell, write a 3-paragraph summary for the CMO:

    * *Paragraph 1:* "The overall churn rate is 22%. Our primary churn driver is lack of engagement, specifically customers who do not purchase within 30 days."

    * *Paragraph 2:* "Customers who file more than 3 support tickets are 4x more likely to churn. This suggests a product usability issue, not a price issue."

    * *Paragraph 3:* "Recommendation: Launch a 'Reactivate' email campaign targeting customers inactive for 25 days, and overhaul the help center to reduce ticket volume."
    
---

## Interview Questions (Python Specific for DA)

**1. Beginner:** "What is the difference between a Python List and a NumPy Array?"

* **Answer:** Lists can hold heterogeneous data types and are slower. NumPy arrays are homogeneous, allow vectorized operations, and are much faster for numerical computations.

**2. Intermediate:** "You have a DataFrame `df` with a column `date`. How do you filter rows for March 2024 without using a loop?"

* **Answer:** `df[(df['date'] >= '2024-03-01') & (df['date'] <= '2024-03-31')]` OR `df[(df['date'].dt.month == 3) & (df['date'].dt.year == 2024)]`.

**3. Advanced:** "Explain the difference between `merge`, `join`, and `concat` in Pandas."

* **Answer:** `merge` is primarily used for joining on key columns (like SQL). `join` is used for joining on the index. `concat` is used for stacking tables vertically (adding rows) or horizontally (adding columns) without necessarily aligning on a key.

**4. Scenario:** "Your Jupyter notebook is running very slowly on a 5GB dataset. How do you optimize it?"

* **Answer:** 1. Use `pd.read_csv()` with `chunksize` to process in pieces. 2. Downcast numeric columns to smaller dtypes (`int64` $\rightarrow$ `int32`). 3. Use `df.query()` for filtering. 4. Avoid `apply()` with lambda functions on large datasets; use vectorized operations. 5. Use the `pandas profiling` library to identify bottlenecks.


---

### Phase 5 Summary / Cheat Sheet

#### The 20% Python Commands You Must Memorize:

| Task | Command |
| :--- | :--- |
| **Read Data** | `pd.read_csv('file.csv')` |
| **Preview** | `df.head()`, `df.info()`, `df.describe()` |
| **Filter Rows** | `df[df['col'] > 100]` |
| **Filter Columns** | `df[['col1', 'col2']]` |
| **Create Column** | `df['new'] = df['col1'] + df['col2']` |
| **Group & Aggregate** | `df.groupby('cat')['num'].sum().reset_index()` |
| **Merge (SQL Join)** | `pd.merge(left, right, on='key', how='left')` |
| **Handle Nulls** | `df.fillna(df.median())` |
| **Pivot** | `pd.pivot_table(df, index='row', columns='col', values='val', aggfunc='sum')` |
| **Date Extraction** | `df['date'].dt.month`, `df['date'].dt.year` |
| **Export** | `df.to_csv('clean.csv', index=False)` |

**The Golden Rule:**

* If you have to write a `for` loop over rows, you are doing it wrong. Use `apply()` or vectorization.

---


### Assignment: Phase 5 (Graded)

1. **Setup:** Install Python, Pandas, and NumPy. Launch a Jupyter Notebook.

2. **Data Creation:** Create a dictionary with three lists (`Name`, `Age`, `Salary`). Convert it to a DataFrame.

3. **Data Manipulation:** Filter the DataFrame to show only people over 30. Create a new column `Bonus` that equals `0.1 * Salary`. Group by `Age` and calculate the average `Bonus`.

4. **Challenge:** Write a function that takes a DataFrame, checks for nulls, and fills them with the median of that column if numeric, or `"Missing"` if categorical.


***

### Recommended Industry Resources (Python)

* **Books:** *Python for Data Analysis* by Wes McKinney (The creator of Pandas—this is your bible, read Chapters 5-10).

* **Interactive Learning:** DataCamp (Python for Data Science track) and Kaggle's "Learn" section (Micro-courses on Pandas).

* **YouTube:** Corey Schafer (for pure Python basics) and Keith Galli (for practical Pandas walkthroughs).

* **Documentation:** The official Pandas Cheat Sheet (print it and stick it on your wall).

---

## Professor's Final Note on Phase 5:

You have just graduated from manual laborer to automation engineer.

When you were using Excel, you had to clean the data by hand every week. With SQL, you just pulled it. With Python, you now have a **reproducible, auditable, automatic pipeline**. The beauty of a Jupyter Notebook is that you can show a hiring manager your *thinking process* the messy cleaning steps, the failed attempts, the final visualization.

The **"ShopSphere Churn" Major Project** is a common take-home assignment for mid-level Data Analyst roles. If you can complete this, explain your code, and articulate the business recommendations, you are ready for the final stage.

For now, open your terminal, type `jupyter notebook`, and start butchering some data. Make mistakes. Break things. That is how you learn. I will see you in Phase 6.

---




<br/><br/><br/>

### Happy Learning!😊
