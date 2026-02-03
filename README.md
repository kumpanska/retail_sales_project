# Retail Sales Analysis SQL Project

### Project Overview

**Project Title**: Retail Sales Analysis  
**Tool Used**: PostgreSQL  
**Database**: `sql_project`

This project involves a comprehensive analysis of retail sales data using SQL. The goal is to explore consumer behavior, perform data cleaning, and solve key business problems. By analyzing transaction records, we derive insights into sales performance, customer demographics, and time-based purchasing trends.

## Objectives

1. **Database Infrastructure**: Establish and populate a structured PostgreSQL database to manage retail transaction records.
2. **Data Cleaning**: Perform rigorous data cleaning by identifying and handling missing or null entries to ensure dataset integrity.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Insight Generation**: Develop complex SQL queries to solve real-world business problems, providing actionable insights into sales performance and customer behavior.

## Project Structure

- **Database Creation**: The project starts by creating a database named `sql_project`.
- **Table Creation**: A table named `retail_sales` is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

1. Database & Table Setup
 ```sql
-- Create database sql_project
CREATE DATABASE sql_project
-- Create retail_sales table
DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales(
	transactions_id INT PRIMARY KEY,
	sale_date DATE,
	sale_time TIME,
	customer_id INT,
	gender VARCHAR(15),
	age INT,
	category VARCHAR(15),
	quantity INT,
	price_per_unit FLOAT,
	cogs FLOAT,
	total_sale FLOAT
);
```
## Workflow

1. Data Cleaning
To ensure data integrity, records with missing values (NULL) across key columns were identified and removed. This step guarantees that subsequent analysis is based on complete and accurate information.
```sql
--Cleaning null data if there's any
DELETE FROM retail_sales
WHERE transactions_id IS NULL 
OR sale_date IS NULL
OR sale_time IS NULL
OR gender IS NULL
OR category IS NULL
OR quantity IS NULL
OR cogs IS NULL
OR total_sale IS NULL;
```
2. Data Exploration
Initial queries were conducted to understand the dataset's scale:

Volume: Total number of recorded sales.
```sql
-- How many sales we have?
SELECT COUNT(*) FROM retail_sales;
```
Customer Base: Count of unique customers.
```sql
-- How many unique customers we have?
SELECT COUNT(DISTINCT customer_id) 
AS total_sale 
FROM retail_sales;
```
Inventory: List of unique product categories available.
```sql
-- Identify all unique product categories in the dataset
SELECT DISTINCT category
FROM retail_sales;
```
3. Business Key Problems & Solutions
The project answers specific business questions through advanced SQL queries, including:

- Specific Date Analysis: Retrieving all sales for a particular day.
  Solution:
```sql
-- Q.1 Write a SQL query to retrieve all columns for sales made on '2022-11-05'
SELECT * FROM retail_sales
WHERE sale_date = '2022-11-05';
```
- Category & Quantity Filtering: Identifying high-volume sales in specific categories (e.g., 'Clothing') during certain months.
```sql
-- Q.2 Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022
SELECT * FROM retail_sales
WHERE category = 'Clothing'
AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
AND quantity >= 4;
```
- Revenue Summary: Calculating net sales and total orders per category.
```sql
-- Q.3 Write a SQL query to calculate the total sales (total_sale) for each category
SELECT category, 
SUM(total_sale) AS net_sale,
COUNT (*) AS total_orders
FROM retail_sales
GROUP BY 1;
```
- Customer Insights: Finding the average age of customers in high-interest categories like 'Beauty'.
```sql
-- Q.4 Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category
SELECT ROUND(AVG(age),2) AS avg_age 
FROM retail_sales
WHERE category = 'Beauty';
```
- High-Value Transactions: Filtering sales where the total value exceeds a specific threshold (e.g., 1000).
```sql
-- Q.5 Write a SQL query to find all transactions where the total_sale is greater than 1000
SELECT * FROM retail_sales
WHERE total_sale > 1000;
```
- Segmented Analysis: Breakdown of transaction counts by gender and category.
```sql
-- Q.6 Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category
SELECT category, gender,
COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY 1;
```
- Time-Series Analysis: Determining the best-selling month for each year based on average sales.
```sql
-- Q.7 Write a SQL query to calculate the average sale for each month. Find out best selling month in each year
SELECT year, month, avg_sale
FROM
(SELECT 
	EXTRACT(YEAR FROM sale_date) AS year, 
	EXTRACT(MONTH FROM sale_date) AS month,
	AVG(total_sale) as avg_sale,
	RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) AS rank
FROM retail_sales
GROUP BY 1, 2) AS t1
WHERE rank = 1;
```
- Loyalty Programs: Identifying the top 5 customers by total spend.
```sql
-- Q.8 Write a SQL query to find the top 5 customers based on the highest total sales
SELECT customer_id, SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```
- Unique Reach: Counting unique customers per product category.
```sql
-- Q.9 Write a SQL query to find the number of unique customers who purchased items from each category
SELECT category,
COUNT(DISTINCT customer_id) AS cnt_unique_cs
FROM retail_sales
GROUP BY 1
```
- Shift Analysis: Categorizing sales into "Morning", "Afternoon", and "Evening" shifts to identify peak operational hours.
```sql
-- Q.10 Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)
WITH hourly_sale
AS
(
SELECT *,
CASE 
	WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
	WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
	ELSE 'Evening'
END AS shift
FROM retail_sales
)
SELECT shift, COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY 1;
```

## Findings

- **Customer Demographics**: The project reveals a diverse customer base spanning various age groups. Sales are distributed across multiple categories, with Clothing and Beauty showing significant engagement.
- **High-Value Transactions**: Several transactions exceeded a total sale amount of 1000, highlighting a segment of premium purchases and high-ticket items.
- **Sales Trends**: Through time-series analysis, distinct variations in monthly sales were identified, allowing for the pinpointing of peak shopping seasons and low-activity periods.
- **Customer Insights**: The analysis successfully identified top-spending customers and the most popular product categories, providing a clear picture of what drives revenue.

## Reports

- **Sales Summary**: A comprehensive report detailing total revenue, total orders, and performance metrics across all product categories.
- **Trend Analysis**: A longitudinal study of sales patterns, categorized by year, month, and even specific time-of-day shifts (Morning, Afternoon, Evening).
- **Customer Insights**: A specialized report profiling top customers by expenditure and analyzing the unique customer reach of each category.

## Conclusion

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data. The findings from this project can help drive business decisions by understanding sales patterns, customer behavior, and product performance.
