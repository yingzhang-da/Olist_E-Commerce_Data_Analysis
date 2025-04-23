# Olist E-Commerce Data Analysis



## Context
This dataset was generously provided by Olist, the largest department store in Brazilian marketplaces. Olist connects small businesses from all over Brazil to channels without hassle and with a single contract. Those merchants are able to sell their products through the Olist Store and ship them directly to the customers using Olist logistics partners


## About Dataset
The dataset used for this project is sourced from [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce), It has information of 100k orders from 2016 to 2018 made at multiple marketplaces in Brazil. Its features allows viewing an order from multiple dimensions: from order status, price, payment and freight performance to customer location, product attributes and finally reviews written by customers.

For this project, six key tables from the dataset are utilized：

1. "olist_customers_dataset.csv"

Columns:  `customer_id`  `customer_unique_id`  `customer_zip_code_prefix`  `customer_city`  `customer_state`

2. "olist_order_items_dataset.csv"

Columns:  `order_id`  `order_item_id`  `product_id`  `seller_id`  `shipping_limit_date`  `price`  `freight_value`

3. "olist_order_payments_dataset.csv"

Columns:  `order_id`  `payment_sequential`  `payment_type`  `payment_installments`  `payment_value`

4. "olist_order_reviews_dataset.csv"

Columns:  `review_id`  `order_id`  `review_score`  `review_comment_title`  `review_comment_message`  `review_creation_date`  `review_answer_timestamp`

5. "olist_orders_dataset.csv"

Columns:  `order_id`  `customer_id`  `order_status`  `order_purchase_timestamp`  `order_approved_at`  `order_delivered_carrier_date`  `order_delivered_customer_date`  `order_estimated_delivery_date`

6. "olist_products_dataset.csv"

Columns:  `product_id`  `product_category_name`


## Data limitation and problem
The dataset covers the period from **Sep 4, 2016 to Oct 17, 2018**, However, **the data from 2016 is highly skewed**, with only 329 orders recorded, indicating limited activity or incomplete records for that year. Additionally, the data between **Sep 1, 2018 and Oct 17, 2018** also appears to be incomplete or less consistent. To ensure data reliability and analytical consistency, the date range of this analysis has been limited to the period between January 1, 2017 and August 31, 2018.


## Data Analysis tool
In this project, MySQL Server is used as the database to store and manage the dataset. SQL is applied for querying, data exploration, and analysis. The processed data is then imported into Tableau to create interactive visualizations and dashboards for deeper insights.


## Objective
This project aims to evaluate the performance of the Olist e-commerce platform through a comprehensive analysis of sales and customer-related metrics. The objective is to uncover underlying issues, identify growth opportunities, and provide actionable insights for platform optimization. 


### Define the problems:
1. Platform Sales

- What are the overall trends in order volume and transaction value over time?

- How are sales orders distributed across different geographical regions?

- How do order trends vary by order status and reviews (e.g., delivered, canceled, in transit)?

- Which products generate the highest number of orders, and which don't?


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

**What are the overall trends in order volume and transaction value over time?**


1.Key Metrics: Total Sales, Total Orders, Total Customers, Average Order Value(AOV)  

<details>
<summary> Show SQL Query: Year over Year Comparison (Jan-Aug 2017 vs. Jan-Aug 2018)</summary>

```sql
-- Total Sales
SELECT
    CASE 
	WHEN YEAR(o.order_purchase_timestamp)= 2017 AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8 THEN '2017 Jan-Aug'
	WHEN YEAR(o.order_purchase_timestamp)= 2018 AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8 THEN '2018 Jan-Aug'
    END AS year_range,
    ROUND(SUM(pay.payment_value),2) AS gmv
FROM orders_dataset o
JOIN order_payments pay ON o.order_id = pay.order_id
WHERE order_status = 'delivered'
    AND YEAR(order_purchase_timestamp) IN (2017, 2018)
    AND MONTH(order_purchase_timestamp) BETWEEN 1 AND 8
GROUP BY 1
ORDER BY 1;

-- Total Orders
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

-- Total Customers
SELECT 
    CASE 
        WHEN YEAR(o.order_purchase_timestamp) = 2017 THEN '2017 Jan-Aug'
        WHEN YEAR(o.order_purchase_timestamp) = 2018 THEN '2018 Jan-Aug'
    END AS year_range,
    COUNT(DISTINCT c.customer_unique_id) AS total_order
FROM orders_dataset o
JOIN customers c ON o.customer_id = c.customer_id
WHERE order_status = 'delivered'
    AND YEAR(order_purchase_timestamp) IN (2017, 2018)
    AND MONTH(order_purchase_timestamp) BETWEEN 1 AND 8
GROUP BY year_range;

-- Average Order Value(AOV) 
SELECT
    CASE 
        WHEN YEAR(o.order_purchase_timestamp) = 2017 AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8 THEN '2017 Jan-Aug'
        WHEN YEAR(o.order_purchase_timestamp) = 2018 AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8 THEN '2018 Jan-Aug'
    END AS year_range,
    ROUND(SUM(pay.payment_value) / COUNT(DISTINCT o.order_id), 2) AS avg_order_value
FROM orders_dataset o
JOIN order_payments pay ON o.order_id = pay.order_id
WHERE o.order_status = 'delivered'
  AND YEAR(o.order_purchase_timestamp) IN (2017, 2018)
  AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8
GROUP BY year_range
ORDER BY year_range;
```
</details>

