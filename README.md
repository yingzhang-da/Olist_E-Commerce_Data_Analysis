# Olist E-Commerce Data Analysis

### Table of Contents
1. Introduction
    - 1.1 Context
    - 1.2 Objective
    - 1.3 Business Questions to Ask
2. About the Dataset
   - 2.1 Data Description
   - 2.2 Data Limitations
3. Data Analysis Tools
4. Data Preprocessing
   - 4.1 Data Cleaning
   - 4.2 Database Setup
5. Exploratory Data Analysis(EDA)
   - 5.1 Key Metrics
   - 5.2 Sales Trends Over Time
   - 5.3 Order Volume Trends
   - 5.4 Geographical Sales Distribution
   - 5.5 Product Category Performance
   - 5.6 Customer Review Analysis
6. Customer Segmentation & Behavior Analysis
   - 6.1 RFM Customer Group Distribution
   - 6.2 Customer Activity and Value Breakdown
   - 6.3 Payment Method Preferences
   - 6.4 Weekly Customer Activity by Hour
   - 6.5 Optional: Pareto Analysis(80/20 Principle)
7. Key Findings & Recommendations 
8. Tableau Dashboard

### 1. Introduction

**1.1 Context**  

This dataset was generously provided by Olist, the largest department store in Brazilian marketplaces. Olist connects small businesses from all over Brazil to channels without hassle and with a single contract. Those merchants are able to sell their products through the Olist Store and ship them directly to customers using Olist logistics partners.<br/>

**1.2 Objective**
- Creating and structuring a MySQL database to store and query Olist e-commerce data, supporting data exploration, KPI reporting, and dashboard visualization.
- Developing an interactive Tableau dashboard integrated with MySQL, visualizing sales performance and customer behavior for stakeholder use.
- Analyzing sales, order volume, and customer data to identify key challenges, uncover growth opportunities, and deliver actionable recommendations to optimize platform performance.

**1.3 Business Questions to Ask:**

1. Platform Sales

- What are the overall trends in order volume and transaction value over time?

- How are sales orders distributed across different geographical regions?

- What patterns emerge by order status and review ratings?

- Which products are top performers and which are underperforming?


2. Customer
- Who are our customers and are they concentrated in specific regions?

- How do customers engage with the platform( one-time vs. repeat purchases)?
  
- How loyal are customers, and when did they last purchase?

- Which customer segments should be targeted for marketing or retention?



### 2. About Dataset

**2.1 Data Description**
The dataset used for this project is sourced from [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce). It has information on 100k orders from 2016 to 2018 made at multiple marketplaces in Brazil. Its features allow viewing an order from multiple dimensions: from order status, price, payment, and freight performance to customer location, product attributes, and finally, reviews written by customers.

For this project, six key tables from the dataset are utilized:

1. "olist_customers_dataset.csv"

Columns:  
`customer_id`  
`customer_unique_id`  
`customer_zip_code_prefix`  
`customer_city`  
`customer_state`

2. "olist_order_items_dataset.csv"

Columns:  
`order_id`  
`order_item_id`  
`product_id`  
`seller_id`  
`shipping_limit_date`  
`price`  
`freight_value`

3. "olist_order_payments_dataset.csv"

Columns:  
`order_id`  
`payment_sequential`  
`payment_type`  
`payment_installments`  
`payment_value`

4. "olist_order_reviews_dataset.csv"

Columns:  
`review_id`  
`order_id`  
`review_score`  
`review_comment_title`  
`review_comment_message`  
`review_creation_date`  
`review_answer_timestamp`

5. "olist_orders_dataset.csv"

Columns:  
`order_id`  
`customer_id`  
`order_status`  
`order_purchase_timestamp`  
`order_approved_at`  
`order_delivered_carrier_date`  
`order_delivered_customer_date`  
`order_estimated_delivery_date`

6. "olist_products_dataset.csv"

Columns:  
`product_id`  
`product_category_name`

**2.2 Data Limitations**
The dataset covers the period from **Sep 4, 2016 to Oct 17, 2018**. However, **the data from 2016 is highly skewed**, with only 329 orders recorded, indicating limited activity or incomplete records for that year. Additionally, the data between **Sep 1, 2018 and Oct 17, 2018** also appears to be incomplete or less consistent. To ensure data reliability and analytical consistency, the date range of this analysis has been limited to the period between January 1, 2017 and August 31, 2018.



