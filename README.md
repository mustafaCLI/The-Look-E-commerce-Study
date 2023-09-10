# The-Look-E-commerce Case Study (SQL)
The ability to analyze data with a cloud-based data warehouse and analytics platform such as 𝐁𝐢𝐠𝐐𝐮𝐞𝐫𝐲 is a crucial skill to have as a Data Analyst. In this project I answered 16 important Business Questions with precise inshights.
![online-fashion-shopping-with-laptop_LINKEDIN](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/43d5a0b2-b8e4-472d-b5ff-577b552c4107)
# Objective (16 Business Questions)
To provide Important Inshights (Key Answers) for crucial decisions by the Adminstrator of a fictitious eCommerce clothing site.

1. What are the top 5 Countries having maximum customers?
2. Show the all orders placed with respect to customers by Countries?
3. What are the Best Selling Items with quantity and revenue?
4. Show the sales revenue of all product by country in descending order?
5. Top 10 Customers by Average Order Price?
6. Top 10 customers with the highest average price per order?
7. Top 5 Highest Sold Products Monthly (Quantity)?
8. Top 5 Least Sold Products Monthly & Yearly?
9. Show the Customers by Country?
10. Show the Customer's Gender by top countries?
11. Show the Customer's Age Group with countries?
12. What are the most canceled and returned product/brand?
13. What are the most canceled and returned product-category?
14. What marketing channels are doing well?
15. Show the 10 customer ids and emails with the largest total overall purchase?
16. Show all Customers Name, Age and Country who ordered only once?

# Overview
The Look E-commerce Case Study (Big Query)
TheLook is a fictitious eCommerce clothing site developed by the Looker team. The dataset contains information about customers, products, orders, logistics, web events and digital marketing campaigns. The contents of this dataset are synthetic, and are provided to industry practitioners for the purpose of product discovery, testing, and evaluation.
This public dataset is hosted in Google BigQuery and is included in BigQuery's 1TB/mo of free tier processing.

# Dataset Info (Source)
1. Dataset ID: bigquery-public-data.thelook_ecommerce
2. Created: Feb 26, 2022, 5:22:02 AM UTC+5:30
3. Default table expiration: Never
4. Last modified: Sep 20, 2022, 1:28:24 PM UTC+5:30
5. Data location: US
6. Description: Fictitious E-Commerce Dataset

# Case Sutdy (16 Answers)
## Question 1. Show the top 5 Countries having maximum customers
```select count(id) as customers, country 
from bigquery-public-data.thelook_ecommerce.users 
group by country order by customers desc limit 5;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/0e66c298-c2ab-495c-b9cd-19f646fe7e93)
## Insights
We have total 16 Countires amongst them China, United States and Brasil having maximum customers which is 70% more than the rest of all.

## Question 2. Show the all orders placed with respect to customers by Countries?
```SQL
WITH users_orders AS (
    SELECT user_id, country, order_id,num_of_item 
    FROM bigquery-public-data.thelook_ecommerce.users AS u
    INNER JOIN bigquery-public-data.thelook_ecommerce.orders AS o
    ON u.id = o.user_id)
SELECT country, COUNT(DISTINCT user_id) AS CUSTOMERS, 
COUNT(order_id) AS ORDERS,
SUM(num_of_item) AS QUANTITY
FROM users_orders
GROUP BY country
ORDER BY  QUANTITY DESC
LIMIT 10;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/7daf990e-e0db-4c86-b5c5-5668b69b28e4)

![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/8aaa3e5b-41b1-4bad-82be-2a58efd6a937)

Chart 1

![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/a33cda61-b6f8-46f4-bc20-096228b47427)

Chart 2
## Insight
There is a linear relationship between No. of Customers and orders. If customer is 1 then order will be 1.56. 

Also Another linear relationship between No. of Customers and product quantities. The equation shows 2.9 quantities are being sold per customers in all top country an average.

## Question 3. What are the Best Selling Brands with quantity and revenue?
``` sql
    SELECT 
    p.brand, sum(num_of_item) as Quantity, 
    ROUND(SUM(sale_price * num_of_item), 1) AS Revenue
    FROM bigquery-public-data.thelook_ecommerce.orders AS o 
    INNER JOIN bigquery-public-data.thelook_ecommerce.order_items AS oi
    ON o.order_id = oi.order_id
    INNER JOIN bigquery-public-data.thelook_ecommerce.products AS p
    ON oi.id = p.id
    WHERE oi.status NOT IN ('Cancelled', 'Returned')
    GROUP BY brand 
    ORDER BY Revenue DESC
    LIMIT 10;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/5c77748b-d9e7-4e89-927e-dd309e467955)

## Insight