<p float="left">
  <img src="https://github.com/user-attachments/assets/bb9ababa-bcd7-4b52-9c28-297804645dd0" width="200" />
  <img src="https://github.com/user-attachments/assets/37e96012-da9a-4eeb-ac77-6e5792d3fc74" width="200" />
  <img src="https://github.com/user-attachments/assets/1fb4728d-fb8f-4dd3-8e01-e2279f4dcff2" width="200" />
  <img src="https://github.com/user-attachments/assets/1c5443df-d2b6-4614-b835-4c5916c5a0ae" width="200" />
</p>

<p float="left">
  <img src="https://github.com/user-attachments/assets/16145eff-d378-49ce-9b6f-2a1dbb88e62f" width="200" />
  <img src="https://github.com/user-attachments/assets/5de6b2da-c407-42eb-8b27-237d100e9c0a" width="200" />
  <img src="https://github.com/user-attachments/assets/4630c021-f2c6-44aa-85b0-25c2ce54a5ae" width="200" />
  <img src="https://github.com/user-attachments/assets/f4a478c1-3e78-4ccf-9fb5-2cf8fcfd3bbf" width="200" />
</p>






<details>
<summary> Show SQL Query: Sales Trend by Date</summary>

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

<img src="https://github.com/user-attachments/assets/da29476c-d2a7-4289-99db-438812f573ae" width="580" />



<details>
<summary> Show SQL Query: Orders Trend By Date </summary>

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
```
</details>

<img src="https://github.com/user-attachments/assets/a4a4262e-06d7-44a0-b135-d144125a8d61" width="200" />
<img src="https://github.com/user-attachments/assets/89033553-32aa-4a30-83ae-b390d4f5c137" width="560" />

**insights:**
Let’s dive into the year’s sales data. sales started at R$127,550 in January 2017 and exhibited a consistent upward trajectory. The peak sales occurred in November 2017, reaching R$1153,53k. Notably, the surge in orders around November 24 was likely attributed to the Black Friday event. Following the peak, sales declined to R$966,51k in February 2018 and stabilized within the range of R$1120.68k to R$1128.84k until May 2018. Subsequently, sales experienced a decline to R$985.41k by August,2018.

A comparative analysis of the January–August period in 2018 demonstrates a 143% increase in sales compared to the corresponding period in 2017. However, despite this substantial growth, 2018 lacked a sustained upward trend, with sales reaching a plateau, unlike the consistent growth trajectory observed in 2017.

Correspondingly, orders and customers both experienced significantly growth in 2018

**How are sales orders distributed across different geographical regions?**

<details>
<summary> Show State level Sale,orders and customer Trend by Date Query</summary>

```sql
SELECT
    c.customer_state AS state,
    COUNT(DISTINCT o.order_id) AS total_orders,
    ROUND(SUM(p.payment_value), 2) AS total_revenue,
    COUNT(DISTINCT c.customer_unique_id) AS total_customers
FROM orders_dataset o
JOIN order_payments p ON o.order_id = p.order_id
JOIN customers c ON o.customer_id = c.customer_id
WHERE o.order_status = 'delivered'
GROUP BY c.customer_state
ORDER BY total_revenue DESC;
```
</details>

<img src="https://github.com/user-attachments/assets/873ac07c-5db4-4f1e-9448-e73d534a59d8" width="680" />
<img src="https://github.com/user-attachments/assets/306a8306-02a8-4bbc-af01-af8752bcf27d" width="680" />

No doubt,over 60% of revenue comes from SP, RJ, and MG. The Southeast region of Brazil has the largest population. 

(Based on the sales rankings:

Focus on SP, RJ, MG: Allocate marketing and inventory to these states, emphasizing São Paulo, Rio, and Belo Horizonte. Promote high-margin categories like electronics and fashion.
Strengthen Logistics in PR, RS, SC: Leverage their efficient infrastructure for faster deliveries, especially in Curitiba and Porto Alegre.
Target High-Income Cities: Brasília and Niterói are ideal for premium products due to affluent consumers.
Improve Northeast Penetration: Invest in logistics for Salvador to boost BA’s sales, focusing on consumer goods and tourism-related products.
Monitor Smaller Markets: Goiás and Espírito Santo are growing; consider targeted campaigns in Goiânia and Vitória.)

**Which products generate the highest number of orders, and which do not?
<details>
<summary> Show Top 5 Product Categories Query</summary>

```sql
WITH category_order AS(
SELECTt
    p.product_category_name AS product_category_name,
    COUNT(DISTINCT oi.order_id) AS total_orders
FROM 
    orders_items oi
JOIN 
    orders_dataset o ON oi.order_id = o.order_id
JOIN 
    products p ON oi.product_id = p.product_id
WHERE 
    o.order_status = 'delivered'
GROUP BY 
    1
),
total_order AS(
	SELECT SUM(total_orders) AS overall_order
    FROM category_order
)
SELECT
    category_order.product_category_name,
    category_order.total_orders,
    category_order.total_orders/total_order.overall_order AS percent_of_total_order
