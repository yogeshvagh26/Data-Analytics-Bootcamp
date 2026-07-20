####  Welcome back! 😀

You have survived the gauntlet. You have mastered Excel, SQL, Python, Statistics, Power BI, and Business Acumen. But there is one final test: **Can you apply all of it to an industry you have never seen before?**

**The Pareto Principle for Case Studies:** The specifics of "Netflix" and "Uber" and "Banking" are different, but the **analytical workflow** is identical. 80% of the value in this phase is learning the *pattern recognition—how to walk into any industry, ask the right questions, find the data, and build the dashboard.*

In this Phase, we will do a rapid-fire tour of **13 major industries.** For each, I will give you the cheat sheet: the business model, the core metrics, the data you need, and the analytical twist. Then, we will dive deep into **two Capstone Projects** (E-commerce/Retail and SaaS/Subscription) where we will go from raw CSV to an executive dashboard, step-by-step.

Welcome to Phase 9: The Real Business Case Studies.

---

# Phase 9 — Real Business Case Studies



## Part 1: The Universal Analytical Workflow

Before we look at a single industry, memorize this 6-step workflow. You will apply this to *every* case study.

1. **Understand the Business Model:** (B2B vs B2C? Transactional vs Subscription? Freemium vs Paid?) - *This dictates your North Star Metric.*

2. **Map the Customer Journey:** Acquisition → Activation → Engagement → Revenue → Churn/Retention.

3. **Identify the Levers:** What can the business *actually control?* (Price? Ads? Product features? Staffing?).

4. **Define the KPIs:** Translate the business goal into 3-5 measurable numbers.

5. **Data Extraction & Cleaning:** Use SQL/Python/Power Query to build the pipeline.

6. **Visualize & Recommend:** Build the dashboard that clearly answers "So what?"


---


## Part 2: The Industry KPI Cheat Sheet (The 80/20 Tour)

Here is your field guide to the most common industries. Study this table until it is instinctive.

| Industry | Example Company | Primary Question | Core Data Tables | Top 3 KPIs | The "Golden" Analytical Twist |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Retail** | Amazon / Walmart | "How do we maximize profit per shelf?" | Sales, Inventory, Promotions | 1. Sell-Through Rate, 2. Gross Margin %, 3. Inventory Turnover | Analysis of Promotional Lift—did the discount actually increase total revenue, or just cannibalize full-price sales? |
| **SaaS / Subscription** | Netflix / Spotify | "How do we stop people from canceling?" | User Activity, Subscriptions, Content Views | 1. MRR / Churn, 2. LTV:CAC Ratio, 3. DAU/MAU | Cohort Analysis is king here. You don't look at overall retention; you look at retention by signup month to see if product improvements are working. |
| **Marketplace / Gig** | Uber / Airbnb | "How do we balance supply & demand?" | Trips, Driver/Partner Availability, Surge Data | 1. Utilization Rate, 2. ETA (Estimated Time Arrival), 3. Take Rate | Spatial-Temporal Analysis—analyzing heatmaps of demand by day/hour to recommend dynamic pricing and driver placement. |
| **AdTech / Media** | Google / Meta | "How do we sell attention for the highest price?" | Impressions, Clicks, Bids, Conversion | 1. RPM (Revenue per Mille), 2. CTR (Click-Through Rate), 3. eCPM | Attribution Modeling—figuring out which ad slot/targeting actually drove the sale, not just the click. |
| **Healthcare** | Hospital Network | "How do we treat patients better for less cost?" | Patient Records, Admissions, Staffing, Outcomes | 1. Readmission Rate, 2. Average Length of Stay (ALOS), 3. Bed Occupancy % | Patient Journey Mapping—finding the bottleneck in the ER/Admission process using timestamps. |
| **Aviation** | Delta / American | "How do we fill seats profitably?" | Flights, Bookings, Weather, Loyalty | 1. Load Factor, 2. RASM (Revenue per ASM), 3. On-Time Performance | Yield Management—forecasting demand to adjust ticket prices dynamically. |
| **Manufacturing** | Tesla / GE | "How do we reduce defects and downtime?" | Production Logs, Machine Sensor Data, Quality Checks | 1. OEE (Overall Equipment Effectiveness), 2. Defect Rate, 3. Downtime % | *Root Cause Analysis*—using correlation (Python) to identify which machine/temperature setting causes specific defects. |
| **Banking** | JPMorgan / Citi | "Who is profitable and who is risky?" | Transactions, Accounts, Credit Scores, Loans | 1. NPL (Non-Performing Loan) Ratio, 2. Cross-Sell Rate, 3. ROA (Return on Assets) | *Segmentation*—segmenting customers by profitability vs. risk to tailor credit card offers. |
| **HR / People** | Any Internal Team | "How do we hire and keep the best talent?" | Employee Demographics, Performance, Turnover | 1. Attrition Rate, 2. Time-to-Hire, 3. eNPS | *Predictive Retention*—using Python (Logistic Regression) to predict which employees are likely to leave in the next 3 months. |
| **Telecom** | Verizon / AT&T | "How do we stop churn in a saturated market?" | CDRs (Call Records), Subscriptions, Complaints | 1. Churn Rate, 2. ARPU (Avg Revenue Per User), 3. NPS | *Network Quality Correlation*—correlating dropped call rates with customer churn zip codes. |
| **Education** | Coursera / Harvard | "How do we increase graduation/completion rates?" | Enrollments, Quiz Scores, Video Viewing | 1. Completion Rate, 2. Dropout Rate (Week 1), 3. Engagement (Minutes/Week) | *Learning Journey Analysis*—finding the exact lecture/video where students drop off and recommending a redesign. |


