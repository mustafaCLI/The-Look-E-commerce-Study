# The-Look-E-commerce Case Study (SQL)
The ability to analyze data with a cloud-based data warehouse and analytics platform such as 𝐁𝐢𝐠𝐐𝐮𝐞𝐫𝐲 is a crucial skill to have as a Data Analyst. In this project I answered 15 important Business Questions with precise inshights.
![online-fashion-shopping-with-laptop_LINKEDIN](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/43d5a0b2-b8e4-472d-b5ff-577b552c4107)
# Objective (15 Business Questions)
To provide Important Inshights (Key Answers) for crucial decisions by the Adminstrator of a fictitious eCommerce clothing site.

1. What are the top 5 Countries having maximum customers?
2. Show the all orders placed with respect to customers by Countries?
3. What are the Best Selling Items with quantity and revenue?
4. Show the sales revenue of all product by country in descending order?
5. Top 10 Customers by Average Order Price?
6. Top 5 Highest Sold Products Monthly (Quantity)?
7. Top 5 Least Sold Products Monthly & Yearly?
8. Show the Customer's Gender by top countries?
9. Show the Customer's Age Group with countries?
10. What are the most canceled and returned product/brand?
11. What are the least canceled and returned product-category?
12. What marketing channels are doing well?
13. Show the 10 customer ids and emails with the largest total overall purchase?
14. How many Customers ordered only once all time?
15. If count > 20,000 who ordered only once then provide customer's count by top 10 country?
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

# Case Sutdy (15 Answers)
## Question 1. Show the top 5 Countries having maximum customers
```
SELECT country, COUNT(distinct id) AS TotalCustomers
FROM bigquery-public-data.thelook_ecommerce.users
GROUP BY country
ORDER BY TotalCustomers DESC
LIMIT 5;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/fa006d5e-04c0-4c5b-a1f6-d5fa950a6717)

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

## 4. Show the sales revenue of all product by country in descending order?
```sql
 SELECT u.country as Country, round(sum(num_of_item*sale_price),1) as Revenue
    FROM bigquery-public-data.thelook_ecommerce.order_items as oi
    left join bigquery-public-data.thelook_ecommerce.orders as o
    on oi.order_id = o.order_id
    left join  bigquery-public-data.thelook_ecommerce.users as u
    on oi.user_id = u.id
    group by Country
    order by Revenue desc
    limit 16;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/bfa3587b-01cb-43b0-bcd4-7b7bb21ee4fa)

## Insight

## 5. Top 10 Customers by Average Order Price?
```
 with Users as
    (SELECT u.first_name||last_name||email as User, 
    round(avg(num_of_item*sale_price),1) 
    as    AvgOrderPrice
    FROM bigquery-public-data.thelook_ecommerce.order_items as oi
    left join bigquery-public-data.thelook_ecommerce.orders as o
    on oi.order_id = o.order_id
    left join  bigquery-public-data.thelook_ecommerce.users as u
    on oi.user_id = u.id
    group by User
    order by AvgOrderPrice desc
    limit 10)
    select left(User,5) as Name, AvgOrderPrice
    from Users;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/75f35e09-5fad-4277-b52f-d54225482333)

## Insight


## 6. Top 5 Highest Sold Products Monthly (Quantity)?

```
select sum(o.num_of_item) as Sold,
    format_date('%B',o.created_at) as Month
    from bigquery-public-data.thelook_ecommerce.orders as o
    group by Month
    order by Sold DESC
    limit 5;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/02ce5289-696e-409e-8922-d479251b9e1e)

## Insight


## 7. Top 5 Least Sold Products Monthly & Yearly?
```
select sum(o.num_of_item) as Sold,
    format_date("%Y",o.created_at) as Year
    from bigquery-public-data.thelook_ecommerce.orders as o
    group by 2
    order by 1 asc
    limit 5;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/ed1977c3-89e8-46f1-9def-91afd281b6f0)

## 8. Show the Customer's Gender by top countries?
```
select country as Country,
    sum(case when gender = 'M' then 1  else null end) as Male,
    sum(case when gender = 'F' then 1  else null end) as Female
    from bigquery-public-data.thelook_ecommerce.users
    group by 1
    order by 2 desc
    limit 10;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/ad829842-768e-46ec-843b-d182fae48495)

