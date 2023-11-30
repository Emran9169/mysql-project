Question 1: 
In the year 2016, list the top 5 month that has the highest total ordered quantity for a specific product? Display the name of the product along with the total ordered quantity.

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
When we run the above query we will see the top 5 month that got the highest amount of total ordered quantity with their names. so as we can see from the data 
output area the month August is ranked first, second and third for the highest total ordered quantity and the product name ballpoint LED light pen is mentioned three times among the top 5 products. From this we can understand that August month is the busiest month and the product is in high demand.


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
      WHERE city != '(not set)' AND city != 'not available in demo dataset' and a.country = 'United States'
    ) AS revenue
  FROM 
    all_sessions al
  JOIN
    analytics an ON al.visitid = an.visitid
  WHERE
    city != '(not set)' AND city != 'not available in demo dataset' and al.country = 'United States'
  GROUP BY
    city
) AS subquery
WHERE 
  revenue IS NOT NULL
ORDER BY
  revenue DESC;

Answer:

As United States generate the most revenue we wanted to see whuch states contribute the most so as we run the above query we can see Newyork is the state with the highest amount of revenue generating.

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

By running the above code, we can see the most popular product in each country and I can see that the products varies across country's choice so this data will
help us understand which counntry priortize which product.

Question 4: find each unique product viewed by each visitor

SQL Queries:
SELECT
  p.SKU,
  p.name,
  COUNT(*) AS view_count
FROM
  all_sessions a
JOIN
  products p ON a.productSKU = p.SKU
GROUP BY
  p.SKU
ORDER BY
  view_count DESC

Answer:

We can see from running the above query which product is visited more and is demanded more than other products so that we can focus on the quality of the product
as it is wanted by most peoples. Men 100% cotton short sleeve hero tee white is the product that has the most views.

Question 5: 
 Calculate the average revenue per visit by channel grouping:

SQL Queries:
SELECT
  channelgrouping,
  AVG(unitprice / 1000 * unitssold) AS avg_revenue
FROM
  analytics
WHERE
  unitprice IS NOT NULL AND unitssold IS NOT NULL
GROUP BY
  channelgrouping
ORDER BY
  AVG(unitprice / 1000 * unitssold) DESC
Answer:

The purpose of the above code is to see which channel is used the most and generates the most revenue. By executing the code we can see the Dissplay channel grouping generates the most revenue, it even generates two times more than what the second channel grouping generates.
