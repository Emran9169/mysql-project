What are your risk areas? Identify and describe them.
1. My first risk area is to make sure in my new cleaned dataset the visit id is unique. if it is unique i am right if it is not either I am wrong or the visitor purchased more products so iam gonna valid this thing.
2.  


QA Process:
Describe your QA process and include the SQL queries used to execute it.
1. First i am gonna execute my cleaned code which is in the cleaning data section. so when I execute it there is 1052 number of rows.
  Then when i run the code: SELECT DISTINCT(visitid) it is 1189. so it is either I am wrong or or there are multiple products for some visitors so lets run this code
SELECT n.visitid, count(s.productsku)
FROM new_analytics n
JOIN new_all_sessions a ON a.visitid = n.visitid
JOIN products p ON p.sku = a.productsku
JOIN sales_by_sku s ON s.productsku = a.productsku
GROUP BY n.visitid
HAVING count(s.productsku)> 1
when we run the above code we can get many rows which shows that visitid cant be unique.
