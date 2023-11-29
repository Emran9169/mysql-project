What issues will you address by cleaning the data?





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
        pageviews, 
        sum(unitprice)/1000 as total_unitprice
    from analytics
    group by 
	    visitid, 
        fullvisitorid,  
        pageviews 
) 

SELECT DISTINCT
    n.visitid,
    n.fullvisitorid,
    n.pageviews,
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
