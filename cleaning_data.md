What issues will you address by cleaning the data?





Queries:
Below, provide the SQL queries you used to clean your data.
with new_all_sessions as (
select fullvisitorid, channelgrouping, time, updatedcity, updatedcountry, timeonsite, pageviews, date, visitid, type, productprice, productsku,
			v2productname, v2productcategory, pagetitle, pagepathlevel1
			from(
select *, CASE
                WHEN city IN ('(not set)', 'not available in demo dataset') THEN NULL
                ELSE city
            END AS updatedcity,
            CASE
                WHEN country = '(not set)' THEN NULL
                ELSE country
            END AS updatedcountry
			from all_sessions
		)	where updatedcity is not null and updatedcountry is not null and timeonsite is not null
		)
