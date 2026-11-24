# 🎯 Power BI Job Interview Preparation Guide
## Project: E-Commerce & Retail Sales Performance Analytics
**Target Roles**: Data Analyst, Business Intelligence Analyst, Power BI Developer, Junior Reporting Specialist  
**Course/Affiliation**: Anudip Foundation Capstone Project

---

## 📌 How to Use This Guide
In interviews, panels don't just test theoretical definitions—they want to see **how you applied concepts to a real project**. Whenever an interviewer asks a question, always ground your answer in this E-Commerce project.

---

# SECTION 1: Project Walkthrough & Behavioral Questions

### Q1: "Can you walk me through your recent Power BI project?"
**How to answer**:
> "Certainly! In my recent capstone project at Anudip Foundation, I designed and developed an end-to-end **E-Commerce Sales Performance Dashboard** in Power BI. 
> 
> The business challenge was that management had over 6,000 transaction records spanning 2022 to 2024 across 29 Indian cities, but lacked real-time visibility into sales margins, category profitability, and customer behavior.
> 
> I approached this in 5 stages:
> 1. **ETL in Power Query**: Cleaned data types, validated zero errors, and created a custom shipping lead-time calculation.
> 2. **Data Modeling**: Built a Star Schema with a dedicated dynamic Date dimension table with custom chronological sorting.
> 3. **DAX Measures**: Formulated an organized measures table calculating Total Sales (\$205M), Net Profit, Profit Margin %, AOV, and Total Customers.
> 4. **Visual Design**: Built a 2-page interactive report with Executive Overview and Customer Regional Insights, incorporating KPI cards, monthly trend lines, and an Indian regional map.
> 5. **Business Impact**: My analysis identified a 35% seasonal demand spike in Q4, and highlighted that excessive discounting (>25%) on Furniture was eroding net profits."

---

### Q2: "What was the most challenging part of this project, and how did you solve it?"
**How to answer**:
> "One challenge was handling the **Time Intelligence and chronological month sorting**. Initially in Power BI, month names sort alphabetically (*Apr, Aug, Dec...*), which distorts monthly trend lines. 
> 
> To solve this, I created a custom DAX Date Dimension (`Dim_Date`) containing `Month Number` and used the **'Sort by Column'** feature in Power BI so that the months displayed chronologically from January to December. 
> 
> Another challenge was handling the diverse scale of metrics—displaying sales in millions alongside 18% margins. Rather than cluttering a single visual, I separated high-level metrics into formatted KPI cards and category comparisons into clustered bar charts."

---

### Q3: "What actionable recommendations did you deliver to stakeholders?"
**How to answer**:
> "I delivered four key business recommendations:
> 1. **Inventory Preparation for Q4**: Sales jump by ~35% in October–December due to the festival season. I recommended locking supplier inventory 6 weeks in advance.
> 2. **Furniture Discount Cap**: Deep discounts on furniture were causing negative margins. I recommended capping furniture discounts at 20%.
> 3. **UPI Payment Optimization**: UPI drove over 40% of orders, so prioritizing seamless UPI checkout flows reduces abandoned carts.
> 4. **Regional Warehousing**: 65% of revenue came from the West and South regions (Maharashtra, Karnataka, Tamil Nadu). Placing fulfillment centers in Mumbai and Bengaluru will reduce delivery times below 3 days."

---

# SECTION 2: Power Query & ETL Questions

