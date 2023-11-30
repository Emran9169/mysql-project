What issues will you address by cleaning the data?
In the meticulous process of data cleaning what i did was cleaning each individual tables, with a primary focus on refining the 'analytics' and 'all_sessions'
tables. starting with the 'all_sessions' table, a strategic approach was employed. Lusing the Common Table Expressions (CTE) function, a new table named 
'new_all_sessions' was created. Among the numerous columns within 'all_sessions,' a thoughtful decision was made to exclude certain columns, namely 
'totaltransactionrevenue,' 'transaction,' 'sessionQualityDim,' 'productRefundAmount,' 'productQuantity,' 'productRevenue,' 'productVariant,' 'currencyCode,' 
'itemQuantity,' 'itemRevenue,' 'transactionRevenue,' 'transactionId,' 'eCommerceAction_type,' 'eCommerceAction_step,' and 'eCommerceAction_option.' This 
selective exclusion was based on the presence of either null values or data deetermined to be irrelevant to the analytical objectives.
Having streamlined the columns, the next step involved concatenating the 'categoryname' and 'pagetitle' columns, due to their inherent similarity. Furthermore, 
a targeted effort was made to enhance data clarity by addressing the 'city' and 'country' columns. Specifically, occurrences of 'nonset' and 'not available' were
systematically transformed into null values, and subsequently, the null columns were removed. This  sequence of actions aimed to improve data integrity and 
relevance within the 'all_sessions' table.
The other column which is very bulky is the analytics table so in the analytics table after i assessed the data in each column as i did with the all_sessions
table I got rid of the following columns: userid, unitssold, bounces, revenues as most of them are null and have no application at all. After that I only 
selected that I think is relevant for the dataset i am gonna create which are: visitid, fullvisitor id and sum of the unit price. The purpose of this is to omit 
duplicates so by using the group function i finished my task.
The last task is joining the tables to make the dataset full so the new tables created from all_sessions and analytics by using the cte function is inner joined 
with products for the purpose of getting insights about the product and joined with sales_by_sku to get the total ordered information only. I really believe the 
table sales_report has no value so i didnt clean it as it is not part of my dataset.






Queries:
Below, provide the SQL queries you used to clean your data.
with new_all_sessions as (
    select 
        fullvisitorid, 
        channelgrouping, 
        time, 
        updatedcity, 
        updatedcountry, 
        timeonsite, 
        pageviews, 
        date, 
        visitid, 
        type, 
        productprice, 
        productsku,
        v2productname, 
        v2productcategory || 
  CASE 
    WHEN pagetitle LIKE '%|%' 
      THEN 
        REVERSE(SPLIT_PART(REVERSE(pagetitle), '|', 1))
    ELSE
      ''
  END AS full_category_product,
        pagepathlevel1
    from (
        select 
            *, 
            CASE
                WHEN city IN ('(not set)', 'not available in demo dataset') THEN NULL
                ELSE city
            END AS updatedcity,
            CASE
                WHEN country = '(not set)' THEN NULL
                ELSE country
            END AS updatedcountry
        from all_sessions
    ) where updatedcity is not null and updatedcountry is not null and timeonsite is not null
)
, new_analytics as (
    select
	    visitid, 
        fullvisitorid,  
        sum(unitprice)/1000 as total_unitprice
    from analytics
    group by 
	    visitid, 
        fullvisitorid         
) 

SELECT DISTINCT
    n.visitid,
    n.fullvisitorid,
    n.total_unitprice,
    a.channelgrouping,
    a.time,
    a.updatedcity,
    a.updatedcountry,
    a.timeonsite,
    a.pageviews,
    a.date,
    a.type,
    a.productprice,
    a.productsku,
    a.v2productname,
    a.full_category_product,
    a.pagepathlevel1,
    p.orderedquantity,
    p.stocklevel,
    p.restockingleadtime,
    p.sentimentscore,
    p.sentimentmagnitude,
    s.total_ordered
FROM new_analytics n
JOIN new_all_sessions a ON a.visitid = n.visitid
JOIN products p ON p.sku = a.productsku
JOIN sales_by_sku s ON s.productsku = a.productsku
