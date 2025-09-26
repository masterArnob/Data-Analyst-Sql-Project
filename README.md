# Data-Analyst-Sql-Project

ms sql dwonload: https://www.microsoft.com/en-us/sql-server/sql-server-downloads
ssms download: https://learn.microsoft.com/en-us/ssms/install/install?view=sql-server-ver16 


### import csv in ms sql
```
create new database
then go to task
then import flat files
```

# Data Warehouse Analytics Project

A comprehensive data analytics project for analyzing sales data from a data warehouse environment using SQL.

## 📊 Project Overview

This repository contains SQL scripts for analyzing sales data trends and performance metrics over time.

## 🗂️ Database Structure

**Database Name:** `DataWarehouseAnalytics`

**Main Tables:**
- `customers` - Customer information
- `products` - Product catalog
- `sales` - Sales transactions

## 📈 Analysis Features

### Time-Based Trend Analysis
- **Yearly Trends**: Total sales, quantities, and customer counts by year
- **Monthly Trends**: Monthly performance metrics and seasonal patterns
- **Year-Month Analysis**: Detailed monthly trends within each year

### Key Metrics
- Total Sales Amount
- Quantity Sold
- Total Price
- Unique Customer Counts

## 🛠️ SQL Queries

### 1. Yearly Sales Analysis
```
SELECT
    YEAR(order_date) AS order_year,
    SUM(sales_amount) AS total_sales,
    SUM(quantity) AS total_qty, 
    SUM(price) AS total_price,
    COUNT(DISTINCT customer_key) AS total_customers
FROM sales
WHERE order_date IS NOT NULL
GROUP BY YEAR(order_date)
ORDER BY YEAR(order_date) ASC;
```


### 2. Monthly Sales Analysis
```
SELECT
    MONTH(order_date) AS order_month,
    SUM(sales_amount) AS total_sales,
    SUM(quantity) AS total_qty, 
    SUM(price) AS total_price,
    COUNT(DISTINCT customer_key) AS total_customers
FROM sales
WHERE order_date IS NOT NULL
GROUP BY MONTH(order_date)
ORDER BY MONTH(order_date) ASC;
```

### 3. Combined Year-Month Analysis
```
SELECT
    YEAR(order_date) AS order_year,
    MONTH(order_date) AS order_month,
    SUM(sales_amount) AS total_sales,
    SUM(quantity) AS total_qty, 
    SUM(price) AS total_price,
    COUNT(DISTINCT customer_key) AS total_customers
FROM sales
WHERE order_date IS NOT NULL
GROUP BY YEAR(order_date), MONTH(order_date)
ORDER BY YEAR(order_date), MONTH(order_date) ASC;
```
 
