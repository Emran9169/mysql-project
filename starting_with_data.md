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

SQL Queries:

Answer:



Question 3: 

SQL Queries:

Answer:



Question 4: 

SQL Queries:

Answer:



Question 5: 

SQL Queries:

Answer:
