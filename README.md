# DSA-PROJECT-KMS
My learning journey at the Digital Skill Up Africa at the Incubator Hib


# Project Topic: Kultra Mega Stores Inventory.


## Project Overview
Kultra Mega Stores (KMS), headquartered in Lagos, specialises in offi ce supplies and furniture. Its customer base includes individual consumers, small businesses (retail), and large corporate clients (wholesale) across Lagos, Nigeria. You have been engaged as a Business Intelligence Analyst to support the Abuja division of KMS. The Business Manager has shared an Excel file containing order data from 2009 to 2012 and has requested that you analyze the data and present your key insights and findings.

# Dataset Description
The dataset contains information scraped from Amazon product pages, including:
- Product details: name, category, price, discount, and ratings
- Customer engagement: user reviews, titles, and content
- Each row represents a unique product, with aggregated reviewer data stored as comma-separated values
- Applying my SQL skills from the DSA Data Analysis class to solve both case scenarios as shared in the document.

### Case Scenario I
- Which product category had the highest sales?
- What are the Top 3 and Bottom 3 regions in terms of sales?
- What were the total sales of appliances in Ontario?
- Advise the management of KMS on what to do to increase the revenue from the bottom 10 customers
- KMS incurred the most shipping cost using which shipping method?

### Case Scenario II
- Who are the most valuable customers, and what products or services do they typically purchase?
- Which small business customer had the highest sales?
- Which Corporate Customer placed the most number of orders in 2009 – 2012?
- Which consumer customer was the most profitable one?
- Which customer returned items, and to which segment do they belong?
- If the delivery truck is the most economical but the slowest shipping method, and Express Air is the fastest but the most expensive one, do you think the company appropriately spent shipping costs based on the Order Priority? Explain your answer.

## Step-By-Step Guide

### Using MS Excel and SQL

Data Cleaning, Preparation and Database Creation.
1. Open your dataset in Excel.
2. Check for missing values:
  - Check for blank spaces or missing values and fill or allow nulls.
3. Create Database on SQL:

```
CREATE DATABASE DSAPROJECT_DBO
```

4. Importing and Data call out on SQL: Right click on the newly created database and select task, and the select "Import Flat Files". Select my file and set the necessary data type to the following data like;(Row_ID as (integer) or (float), etc).

--Data call_out;
```
SELECT * FROM kms_sql_case
```
### 📦 Case Scenario I
1. Which product category had the highest sales?
```
select top 1* from (
       select Product_category, Sales
	          from kms_sql_case) AS Sales
order by Sales desc
-- OR

SELECT Category, SUM(Sales) AS TotalSales
FROM kms_sql_case
GROUP BY Category
ORDER BY TotalSales DESC
LIMIT 1;
```
- Assumes a Category and Sales columm 🧠 We’re summing up sales per category and picking the top one

2. Top 3 and Bottom 3 regions in terms of sales.
```
select top 3* from (
       select Region, Sales
	          from kms_sql_case) AS Region
order by Sales desc

select top 3 * from (
       select Region, Sales
	          from kms_sql_case) AS Region
order by Sales asc
--OR

-- Top 3
SELECT TOP 3 Region, SUM(Sales) AS TotalSales
FROM kms_sql_case
GROUP BY Region
ORDER BY TotalSales DESC

-- Bottom 3
SELECT TOP 3 Region, SUM(Sales) AS TotalSales
FROM kms_sql_case
GROUP BY Region
ORDER BY TotalSales ASC
```
- 📊 Use of ORDER BY both ways lets you find top and bottom

3. Total sales of appliances in Ontario.

```
SELECT SUM(Sales) AS ApplianceSales
FROM kms_sql_case
WHERE Category = 'Appliances'
  AND Region = 'Ontario';
```
- Simple filter on both category and region.

4. Advice for bottom 10 customers.
```
select top 10 * from (
       select Customer_Name, Product_Category, Product_Name, Product_Container, Region, Unit_Price, Shipping_Cost, Sales
	          from kms_sql_case) AS Region
order by Sales asc
--OR

SELECT CustomerID, CustomerName, SUM(Sales) AS TotalSales
FROM kms_sql_case
GROUP BY CustomerID, CustomerName
ORDER BY TotalSales ASC
LIMIT 10;
```
- 🚀 After identifying them, look into:
  - Their purchase frequency
  - Types of products bought
  - Possible upselling/cross-selling strategies
  - Shipping method with the highest total shipping cost.

5. Select top 3 (Ship_Mode),
```
      SUM(Shipping_Cost) AS Total_Shipping_Cost
	  from kms_sql_case
group by Ship_Mode
order by Total_Shipping_Cost desc
--OR

SELECT ShipMode, SUM(ShippingCost) AS TotalShippingCost
FROM kms_sql_case
GROUP BY ShipMode
ORDER BY TotalShippingCost DESC
LIMIT 1;
```
- 💸 Useful for analyzing cost efficiency

### 🧑‍💼 Case Scenario II
6. Most valuable customers and what they bought.
```
SELECT CustomerID, CustomerName, SUM(Sales) AS TotalSales,
       STRING_AGG(DISTINCT ProductName, ', ') AS Products
FROM kms_sql_case
GROUP BY CustomerID, CustomerName
ORDER BY TotalSales DESC
LIMIT 5;
```

- 💡 Modify ProductName based on the actual column.
- 📦 This shows both who they are and their preferences.

7. Small Business customer with the highest sales.
```
SELECT CustomerID, CustomerName, SUM(Sales) AS TotalSales
FROM kms_sql_case
WHERE Segment = 'Small Business'
GROUP BY CustomerID, CustomerName
ORDER BY TotalSales DESC
LIMIT 1;
```
8. Corporate Customer with most orders (2009–2012).
```
SELECT CustomerID, CustomerName, COUNT(OrderID) AS OrderCount
FROM kms_sql_case
WHERE Segment = 'Corporate'
  AND OrderDate BETWEEN '2009-01-01' AND '2012-12-31'
GROUP BY CustomerID, CustomerName
ORDER BY OrderCount DESC
LIMIT 1;
```

9. Most profitable (consumer) customer.
```
SELECT CustomerID, CustomerName, SUM(Profit) AS TotalProfit
FROM kms_sql_case
WHERE Segment = 'Consumer'
GROUP BY CustomerID, CustomerName
ORDER BY TotalProfit DESC
LIMIT 1;
```

10. Customers who returned items and their segments.
```
SELECT * FROM kms_sql_case

SELECT * FROM Order_Status

SELECT kms_sql_case.Order_ID,
       kms_sql_case.Customer_Name,
	   kms_sql_case.Customer_Segment,
	   Order_Status.Order_ID,
	   Order_Status.[Status]
from kms_sql_case
join Order_Status
on Order_Status.Order_ID = kms_sql_case.Order_ID
--OR

SELECT DISTINCT CustomerID, CustomerName, Segment
FROM Returns
JOIN kms_sql_case USING (OrderID);
```
- 📥 Assumes you have a Returns table
- 🔗 Use a JOIN on OrderID to pull customer info

11. Analyze shipping mode vs. order priority.
```
SELECT ShipMode, OrderPriority, COUNT(*) AS OrderCount,
       AVG(ShippingCost) AS AvgShippingCost
FROM kms_sql_case
GROUP BY ShipMode, OrderPriority;
```
