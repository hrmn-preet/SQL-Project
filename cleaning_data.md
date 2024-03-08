## What issues will you address by cleaning the data?

### ***ALL_SESSIONS TABLE***
1. Eliminating the '(not set)' values from country column will assist with a clear result.
2. Eliminating the '(not set)' and 'not available in demo dataset' values from country column will assist with a clear result.
3. The unit cost in the data needs to be divided by 1,000,000. 
4. Assumption of product_quantity to be atleast 1 to avoid incorrect analysis as 80% rows will be out of the database if null values rows are eliminated.
5. Deleting the following rows to compute transform processing as these rows consist of 90%-95% null values,
		rows transcations, 
		item_quanity, 
		item_revenue, 
		transaction_revenue, 
		transaction_id, 
		reaction_type, 
        reaction_step, 
		reaction_option,
		search_keyword,
		session_quality_dim,
		product_refund_amount,
		product_revenue,
		product_variant 
6. Replacing NULL values for Currency_code Column to 'USD', assumption based on average data.
7. Replacing NULL values for timeonsite Column to Average of timeonsite data.
8. Replacing NULL values for total_transactionrevenue Column to 0 as limited date is available to calculate the avg or mean of the column.

### Queries:

```
CREATE OR REPLACE VIEW all_sessions_clean AS
SELECT 	full_visitor_id,
		channel_grouping,
		time_on_site,	
		country,	
		city,
		COALESCE(total_transactionrevenue,0) as total_transactionrevenue,
		COALESCE(timeonsite,(SELECT ROUND(AVG(timeonsite),2) as avg_timeonsite FROM all_sessions )) as timeonsite,
		page_views,
		transaction_date,
		visitid,
		trans_type,
		(CASE WHEN product_quantity IS NULL THEN '1'
		ELSE product_quantity
		END) as product_quantity,
		(product_price/1000000) as product_price,
		productsku,
		product_name,	
		product_category,
		COALESCE(currency_code,'USD'),
		page_title,
		page_path
FROM    all_sessions
WHERE   city NOT IN ('not available in demo dataset','(not set)')
        AND country NOT IN ('(not set)' ,'not available in demo dataset')
```

### ***PRODUCTS TABLE***
1. Removing rows with no stock_level to reduce computation.
2. Replacing NULL values for sentiment_score column and sentiment_magnitude column with average column value.
3. Filtering DISTINCT SKU's to avoid duplication.

### Queries:
```
CREATE OR REPLACE VIEW products_clean AS
SELECT	DISTINCT SKU,
		product_name,
		ordered_quantity,
		stock_level,
		restocking_leadtime,
		COALESCE(sentiment_score,ROUND((SELECT AVG(sentiment_score) FROM products),2)) as sentiment_score,
		COALESCE(sentiment_magnitude,ROUND((SELECT AVG(sentiment_magnitude) FROM products),2)) as sentiment_magnitude
FROM    products
WHERE   stock_level > '0'
```

### ***SALES_REPORT TABLE***
1. Removing rows with no stock_level to reduce computation.
2. Replacing NULL values for sentiment_score, sentiment_magnitude and ratio column with average column value.
3. Filtering DISTINCT SKU's to avoid duplication.

### Queries:
```
CREATE OR REPLACE VIEW sales_report_clean AS
SELECT  productsku,
		product_name,
		total_ordered,
		stock_level,
		restocking_leadtime,
		COALESCE(sentiment_score,ROUND((SELECT AVG(sentiment_score) FROM sales_report),2)) as sentiment_score,
		COALESCE(sentiment_magnitude,ROUND((SELECT AVG(sentiment_magnitude) FROM sales_report),2)) as sentiment_magnitude,
		COALESCE(ratio,(SELECT AVG(ratio) FROM sales_report)) as ratio
FROM    sales_report
WHERE   stock_level > '0' 
```