FROM category_order,total_order
ORDER BY 2 DESC
LIMIT 5;
```
</details>

<details>
<summary> Show Botton 5 Product Categories Query</summary>

```sql

SELECT
    p.product_category_name,
    COUNT(DISTINCT oi.order_id) AS total_orders
FROM 
    orders_items oi
JOIN 
    orders_dataset o ON oi.order_id = o.order_id
JOIN 
    products p ON oi.product_id = p.product_id
WHERE 
    o.order_status = 'delivered'
GROUP BY 1
ORDER BY 2 
LIMIT 5;
```
</details>


<p float="left">
  <img src="https://github.com/user-attachments/assets/7eb3b6b9-b3c6-4c45-a9d1-62d27101d6f3" width="600" />
  <img src="https://github.com/user-attachments/assets/f960b1b8-221f-4e3f-958f-d8502710e603" width="600" />
</p>


High-demand categories such as Bed & Bath, Health & Beauty, and Sports & Leisure suggest that customers prioritize essentials items, personal care products, and lifestyle. These are likely stable, high-turnover categories to focus on for inventory and marketing.

Low-demand categories such as security and services,fashion childrens clothes indicate niche markets or declining relevance. Consider reducing stock or reevaluating their place in the catalog.

**Customer Review Scores**

</details>

<details>
<summary> Show SQL Query: the number of reviews grouped by each rating score </summary>

```sql
SELECT
    r.review_score,
    COUNT(DISTINCT o.order_id) AS review_count,
    ROUND(
        COUNT(DISTINCT o.order_id) * 100.0 / 
        SUM(COUNT(DISTINCT o.order_id)) OVER (), 
        2
    ) AS percent_of_total
FROM order_reviews r
JOIN orders_dataset o ON r.order_id = o.order_id
-- WHERE order_status = 'canceled'
GROUP BY 1
ORDER BY 1 DESC;
LIMIT 5;
```
</details>


<img src="https://github.com/user-attachments/assets/4d986af8-93d1-4057-b5f3-8e54588eddb1" width="200" />

<img src="https://github.com/user-attachments/assets/1589d7b2-ca59-4a90-abca-0c7ced2dbe88" width="580" />


Customer reviews significantly influence purchasing decisions, often determining whether a consumer chooses one business over another. Between January 2017 and August 2018, Olist's e-commerce platform received 11,293 one-star reviews and 3,136 two-star reviews — together accounting for approximately 15% of all reviews during that period. 


<p float="left">
  <img src="https://github.com/user-attachments/assets/d608e968-0a6a-47c1-b7a3-28828289a0cb" width="580" />
  <img src="https://github.com/user-attachments/assets/c8022b2f-da58-453c-8cd8-8ec81174e6af" width="580" />
</p>

The depicted figure shows the average rating trend line as order volume fluctuates. The negative correlation observed between order volume peaks and rating declines indicates that service quality may encounter challenges in maintaining its standards as demand increases. 

The lowest average review scores occurred around November 24, 2017, and March 2, 2018 — both of which align with major holidays in Brazil (Black Friday and Carnival, respectively). These periods likely experienced a surge in order volume, which may have strained logistics and customer service, resulting in a dip in customer satisfaction.

This highlights the critical need for proactive operational planning during high-demand seasons to preserve customer experience.

When filtering for orders with a 'canceled' status, the overall average review score drops below 3. This indicates a strong correlation between order cancellations and negative customer experiences.


next step:
The business should closely investigate the decline in review scores during periods of high order volume. This trend suggests potential operational strain, and addressing it may involve optimizing logistics, improving fulfillment processes, or scaling support capacity during peak demand. Enhancing service performance in these critical periods can directly improve customer satisfaction and loyalty."
Additionally, the observed decline in both payment value and order volume in 2018 warrants further analysis. Understanding the underlying causes—whether market saturation, increased competition, pricing issues, or changes in consumer behavior—will be essential for developing informed strategies to regain momentum and sustain growth.




