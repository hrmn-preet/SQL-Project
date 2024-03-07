Question 1: List the products which were sold out by more than 60% of the total stock level.

SQL Queries:  

WITH sold_percentage AS
(
	SELECT	productSKU,
			name as product_name,
			ROUND( ((total_ordered::FLOAT / stocklevel::FLOAT)*100)::NUMERIC,2) sold_level
	FROM sales_report
	WHERE stocklevel > 0 AND total_ordered <= stocklevel
)

SELECT *
FROM sold_percentage
WHERE sold_level > 60.00


Answer: 7 Rows 



Question 2: For product GGOENEBB079399, how many times it was viewed in Months of 2017 as compared to Months in 2016. Display the result in Mon-Year format ?

SQL Queries:  SELECT 		TO_CHAR(DATE_TRUNC('MONTH', date)::DATE,'Mon YYYY') as MonthYear, 
			COUNT(pageviews) as num_views
FROM 		all_sessions
WHERE 		productSKU = 'GGOENEBB079399' 
GROUP BY 	DATE_TRUNC('MONTH', date) 
	
Answer: 6 rows



Question 3: List the Top DISTINCT 10 SKU and names of the product which were ordered through Paid search during June 2017 with the result ordered by highest restocking lead time.

SQL Queries: SELECT 		DISTINCT productSKU, 
			name as product_name, restockingleadtime
FROM 		sales_report p
JOIN 		all_sessions alls USING(productSKU)
WHERE 		lower(channelgrouping) LIKE 'organic%' 
			AND EXTRACT(YEAR FROM date) = '2017' 
			AND EXTRACT(MONTH FROM date) = '03'
ORDER BY 	restockingleadtime DESC
LIMIT 10



Answer: 10 rows



Question 4: For the Question above, how many visitors of the respective transaction were socially engaged?

SQL Queries: SELECT 		DISTINCT productSKU,
			name as product_name, restockingleadtime,alls.date
FROM 		sales_report p
JOIN 		all_sessions alls USING(productSKU)
JOIN		analytics ana USING(visitid) 
WHERE 		lower(alls.channelgrouping) LIKE 'paid%' 
			AND EXTRACT(YEAR FROM alls.date) = '2017' 
			AND EXTRACT(MONTH FROM alls.date) = '06'
			AND socialengagementtype NOT LIKE 'Not Socially Engaged'
ORDER BY 	restockingleadtime DESC
LIMIT 10 

Answer: 0 rows - all of them were not socially engaged


