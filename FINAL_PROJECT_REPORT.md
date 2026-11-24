# 📊 End-to-End E-Commerce & Retail Sales Performance Dashboard
**Project Report & Documentation**  
*Capstone Project for Anudip Foundation & Data Analytics Portfolio*

---

## 1. Executive Summary
This project delivers an interactive, two-page enterprise business intelligence solution built using **Microsoft Power BI Desktop**. By analyzing 6,000 retail transaction records across India from 2022 to 2024, the dashboard empowers executive leadership and regional sales heads with actionable visibility into revenue trends, product category profit margins, customer segment behavior, and logistics efficiency.

---

## 2. Business Problem Statement
Leadership faced difficulties in:
- Tracking high-level KPIs (Revenue, Profit, Margins, Order Volumes) in real-time.
- Detecting hidden margin losses caused by deep discounting across specific product sub-categories.
- Identifying high-performing geographic territories and customer segments.
- Monitoring shipping lead times and payment method trends.

The objective was to transform disparate transactional records into a centralized, interactive BI dashboard that drives data-driven strategic decisions.

---

## 3. Dataset Architecture
- **Source File**: `ecommerce_sales_data.csv`
- **Volume**: 6,000 rows | 26 columns
- **Time Horizon**: January 2022 – December 2024 (3 Years)
- **Key Dimensions**:
  - **Temporal**: `Order_Date`, `Ship_Date`, `Shipping_Days`
  - **Geographic**: `Country` (India), `Region` (North, South, East, West, Central), `State`, `City`, `Postal_Code`
  - **Product**: `Category` (Technology, Furniture, Office Supplies), `Sub_Category`, `Product_Name`
  - **Customer**: `Customer_ID` (450 unique customers), `Customer_Name`, `Customer_Segment` (Consumer, Corporate, Home Office)
  - **Financial**: `Unit_Cost`, `Unit_Price`, `Quantity`, `Discount_Percent`, `Sales_Amount`, `Total_Cost`, `Profit`, `Profit_Margin_Pct`
  - **Operational**: `Ship_Mode`, `Payment_Method`, `Order_Status`

---

## 4. Power Query ETL & Data Transformation
1. **Type Casting**: Enforced clean typing across numeric columns (Decimal for Financials, Whole Number for Counts/Quantities) and Dates.
2. **Text Sanitation**: Configured `Postal_Code` as Text format to avoid mathematical aggregation.
3. **Feature Engineering**:
   - Added custom column `Shipping_Days` using M-code:
     ```powerquery
     Duration.Days([Ship_Date] - [Order_Date])
     ```
4. **Data Validation**: Verified 100% data validity with zero errors or empty values using Power Query Column Quality profiler.

---

## 5. Data Modeling (Star Schema)
To ensure optimal performance and proper Time Intelligence calculations, a dedicated **Date Dimension (`Dim_Date`)** was created and connected to `ecommerce_sales_data`:
- **Relationship**: `Dim_Date[Date]` 1 ➔ Many (*) `ecommerce_sales_data[Order_Date]`
- **Chronological Sorting**: Configured `Month Name` sorted by `Month Number`, and `Month-Year` sorted by `Month-Year Sort` to prevent alphabetical misalignment in time series.

---

## 6. DAX Measure Dictionary (`_Measures`)

All measures are organized within a dedicated `_Measures` table:

| Measure Name | DAX Formula | Business Purpose |
|---|---|---|
| **Total Sales** | `SUM(ecommerce_sales_data[Sales_Amount])` | Gross revenue generated |
| **Total Profit** | `SUM(ecommerce_sales_data[Profit])` | Net profit after unit costs & discounts |
| **Total Cost** | `SUM(ecommerce_sales_data[Total_Cost])` | Total cost of goods sold (COGS) |
| **Profit Margin %** | `DIVIDE([Total Profit], [total_sales], 0)` | Overall business profitability percentage |
| **Total Orders** | `DISTINCTCOUNT(ecommerce_sales_data[Order_ID])` | Total order transactions |
| **Total Quantity** | `SUM(ecommerce_sales_data[Quantity])` | Total units dispatched |
| **Avg Order Value (AOV)** | `DIVIDE([total_sales], [Total Orders], 0)` | Average basket size per transaction |
| **Total Customers** | `DISTINCTCOUNT(ecommerce_sales_data[Customer_ID])` | Unique purchasing customer base |
| **Avg Shipping Days** | `AVERAGE(ecommerce_sales_data[Shipping_Days])` | Average logistics turnaround time |

