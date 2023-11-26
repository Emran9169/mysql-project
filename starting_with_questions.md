Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**

SQL Queries:
select country, city, transactionrevenue from all_sessions
where transactionrevenue is not null
order by transactionrevenue

Answer:
When executing the provided code, the data output indicates that the United States has the highest transaction revenues. However, it's crucial to note that a substantial portion of the transaction revenue data is missing, rendering the comparison of the highest transaction revenues inadequate due to insufficient data.



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
SELECT
  AVG(orderedquantity) AS avg_ordered_quantity,
  CASE
    WHEN city = '(not set)' OR city = 'not available in demo dataset' THEN NULL
    ELSE city
  END AS city,
  country
FROM
  products
JOIN
  all_sessions ON products.sku = all_sessions.productsku
GROUP BY
  country, city
ORDER BY
  avg_ordered_quantity;

Answer:





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:



Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
WITH ranked_products AS (
  SELECT
   case when city = '(not set)' or city = 'not available in demo dataset' then null 
	else city end as city,
    case when country = '(not set)' then null else country end as country,
    name AS product_name,
    RANK() OVER (PARTITION BY city, country ORDER BY COUNT(*) DESC) AS product_rank
  FROM
    all_sessions a
  JOIN
    products p ON a.productsku = p.sku	
  GROUP BY
    country, city, name
)
SELECT
  city,
  country,
  product_name AS top_selling_product
FROM
  ranked_products
WHERE
  product_rank = 1 AND city IS NOT null AND country IS NOT null
ORDER BY
  country


Answer:
I was able to notice as the diversity of top-selling products across cities and countries suggests that consumer preferences may vary significantly based on local factors, cultural influences, or regional trends. the other thing i noticed is most of the top_selling product across each city as well as country is not unBuenoique, what i meant is most coutries and cities have multiple top selling products for instance United States, Atlanta has two top selling products among many products that are sold there.




**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:







