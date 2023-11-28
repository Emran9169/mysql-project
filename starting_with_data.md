Question 1: 
In the year 2016, which month has the highest total ordered quantity for a specific product? Display the name of the product along with the total ordered quantity.

SQL Queries:
SELECT
  name,
  SUM(total_ordered) AS total_ordered_amount,
  EXTRACT(MONTH FROM date) AS order_month
FROM
  products p
JOIN
  all_sessions s ON p.sku = s.productsku
JOIN
  sales_by_sku a ON a.productsku = p.sku
WHERE
  EXTRACT(YEAR FROM date) = 2016
GROUP BY
  EXTRACT(MONTH FROM date), name
ORDER BY
  total_ordered_amount DESC;

Answer: 



Question 2: 
What percentage of total revenue is contributed by each city in united states
SQL Queries:

SELECT
  city,
  revenue * 100 AS revenue_percent
FROM (
  SELECT
    city,
    SUM(unitprice * unitssold) / (
      SELECT SUM(b.unitprice * b.unitssold)
      FROM all_sessions a
      JOIN analytics b ON a.visitid = b.visitid
      WHERE a.country = 'United States'
    ) AS revenue
  FROM 
    all_sessions al
  JOIN
    analytics an ON al.visitid = an.visitid
  WHERE
    city != '(not set)' AND al.country = 'United States'
  GROUP BY
    city
) AS subquery
WHERE 
  revenue IS NOT NULL
ORDER BY
  revenue DESC;

Answer:



Question 3: 
Identify the most popular product in each country based on the total ordered quantity.

SQL Queries:
WITH ranked_product AS (
  SELECT
    p.name,
    SUM(total_ordered) AS total_ordered,
    country,
    RANK() OVER (PARTITION BY country ORDER BY SUM(total_ordered) DESC) AS product_rank
  FROM
    products p
  JOIN
    all_sessions a ON p.sku = a.productsku
  JOIN
    sales_by_sku s ON p.sku = s.productsku
  GROUP BY
    p.name, a.country
)
SELECT
  name,
  total_ordered,
  country
FROM
  ranked_product
WHERE
  product_rank = 1 AND country != '(not set)'
ORDER BY
  total_ordered desc


Answer:



Question 4: 

SQL Queries:

Answer:



Question 5: 

SQL Queries:

Answer:
