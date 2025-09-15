# 01_SQLretail_sales_analysis
15 September 2025
```
DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales(
 				transactions_id INT PRIMARY KEY,	
                sale_date DATE,	 
                sale_time TIME,	
                customer_id	INT,
                gender	VARCHAR(15),
                age	INT,
                category VARCHAR(15),	
                quantity	INT,
                price_per_unit FLOAT,	
                cogs	FLOAT,
                total_sale FLOAT
);
```
### DATA EXPLAORATION AND CLEANING
```
SELECT * FROM retail_sales;
--abhi tak sab sahi gaya bhai, itni inconsistency kon deta hai bhai?
--chutiye saari queries baar baar nhi run kari jaati hai, ek query ek baar bas.
--Nhi to ye chod chalta rahega.
SELECT COUNT(*) FROM retail_sales;

SELECT * FROM retail_sales LIMIT 10;

SELECT * FROM retail_sales
WHERE age IS NULL;
--10 rows had 

DELETE FROM retail_sales
WHERE age IS NULL;

SELECT COUNT(*) as total_valid_rows 
FROM retail_sales;
--1990 rows are valid non-null rows.

--HOW MANY SALES DO WE HAVE?
SELECT COUNT(*) as total_sales
FROM retail_sales;
--1990

##--HOW MANY UNIQUE CUSTOMERS DO YOU HAVE
SELECT COUNT(DISTINCT customer_id) as unique_customers
FROM retail_sales;
--155
```
###-- Data Analysis & Business Key Problems & Answers

-- My Analysis & Findings
##-- Q.1 Write a SQL query to retrieve all columns for sales made on '2022-11-05
```
SELECT * FROM retail_sales 
WHERE sale_date='2022-11-05';
```
##-- Q.2 Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 10 in the month of Nov-2022
```
SELECT * FROM retail_sales
WHERE category='Clothing'	
	AND
	quantity>=4
	AND
	TO_CHAR(sale_date,'YYYY-MM')='2022-11';
```
##-- Q.3 Write a SQL query to calculate the total sales (total_sale) for each category.
```
SELECT category,SUM(total_sale) as total_sale,COUNT(*) as total_orders
FROM retail_sales
GROUP BY category;
```

##-- Q.4 Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.
```
SELECT ROUND(AVG(age),2) as average_age
FROM retail_sales
WHERE category='Beauty';
```

##-- Q.5 Write a SQL query to find all transactions where the total_sale is greater than 1000.
```
SELECT  COUNT(transactions_id) as number_of_trxn FROM retail_sales
WHERE total_sale>1000;
```

##-- Q.6 Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.
```
SELECT gender, COUNT(transactions_id) as transactions
FROM retail_sales
GROUP BY gender;
```

##-- Q.7 Write a SQL query to calculate the average sale for each month. Find out best selling month in each year
```
SELECT EXTRACT(MONTH FROM sale_date) as month_name, COUNT(transactions_id) as total_transactions
FROM retail_sales
GROUP BY month_name
ORDER BY month_name ASC;
```

##-- SELECT 
```
--     TO_CHAR(sale_date, 'Month') AS month_name,
--     TRUNC(AVG(total_sale)::numeric, 2) AS avg_sales
-- FROM retail_sales
-- GROUP BY month_name
-- ORDER BY TO_DATE(month_name, 'Month');  

--NOTE: ORDER BY aur GROUP BY me tum alias columns ko use nhi kar sakte in PostgreSQL.

SELECT 
    EXTRACT(MONTH FROM sale_date) AS month_no,
    TO_CHAR(sale_date, 'Mon') AS month_name,
    ROUND(AVG(total_sale)::numeric, 2) AS avg_sales
FROM retail_sales
GROUP BY month_no, month_name
ORDER BY month_no;
```

##-- Q.8 Write a SQL query to find the top 5 customers based on the highest total sales 
```
SELECT customer_id, SUM(total_sale) as total_sales FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```


##-- Q.9 Write a SQL query to find the number of unique customers who purchased items from each category.
```
SELECT 
    category,    
    COUNT( DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales
GROUP BY category;
```
##-- Q.10 Write a SQL query to create each shift and number of orders (Example Morning <=12, Afternoon Between 12 & 17, Evening >17)

```
WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift
```
###--THE END
