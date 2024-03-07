## Possible Queries
>>Please see below other possible queries based on E-commerce Database.

### Question 1: List the products which were sold out by more than 60% of the total stock level.

```
WITH sold_percentage AS
(
	SELECT  productSKU,
		product_name as product_name,
		ROUND( ((total_ordered::FLOAT / stock_level::FLOAT)*100)::NUMERIC,2) sold_level
	FROM    sales_report
	WHERE   stock_level > 0 
	        AND total_ordered <= stock_level
)

SELECT  *
FROM    sold_percentage
WHERE   sold_level > 60.00
```

**Answer: 7 Rows**



### Question 2: For product GGOENEBB079399, how many times it was viewed in Months of 2017 as compared to Months in 2016. Display the result in Mon-Year format ?
```
SELECT 		TO_CHAR(DATE_TRUNC('MONTH', transaction_date)::DATE,'Mon YYYY') as MonthYear, 
		COUNT(page_views) as num_views
FROM 		all_sessions
WHERE 		productSKU = 'GGOENEBB079399' 
GROUP BY 	DATE_TRUNC('MONTH', transaction_date) 
```
**Answer: 6 rows**

### Question 3: List the Top DISTINCT 10 SKU and names of the product which were ordered through Paid search during June 2017 with the result ordered by highest restocking lead time.
```
SELECT 		DISTINCT productSKU, 
		sr.product_name, 
		restocking_leadtime
FROM 		sales_report sr
JOIN 		all_sessions alls USING(productSKU)
WHERE 		lower(channel_grouping) LIKE 'organic%' 
		AND EXTRACT(YEAR FROM transaction_date) = '2017' 
		AND EXTRACT(MONTH FROM transaction_date) = '03'
ORDER BY 	restocking_leadtime DESC
LIMIT 10
```
**Answer: 10 rows**

### Question 4: For the Question above, how many visitors of the respective transaction were socially engaged?

```
SELECT 		DISTINCT productSKU,
		sr.product_name, 
		restocking_leadtime,
		alls.transaction_date
FROM 		sales_report sr
JOIN 		all_sessions alls USING(productSKU)
JOIN		analytics ana USING(visitid) 
WHERE 		lower(alls.channel_grouping) LIKE 'paid%' 
		AND EXTRACT(YEAR FROM alls.transaction_date) = '2017' 
		AND EXTRACT(MONTH FROM alls.transaction_date) = '06'
		AND social_engagement_type NOT LIKE 'Not Socially Engaged'
ORDER BY 	restocking_leadtime DESC
LIMIT 10 
```

**Answer: 0 rows - all of them were not socially engaged**


