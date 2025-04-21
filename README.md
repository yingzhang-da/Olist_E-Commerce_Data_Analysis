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




<details>
<summary> Show Monthly Sales Query</summary>

```sql
SELECT
    DATE_FORMAT(o.order_purchase_timestamp,'%Y-%m') AS month_year,
    ROUND(SUM(pay.payment_value),2) AS sales
FROM orders_dataset o
JOIN order_payments pay ON o.order_id = pay.order_id
WHERE order_status = 'delivered'
GROUP BY 1
ORDER BY 1;
```
</details>



<details>
<summary> Show Monthly Orders and Average Review Score Trend Query</summary>

```sql
SELECT 
    DATE_FORMAT(order_purchase_timestamp,'%Y-%m') AS month_year,
    COUNT(DISTINCT order_id) AS delivered_orders
FROM 
    orders_dataset
WHERE 
    order_status = 'delivered'
    AND order_purchase_timestamp > '2016-12-31'
    AND order_purchase_timestamp < '2018-09-01'
GROUP BY 1
ORDER BY 1;

-- Average review score by order date, including all order statuses (e.g., delivered, canceled, etc.)
SELECT
    DATE_FORMAT(o.order_date, '%Y-%m') AS order_month,
    ROUND(AVG(r.review_score), 1) AS avg_review_score
FROM orders_dataset o
JOIN order_reviews r ON o.order_id = r.order_id
WHERE 
    order_status = 'delivered'
GROUP BY 1
ORDER BY 1;
```
</details>


The following results show the monthly order trend and average review score by date for orders with a delivered status.

<p float="left">
  <img src="https://github.com/user-attachments/assets/a4a4262e-06d7-44a0-b135-d144125a8d61" width="200" />
  <img src="https://github.com/user-attachments/assets/a2ce94c9-8dca-4021-b73c-8194b74987ba" width="200" />
</p>

In tableau, i combined these two chart to a dual chart. you can use order status filter to see the order trend with average review score by different order status(e.g. dilivered, canceled.)
<p float="left">
  <img src="https://github.com/user-attachments/assets/21d65910-e2df-4756-8dd9-fd60b20f6b4b" width="500" />
  <img src="https://github.com/user-attachments/assets/f71ddaec-4a89-4cba-a998-6b0e787f85f9" width="500" />
</p>

<details>
<summary> Show 2017 vs 2018 Sales & Orders (January to August) Query</summary>

```sql
SELECT
    CASE 
		WHEN YEAR(o.order_purchase_timestamp)= 2017 AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8 THEN '2017 Jan-Aug'
		WHEN YEAR(o.order_purchase_timestamp)= 2018 AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8 THEN '2018 Jan-Aug'
    END AS year_range,
    ROUND(SUM(pay.payment_value),2) AS gmv
FROM orders_dataset o
JOIN order_payments pay ON o.order_id = pay.order_id
WHERE order_status = 'delivered'
AND (
(YEAR(o.order_purchase_timestamp)= 2017 AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8)
OR (YEAR(o.order_purchase_timestamp)= 2018 AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8)
)
GROUP BY 1
ORDER BY 1;

SELECT
    CASE 
        WHEN YEAR(o.order_purchase_timestamp) = 2017 AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8 THEN '2017 Jan-Aug'
        WHEN YEAR(o.order_purchase_timestamp) = 2018 AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8 THEN '2018 Jan-Aug'
    END AS year_range,
    COUNT(DISTINCT o.order_id) AS total_order
FROM orders_dataset o
JOIN orders_items oi ON o.order_id = oi.order_id
WHERE o.order_status = 'delivered'
  AND (
      (YEAR(o.order_purchase_timestamp) = 2017 AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8)
      OR (YEAR(o.order_purchase_timestamp) = 2018 AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8)
  )
GROUP BY year_range;

```
</details>

<p float="left">
  <img src="https://github.com/user-attachments/assets/0f3fc57c-b4a1-47e0-bccf-bd56d0528d5e" width="200" />
  <img src="https://github.com/user-attachments/assets/0db58073-0d8f-4318-81e7-aab418e70efd" width="200" />
</p>

<p float="left">
  <img src="https://github.com/user-attachments/assets/16145eff-d378-49ce-9b6f-2a1dbb88e62f" width="200" />
  <img src="https://github.com/user-attachments/assets/5de6b2da-c407-42eb-8b27-237d100e9c0a" width="200" />
</p>





insights: 
Looking at the year overall, sales started at R$127,650 in January 2017. followed by a steady upward trend, peaking at R$153,530 in November 2017(The spike in orders around November 24 was likely driven by Black Friday).After a dip to R$96.51K in February 2018, it stabilizes around R$120K-R$132K until May 2018, then declines to R$98.41K by July 2018.

A year-over-year comparison of the January–August period reveals a 143% increase in sales in 2018 compared to the same period in 2017.However, despite this significant growth, 2018 lacked a sustained upward trend, with sales plateauing — unlike the consistent growth trajectory observed in 2017.

Overall, the order trend follows a similar pattern to sales revenue, with both showing steady growth.

Let's look at the orange line on the order chart, the average review score remains relatively stable but drops when order volume peaks, suggesting that potential strain on service quality during  periods of high demand.

next step:
1. The business should investigate the drop in review scores during high order periods to improve customer satisfaction, possibly by optimizing operations or increasing capacity during peak times. The decline in payment value and orders in 2018 deserves further analysis to understand its root causes.



