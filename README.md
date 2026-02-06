
Query for:
•	Total revenue
•	Average order value
•	Total pizza sold 
•	Total orders 
•	Average pizza per order 

SELECT 
    SUM(total_price) AS Total_Revenue,
    SUM(total_price) / COUNT(DISTINCT order_id) AS Average_Order_Value,
    SUM(quantity) AS Total_Pizza_Sold,
    COUNT(DISTINCT order_id) AS Total_Orders,
    CAST(CAST(SUM(quantity) AS DECIMAL(10,2)) / 
         CAST(COUNT(DISTINCT order_id) AS DECIMAL(10,2)) AS DECIMAL(10)) AS Avg_Pizzas_Per_Order
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Pizza_Sales;

 

Query for daily sales trends 

SELECT 
    DATENAME(DW, order_date) AS Day_of_Week, 
    COUNT(DISTINCT order_id) AS Total_Orders
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
GROUP BY DATENAME(DW, order_date)
ORDER BY Total_Orders DESC;


 

Query for hourly trends for total orders

SELECT 
    DATEPART(HOUR, order_time) AS Order_Hour, 
    COUNT(DISTINCT order_id) AS Total_Orders
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
GROUP BY DATEPART(HOUR, order_time)
ORDER BY Order_Hour;
 

PERCENTAGE OF SALE BY CATEGORY

Query for percentage of sales by pizza category 

SELECT 
    pizza_category, 
    CAST(SUM(total_price) AS DECIMAL(10,2)) AS Total_Revenue,
    CAST(SUM(total_price) * 100 / (SELECT SUM(total_price) FROM (SELECT DISTINCT * FROM pizza_sales) AS X) AS DECIMAL(10,2)) AS PCT
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Pizza_Sales
GROUP BY pizza_category
ORDER BY PCT DESC;

 

Query for percentage of sales by pizza category (monthly)

SELECT 
    DATENAME(MONTH, order_date) AS Month_Name,
    pizza_category,
    CAST(SUM(total_price) AS DECIMAL(10,2)) AS Monthly_Category_Revenue,
    CAST(SUM(total_price) * 100 / 
         SUM(SUM(total_price)) OVER(PARTITION BY DATENAME(MONTH, order_date)) 
    AS DECIMAL(10,2)) AS PCT_of_Monthly_Sales
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
GROUP BY DATENAME(MONTH, order_date), pizza_category
ORDER BY MONTH(MIN(order_date)), PCT_of_Monthly_Sales DESC;

 

Query for percentage of sales by pizza category (Quarterly)

SELECT 
    DATEPART(QUARTER, order_date) AS Quarter_Number,
    pizza_category,
    CAST(SUM(total_price) AS DECIMAL(10,2)) AS Quarterly_Category_Revenue,
    CAST(SUM(total_price) * 100 / 
         SUM(SUM(total_price)) OVER(PARTITION BY DATEPART(QUARTER, order_date)) 
    AS DECIMAL(10,2)) AS PCT_of_Quarterly_Sales
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
GROUP BY DATEPART(QUARTER, order_date), pizza_category
ORDER BY Quarter_Number, PCT_of_Quarterly_Sales DESC;

 
Query for percentage of sales by pizza category (Weekly)

SELECT 
    DATEPART(WEEK, order_date) AS Week_Number,
    pizza_category,
    CAST(SUM(total_price) AS DECIMAL(10,2)) AS Weekly_Category_Revenue,
    CAST(SUM(total_price) * 100 / 
         SUM(SUM(total_price)) OVER(PARTITION BY DATEPART(WEEK, order_date)) 
    AS DECIMAL(10,2)) AS PCT_of_Weekly_Sales
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
GROUP BY DATEPART(WEEK, order_date), pizza_category
ORDER BY Week_Number, PCT_of_Weekly_Sales DESC;
 

Query for percentage of sales by pizza size

SELECT 
    pizza_size, 
    CAST(SUM(total_price) AS DECIMAL(10,2)) AS Total_Revenue,
    CAST(SUM(total_price) * 100 / (SELECT SUM(total_price) FROM (SELECT DISTINCT * FROM pizza_sales) AS X) AS DECIMAL(10,2)) AS PCT_of_Total_Sales
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
GROUP BY pizza_size
ORDER BY PCT_of_Total_Sales DESC;
 

Query for total pizzas sold by pizza category

SELECT 
    pizza_category, 
    SUM(quantity) AS Total_Pizzas_Sold
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
GROUP BY pizza_category
ORDER BY Total_Pizzas_Sold DESC;
 

Query for top 5 best sellers by total pizzas sold

SELECT TOP 5 
    pizza_name, 
    SUM(quantity) AS Total_Pizzas_Sold
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
GROUP BY pizza_name
ORDER BY Total_Pizzas_Sold DESC;

 

Query for bottom 5 worst sellers by total pizza sold

SELECT TOP 5 
    pizza_name, 
    SUM(quantity) AS Total_Pizzas_Sold
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
GROUP BY pizza_name
ORDER BY Total_Pizzas_Sold ASC;
 

Note 
To filter for monthly, quarterly and weekly swap:
•	Monthly: WHERE MONTH(order_date) = 1 (for January)
•	Quarterly: WHERE DATEPART(QUARTER, order_date) = 1 (for Q1)
•	Weekly: WHERE DATEPART(WEEK, order_date) = 1 (for Week 1)

Example

Percentage of Sales by Pizza Size
SELECT 
    pizza_size, 
    CAST(SUM(total_price) AS DECIMAL(10,2)) AS Total_Revenue,
    CAST(SUM(total_price) * 100 / (
        SELECT SUM(total_price) 
        FROM (SELECT DISTINCT * FROM pizza_sales) AS X 
        WHERE MONTH(order_date) = 1 -- Change to Quarter or Week here
    ) AS DECIMAL(10,2)) AS PCT
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
WHERE MONTH(order_date) = 1 -- Change to Quarter or Week here
GROUP BY pizza_size
ORDER BY PCT DESC;


Total Pizzas Sold by Pizza Category

SELECT 
    pizza_category, 
    SUM(quantity) AS Total_Pizzas_Sold
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
WHERE MONTH(order_date) = 1 -- Filter: Change to Quarter or Week
GROUP BY pizza_category
ORDER BY Total_Pizzas_Sold DESC;

Top 5 Best Sellers (by Quantity)

SELECT TOP 5 
    pizza_name, 
    SUM(quantity) AS Total_Pizzas_Sold
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
WHERE MONTH(order_date) = 1 -- Filter: Change to Quarter or Week
GROUP BY pizza_name
ORDER BY Total_Pizzas_Sold DESC;

Bottom 5 Worst Sellers (by Quantity)

SELECT TOP 5 
    pizza_name, 
    SUM(quantity) AS Total_Pizzas_Sold
FROM (
    SELECT DISTINCT * FROM pizza_sales
) AS Clean_Data
WHERE MONTH(order_date) = 1 -- Filter: Change to Quarter or Week
GROUP BY pizza_name
ORDER BY Total_Pizzas_Sold ASC;


















