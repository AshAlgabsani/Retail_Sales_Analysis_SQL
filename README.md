# Retail_Sales_Analysis_SQL

## Project Overview

**Project: Title:** Retail Sales Analysis                                                                                                                                                                                     
**Client:** Memo is a hypothetical retail store on Shopify who needs the help of data analysts to explore, clean, and analyze their sales data to answer specific business questions and derive insights from the sales data                                                                                                                                                                                                                          
**Analysis tool:** SQL                                                                                                                                                                                                        
**Data Source:** EXCEL                                                                                                                                                                                                        

## Objectives
**1. Database Setup.**                                                                                                                                                                                                
**2. Data Cleaning.**                                                                                                                                                                                                         
**3. Exploratory Data Analysis (EDA).**                                                                                                                                                                                       
**4. Data Analysis.**                                                                                                                                                                                               

## Project Structure

### 1. Database Setup
```sql
CREATE DATABASE memoshopifystore
```

### 2. TABLE CREATION
```sql
CREATE TABLE retail_sales
			(
		transaction_id INT PRIMARY KEY, 
                sale_date DATE,
                sale_time TIME,
                customer_id  INT,
                gender VARCHAR(15),
                age  INT,
                category VARCHAR(25),
                quantity INT,
                price_per_unit FLOAT,
                cogs FLOAT,
                total_sale FLOAT
			)

```

### 3. LOADING THE DATA FROM THE DATA SOURCE [CSV] INTO THE DATABASE
```sql
LOAD DATA LOCAL INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/SQL_Retail_Sales_Analysis_utf.csv'
INTO TABLE retail_sales
FIELDS TERMINATED BY ','
IGNORE 1 ROWS;
```

### 4. DATA EXPLORATION
```sql
SELECT * FROM retail_sales
SELECT COUNT(*) FROM retail_sales 
SELECT COUNT(DISTINCT customer_id) from retail_sales
SELECT DISTINCT category FROM retail_sales
```

### 5. DATA CLEANING
```sql
SELECT * FROM retail_sales
WHERE 
	transaction_id IS NULL
        OR
        sale_date IS NULL
        OR
        sale_time IS NULL
        OR
        gender IS NULL
        OR
        category IS NULL
        OR
        quantity IS NULL
        OR
        cogs IS NULL
        OR
       total_sale IS NULL;
```

## 6. Data Analysis based on client's [questions and concerns]

#### 7. Retrieve all of the sales that were made on '2022-11-05'
```sql
SELECT * FROM retail_sales
WHERE sale_date = '2022/11/05';
```

#### 8. Retrieve all of the transactions of the month of Nov-2022, where the category is 'Clothing' and the quantity sold is more than 10 in
```sql
SELECT * FROM retail_sales
WHERE 
	DATE_FORMAT(sale_date, '%Y-%m') = '2022-11'
	AND
	category = 'Clothing'
 	AND
    	quantity >= 10;
```

#### 9. Calculate the total revenue for each category
```sql
SELECT category,
	SUM(total_sale) as total_revenue_per_category
FROM retail_sales
GROUP BY category
```

#### 10. What is the average age of customers who purchased items from the 'Beauty' category
```sql
SELECT
	ROUND(AVG(age)) as avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

#### 11. Retrieve all of the transactions that has a total_sale greater than 1000 and count what is the number of transactions over 1000
```sql
SELECT *,
		(SELECT COUNT(*)
		FROM retail_sales
		WHERE total_sale > 1000) AS number_of_transactions
	FROM retail_sales
	WHERE total_sale > 1000; 
```

#### 12. What is the total number of transactions for each gender in each category
```sql
SELECT 
	category,
        gender,
        COUNT(*) as total_transactions_per_gender
FROM retail_sales
GROUP BY
	category,
        gender
ORDER BY category;
```

#### 13. Calculate the average sale for each month. Find out best selling month in each year
```sql
SELECT 
  year,
  month,
  avg_sale
	FROM (
 	 SELECT 
		EXTRACT(YEAR FROM sale_date) AS year,
		EXTRACT(MONTH FROM sale_date) AS month,
    		ROUND(AVG(total_sale)) AS avg_sale,
	RANK() OVER (
      	PARTITION BY EXTRACT(YEAR FROM sale_date)
     	 ORDER BY AVG(total_sale) DESC
    ) AS rnk
	FROM retail_sales
  	GROUP BY year, month
) AS ranked_months
	WHERE rnk = 1;
```

#### 14. Who are the top 5 customers based on the highest total sales 
```sql
SELECT 
    customer_id,
    SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

#### 15. Create each shift and number of orders (Example Morning <=12, Afternoon Between 12 & 17, Evening >17)
```sql
SELECT 
	CASE
	WHEN HOUR(sale_time) < 12 THEN 'Morning'
        WHEN HOUR(sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
	END AS Shift,
COUNT(*) AS total_orders
FROM retail_sales
GROUP BY shift;
```