## 9. Show the Customer's Age Group by Country?
```
select
    case 
    when u.age < 12 then 'Kids'  
    when u.age between 12 and 20 then 'Teenager'
    when u.age between 20 and 40 then 'young_adult'
    when u.age between 40 and 60 then 'adult'
    else 'Old' end as Age_Group,
    count(o.num_of_item) as Orders
    from bigquery-public-data.thelook_ecommerce.users as u
    right join bigquery-public-data.thelook_ecommerce.orders as o
    on o.user_id = u.id
    group by 1
    order by 2 desc
    limit 10;
```
    ![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/d6ea8666-7c93-4d46-a980-2da2aa56f8e7)
    
## 10. What are the most canceled and returned product/brand?
    
```
  select p.brand, count(distinct p.id) as Returned
    from bigquery-public-data.thelook_ecommerce.order_items as oi 
    left join bigquery-public-data.thelook_ecommerce.products as p
    on oi.product_id = p.id
    where oi.status in ('Returned')
    group by 1
    order by 2 desc
    limit 5;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/a078d5d1-6d1f-481a-bde3-f9064f00ea10)

## 11. What are the least canceled and returned product-category?
```
select p.category, count(distinct p.id) as Returned
    from bigquery-public-data.thelook_ecommerce.order_items as oi 
    left join bigquery-public-data.thelook_ecommerce.products as p
    on oi.product_id = p.id
    where oi.status in ('Returned')
    group by 1
    order by 2 desc
    limit 5;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/1167ff5a-b406-430e-ba79-65527d52025f)

## 12. What marketing channels are doing well?

```
select traffic_source as MarktChannel, count(distinct oi.order_id) as Orders 
from bigquery-public-data.thelook_ecommerce.users as u 
left join  bigquery-public-data.thelook_ecommerce.order_items as oi
on  u.id = oi.user_id
where oi.status not in ('Cancelled', 'Returned')
group by 1
order by 2 desc
limit 10;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/1466f85f-2837-43b9-9f6f-fa7bc59613bb)

## 13. Show the 10 customer ids and emails with the largest total overall purchase?

```
select u.email, u.id, round(sum(oi.Total_Price_per_order),1) as TtlPurchased from
(select  order_id, sum(sale_price) as Total_Price_per_order from bigquery-public-data.thelook_ecommerce.order_items 
group by 1) as oi
inner join 
(select order_id, user_id from bigquery-public-data.thelook_ecommerce.orders) as o
on oi.order_id = o.order_id
inner join bigquery-public-data.thelook_ecommerce.users as u
on o.user_id = u.id
group by 1,2
order by 3 desc
limit 10;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/6a465032-87bf-4461-98eb-6693e4991beb)

## 14. How many Customers ordered only once all time?

```
select sum(OrderCnt) as TotalCount from
(select id, count(o.order_id) as OrderCnt
from bigquery-public-data.thelook_ecommerce.users as u
join
bigquery-public-data.thelook_ecommerce.orders as o
on u.id = o.user_id
group by 1
having OrderCnt = 1);
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/a14e30a5-5c41-4a18-bab1-cc8d2c95362b)

## 15. If count > 20,000 who ordered only once then provide customer's count by top 10 country?
    
 ```
select country, sum(orderCnt) as OneTimeOrders, from
(select id, country, count(o.order_id) as OrderCnt
from bigquery-public-data.thelook_ecommerce.users as u
join
bigquery-public-data.thelook_ecommerce.orders as o
on u.id = o.user_id
group by 1,2
having OrderCnt = 1)
group by 1
order by 2 desc
limit 10;
```
![image](https://github.com/mustafaCLI/The-Look-E-commerce-Study/assets/121651184/babea5ea-26be-4a07-bf0c-ead504e4d44d)

