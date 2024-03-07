# Starting with the Questions

## Assumptions 
> Each row has ordered atleast 1 product per productSKU so replacing all the null values with quantity 1 as the omission of null product quantity rows leaves the dateset with 40 rows out of 15134 rows which can result in incorrect analysis. 

> The transaction revenue column has NULL and Missing values but not enough values to replace the them with MEAN, MEDIAN or MODE so only rows which have the column's values are considered.

    
### Question 1: Which cities and countries have the highest level of transaction revenues on the site?**
**Query #1 :  List of the cities and coutries ranked on the basis of transaction revenue.**
```
SELECT 		CASE WHEN city IN ('not available in demo dataset','NULL') THEN country
	     	ELSE city
	     	END as city,
		country,
		SUM(total_transactionrevenue) as total_revenue
FROM 		all_sessions
WHERE		total_transactionrevenue > 0
GROUP BY	city, 
		country
ORDER BY 	total_revenue DESC
```
**Query #2 : Ranked list of cities grouped by countries. The RANK function used gives ranking to different cities in each country based on the revenue it has generated.**
```
WITH revenue as
(   
        SELECT		CASE WHEN city IN ('not available in demo dataset','NULL') THEN country
                	ELSE city
			END as city,
			country,
			SUM(total_transactionrevenue) as total_revenue  
			FROM all_sessions
	WHERE		total_transactionrevenue > 0
	GROUP BY   	city,
			country
	ORDER BY total_revenue DESC
)
				
SELECT  city, 
        country,
        total_revenue, 
        RANK() OVER(
        PARTITION BY country 
        ORDER BY total_revenue DESC) 
FROM 	revenue
```

### Question 2: What is the average number of products ordered from visitors in each city and country?

**QUERY #1 : Result with the assumption of atleast 1 product ordered per row.** 
```
WITH number_city_country AS
(
    SELECT  	city,
            	country,
            	SUM(CASE WHEN product_quantity IS NULL THEN '1'
		ELSE product_quantity
		END) as products_per_city,
		COUNT(city) as orders_per_city
    FROM 	all_sessions
    WHERE 	city NOT IN ('not available in demo dataset','(not set)')
    GROUP BY    city,
                country
    ORDER BY	city
)

SELECT  	city,
      		country,
        	ROUND((products_per_city::FLOAT/orders_per_city::FLOAT)::NUMERIC,2) as average_products
FROM		number_city_country
ORDER BY	country,
        	city
```
**QUERY #2 : More filtered with the average above 1 which corresponds to the rows that had non-null product quantity values.** 
```
WITH number_city_country AS
(
    SELECT	city,
          	country,
          	SUM(CASE WHEN product_quantity IS NULL THEN '1'
		ELSE product_quantity
		END) as products_per_city,
		COUNT(city) as orders_per_city
    FROM 	all_sessions
    WHERE 	city NOT IN ('not available in demo dataset','(not set)')
    GROUP BY    city,
                country
    ORDER BY	city
)


SELECT      city,
            country,
            ROUND((products_per_city::FLOAT/orders_per_city::FLOAT)::NUMERIC,2) as average_products
FROM        number_city_country
WHERE       ROUND((products_per_city::FLOAT/orders_per_city::FLOAT)::NUMERIC,2) > 1.0
ORDER BY    country,
            city
```

### Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?

**QUERY #1: Fetch highest count per country for each category.** 
```
SELECT *
FROM 
(
	WITH categories_per_country AS  
	(
		SELECT      	country,
				product_category,
				COUNT(product_category) as count_per_country
		FROM        	all_sessions
		WHERE       	product_category LIKE '%/%'
		GROUP BY	product_category,
		           	country
		ORDER BY	product_category,
		        	count_per_country DESC
	
	),
	categories_distribution AS  
	(
		SELECT		DISTINCT product_category,
		            	COUNT(product_category) as count_per_category
		FROM        	all_sessions
		WHERE       	product_category LIKE '%/%'
		GROUP BY	product_category
	)	

	
	SELECT 	    	country,
	            	product_category,     
			ROUND(((count_per_country::numeric/count_per_category::numeric)*100),2) as percentage,
		        DENSE_RANK()OVER(PARTITION BY product_category ORDER BY count_per_country DESC) as ranking
	FROM 	    	categories_distribution
	JOIN	    	categories_per_country USING (product_category)
	ORDER BY	product_category 
)
WHERE ranking = '1'
```

