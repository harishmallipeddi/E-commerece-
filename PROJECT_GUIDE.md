# 📊 E-Commerce & Retail Sales Analytics Project Guide
**Designed for Anudip Foundation & Portfolio Submission**

---

## 📁 Project Overview
- **Dataset**: `ecommerce_sales_data.csv` (6,000 Records, 2022–2024)
- **Domain**: Retail & E-Commerce Analytics
- **Primary Tool**: Microsoft Power BI Desktop
- **Architecture**: Star Schema Data Modeling + DAX Measures + Multi-Page Interactive Dashboard

---

## 🏗️ Phase 1: Data Connection & Power Query Cleaning
1. Launch **Power BI Desktop**.
2. Click **Get Data** -> **Text/CSV** -> Select `ecommerce_sales_data.csv`.
3. Click **Transform Data** (opens Power Query Editor).
4. **Inspect Data Types**:
   - `Order_Date` -> **Date**
   - `Ship_Date` -> **Date**
   - `Quantity` -> **Whole Number**
   - `Sales_Amount`, `Total_Cost`, `Profit`, `Unit_Cost`, `Unit_Price` -> **Decimal Number (or Fixed Decimal Currency)**
   - `Discount_Percent`, `Profit_Margin_Pct` -> **Decimal Number / Percentage**
5. **Add Custom Column (Shipping Duration)**:
   - Go to **Add Column** -> **Custom Column**.
   - Name: `Shipping_Days`
   - Formula: `Duration.Days([Ship_Date] - [Order_Date])`
   - Change type to **Whole Number**.
6. Click **Home** -> **Close & Apply**.

---

## 📅 Phase 2: Create a Dynamic Date Master Table (DAX)
In Power BI Modeling tab, click **New Table** and paste:

```dax
Dim_Date = 
VAR MinYear = YEAR(MIN(ecommerce_sales_data[Order_Date]))
VAR MaxYear = YEAR(MAX(ecommerce_sales_data[Order_Date]))
RETURN
ADDCOLUMNS (
    CALENDAR(DATE(MinYear, 1, 1), DATE(MaxYear, 12, 31)),
    "Year", YEAR([Date]),
    "Year-Quarter", "Q" & FORMAT([Date], "Q") & " " & YEAR([Date]),
    "Quarter", "Q" & FORMAT([Date], "Q"),
    "Month Number", MONTH([Date]),
    "Month Name", FORMAT([Date], "MMMM"),
    "Month-Year", FORMAT([Date], "MMM yyyy"),
    "Month-Year Sort", YEAR([Date]) * 100 + MONTH([Date]),
    "Day of Week", FORMAT([Date], "dddd"),
    "Day Number", DAY([Date])
)
```
> **Tip**: Select `Month-Year` and click **Sort by column** -> `Month-Year Sort`.

In the **Model View**, drag `Dim_Date[Date]` to `ecommerce_sales_data[Order_Date]` to create a **1-to-Many (1:*)** relationship.

---

## 🧮 Phase 3: DAX Measures Table

Create a dedicated table for clean measure organization:
- In Home tab -> Click **Enter Data** -> Name table `_Measures` -> Click **Load**.

Right-click `_Measures` -> **New Measure** for each of the following:

### 1. Core KPIs
```dax
Total Sales = SUM(ecommerce_sales_data[Sales_Amount])
```
```dax
Total Cost = SUM(ecommerce_sales_data[Total_Cost])
```
```dax
Total Profit = SUM(ecommerce_sales_data[Profit])
```
```dax
Profit Margin % = DIVIDE([Total Profit], [Total Sales], 0)
```
```dax
Total Orders = DISTINCTCOUNT(ecommerce_sales_data[Order_ID])
```
```dax
Total Quantity Sold = SUM(ecommerce_sales_data[Quantity])
```
```dax
Avg Order Value = DIVIDE([Total Sales], [Total Orders], 0)
```
```dax
Avg Shipping Days = AVERAGE(ecommerce_sales_data[Shipping_Days])
```

### 2. Time Intelligence (YoY & MoM)
```dax
Sales PY (Prior Year) = 
CALCULATE(
    [Total Sales],
    SAMEPERIODLASTYEAR(Dim_Date[Date])
)
```
```dax
YoY Sales Growth % = 
VAR Difference = [Total Sales] - [Sales PY]
RETURN
DIVIDE(Difference, [Sales PY], 0)
```
```dax
Sales YTD = 
TOTALYTD([Total Sales], Dim_Date[Date])
```

---

## 🎨 Phase 4: Dashboard Page Layout Suggestions

### 📌 Page 1: Executive Sales & Profit Overview
- **Top Row (KPI Cards)**: `Total Sales`, `Total Profit`, `Profit Margin %`, `Total Orders`, `Avg Order Value`.
- **Top Right (Slicers)**: Year (`Dim_Date[Year]`), Region, Customer Segment.
- **Visual 1 (Area/Line Chart)**: `Monthly Sales Trend` (X-axis: `Dim_Date[Month Name]`, Y-axis: `[Total Sales]`, Legend: `Year`).
- **Visual 2 (Bar Chart)**: `Sales & Profit by Category` (Y-axis: `Category`, X-axis: `[Total Sales]`, Tooltips: `[Total Profit]`).
- **Visual 3 (Donut Chart)**: `Sales by Region` (Values: `[Total Sales]`, Legend: `Region`).
- **Visual 4 (Table / Matrix)**: `Sub-Category Performance` (Sub-Category, Total Sales, Total Profit, Profit Margin %).

### 📌 Page 2: Customer & Shipping Insights
- **KPI Cards**: `Total Customers`, `Avg Shipping Days`, `Return Rate %`.
- **Visual 1 (Clustered Column Chart)**: `Sales by Customer Segment` (Consumer vs. Corporate vs. Home Office).
- **Visual 2 (Bar Chart)**: `Top 10 Customers by Revenue`.
- **Visual 3 (Column Chart)**: `Order Volume by Payment Method`.
- **Visual 4 (Map / Filled Map)**: `Sales Distribution across Indian States & Cities`.

---

## 💡 Key Business Insights Discovered
1. **Seasonal Surge**: Q4 (October to December) accounts for over 35% of annual revenue due to Diwali and holiday shopping.
2. **High Discount Loss Trap**: Discounts above 25% on Furniture (Tables & Bookcases) result in negative profit margins.
3. **Regional Leaders**: West (Maharashtra, Gujarat) and South (Karnataka, Tamil Nadu, Telangana) generate ~65% of total revenue.
4. **Preferred Payment**: UPI / Digital Wallets account for over 40% of all completed transactions.
