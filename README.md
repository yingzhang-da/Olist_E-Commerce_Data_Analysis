# Olist E-Commerce Data Analysis



## Context
This dataset was generously provided by Olist, the largest department store in Brazilian marketplaces. Olist connects small businesses from all over Brazil to channels without hassle and with a single contract. Those merchants are able to sell their products through the Olist Store and ship them directly to the customers using Olist logistics partners


## About Dataset
The dataset used for this project are come from [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce), It has information of 100k orders from 2016 to 2018 made at multiple marketplaces in Brazil. Its features allows viewing an order from multiple dimensions: from order status, price, payment and freight performance to customer location, product attributes and finally reviews written by customers.

There are 5 tables will be used in this dataset as follows;

1. olist_customers_dataset.csv  
Columns:  `customer_id`  `customer_unique_id`  `customer_zip_code_prefix`  `customer_city`  `customer_state`

2. olist_order_items_dataset.csv
Columns:  `order_id`  `order_item_id`  `product_id`  `seller_id`  `shipping_limit_date`  `price`  `freight_value`

4. olist_order_payments_dataset.csv
Columns:  `order_id`  `payment_sequential`  `payment_type`  `payment_installments`  `payment_value`

6. olist_order_reviews_dataset.csv
Columns:  `review_id`  `order_id`  `review_score`  `review_comment_title`  `review_comment_message`  `review_creation_date`  `review_answer_timestamp`

8. olist_orders_dataset.csv
Columns:  `order_id`  `customer_id`  `order_status`  `order_purchase_timestamp`  `order_approved_at`  `order_delivered_carrier_date`  `order_delivered_customer_date`  `order_estimated_delivery_date`

10. olist_products_dataset.csv
Columns:  `product_id`  `product_category_name`
### Data limitation and problem

The dataset covers the period from **Sep 4, 2016 to Oct 17, 2018**, However, **the data from 2016 is highly skewed**, with only 329 orders recorded, indicating limited activity or incomplete records for that year. Additionally, the data between **Sep 1, 2018 and Oct 17, 2018** also appears to be incomplete or less consistent. Therefore, to ensure consistency in the data, the date range for this analysis has been narrowed to January 1, 2017 to August 31, 2018.

### Data Analysis tool

In this project, MySQL Server is used as the database to store and manage the dataset. SQL is applied for querying, data exploration, and analysis. The processed data is then imported into Tableau to create interactive visualizations and dashboards for deeper insights.


### Objective
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

using Excel to conduct cleaning process for each csv file which will be used in this project.

The following are place where have modified:
1. Date range: delete the date beyond 2017-01-01 to 2018-08-31 (the original date range is from 2016-09-04 to 2018-10-17, the data in 2016 are higly devisted,also data between 2018-09-01 to 2018-10-17)

2. Format standard, transform each column to starndard format (etg,. text, int, datetime)

3. Remove spacing with trim formula

4.Delete irrelevant columns and rows 

5. for product_category_name in products table, use VLOOKUP formular lookup english name in product_category_name_translation table. 























