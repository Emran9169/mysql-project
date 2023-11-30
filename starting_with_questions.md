Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**

SQL Queries:
SELECT
  country,
  city,
  SUM(totaltransactionrevenue) AS total_revenue
FROM
  all_sessions
WHERE
  totaltransactionrevenue IS NOT NULL
GROUP BY
  city, country
ORDER BY
  total_revenue

Answer:
When executing the provided code, the data output indicates that Swizerland, Zurich has the highest total transaction revenues. However, it's crucial to note that a substantial portion of the transaction revenue data is missing, rendering the comparison of the highest transaction revenues inadequate due to insufficient data. As we can see from the data output area United States is the country that is mentioned multiple times, if the total transaction revenue was only by country, United States would be the one with the highest level of total transaction revenues, but as we need to group it also by city then Swizerland and zurich are the country and the city with the most amount of total transactionrevenue generated respectively.



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
  SELECT
  avg_ordered_quantity,
  newcity,
  country
FROM
  (
    SELECT
      AVG(total_ordered) AS avg_ordered_quantity,
      CASE
        WHEN city = '(not set)' OR city = 'not available in demo dataset' THEN NULL
        ELSE city
      END AS newcity,
      country
    FROM
      sales_by_sku
    JOIN
      all_sessions ON sales_by_sku.productsku = all_sessions.productsku
    GROUP BY
      country, city
  ) AS subquery
WHERE
  newcity IS NOT NULL
ORDER BY
  avg_ordered_quantity DESC


Answer:

By running theabove code we can see the average total ordered quantity of each country and each city by using the group function. It gives us a great insight
about which country and city have the highest number of total ordered quantities. By executing the above code we can see that Saudi Arabia with it's city Riyadh and czechia with its city brno are the top country with the highest number of average ordered countries which is 319



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
SELECT
    productname,
    country,
    city
FROM
    (
        SELECT
            name AS productname,
            CASE
                WHEN city IN ('(not set)', 'not available in demo dataset') THEN NULL
                ELSE city
            END AS city,
            CASE
                WHEN country = '(not set)' THEN NULL
                ELSE country
            END AS country
        FROM
            products p
        JOIN
            all_sessions a ON p.sku = a.productsku
    ) AS newtable
WHERE
    city IS NOT NULL
    AND country IS NOT NULL


Answer:

Looking at the data from our query, we can see that the types of products people order vary between different countries and even within cities of the same 
country. This tells us that what people want can change depending on where they are. The fact that cities in the same country have different product preferences
indicates that local factors play a role. It could be influenced by things like culture, lifestyle, or specific needs of people in that area.  So, by looking at 
these patterns, we can better adapt and provide what people are looking for in different cities and countries.









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

SELECT totalrevenue, city, country
FROM (
SELECT
 sum((unitprice/1000)*unitssold) as totalrevenue,
  CASE WHEN city = '(not set)' OR city = 'not available in demo dataset' THEN NULL
       ELSE city
  END AS city,
  CASE WHEN country = '(not set)' THEN NULL
       ELSE country
  END AS country
FROM
  analytics a
JOIN
  all_sessions b ON a.visitid = b.visitid
GROUP BY 
	city, country
	)
WHERE
  city IS NOT null AND country IS NOT null and totalrevenue IS NOT null
ORDER BY 
  totalrevenue desc
  
Answer:

The purpose of the provided code is to figure out how much money comes in from different countries and cities. In simple terms, it helps us see which places
bring in the most money.
By looking at the code, we can tell that the revenue is calculated by multiplying the unit price of a product by how many units are sold. So, when we run the 
code, it gives us a list, and what stands out is that the United States is way ahead in terms of making money. It's mentioned nine times in the top ten, with
different cities contributing. This clearly shows how the United States is the big player in this, and New York is leading the way among its cities.
In essence, this code helps us understand where the money is coming from the most, and it's quite clear that the United States, especially New York, plays a significant role in generating revenue.