---


## Part 3: In-Depth Capstone A — E-Commerce/Retail (Amazon Style)

### Scenario: 

You are hired as a consultant for "MarketSphere," an online marketplace. They sell 10,000 products across 50 states. The CEO says: *"We have a mountain of data, but I don't understand why our Q4 profit dropped while our Q4 sales went up."*

**Your Mission:** Diagnose the problem and build an automated monitoring dashboard.

**Step 1: Understanding the Business Model.**

* They are a marketplace. They buy products wholesale (`COGS`) and sell them at a markup (`Revenue`).

* `Profit = Revenue - COGS - Shipping Costs - Marketing Costs.`

**Step 2: The Data (Your Raw Sources).**

* `Sales.csv`: `OrderID`, `Date`, `ProductID`, `CustomerID`, `Sales_Amount`, `Quantity`.

* `Products.csv`: `ProductID`, `ProductName`, `Category`, `Cost_Price`, `Weight`.

* `Marketing.csv`: `CampaignID`, `Date`, `Channel`, `Spend`.

* `Shipping.csv`: `OrderID`, `Ship_Date`, `Carrier`, `Shipping_Cost`.


**Step 3: Data Modeling (Power Query + Star Schema).**

* Load all tables into Power BI.

* Create a `DimDate` table (using `CALENDAR()`).

* **Fact Table:** `Sales`.

* **Dimensions:** `Products`, `Customers`, `Dates`.

* **Bridge Table (Optional):** `Campaigns` mapped to sales if possible.


**Step 4: DAX Detective Work (Finding the Profit Killer).**
You build the following measures:

* `Total Revenue = SUM(Sales[Sales_Amount])`

* `Total COGS = SUMX(Sales, Sales[Quantity] * RELATED(Products[Cost_Price]))`

* `Total Shipping = SUM(Sales[Shipping_Cost])`

* `Net Profit = [Total Revenue] - [Total COGS] - [Total Shipping] - [Total Marketing Spend]`

* `Profit Margin % = DIVIDE([Net Profit], [Total Revenue], 0)`


**Step 5: The "Aha!" Moment.**

You create a Matrix visual: `Product Category` in Rows, `Month` in Columns, `Profit Margin %` as Values.

* You see that in Q4, the "Electronics" category margin dropped from 25% to 8%.