### Q4: "Why should you always transform data in Power Query instead of loading it directly?"
**Answer**:
> "Loading raw data directly bypasses essential data hygiene checks. In Power Query, we can:
> - Validate column data types (ensuring dates aren't parsed as text).
> - Profile data quality (identifying nulls, duplicates, and column distribution).
> - Perform row-level feature engineering (like computing `Shipping_Days` using M-code `Duration.Days([Ship_Date] - [Order_Date])`).
> - Filter out unwanted columns early to reduce the memory footprint of the VertiPaq storage engine."

---

### Q5: "What is the difference between M-code (Power Query) and DAX?"
**Answer**:
> - **M-code (Power Query)** is an ETL mashup language used **during data ingestion**. It transforms, cleans, and shapes tables before they are loaded into memory.
> - **DAX (Data Analysis Expressions)** is an analytical query language used **after data is loaded**. It computes dynamic calculations, aggregations, and business logic on the fly based on user filter context.

---

### Q6: "Why did you convert Postal_Code to Text format instead of Whole Number?"
**Answer**:
> "Postal codes are categorical identifiers, not quantitative values. If left as Whole Number:
> 1. Power BI will attempt to calculate automatic sums or averages on them, which is meaningless.
> 2. Leading zeros in pin codes would be stripped away.
> Setting it to Text preserves the exact code and tells the engine it is a dimension attribute."

---

# SECTION 3: Data Modeling & Star Schema Questions

### Q7: "What data modeling schema did you use in your project, and why?"
**Answer**:
> "I implemented a **Star Schema**, which is the gold standard for Power BI modeling recommended by Microsoft.
> 
> In my model:
> - **Fact Table**: `ecommerce_sales_data` (contains transactions, quantitative measures like Sales, Profit, Quantity, and foreign keys).
> - **Dimension Table**: `Dim_Date` (contains date attributes like Year, Quarter, Month Name, Day of Week).
> - Connected via a **1-to-Many (1:*)** one-way single cross-filter relationship from `Dim_Date[Date]` to `ecommerce_sales_data[Order_Date]`.
> 
> **Why Star Schema?**
> It maximizes query performance in the VertiPaq engine, simplifies DAX logic, and avoids ambiguous circular relationship paths."

---

### Q8: "Why did you build a separate Date Dimension table instead of using Power BI's Auto Date/Time?"
**Answer**:
> "While Auto Date/Time is convenient for quick demos, in enterprise projects it has serious drawbacks:
> 1. It secretly creates hidden date tables behind every date column, bloating file size and degrading memory performance.
> 2. It doesn't allow unified filtering across multiple tables.
> 3. Creating a dedicated `Dim_Date` table gives us complete control over custom fiscal calendars, quarter formats, and chronological sort columns."

---

# SECTION 4: DAX (Data Analysis Expressions) Questions

### Q9: "Why did you use `DIVIDE([Total Profit], [Total Sales], 0)` instead of `[Total Profit] / [Total Sales]`?"
**Answer**:
> "The `/` operator in DAX will return `NaN` or an error if the denominator is zero or blank. 
> The `DIVIDE()` function is optimized internally for division by zero: it takes an optional third argument (in our case `0`) that handles division by zero gracefully, preventing broken visual cards or error messages."

---

### Q10: "What is the difference between a Calculated Column and a DAX Measure?"
**Answer**:
> | Feature | Calculated Column | DAX Measure |
> |---|---|---|
> | **Evaluation Time** | Computed during data refresh | Computed on the fly when visuals render |
> | **Memory Usage** | Stored in RAM (consumes file size) | Consumes virtually zero RAM |
> | **Evaluation Context** | Evaluated under **Row Context** | Evaluated under **Filter Context** |
> | **Best Use** | Slicers, axis labels, matrix rows | Numerical KPIs, totals, ratios, rates |
> 
> *In my project, `Shipping_Days` was created as a column because it's an order attribute, while `Total Sales` and `Profit Margin %` were DAX measures.*"

---

### Q11: "Explain Filter Context and Row Context with an example from your project."
**Answer**:
> - **Row Context**: Evaluates row by row. For example, during data ingestion, calculating `Sales_Amount - Total_Cost` for each individual transaction row.
> - **Filter Context**: The active filters applied to the calculation by visual elements, slicers, and cross-highlighting.
>   *Example*: When the user clicks **'2024'** on the Year slicer and looks at the **Total Sales** card, the filter context restricts `Dim_Date[Year] = 2024`, causing `SUM(ecommerce_sales_data[Sales_Amount])` to calculate only for 2024 orders."

---

### Q12: "How would you calculate Year-over-Year (YoY) Sales Growth in DAX?"
**Answer**:
> "Using Time Intelligence functions:
> ```dax
> Sales PY = 
> CALCULATE(
>     [Total Sales],
>     SAMEPERIODLASTYEAR(Dim_Date[Date])
> )
> 
> YoY Sales Growth % = 
> VAR Variance = [Total Sales] - [Sales PY]
> RETURN
> DIVIDE(Variance, [Sales PY], 0)
> ```
> This requires the dedicated `Dim_Date` table we created to work accurately."

---

# SECTION 5: Visuals, UX & Architecture Questions

### Q13: "How did you ensure your dashboard was easy for non-technical executives to use?"
**Answer**:
> "I followed modern business intelligence UX best practices:
> 1. **Visual Hierarchy**: High-level KPIs at the top (Z-pattern reading order), followed by temporal trends, category breakdowns, and geographic insights.
> 2. **Intuitive Filtering**: Tile-style slicers that let executives filter by Year or Customer Segment with a single click.
> 3. **Page Navigation**: Implemented a built-in Page Navigator bar so users can seamlessly move between Executive Overview and Customer Insights.
> 4. **Visual Polish**: Replaced default flat white backgrounds with an off-white canvas, rounded borders (10px), and subtle drop shadows for a clean 'floating card' UI."

---

### Q14: "How did you handle the Top 10 Customers visual?"
**Answer**:
> "Instead of writing complex DAX ranking tables, I used Power BI's native **Top N filter** on the visual level:
> - Placed `Customer_Name` on the Y-axis and `Total Sales` on the X-axis.
> - In the Filters pane, selected Filter Type: **Top N**, set count to **10**, and dragged `Total Sales` into the 'By Value' field.
> This keeps the visual fast, reactive to slicers, and easy to maintain."

---

### Q15: "What would be your next steps if this dashboard were deployed into production on Power BI Service?"
**Answer**:
> "If deploying to Power BI Service:
> 1. **Automated Scheduled Refresh**: Set up an On-premises / Cloud Gateway to refresh data daily or weekly.
> 2. **Row-Level Security (RLS)**: Configure role-based security so regional managers can only view data for their assigned region (e.g., North Manager only sees North region data).
> 3. **Mobile Layout**: Design a mobile-optimized portrait layout in Power BI Mobile view for executives viewing reports on smartphones.
> 4. **Alerts & Subscriptions**: Configure data-driven alerts when profit margins drop below 15%."
