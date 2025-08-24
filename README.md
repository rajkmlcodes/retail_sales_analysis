##🛍️ Retail Sales Data Analysis using PostgreSQL

This project focuses on Retail Sales Data Analysis using PostgreSQL. It covers data cleaning, exploration, and analysis to extract valuable business insights.



/* checking if a table containing same name is available in our existing database or not, if not 
then the below command will create a table with provided name */

```
DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales
            (
                transaction_id INT PRIMARY KEY,	
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
-- checking that our is created or not
```
SELECT * FROM retail_sales;
```
-- imported data from a excel/csv file using import/export option in postgresql
-- data imported successfully, and for cross checking we are running the below command
```
SELECT * FROM retail_sales;
```
-- counting the total no. of rows in our dataset, and the result showing 2000 rows 
```
SELECT COUNT(*) FROM retail_sales;
```

-- from here, the data cleaning part is started and checking if we have any null value (and in output i got 3 rows/id's with null values)
```
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

-- deleting the null values that i got after running the above code
```
DELETE FROM retail_sales
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

-- after deleting the null values, here we are cross checking the total no. of rows in our dataset, and the result showing 1997 rows 
```
SELECT COUNT(*) FROM retail_sales;
```

-- data exploration part starts here
-- Find the total revenue generated
```
SELECT sum(total_sale) AS total_revenue FROM retail_sales;
```

-- Find the total number of transactions.
```
SELECT sum(transaction_id) AS total_trans FROM retail_sales;
```

-- Find the total no. of unique customers
```
SELECT COUNT(DISTINCT customer_id) AS unique_customers FROM retail_sales;
```

-- Find the total no. of unique categories
```
SELECT DISTINCT category FROM retail_sales;
```

 -- Write a SQL query to retrieve all columns for sales made on '2022-11-05
```
SELECT * 
FROM retail_sales 
WHERE sale_date = '2022-11-05';
```

-- Top-selling product by categories
```
SELECT category, 
	SUM(total_sale) AS total_revenue
FROM retail_sales
GROUP BY category
ORDER BY 2 DESC;
```

-- Find the average age of customers who purchased items from the 'Beauty' category.
```
SELECT
    ROUND(AVG(age), 0) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

-- Gender-Based Sales Analysis (Male VS Female)
```
SELECT 
    gender,
    SUM(total_sale) AS total_sales,
    COUNT(transaction_id) AS total_orders
FROM retail_sales
GROUP BY gender
ORDER BY total_sales DESC;
```

-- Find 5 highest spending customer
```
SELECT 
	customer_id,
	SUM(total_sale) AS high_spent
FROM retail_sales
GROUP BY customer_id
ORDER BY 2 DESC
LIMIT 5;
```
-- Find all transactions where the total_sale is greater than 1000.
```
SELECT * FROM retail_sales WHERE total_sale > 1000
```

-- calculate the average sale for each month. Find out best selling month in each year
```
SELECT 
       year,
       month,
       avg_sale
FROM (    
	SELECT 
	    EXTRACT(YEAR FROM sale_date) as year,
	    EXTRACT(MONTH FROM sale_date) as month,
	    AVG(total_sale) as avg_sale,
	    RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) as rank
	FROM retail_sales
	GROUP BY 1, 2
) as t1 WHERE rank = 1
```

-- Find the total quantity of products sold
```
SELECT 
	SUM(quantity) AS tot_qty_sold
FROM retail_sales;
```

-- Find the minimum and maximum sale values
```
SELECT
	MIN(transaction_id) AS min_transc,
	MAX(transaction_id) AS max_transc
FROM retail_sales;
```

-- Find total sales for each day
```
SELECT 
	sale_date,
	SUM(total_sale) as daily_revenue
FROM retail_sales
GROUP BY 1
ORDER BY 1;
```

-- Find the day with the highest total sales
```
SELECT 
	sale_date,
	SUM(total_sale) AS daily_sale
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1;
```

-- Find the percentage contribution of each category to total revenue
/* Explanation: In PostgreSQL, 100.0 creates a double precision value. When combined with SUM(total_sale), 
the result becomes double precision. But ROUND() with two arguments (ROUND(value, decimal_places)) requires a numeric type.
so that's why we are using ::numeric*/ 
```
SELECT
	category,
	SUM(total_sale) AS total_revenue,
	ROUND((SUM(total_sale) * 100.0 / (SELECT SUM(total_sale) FROM retail_sales))::numeric, 2)	AS rev_percentage
FROM retail_sales
GROUP BY 1;
```

-- Find peak sales hour of the day
```
SELECT 
	EXTRACT(HOUR FROM sale_time) AS peak_hod,
	SUM(total_sale) AS total_sales, 
	COUNT (*) AS total_trans
FROM retail_sales
GROUP BY peak_hod;
```

-- Show all sales made between ₹500 and ₹1500
```
SELECT *
FROM sales_data
WHERE total_sale BETWEEN 500 AND 1500
ORDER BY total_sale DESC;
```