* You drill down. The `shipping_cost` for Electronics spiked in Q4.

* *Why?* You check the Products table. A specific sub-category of "Heavy Batteries" sold exceptionally well in Q4 (holiday season). The shipping cost for these heavy items was 4x the normal rate. The revenue was high, but the *profit* was decimated by logistics.


**Step 6: The Dashboard (The Solution).**
You build a "Profitability Command Center":

* **Top KPI Cards:** Total Revenue, Net Profit, Margin %.

* **Line Chart:** Revenue vs. Shipping Cost over time (overlayed to show the correlation).

* **Treemap:** Product Categories by Profitability (Red for Electronics).

* **Slicers:** Date Range, Product Category, Region.

* **Conditional Formatting:** Any product category with a margin < 15% turns red.

* **Recommendation Page:** A bookmark that says: *"Recommendation: Increase prices on heavy electronics by 15% to offset shipping costs, or negotiate a bulk shipping rate with carriers for Q4."*

---

## Part 4: In-Depth Capstone B — SaaS/Subscription (Netflix Style)

### Scenario: 

You are the sole analyst for "StreamFlix," a video streaming service. They are growing subscribers, but their *revenue per user* (ARPU) is declining. They are losing high-value customers.

**Your Mission:** Understand why high-value users are churning and build a predictive retention dashboard.

**Step 1: Understanding the Business Model.**

* Freemium (Ad-based) vs. Premium (Ad-free). High-value = Premium users.

**Step 2: The Data.**

* `Subscriptions`: `UserID`, `Signup_Date`, `Plan_Type`, `Status` (Active/Canceled), `Billing_Amount`.

* `Viewing_Logs`: `UserID`, `Date`, `content_ID`, `Minutes_Watched`, `content_Genre`.

* `Support_Tickets`: `TicketID`, `UserID`, `Created_Date`, `Resolution_Time_Hours`.


**Step 3: ETL & Python EDA.**
You load the data into Python for deep exploration.

* **Churn Calculation:** You find the overall churn is 8% monthly, but for Premium users, it's 12%.

* **Engagement Analysis:** You group by `UserID` and calculate `Avg_Minutes_Per_Day` and `Days_Since_Last_Login`.

* **Correlation:** You run a correlation matrix. The highest correlation with churn is `Days_since_Last_Login`. Users who don't log in for 14 days are 80% likely to cancel.

**Step 4: SQL for the Cohort.**
You write a CTE to create a Cohort Retention Table.

```sql
    WITH cohorts AS (
        SELECT
            UserID,
            DATE_TRUNC('month', Signup_Date) AS cohort_month
        FROM Subscriptions
    ),
    user_activities AS (
        SELECT
            s.UserID,
            c.cohort_month,
            DATEDIFF(month, c.cohort_month, v.View_Date) AS month_number
        FROM Viewing_Logs v
        JOIN cohorts c ON v.UserID = c.UserID
    )
    SELECT
        cohort_month,
        month_number,
        COUNT(DISTINCT UserID) / MAX(COUNT(DISTINCT UserID)) OVER (PARTITION BY cohort_month) * 100 AS retention_pct
    FROM user_activities
    GROUP BY cohort_month, month_number;
```

(You run this in Python via `pd.read_sql` or in Power Query).


**Step 5: The DAX Measures (Power BI).**

* `Active Users = CALCULATE(COUNTROWS(Subscriptions), Subscriptions[Status] = "Active")`
* `Predicted Churn Risk = CALCULATE([Active Users], FILTER(Viewing_Logs, Viewing_Logs[Days_Since_Last_Login] > 14))`

**Step 6: The Dashboard (The Action Engine).**

* **Top KPI:** MRR, Churn Rate, Predicted Churn Risk.

* **Line Chart:** Cohort Retention Matrix (visualized as a line chart showing Month 1, Month 2, Month 3 retention).

* **Bar Chart:** Churn Rate by Plan Type (showing Premium is bleeding).