### 3. Data Analysis tool
For this project, I used MySQL Server to manage and structure all the datasets, ensuring their organization and accessibility. MySQL was integrated with Tableau to develop an interactive Olist e-commerce dashboard. For advanced analyses,such as customer segmentation, I crafted SQL queries in MySQL to process the data, then imported the results into Tableau to create insightful visualizations for informed decision-making.  To maintain data consistency between MySQL and Tableau, I documented the SQL queries used for data extraction and preparation, ensuring seamless alignment across both tools.<br/>


### 4. Data Preprocessing
**4.1 Data Cleaning:** I used Excel to perform data cleaning and preprocessing for each CSV file prior to importing into MySQL. 

The following steps were taken to ensure data quality and compatibility with MySQL formatting:  
1. **Date Range Filtering**: Removed records outside the selected timeframe: January 1, 2017 to August 31, 2018.

2. **Data Type Standardization**:Converted columns to appropriate data types (e.g., Text, Integer, DateTime) to ensure consistency during querying and visualization.

3. **Whitespace Removal**: Applied the TRIM() function to eliminate leading and trailing spaces in text fields.

4. **Removal of Irrelevant Data**: Deleted unnecessary columns and rows that were not relevant to the scope of the analysis.

5. **Product Category Name Translation**: Used the VLOOKUP() function in Excel to match the original Portuguese product category names with their English equivalents, using the product_category_name_translation table as a reference.<br/>

**4.2 Database Setup:** Created the Olist database and imported all relevant datasets into MySQL Server.

<img src="https://github.com/user-attachments/assets/e9c31398-c3ba-4869-a7e2-ee7d8b4b56c6" width="400" /><br/>

### 5.Exploratory Data Analysis(EDA)
**Q: What are the overall trends in order volume and transaction value over time?**<br/>

**5.1 Key Metrics:**  
Total Sales – Aggregate value of all completed transactions.  
Total Orders – Number of orders placed across the platform.  
Total Customers – Count of unique customers.  
Average Order Value (AOV) – Calculated as Total Sales divided by Total Orders; 

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
    AND YEAR(order_purchase_timestamp) IN (2017, 2018)
    AND MONTH(order_purchase_timestamp) BETWEEN 1 AND 8
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

Let's look at the KPIs from January to August 2017 and 2018,  Business has been doing great! Sales have gone up by over 139%, and we’ve seen a significant increase in the number of orders and customers. The Average Order Value (AOV) has also grown, but not by much. suggesting that the revenue surge is mainly due to a larger number of customers and orders, rather than higher spending per order.<br/>

**5.2 Sales Trends Over Time**

<details>
<summary> Show SQL Query </summary>

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

<img src="https://github.com/user-attachments/assets/a9373aef-0c4e-4c15-b229-d106246efe5b" width="580" /><br/>

**5.3 Order Volume Trends**

<details>
<summary> Show SQL Query </summary>

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
<img src="https://github.com/user-attachments/assets/89033553-32aa-4a30-83ae-b390d4f5c137" width="560" /><br/>

Let’s explore the 2017–2018 sales data. sales started at R$127,550 in January 2017 and followed a consistent upward trend, peaking at R$1,153,530 in November 2017, likely driven by a surge in orders during Black Friday (around November 24). Sales then declined to R$966,51k in February 2018 and stabilized within the range of R$1120.68k to R$1128.84k until May 2018. By August 2018, sales dropped to R$985,410.  Overall, 2017 exhibited strong, consistent growth, while 2018 showed significant growth but lacked a sustained upward trend, plateauing instead.

**5.4 Geographical Sales Distribution** 

**Q: How are sales orders distributed across different geographical regions?**

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

<img src="https://github.com/user-attachments/assets/b1847314-c94e-4eac-a54f-840433c66776" width="580" />
<img src="https://github.com/user-attachments/assets/a47bec1d-5650-42c5-a5bb-aa345f84db5a" width="580" />

Undoubtedly,over 60% of revenue comes from SP, RJ, and MG.As the most populous and economically significant region in Brazil, the Southeast plays a crucial role. Prioritize SP, RJ, and MG in marketing and inventory planning, focusing on major cities like Sao Paulo, Rio, and Belo Horizonte. Leverage these markets to push high-margin products and drive profitability.<br/>

**5.5 Product Category Performance**

**Q:Which products are top performers and which are underperforming?**

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

<img src="https://github.com/user-attachments/assets/bb465851-98f3-4573-ac94-e49c8733f922" width="580" />

High-demand categories such as Bed & Bath, Health & Beauty, and Sports & Leisure suggest that customers prioritize essentials items, personal care products, and lifestyle. These are likely stable, high-turnover categories to focus on for inventory and marketing.

