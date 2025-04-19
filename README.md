# Olist E-Commerce Data Analysis



## Context
This dataset was generously provided by Olist, the largest department store in Brazilian marketplaces. Olist connects small businesses from all over Brazil to channels without hassle and with a single contract. Those merchants are able to sell their products through the Olist Store and ship them directly to the customers using Olist logistics partners


## About Dataset
The dataset used for this project is sourced from [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce), It has information of 100k orders from 2016 to 2018 made at multiple marketplaces in Brazil. Its features allows viewing an order from multiple dimensions: from order status, price, payment and freight performance to customer location, product attributes and finally reviews written by customers.

For this project, six key tables from the dataset are utilized：

1. `olist_customers_dataset.csv`

Columns:  `customer_id`  `customer_unique_id`  `customer_zip_code_prefix`  `customer_city`  `customer_state`

2. `olist_order_items_dataset.csv`

Columns:  `order_id`  `order_item_id`  `product_id`  `seller_id`  `shipping_limit_date`  `price`  `freight_value`

3. `olist_order_payments_dataset.csv`

Columns:  `order_id`  `payment_sequential`  `payment_type`  `payment_installments`  `payment_value`

4. `olist_order_reviews_dataset.csv`

Columns:  `review_id`  `order_id`  `review_score`  `review_comment_title`  `review_comment_message`  `review_creation_date`  `review_answer_timestamp`

5. `olist_orders_dataset.csv`

Columns:  `order_id`  `customer_id`  `order_status`  `order_purchase_timestamp`  `order_approved_at`  `order_delivered_carrier_date`  `order_delivered_customer_date`  `order_estimated_delivery_date`

6.` olist_products_dataset.csv`  

Columns:  `product_id`  `product_category_name`

## Data limitation and problem

The dataset covers the period from **Sep 4, 2016 to Oct 17, 2018**, However, **the data from 2016 is highly skewed**, with only 329 orders recorded, indicating limited activity or incomplete records for that year. Additionally, the data between **Sep 1, 2018 and Oct 17, 2018** also appears to be incomplete or less consistent. Therefore, to ensure consistency in the data, the date range for this analysis has been narrowed to January 1, 2017 to August 31, 2018.

## Data Analysis tool

In this project, MySQL Server is used as the database to store and manage the dataset. SQL is applied for querying, data exploration, and analysis. The processed data is then imported into Tableau to create interactive visualizations and dashboards for deeper insights.


## Objective
This project aims to evaluate the performance of the Olist e-commerce platform through a comprehensive analysis of sales and customer-related metrics. The objective is to uncover underlying issues, identify growth opportunities, and provide actionable insights for platform optimization. 


### Define the problems:
1. Platform Sales

- What are the overall trends in order volume and transaction value over time?

- How are sales orders distributed across different geographical regions?

- How do order trends vary by order status and reviews (e.g., delivered, canceled, in transit)?

- Which products generate the highest number of orders and the most revenue?


2. Customer
- Who are our customers?
  - Are they concentrated in specific regions or cities?

- How do customers interact to out platform?
  - How many are one-time buyers vs. repeat customers?
  
- How loyal are our customers?
  - How recently did they make their last purchase?

- Are there any segments of customers worth targeting?

- Are there any issues in customer experience?
  - Are there common complaints or cancellations from specific customers or regions?




### Data Cleaning

I used Excel to perform data cleaning and preprocessing for each CSV file prior to importing into MySQL. 


The following steps were taken to ensure data quality and compatibility with MySQL formatting:  
1. **Date Range Filtering**: Removed records outside the selected timeframe: January 1, 2017 to August 31, 2018.

2. **Data Type Standardization**:Converted columns to appropriate data types (e.g., Text, Integer, DateTime) to ensure consistency during querying and visualization.

3. **Whitespace Removal**: Applied the TRIM() function to eliminate leading and trailing spaces in text fields.

4. **Removal of Irrelevant Data**: Deleted unnecessary columns and rows that were not relevant to the scope of the analysis.

5. **Product Category Name Translation**: Used the VLOOKUP() function in Excel to match the original Portuguese product category names with their English equivalents, using the product_category_name_translation table as a reference. 



### Create Olist Database and Tables in MySQL Server




### Exploratory Data Analysis and Business Insights















