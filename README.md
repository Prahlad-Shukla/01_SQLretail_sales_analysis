# 🛍️ SQL Retail Sales Analysis  

📅 **Date:** 15 September 2025  

This project analyzes **retail sales data** using PostgreSQL. The dataset contains ~2000 rows with sales, customers, and transaction details. The goal is to clean, explore, and analyze sales to answer key business questions.  

---

## 📂 Database & Table Creation
```sql
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
