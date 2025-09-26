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
3. `Dimensions are descriptive attributes (who, what, where), facts are measurable metrics (numbers, amounts).`

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
![1](/assets/1.png)

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
![2](/assets/2.png)

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
![3](/assets/3.png)












## 📊 Performance Analysis

### Product Performance Analytics
**Formula**: `Current[Measure] - Target[Measure]`

#### 1. Yearly Product Performance vs Average Sales
Analyze how each product performs compared to its historical average sales:

```sql
WITH yearly_product_sales AS (
SELECT
    YEAR(sales.order_date) AS order_year,
    products.product_name,
    SUM(sales.sales_amount) AS current_sales
FROM
    sales
JOIN 
    products
ON 
    sales.product_key = products.product_key
WHERE 
    sales.order_date IS NOT NULL
GROUP BY
    YEAR(sales.order_date),
    products.product_name
)
SELECT
    order_year,
    product_name,
    current_sales,
    AVG(current_sales) OVER (PARTITION BY product_name) AS avarage_sales,
    current_sales - AVG(current_sales) OVER (PARTITION BY product_name) AS avarage_difference,

    CASE WHEN current_sales - AVG(current_sales) OVER (PARTITION BY product_name) > 0 THEN 'Above Avarage'
         WHEN current_sales - AVG(current_sales) OVER (PARTITION BY product_name) < 0 THEN 'Below Avarage'
         ELSE 'Avarage'
    END avarage_change

FROM 
    yearly_product_sales
ORDER BY
    order_year DESC;
```

![3](/assets/4.png)







## 📊 Proportional Analysis

### Category Performance Analytics
**Formula**: `([Measure]/Total[Measure])*100 By [Dimension]`
#### 1. Which categories contribute the most to overall sales


```sql

WITH category_sales AS(
SELECT 
	products.category,
	SUM(sales.sales_amount) AS total_sale_amount
FROM 
	sales
LEFT JOIN 
	products
ON
	sales.product_key = products.product_key
WHERE 
	sales.order_date IS NOT NULL
GROUP BY 
	products.category
)
SELECT 
	category,
	total_sale_amount,
	SUM(total_sale_amount) OVER() AS overall_sales,
	CONCAT(ROUND((CAST (total_sale_amount AS FLOAT) / SUM(total_sale_amount) OVER())*100, 2), '%') AS percentage_of_total
FROM 
	category_sales
ORDER BY
	percentage_of_total DESC;
```

![3](/assets/5.png)







## 📊 Data Segmentation

### Category Performance Analytics
**Formula**: `[Measure]/[Measure]`
**Example**:  `total_products/sales_range`
**Example**: `total_customers/age`
#### 1. Count how many products fall into each segments


```sql


WITH product_segments AS(
SELECT 
	product_key,
	product_name,
	category, 
	cost,
	CASE WHEN cost < 100 THEN 'Below 100'
	     WHEN cost BETWEEN 100 AND 500 THEN '100-500'
		 WHEN cost BETWEEN 500 AND 1000 THEN '500-1000'
		 ELSE 'Above 1000'
	END cost_range
FROM products
)
SELECT
	cost_range,
	COUNT(product_key) AS total_product
FROM
	product_segments
GROUP BY
	cost_range
ORDER BY total_product DESC;


```

![3](/assets/6.png)



#### 2. Find the lifespan of each customers with total spendings


```sql


SELECT 
	customers.customer_key,
	CONCAT(customers.first_name, ' ', customers.last_name) AS customer_name,
	customers.gender,
	SUM(sales.sales_amount) AS tota_spending,
	MIN(sales.order_date) AS first_order,
	MAX(sales.order_date) AS last_order,
	DATEDIFF(MONTH, MIN(sales.order_date), MAX(sales.order_date)) AS lifespan_months
FROM 
	customers
LEFT JOIN
	sales
ON 
	customers.customer_key = sales.customer_key
GROUP BY 
	customers.customer_key,
	CONCAT(customers.first_name, ' ', customers.last_name),
	customers.gender
	;


```

![3](/assets/7.png)






#### 3. Customer Behavior Segmentation
Group customers into 3 strategic segments based on spending behavior and loyalty:

**Segmentation Criteria:**
- 🏆 **VIP Customers**: At least 12 months of history AND spending more than $5,000
- 👍 **Regular Customers**: At least 12 months of history BUT spending $5,000 or less  
- 🆕 **New Customers**: Customer lifespan less than 12 months


```sql



WITH customer_spendings AS(
SELECT 
	customers.customer_key,
	CONCAT(customers.first_name, ' ', customers.last_name) AS customer_name,
	customers.gender,
	SUM(sales.sales_amount) AS total_spending,
	MIN(sales.order_date) AS first_order,
	MAX(sales.order_date) AS last_order,
	DATEDIFF(MONTH, MIN(sales.order_date), MAX(sales.order_date)) AS lifespan_months
FROM 
	customers
LEFT JOIN
	sales
ON 
	customers.customer_key = sales.customer_key
GROUP BY 
	customers.customer_key,
	CONCAT(customers.first_name, ' ', customers.last_name),
	customers.gender
)
SELECT
	customer_key,
	customer_name,
	gender,
	total_spending,
	lifespan_months,

CASE WHEN lifespan_months >= 12 AND total_spending > 5000 THEN 'VIP'
	 WHEN lifespan_months >= 12 AND total_spending <= 5000 THEN 'Regular'
	 WHEN lifespan_months < 12 THEN 'New'
END customer_segment

FROM
	customer_spendings
WHERE 
	lifespan_months IS NOT NULL
ORDER BY
	lifespan_months DESC
;

```

![3](/assets/8.png)




