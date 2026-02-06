# Pizza Hub Sales Analysis: Data-Driven Growth Strategy

## Project Overview
In this project, I performed a comprehensive analysis of the transactional data for a pizza restaurant. My goal was to move beyond simple record-keeping and transform raw data into a strategic asset. By applying data cleaning techniques in SQL and advanced visualization in Excel, I identified key performance indicators that drive business growth and operational efficiency.

## My Problem Statement
In the competitive quick-service restaurant industry, I observed that Pizza Hub was operating in a data-rich but insight-poor environment. While thousands of transactions were recorded daily, I found that management lacked a clear view of performance trends. 

### Core Issues I Identified:
* **Operational Blind Spots:** I noticed an inability to pinpoint peak staffing requirements, which leads to service delays during rushes.
* **Menu Inefficiency:** I saw a lack of clarity on which items were hero products versus inventory drains.
* **Seasonal Volatility:** I tracked fluctuating monthly revenues with no data-backed strategy to bridge the gaps during slow periods.

## My Objectives
I built a SQL-to-Excel analytical pipeline to identify high-value growth opportunities through:
1. Establishing core financial KPIs.
2. Identifying the annual MVP (Best Seller).
3. Pinpointing the revenue floor (Lowest Sales Month).
4. Developing a menu pruning strategy based on the Bottom 5 performers.

## Tools Used
* **SQL:** Data Extraction, Cleaning, and Metric Calculation.
* **Excel:** Pivot Tables, Timelines, and Slicers for interactive reporting.

---

## My SQL Technical Script
I have grouped my analytical queries here to show the complete logic I used to process the data and generate my insights.

```sql
-- 1. Establishing High-Level KPIs
-- Total Revenue
SELECT SUM(total_price) AS Total_Revenue FROM pizza_sales;

-- Average Order Value
SELECT SUM(total_price) / COUNT(DISTINCT order_id) AS Avg_Order_Value FROM pizza_sales;

-- Total Pizzas Sold
SELECT SUM(quantity) AS Total_pizza_sold FROM pizza_sales;

-- Average Pizzas Per Order
SELECT CAST(CAST(SUM(quantity) AS DECIMAL(10,2)) / 
CAST(COUNT(DISTINCT order_id) AS DECIMAL(10,2)) AS DECIMAL(10,2))
AS Avg_Pizzas_per_order FROM pizza_sales;

-- 2. Trend and Seasonal Analysis
-- Daily Trend for Total Orders
SELECT DATENAME(DW, order_date) AS order_day, COUNT(DISTINCT order_id) AS total_orders 
FROM pizza_sales
GROUP BY DATENAME(DW, order_date);

-- Monthly Trend for Total Orders
SELECT DATENAME(MONTH, order_date) AS Month_Name, COUNT(DISTINCT order_id) AS Total_Orders
FROM pizza_sales
GROUP BY DATENAME(MONTH, order_date);

-- 3. Product and Category Distribution
-- Percentage of Sales by Pizza Category
SELECT pizza_category, CAST(SUM(total_price) AS DECIMAL(10,2)) as total_revenue,
CAST(SUM(total_price) * 100 / (SELECT SUM(total_price) from pizza_sales) AS DECIMAL(10,2)) AS PCT
FROM pizza_sales
GROUP BY pizza_category;

-- Percentage of Sales by Pizza Size
SELECT pizza_size, CAST(SUM(total_price) AS DECIMAL(10,2)) as total_revenue,
CAST(SUM(total_price) * 100 / (SELECT SUM(total_price) from pizza_sales) AS DECIMAL(10,2)) AS PCT
FROM pizza_sales
GROUP BY pizza_size
ORDER BY pizza_size;

-- 4. Performance Rankings (Top and Bottom Sellers)
-- Top 5 Best Sellers by Revenue
SELECT TOP 5 pizza_name, SUM(total_price) AS Total_Revenue
FROM pizza_sales
GROUP BY pizza_name
ORDER BY Total_Revenue DESC;

-- Bottom 5 Worst Sellers by Revenue
SELECT TOP 5 pizza_name, SUM(total_price) AS Total_Revenue
FROM pizza_sales
GROUP BY pizza_name
ORDER BY Total_Revenue ASC;