* **Scatter Plot:** `Minutes_Watched` vs. `Churn` (clearly shows low engagement = high churn).

* **The "At-Risk" Table:** A table listing the top 50 Premium users who haven't logged in for 12 days (before they hit the 14-day mark). The retention team can call them.

* **Recommendation:** "Launch a 'Reactivate' email campaign with a free movie credit to Premium users who are inactive for 10 days."



---

**Mini Project: "The Airline On-Time Performance Analysis"**

**Background:** You have a dataset of 500,000 flights (`FlightID`, `Airline`, `Origin`, `Destination`, `Departure_Time`, `Arrival_Time`, `Delay_Minutes`, `Weather_Condition`).

**Your Tasks:**

1. **Data Cleaning (Python):** Remove flights with negative delays (data entry errors). Convert times to datetime.

2. **EDA (Python):** Calculate the average delay per airline. Which airline is the worst?

3. **Insight Generation:** Calculate the correlation between `Weather_Condition` and `Delay`.

4. **Visualization:** Create a Power BI dashboard with:

    * A bar chart of delays by Airline.

    * A map showing average delay by Origin City (Heatmap).

    * A KPI showing the % of flights on-time (< 15 min delay).

5. **The Recommendation:** If you find that Airline X has 70% delays due to "Mechanical Issues," recommend they invest in a new maintenance facility at their hub airport.


---

## Major Project: The "Omni-Industry Consulting Portfolio"

**Scenario:** You are applying for a job. The interviewer says, *"We are a telecommunications company. We have no data infrastructure. Tell us how you would start from scratch to reduce customer churn."*

**Your Deliverable (A Written Proposal + Conceptual Dashboard):**

**1. The Proposal (Business Understanding):**

* **Objective:** Reduce churn by 10% in the next 6 months.

* **Data Strategy:** We need to collect: a) Customer Demographics, b) Call Detail Records (duration, dropped calls), c) Billing History (late payments), d) Customer Support Logs (complaint frequency).


**2. The Analytical Plan (Data Modeling):**

* Build a Star Schema in Power BI:

    * `FactCDRs` (Call Records).

    * `DimCustomer` (Demographics & Plan Type).

    * `DimDate`.

    * `DimTariff` (Pricing plans).


**3. The Key Metrics (KPIs):**

* **Monthly Churn Rate.**

* **Dropped Call Rate per Zip Code** (to find network black spots).

* **Complaint-to-Resolution Time.**

* **ARPU (Average Revenue Per User).**


**4. The Dashboard Prototype (Visual Design):**

* **Page 1 (Executive Summary):** Churn Trend (Line Chart), Churn by Plan Type (Bar), KPI cards.

* **Page 2 (Root Cause):** A scatter plot of *Call Drops vs Churn*. If they correlate, you found the problem.

* **Page 3 (The Micro-Target):** A table of the top 100 customers with the highest *Complaint Count* and *Lowest Usage* (these are the next to churn).


**5. The Recommendation:**

* "We should prioritize network tower maintenance in the zip codes with the highest dropped call rates and implement a retention call-back campaign for the top 100 at-risk customers identified in Page 3."


---

**Interview Questions (Real-World Case Studies)**

1. **Scenario (Retail):** "A grocery store sees that sales are declining in the frozen food aisle. How do you analyze this?"

    * **Expected Answer:** I would look at three things. First, price elasticity (did we raise prices?). Second, competitor analysis (did a new store open nearby?). Third, product mix (did we discontinue a popular item or run out of stock on a key SKU?). I would pull the daily SKU-level sales data, competitor pricing data, and inventory logs. If I find that "Frozen Pizza" is out of stock every weekend, I recommend adjusting the restocking schedule.

2. **Scenario (SaaS):** "Our trial-to-paid conversion rate is 5%, but the industry average is 15%. What would you look at first?"

    * **Expected Answer:** I would look at User Activation. Specifically, I would define a key "Aha!" moment (e.g., the user completes their first project/invites a teammate). I would then calculate the conversion rate for users who hit that milestone vs. those who don't. Often, conversion is low because users never get to experience the product's value. I'd recommend redesigning the onboarding to force them to reach the Aha! moment.

