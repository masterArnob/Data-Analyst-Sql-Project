# 📊 Data Warehouse Analytics Project

A modern SQL-based data analytics project for comprehensive sales data analysis and business intelligence reporting.

---

## 🚀 Quick Start

### Prerequisites
- **MS SQL Server**: [Download Here](https://www.microsoft.com/en-us/sql-server/sql-server-downloads)
- **SSMS (SQL Server Management Studio)**: [Download Here](https://learn.microsoft.com/en-us/ssms/install/install?view=sql-server-ver16)

### Database Setup
1. Create new database: `CREATE DATABASE DataWarehouseAnalytics;`
2. Import CSV files:
   - Right-click database → Tasks → Import Flat File
   - Follow the import wizard for each table

---

## 🗃️ Project Architecture

### Database Schema
| Table | Description | Key Fields |
|-------|-------------|------------|
| `customers` | Customer master data | customer_key, demographics |
| `products` | Product catalog | product_key, price, category |
| `sales` | Transaction records | order_date, sales_amount, quantity |



---

## Changes over time analysis - trends

### 1. Yearly Sales Performance Dashboard
```sql
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
```sql
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
```sql
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
 