Low-demand categories such as security and services,fashion childrens clothes indicate niche markets or declining relevance. Consider reducing stock or reevaluating their place in the catalog.<br/>

**5.6 Customer Review Analysis**
**Q:What patterns emerge by order status and review ratings?**

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

<img src="https://github.com/user-attachments/assets/6d850711-074c-4309-b9aa-02555918b169" width="380" />

Customer reviews significantly influence purchasing decisions, often determining whether a consumer chooses one business over another. Between January 2017 and August 2018, Olist's e-commerce platform received 11,293 one-star reviews and 3,136 two-star reviews — together accounting for approximately 15% of all reviews during that period. 


<p float="left">
  <img src="https://github.com/user-attachments/assets/d608e968-0a6a-47c1-b7a3-28828289a0cb" width="580" />
  <img src="https://github.com/user-attachments/assets/c8022b2f-da58-453c-8cd8-8ec81174e6af" width="580" />
</p>

1.
The depicted figure shows the average rating trend line as order volume fluctuates. The negative correlation observed between order volume peaks and rating declines indicates that service quality may encounter challenges in maintaining its standards as demand increases. 

The lowest average review scores occurred around November 24, 2017, and March 2, 2018 — both of which align with major holidays in Brazil (Black Friday and Carnival, respectively). These periods likely experienced a surge in order volume, which may have strained logistics and customer service, resulting in a dip in customer satisfaction.

This highlights the critical need for proactive operational planning during high-demand seasons to preserve customer experience.

2.
When filtering for orders with a 'canceled' status, the overall average review score drops below 3. This indicates a strong correlation between order cancellations and negative customer experiences.<br/>

### 6. Customer Segmentation & Behavior Analysis

**Q:How do customers engage with the platform( one-time vs. repeat purchases)?**

**6.1 RFM Customer Group Distribution**

<details>
<summary> Show SQL Query: Customer Segmentation </summary>

```sql
SELECT 
    r.customer_segment AS customer_group,
    COUNT(DISTINCT r.customer_unique_id) AS customers,
    ROUND(COUNT(DISTINCT r.customer_unique_id) * 1.0 / SUM(COUNT(DISTINCT r.customer_unique_id)) OVER(), 2) AS percent_of_all_customers,
    ROUND(AVG(b.monetary), 2) AS avg_spend_per_customer,
    ROUND(SUM(b.monetary), 2) AS total_revenue
FROM rfm_model_customer_division r
JOIN rfm_base_data b ON r.customer_unique_id = b.customer_unique_id
GROUP BY 1
ORDER BY 2 DESC;
```
</details>

<img src="https://github.com/user-attachments/assets/0ca7e378-64c1-49a7-aee8-ce191370ff90" width="580" />

The figure shown 97% customer are one time buyer,which generate the R$14,515.12k revenue , the bulk of total revenue (R$15,375.88k). This heavy reliance on one time buyers indicates a lack of customer retention. it can be risky for long term growth as acquiring new customers is often more expensive than retaining existing ones.<br/>

**Q:How loyal are our customers? How recently did they make their last purchase?**<br/>

**6.2 Customer Activity and Value Breakdown**
 
<details>
<summary> Show SQL Query: Customer Activity Status </summary>

```sql
CREATE TEMPORARY TABLE rfm_with_status AS
SELECT 
    customer_unique_id,
    recency,
    frequency,
    monetary,
    CASE 
        WHEN recency <= 180 THEN 'Active'
        WHEN recency > 180 AND recency <= 365 THEN 'Idle'
        ELSE 'Churned'
    END AS customer_status
FROM rfm_base_data;
```
</details>

<img src="https://github.com/user-attachments/assets/89c1cd67-d935-4796-828f-4ee7bfbfed33" width="580" />

The Champions group, with 77% active customers and an average spend of R$376.2, represents a small yet valuable high-spending segment with strong engagement. Meanwhile, Loyal Customers face an 81% idle rate, indicating a need for re-engagement, while the At-Risk segment (995 customers) is particularly concerning, with 70% already churned, underscoring the urgency for retention efforts. One-Time Buyers, despite driving most revenue, lack loyalty, with 42% idle and 29% churned. To ensure sustainable growth, the business should prioritize converting one-time buyers into repeat customers and revitalizing the idle and at-risk segments.

**6.3 Payment Method Preferences**

<details>
<summary> Show SQL Query: Payment Type </summary>