**QUERY #2: Fetch lowest count per country for each category.**
```
SELECT *
FROM 
(
	WITH categories_per_country AS  
	(
		SELECT		country,
				product_category,
				COUNT(product_category) as count_per_country
		FROM		all_sessions
		WHERE		product_category LIKE '%/%'
		GROUP BY	product_category,country
		ORDER BY	product_category,count_per_country
	
	),
	categories_distribution AS  
	(
		SELECT	    	DISTINCT product_category,
		      		COUNT(product_category) as count_per_category
		FROM		all_sessions
		WHERE		product_category LIKE '%/%'
		GROUP BY	product_category
	)	

	
	SELECT 	    	country,
	            	product_category,     
			ROUND(((count_per_country::numeric/count_per_category::numeric)*100),2) as percentage,
		        (DENSE_RANK()OVER(PARTITION BY product_category ORDER BY count_per_country)) as ranking
	FROM		categories_distribution
	JOIN		categories_per_country USING (product_category)
	ORDER BY	product_category 
)
WHERE ranking = '1'
```
**References**
- CTE categories_per_country generates how each category is distributed over different countries.
- CTE categories_distribution generates total count per category in all_sessions table.
- Select Statement connects both CTE's and rank countries in each category with their respective order percentage value in ascending/descending order.

**Answer:**
Patterns observed
- United states is the top contibutor country with ranking as #1 in 57 out of 61 categories in total.
- The top-selling category is **'Home/Shop by Brand/YouTube/'** whereas the categories **'Bottles/'** and **'Home/Electronics/Accessories/Drinkware/'** are least popular among the globe.


**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**

**Query #1 : Generating the top selling product in each country.**
```
SELECT  country,
        productsku,
        product_name,
        num_purchase
FROM 
(	SELECT		country,
		  	productsku,
			product_name,
			SUM(CASE WHEN product_quantity IS NULL THEN '1'
			ELSE product_quantity
			END) as num_purchase,
			ROW_NUMBER()OVER(
		 	PARTITION by country 
		 	ORDER BY    SUM(CASE WHEN product_quantity IS NULL THEN '1'
		 		    ELSE product_quantity
		 		    END)
		 	DESC) as row_number
	FROM		all_sessions
	WHERE		country NOT LIKE '%[0-9]%' AND country <> '(not set)'  											
	GROUP BY	productsku,
			country,
			product_name
	ORDER BY	country,
			num_purchase DESC	
		 	
) 
WHERE row_number = '1'
```
**Query #2 : A situation where a country can have more than one top selling product.**
```
SELECT  country,
        productsku,
        product_name,
        num_purchase FROM 
(	
	SELECT		country,
			productsku,
			product_name,
			SUM(CASE WHEN product_quantity IS NULL THEN '1'
			ELSE product_quantity
			END) as num_purchase,
			RANK()OVER(
		 	PARTITION by country 
		 	ORDER BY SUM(CASE WHEN product_quantity IS NULL THEN '1'
				 ELSE product_quantity
				 END)DESC) as row_number
	FROM		all_sessions
	WHERE		country NOT LIKE '%[0-9]%' AND country <> '(not set)'  									
	GROUP BY    	productsku,
			country,
			product_name
	ORDER BY	country,
			num_purchase DESC			 	
) 
WHERE	row_number = '1' 
```

***Alertnatively, these queries can be subsituted with city values to find the top-selling product.***

Patterns Observed 
- 

### Question 5: Can we summarize the impact of revenue generated from each city/country?

**Query #1 : Insights how transaction revenue is spread over the cities in each country.**
```
SELECT  * 
FROM    
(
	SELECT		city,
			country,
			SUM(total_transactionrevenue) as total_revenue
	FROM 		all_sessions
	WHERE 		total_transactionrevenue > 0 
			AND city NOT IN ('not available in demo dataset','(not set)')
	GROUP BY	country,
			city
)
ORDER BY total_revenue DESC
```
**Query #2 : Insights how transaction revenue is spread over each country.**
```
SELECT  * 
FROM    
(
	SELECT 		country,
			SUM(total_transactionrevenue) as total_revenue
	FROM 		all_sessions
	WHERE 		total_transactionrevenue > 0
	GROUP BY	country
)
ORDER BY total_revenue DESC
```
**Answer:** 
- The major contibutor country is United States as compared to the least contributing being Switzerland. 
- In addition to this, San Francisco city has the highest revenue and Zurich being the lowest.