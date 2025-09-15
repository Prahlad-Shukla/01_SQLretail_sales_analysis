-- Clean up any existing table
DROP TABLE IF EXISTS retail_sales;

-- Create the retail_sales table
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

-- Check the number of rows after data import
SELECT COUNT(*) FROM retail_sales;

-- View a sample of the data
SELECT * FROM retail_sales LIMIT 10;

-- Data Cleaning: Identify and remove rows with missing 'age' values
SELECT * FROM retail_sales WHERE age IS NULL;
-- Note: 10 rows were identified with NULL age.

DELETE FROM retail_sales WHERE age IS NULL;

-- Verify the final count of valid rows
SELECT COUNT(*) AS total_valid_rows FROM retail_sales;
-- Result: 1990 rows are now valid for analysis.

-- 1. How many sales do we have?
SELECT COUNT(*) AS total_sales FROM retail_sales;
-- Result: 1990

-- 2. How many unique customers do we have?
SELECT COUNT(DISTINCT customer_id) AS unique_customers FROM retail_sales;
-- Result: 155

-- 3. Retrieve all columns for sales made on a specific date.
SELECT * FROM retail_sales WHERE sale_date = '2022-11-05';

-- 4. Find transactions for a specific category with a minimum quantity in a given month.
SELECT * FROM retail_sales
WHERE category = 'Clothing'
  AND quantity >= 4
  AND EXTRACT(MONTH FROM sale_date) = 11
  AND EXTRACT(YEAR FROM sale_date) = 2022;

-- 5. Calculate the total sales for each category.
SELECT category,
       SUM(total_sale) AS total_sale,
       COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;

-- 6. Find the average age of customers for a specific category.
SELECT ROUND(AVG(age), 2) AS average_age FROM retail_sales WHERE category = 'Beauty';

-- 7. Find the number of transactions with a total sale greater than 1000.
SELECT COUNT(transactions_id) AS number_of_trxn FROM retail_sales WHERE total_sale > 1000;

-- 8. Find the total number of transactions by gender.
SELECT gender,
       COUNT(transactions_id) AS transactions
FROM retail_sales
GROUP BY gender;

-- 9. Calculate the average sale for each month and find the best-selling month.
SELECT EXTRACT(MONTH FROM sale_date) AS month_no,
       TO_CHAR(sale_date, 'Mon') AS month_name,
       ROUND(AVG(total_sale)::numeric, 2) AS avg_sales
FROM retail_sales
GROUP BY month_no, month_name
ORDER BY month_no;

-- 10. Find the top 5 customers based on highest total sales.
SELECT customer_id,
       SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;

-- 11. Count the number of unique customers per category.
SELECT category,
       COUNT(DISTINCT customer_id) AS cnt_unique_cs
FROM retail_sales
GROUP BY category;

-- 12. Determine sales by time of day (shift analysis).
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