```sql
SELECT
    p.payment_type AS payment_method,
    COUNT(DISTINCT cu.customer_unique_id) AS total_customers,
    SUM(p.payment_value) AS payment_value,
    ROUND(
        SUM(p.payment_value) * 1.0 / SUM(SUM(p.payment_value)) OVER (), 
        4
    ) AS payment_share,
    ROUND(AVG(p.payment_value), 2) AS average_consumption
FROM order_payments p
JOIN orders_dataset o ON p.order_id = o.order_id
JOIN customers cu ON o.customer_id = cu.customer_id
WHERE o.order_status = 'delivered'
GROUP BY p.payment_type
ORDER BY payment_value DESC;
```
</details>

<img src="https://github.com/user-attachments/assets/e0dcd639-f975-4baa-9dbc-6b06de167019" width="580" />

Insight: Credit card spending accounts for 78.45% of total user spending, making it the most commonly used payment method among customers.

Recommendation:
To enhance customer loyalty and retention, consider partnering with a credit card issuer to introduce a co-branded card offering exclusive benefits for purchases made on your platform. Additionally, for high-value items such as electronics and furniture, collaborating with Bank to offer a "0% interest installment" plan. This strategy can lower purchase barriers,encouraging more frequent and higher spending,while reducing the chances of them switching to your competitors.

**6.4 Weekly Customer Activity by Hour**

<details>
<summary> Show SQL Query: Weekly Order Pattern </summary>

```sql
SELECT 
  DAYNAME(order_purchase_timestamp) AS day_of_week,
  HOUR(order_purchase_timestamp) AS hour_of_day,
  COUNT(order_id) AS total_orders
FROM orders_dataset
GROUP BY day_of_week, hour_of_day
ORDER BY 
  FIELD(day_of_week, 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'),
  hour_of_day;
```
</details>

<img src="https://github.com/user-attachments/assets/1d3a667b-f96c-4e5f-8600-db509ab7bab0" width="580" />


Weekly Trends: Order volume analysis by day of the week indicates a strong start to the week, with peak activity occurring on Mondays and Tuesdays. A gradual decline in orders is observed as the week progresses, reaching lower levels by the weekend. This suggests higher customer engagement during the early weekdays.

Daily Trends: Hourly data reveals a daily cycle, where order activity increase in the morning, a peaks between 2 PM and 4 PM, and a sharp drop in the evening, reaching a low point overnight. This pattern reflects typical consumer online behavior aligned with work and leisure hours.

These patterns offer clear opportunities for optimization:
Schedule marketing campaigns and key promotions for early in the week and during afternoon peak hours.
Adjust operational resources (e.g., customer support, order fulfillment) to align with periods of high activity.

**6.5 Optional: Pareto Analysis(80/20 Principle)** 

<details>
<summary> Show SQL Query </summary>

```sql
CREATE TEMPORARY TABLE temp_pareto_analysis AS
SELECT
    customer_unique_id,
    total_revenue,
    SUM(total_revenue) OVER (ORDER BY total_revenue DESC) AS cumulative_revenue,
    SUM(total_revenue) OVER () AS total_overall_revenue,
    ROW_NUMBER() OVER (ORDER BY total_revenue DESC) AS cumulative_customers,
    COUNT(*) OVER () AS total_customers,
    ROUND(SUM(total_revenue) OVER (ORDER BY total_revenue DESC) / SUM(total_revenue) OVER () * 100, 4) AS cumulative_revenue_pct,
    ROUND(ROW_NUMBER() OVER (ORDER BY total_revenue DESC) / COUNT(*) OVER () * 100, 4) AS cumulative_customer_pct
FROM temp_customer_revenue;

SELECT
    cumulative_customers AS customers_for_80pct,
    cumulative_customer_pct AS customer_pct_for_80pct,
    cumulative_revenue_pct AS revenue_pct,
    total_customers,
    total_overall_revenue
FROM temp_pareto_analysis
WHERE cumulative_revenue_pct >= 80
ORDER BY cumulative_revenue_pct ASC
LIMIT 1;
```
</details>

<img src="https://github.com/user-attachments/assets/2d335656-af33-4919-8961-e0fcaec34a5e" width="580" />

The Pareto principle (80/20 rule) typically suggests that 80% of revenue comes from 20% of customers. Here, 80% of revenue comes from 48.85% of customers, indicating a less concentrated distribution.

### 7. Key Findings & Recommendations --> [Stakeholder Presentation](https://docs.google.com/presentation/d/1BANIRmxoFIX8FTlIWr5cwrdrE1XaBONxXXgfg82vAS0/edit?usp=sharing)
### 8. Tableau Dashboard --> [Olist E-Commerce Performance and Customers Insights](https://public.tableau.com/app/profile/ying.zhang2739/viz/OlistE-CommercePerformanceandCustomersInsights/KPIsSummary)










