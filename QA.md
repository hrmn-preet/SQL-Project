## What are the risk areas? Identification and Description.

***The following are the risk areas that can be found in the provided E-commerce database,*** 
1. NULL values or Missing values in the columns used during analysis.
> There are many columns with missing, NULL and inconsistent values. IN regard to this, there is not much information available to replace those null values which results in data assumption.
2. Data Type Formats
> There are no major data type error founds in this database. 
3. Presence of duplicate values.
> There is a chance of duplicate data finding but following the cleaning and views creation, this risk factor can be evaluated by running SQL queries to check for the distinct versus non-distinct values.


## The QA process is divided into three components : 
- Data Validation
- Data Cleaning 
- Data Testing
- These will be performed on the cleaned data created i.e. ***all_sessions_clean view, products_clean view, sales_report_clean view***

## Data Validation and Data Cleaning : 

- **Checking the generated/cleaned data to make sure there are no NULL or gaps values in the all_sessions_clean view**

```
SELECT 	* 
FROM all_sessions_clean
WHERE full_visitor_id IS NULL
OR channel_grouping IS NULL
OR time_on_site IS NULL
OR country IS NULL
OR total_transactionrevenue IS NULL
OR visitid IS NULL
OR trans_type IS NULL
OR product_quantity IS NULL
OR product_price IS NULL
OR productSKU IS NULL
OR product_name IS NULL
OR product_category IS NULL
OR currency_code IS NULL
OR page_title IS NULL
OR page_path IS NULL
```
 
- **Checking for duplicate rows in products table to ensure each row only represents a single SKU against counting distinct rows**
```
SELECT COUNT(SKU)
FROM products 
 
Answers : 1092
```
```
SELECT COUNT(DISTINCT SKU)
FROM products 
 
Answers : 1092

No duplicates found
```
- **Checking for duplicate rows in sales_report table to ensure each row only represents a single SKU against counting distinct rows**
```
SELECT COUNT(productSKU)
FROM sales_report

Answer : 454 
```
```
SELECT COUNT(DISTINCT productSKU)
FROM sales_report

Answer : 454 
```
- **Checking for duplicate rows in sales_by_sku table to ensure each row only represents a single SKU against counting distinct rows**
```
SELECT COUNT(productSKU)
FROM sales_by_sku

Answer : 462 rows
```
```
SELECT COUNT(DISTINCT productSKU)
FROM sales_by_sku

Answer : 462 rows
```
## Data Testing 

- **Fetching the data from all_sessions_clean view to ensure there are no rows returned with NULL values.**
```
SELECT * FROM 
FROM all_sessions_clean
```

- **Fetching the data from sales_report_clean for NULL values**
```
SELECT * FROM 
FROM sales_report_clean
```

- **Fetching the data from products_clean for NULL values**
```
SELECT * FROM 
FROM products_clean
```

