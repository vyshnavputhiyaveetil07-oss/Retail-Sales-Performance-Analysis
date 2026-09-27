# Retail Sales Performance Analysis

**Category:** Business Analytics | **Status:** COMPLETED

**Subtitle:** Multi-Region Revenue, Profit Margin & Product Performance Analytics

**Technologies:** SQL, Power BI, Excel

## Overview
Comprehensive commercial sales analysis evaluating regional profitability, product category growth, order volumes, and customer cohort trends.

## 01 — Business Problem — Profit Dilution & Regional Margin Disparities
A multi-region UK retail enterprise experienced top-line revenue growth of 8% YoY, yet operating net profit declined by 3.2%. Executive leadership needed to pinpoint which regional branches, product sub-categories, and discount practices were eroding bottom-line margins.

• Why was revenue growth failing to translate into net profit margin?
• Which product categories and customer segments generated negative net contribution margins?
• How can discounting guidelines be restructured without hurting transaction volumes?

## 02 — Data Architecture — Relational Sales Database & Order Grain
Extracted data from a relational database schema comprising Orders, Customers, Products, Returns, and Regional Geography tables.

• 54,200 transaction rows covering 2023-2025 sales orders.
• Fields: Order ID, Order Date, Customer ID, Region, Segment, Sales, Quantity, Discount, Profit, Return Status.
• Data validation & automated null/duplicate handling via SQL CTEs.

## 03 — Analytical Approach & SQL Pipeline — Data Cleaning & Metric Modeling
Executed structured SQL queries in PostgreSQL to clean raw timestamps, compute line-item net revenue after returns, and calculate regional margin metrics using Window Functions.

• Calculated Net Profit Margin % = (SUM(Profit) / SUM(Sales)) * 100 per region.
• Grouped sales into discount bands (<10%, 10-20%, >20%) to measure profit sensitivity.
• Created DAX measure model in Power BI for dynamic time-intelligence reporting (YTD, YoY Growth, Rolling 3-Month Average).

**SQL Query Example:**

```sql
-- SQL Query isolating unprofitable high-discount sales orders
WITH RegionalSalesSummary AS (
  SELECT
    r.region_name,
    p.category_name,
    COUNT(DISTINCT o.order_id) AS total_orders,
    ROUND(SUM(o.sales_amount)::numeric, 2) AS gross_revenue,
    ROUND(SUM(o.profit_amount)::numeric, 2) AS net_profit,
    ROUND((SUM(o.profit_amount) / NULLIF(SUM(o.sales_amount), 0) * 100)::numeric, 2) AS margin_percentage,
    AVG(o.discount_rate) AS avg_discount
  FROM sales_orders o
  JOIN products p ON o.product_id = p.product_id
  JOIN regions r ON o.region_id = r.region_id
  WHERE o.order_status = 'Completed'
  GROUP BY r.region_name, p.category_name
)
SELECT *
FROM RegionalSalesSummary
WHERE margin_percentage < 5.0 OR avg_discount > 0.15
ORDER BY gross_revenue DESC;
```

## 04 — Power BI Dashboard Design — Executive KPI View & Dynamic Drill-Through
Built an interactive 3-page Power BI Executive Dashboard featuring summary KPI cards, regional profit heatmaps, discount erosion scatter plots, and order-level audit tables.

• KPI Header: Total Revenue (£4.2M), Net Profit (£512K), Average Order Value (£148), Return Rate (4.1%).
• Interactive Slicers: Date Range, Region (London, South East, Midlands, North), Product Category.
• Visual Hierarchy: High-level executive cards → Regional map drill-down → SKU profitability decomposition tree.

## 05 — Key Business Insights — Root Cause of Margin Dilution Identified
The analysis revealed that excessive discounting on Office Technology items in the Midlands region was directly destroying profit margins, with discounts over 20% generating negative net contribution.

• Discounts exceeding 18% accounted for 64% of total profit losses across all categories.
• The 'Furniture' category suffered a 9.4% return rate due to shipping damage in 2 regional hubs.
• Top 15% of corporate accounts generated 58% of net profit, highlighting strategic account importance.

## 06 — Actionable Recommendations — Commercial Optimization Strategy
Provided executive leadership with 3 immediate operational recommendations to recover £140,000+ in annual lost margin.

• Cap promotional discount thresholds at 15% for technology products without VP finance sign-off.
• Renegotiate courier packaging agreements for Furniture items to reduce order returns below 4%.
• Reallocate regional marketing budget from underperforming consumer ads to high-margin B2B corporate acquisition.
