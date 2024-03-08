# Final-Project-Transforming-and-Analyzing-Data-with-SQL

>### ***This project focuses on analyzing data and finding helpful insights in relation to shopping behaviours around the globe for set of products using PostgreSQL and PgAdmin.***

##  Process
*This project was divided into the following 4 sections which were initiated with import of the given database : Understanding, Analyzing, Transforming and Quality Assurance of the given database*, 
### Understanding the Data
> This section includes understanding of the database, understanding the relationships between different tables which inturn helped me in determining the primary and foreign keys of individual tables.

### Analyzing the Data
> This section includes working on given analysis to perform as It helped in distinguishing which data was prone to risks and requires cleaning. 

### Transforming the Data
> This section includes cleaning the data that was found at Analyzing step and also transforming any inconsistent data found.

### Data Quality Assurance
> This section includes Data Validation, Data Cleaning and Data Testing to mitigate the risk areas involved in the given database.

## Results
* United states is the top contibutor country with ranking as #1 in 57 out of 61 categories in total.
* The top-selling category is **'Home/Shop by Brand/YouTube/'** whereas the categories **'Bottles/'** and **'Home/Electronics/Accessories/Drinkware/'** are least popular among the globe.
* The major contibutor country is United States as compared to the least contributing being Switzerland. 
* In addition to this, San Francisco city has the highest revenue and Zurich being the lowest.
* The traffic on the site is divided into the following ratio, this could really impact on how the business would develop their marketing strategies to focus on each channel,

    |   Channel       | Traffic Percentage |
    | ------------- |:-------------:     |
    |   Display      | 0.8%          |
    |   Affilates      | 1.75%          |
    |   Paid Search     | 3.36          |
    |   Referral    | 17.04%          |
    |   Direct     | 19.08%          |
    |   Organic Search     | 57.17%          |
* This is evident from that most traffic is coming via Organic search which is contributing 57.17 % of the total views reaching the website.

## Challenges 
* Tables and Columns with missing and NULL values which were hindering the calculations for complex procedures.
* Inconsistent relationships with different tables resulting in lesser combinations to drive more insights.
* The incomplete description of each columns which could make analysis vague. Such as if the following columns were more descriptive in nature, these would have been helpful in making transaction revenue decisions, 
    * ***reaction_type***
    * ***reaction_step***
    * ***reaction_option***

## Future Goals
* This database could has a potential of performing normalization on each table as there were columns which contained non-atomic values.
* Price, product and catergories comparison over quarters in different countries and cities.
* Predictive Analysis using the current and historic data to help with the business sales for different products.