---

## 7. Dashboard Architecture

### Page 1: Executive Overview
- **Header & Navigation**: Title with interactive **Page Navigator** buttons.
- **Slicers**: Year tiles (`2022`, `2023`, `2024`) for dynamic period filtering.
- **KPI Summary Cards**: `Total Sales ($205.18M)`, `Total Profit ($36.58M)`, `Profit Margin (17.83%)`, `Total Orders (6K)`.
- **Monthly Revenue Trend (Line Chart)**: 3-year YoY monthly sales trajectory with seasonal festive spike detection.
- **Category Profitability (Clustered Bar Chart)**: Revenue vs. Net Profit comparison across Technology, Furniture, and Office Supplies.
- **Geographic Share (Donut Chart)**: Revenue contribution across North, South, East, West, and Central regions.

### Page 2: Customer & Regional Insights
- **Slicers**: Customer Segment tiles (`Consumer`, `Corporate`, `Home Office`).
- **KPI Cards**: `Total Customers (450)`, `Avg Order Value ($34.20K)`, `Avg Shipping Days (4.4 Days)`.
- **Payment Method Distribution (Horizontal Bar Chart)**: Order count breakdown across UPI, Credit Card, Debit Card, Net Banking, and COD.
- **Geographic Bubble Map**: India territorial map mapping bubble size to state revenue.
- **Top 10 High-Value Customers (Top N Bar Chart)**: Identifying VIP accounts driving maximum top-line revenue.

---

## 8. Key Analytical Findings & Business Recommendations

1. **Festive Season Revenue Spike**:
   - *Finding*: Monthly sales consistently jump by **~35% during Q4 (October to December)** across all three years.
   - *Recommendation*: Secure inventory 6 weeks prior to October and scale logistics capacity to meet Diwali demand.

2. **The Furniture "Discount Trap"**:
   - *Finding*: Technology commands healthy profit margins (~22%), but Furniture sub-categories (Tables, Bookcases) suffered margin erosion when discounts exceeded 25%.
   - *Recommendation*: Establish an algorithmic discount ceiling of 20% on bulky furniture products.

3. **Digital Payment Dominance**:
   - *Finding*: UPI / Digital Wallets account for over **40% of all completed transactions**, followed by Credit Cards (28%).
   - *Recommendation*: Partner with major UPI gateways to offer instant checkout cashbacks and reduce cart abandonment.

4. **Regional Revenue Concentration**:
   - *Finding*: **West (Maharashtra, Gujarat)** and **South (Karnataka, Tamil Nadu, Telangana)** represent over **65% of total sales**.
   - *Recommendation*: Expand regional fulfillment centers in Mumbai and Bengaluru to shorten shipping times to under 3 days.

---

## 9. Resume Ready Project Bullet Points

> **E-Commerce Sales Performance Dashboard | Power BI, DAX, Power Query, Excel**
> - Architected a 2-page interactive Power BI dashboard analyzing 6,000+ retail records across 29 Indian cities, tracking $205M+ in revenue.
> - Implemented a Star Schema data model with a custom Date dimension and 9+ DAX measures (YoY growth, AOV, Profit Margin %).
> - Designed executive KPI cards, India geographic bubble maps, monthly trend lines, and Top N customer analytics with dynamic cross-filtering.
> - Identified Q4 seasonal revenue surges (+35%) and localized profit margin leakages in furniture discounting to guide retail pricing strategy.
