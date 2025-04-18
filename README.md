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

## Data Analysis tool
I am using MySQL Server as database to store the data in database tables and perform SQL queries. 

## Objective
To analyze trends in key performance indicators within the dataset to identify potential issues and evaluate the operational effectiveness of the Olist platform. This analysis aims to uncover areas requiring improvement and is structured across the following two dimensions:

1. Platform Sales

- What are the overall trends in order volume and transaction value over time?
(Objective: Understand sales growth or decline patterns.)

- How are sales orders distributed across different geographical regions?
(Objective: Identify high-performing and underperforming locations.)

- How do order trends vary by order status and reviews (e.g., delivered, canceled, in transit)?
(Objective: Evaluate order lifecycle efficiency and potential bottlenecks.)

- Which products generate the highest number of orders and the most revenue?
(Objective: Highlight top-performing products in terms of volume and value.)

3. Customer
