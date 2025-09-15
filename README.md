-- Clean up any existing table to start fresh
DROP TABLE IF EXISTS retail_sales;

-- Create the retail_sales table with the specified columns and data types
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

-- Check the initial number of rows after data import
SELECT COUNT(*) FROM retail_sales;

-- View a sample of the data to verify its structure and content
SELECT * FROM retail_sales LIMIT 10;

-- Identify and remove rows with NULL values in the 'age' column, which were found to be invalid for analysis
DELETE FROM retail_sales WHERE age IS NULL;

-- Verify the final count of valid rows after cleaning
SELECT COUNT(*) AS total_valid_rows FROM retail_sales;

-- Q. How many total sales transactions do we have?
SELECT COUNT(*) AS total_sales FROM retail_sales;

-- Q. How many unique customers are there?
-- Q.1 Retrieve all columns for sales made on a specific date.
SELECT * FROM retail_sales WHERE sale_date = '2022-11-05';

-- Q.2 Find transactions for the 'Clothing' category with a minimum quantity in a specific month and year.
SELECT * FROM retail_sales
WHERE category = 'Clothing'
  AND quantity >= 4
  AND EXTRACT(MONTH FROM sale_date) = 11
  AND EXTRACT(YEAR FROM sale_date) = 2022;

-- Q.3 Calculate total sales and number of orders for each product category.
SELECT category,
       SUM(total_sale) AS total_sale,
       COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;

-- Q.4 Find the average age of customers who purchased from the 'Beauty' category.
SELECT ROUND(AVG(age), 2) AS average_age FROM retail_sales WHERE category = 'Beauty';

-- Q.5 Count the number of transactions where the total sale amount exceeds $1000.
SELECT COUNT(transactions_id) AS number_of_transactions FROM retail_sales WHERE total_sale > 1000;

-- Q.6 Find the total number of transactions made by each gender.
SELECT gender,
       COUNT(transactions_id) AS transactions
FROM retail_sales
GROUP BY gender;

-- Q.8 Find the top 5 customers based on their total spending.
SELECT customer_id,
       SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;

-- Q.9 Count the number of unique customers for each product category.
SELECT category,
       COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;
SELECT COUNT(DISTINCT customer_id) AS unique_customers FROM retail_sales;
-- Q.7 Calculate the average sale per month and sort by month number.
SELECT EXTRACT(MONTH FROM sale_date) AS month_no,
       TO_CHAR(sale_date, 'Mon') AS month_name,
       ROUND(AVG(total_sale)::numeric, 2) AS avg_sales
FROM retail_sales
GROUP BY month_no, month_name
ORDER BY month_no;

-- Q.10 Analyze transaction volume by time of day, categorized into shifts (Morning, Afternoon, Evening).
WITH hourly_sale AS (
    SELECT *,
           CASE
               WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
               WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
               ELSE 'Evening'
           END AS shift
    FROM retail_sales
)
SELECT shift,
       COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;