3. **Scenario (Transportation/Uber):** "The driver churn rate is very high. How do you reduce it?"

    * **Expected Answer:** I would analyze driver earnings vs. their expenses (fuel/time). I would look at the correlation between Surge Pricing and Driver Retention. If drivers who only drive during base-fare hours leave quickly, I'd recommend implementing a guaranteed minimum hourly rate or introducing a "quest" bonus system to incentivize driving during peak demand hours.

4. **Scenario (Healthcare):** "The ER wait time is averaging 4 hours. How do you fix it?"

    * **Expected Answer:** I would do a process mining analysis. I would look at the timestamp data: Check-in → Triage → Room Assignment → Lab Test → Physician Assessment → Discharge. I would find the bottleneck. If the wait is mostly between "Lab Test" and "Physician Assessment," I'd recommend hiring an additional lab technician during peak hours or implementing point-of-care testing at the bedside.

---

## Phase 9 Summary / Cheat Sheet

**The Golden Rule for Case Studies:**
*Every business problem is a "Customer Journey" problem in disguise.*

* **Retail Problem: A shelf is empty.** → *Data:* Inventory logs. → *Solution:* Fix the supply chain.

* **SaaS Problem: Users aren't paying.** → *Data:* User activity logs. → *Solution:* Fix the onboarding.

* **Bank Problem: Loans are defaulting.** → *Data:* Credit history. → *Solution:* Adjust the credit scoring model.

* **Airline Problem: Planes are delayed.** → *Data:* Maintenance logs. → *Solution:* Overhaul the maintenance schedule.


### Your Ultimate Analytical Mantra:

1. **What is the objective? (More profit? More retention? Lower costs?)**

2. **What metric defines that objective?**

3. **What data is required to move that metric?**

4. **What can the business do with this analysis?**

---


## Phase 9 Assignment (Graded)

1. **Industry Deep Dive:** Pick one industry from the list (e.g., Education). Find a public dataset on Kaggle (e.g., Student Performance). Write a 300-word summary of the business problem, the relevant KPIs, and how you would design a dashboard for the Principal of that school.

2. **The "Vague Question" Challenge:** A Hospital CEO says: *"Our readmission rates are too high. Fix it."* Write a 1-page email back to the CEO. In the email, you must:

    * Define exactly what "readmission" means (30 days? 90 days?).

    * List the 3 things you suspect are driving it (e.g., poor discharge instructions, lack of follow-up appointments, social determinants of health).

    * Request the specific data tables you need.

    * Outline the timeline for your initial analysis (e.g., "I will present a preliminary dashboard in 3 days").


---


### Recommended Industry Resources (Case Studies)

* **Datasets:**

    * **Kaggle** (Superstore Sales, Telco Churn, Titanic, HR Analytics).

    * **Google Public Datasets** (for BigQuery practice).

* **Books:** *Data Science for Business* by Provost & Fawcett (Focuses on the *business value* of data).

* **Simulations:** Use the "Maven Market" dataset (provided by Maven Analytics) for a full end-to-end retail simulation.

* **Podcasts/News:** Read the *Wall Street Journal* or *Harvard Business Review* daily. Every article describes a business problem. Your job is to mentally frame: *"How would I analyze that with data?"*


---


### Professor's Final Note on Phase 9:

You have just completed the ultimate stress test. Look back at where you started unsure of the difference between a spreadsheet and a database. Now, you can walk into a Telecom, a Hospital, or a Streaming Service, ask the right questions, extract the right data, build the right model, and present the right solution.

This is the 80/20 rule in its final form: 80% of your success is understanding the business problem. The remaining 20% is hitting the keyboard.

now, go find a dataset online. Pick an industry that fascinates you. The only way to become job-ready is to *behave* like you already have the job.

I will see you at the career launch. You are almost there, Analyst.

---





<br/><br/><br/>

### Happy Learning!😊