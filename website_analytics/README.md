# Website Analytics


![web_analytics](https://github.com/user-attachments/assets/de7e400a-9fbf-4e79-b0ce-0283646d7368)



## Dataset Overview and Problem Statement:

The given dataset belongs to an e-commerce website which sells clothing merchandise on its online portal. The data is imported from the website's Google Analytics account. The dataset contains information related to web marketing analytics. It contains information such as product category, source channel, pageviews, sessions, bounce rate, transactions, revenue, etc that give key insights into the website's performance.


The Digital Marketing Team wants to analyse this data to understand the website's performance and also to extract meaningful insights. This
analysis would help the team to come up with effective marketing strategies and better plan its next digital campaigns.


## Project Description:   

As a Data Analyst, I have been given a set of questions which would help in understanding the website's performance. I have used PostgreSQL database to analyse the data.


## Questions and Solutions:

##### Note: I have taken the screenshot of each answer from PostgreSQL's pgAdmin platform and attached the same here under Answer section.


**Q1. What are the most popular top 3 product categories in terms of i) quantity sold and ii) revenue?**

```
SELECT product_category, SUM(quantity_sold) as total_quantity_sold, 
SUM(revenue) as total_revenue 
FROM website_analytics_data
GROUP BY product_category
ORDER BY total_revenue DESC
LIMIT 3;

```

**Answer:**

![a1_1](https://github.com/user-attachments/assets/df4203d6-dd52-44a5-b1ca-61a68ce01a72)


![a1_2](https://github.com/user-attachments/assets/7786eb0f-9005-426d-b38a-75b11e99b3b1)



***Casual, Semi Formal, and Formal are the most popular product categories with higher quantities sold and revenue generated***


**Q2. What are the major traffic sources to the website? What is the percentage share of each channel?**

```
WITH channel_traffic as 
(SELECT channel, SUM(page_views) as total_page_views, SUM(users) as total_users 
FROM website_analytics_data
GROUP BY channel
ORDER BY total_page_views DESC)

SELECT *, ROUND((total_page_views/SUM(total_page_views) OVER())*100, 2) as page_views_percentage, 
ROUND((total_users/SUM(total_users) OVER())*100, 2) as total_users_percentage
FROM channel_traffic
ORDER BY page_views_percentage DESC;

```

**Answer:**

![a2_1](https://github.com/user-attachments/assets/e941a137-8d6c-498f-94ec-1a817ea48c33)



![a2_2](https://github.com/user-attachments/assets/218d34b1-fe36-46ae-b03b-4c1e91d917d6)



***Out of 4 channels, Organic search and PPC constitute to almost 75% of total page views***

**Q3. What are the number of sessions for each browser and device and what are the bounce rates for each?**

```
SELECT device, browser, SUM(sessions) as total_sessions, 
ROUND(AVG(bounce_rate), 2) as avg_bounce_rate
FROM website_analytics_data
GROUP BY device, browser
ORDER BY total_sessions DESC;

```

**Answer:**

![a3_1](https://github.com/user-attachments/assets/ffc6a2f3-d955-490b-8d58-b8035151a057)



![a3_2](https://github.com/user-attachments/assets/47d2dad0-d310-4ee6-9afc-765e835f77f0)



***Users have spent more number of sessions on the following device-browser combination: Mobile-Chrome being the most popular choice, followed by Desktop-Firefox, Desktop-Chrome, and Mobile-Safari***

***The Mobile-Firefox and Desktop-Safari register the highest bounce rates with 75%, while Desktop-Chrome and Tablet-Chrome has the lowest bounce rates. This also implies that Chrome is the browser with lower bounce rates compared to others.***



**Q4. Which device generated the highest number of transactions and revenue? What is the percentage share of each device?**

```
WITH device_transactions_revenue as
(SELECT device, SUM(transactions) as total_transactions, SUM(revenue) as total_revenue
FROM website_analytics_data
GROUP BY device
ORDER BY total_revenue DESC)

SELECT *, ROUND((total_revenue/SUM(total_revenue) OVER())*100, 2) as total_revenue_percentage 
FROM device_transactions_revenue
ORDER BY total_revenue_percentage DESC;

```

**Answer:**

![a4_1](https://github.com/user-attachments/assets/fcc93c94-03ef-4ee4-93a2-dab7903b0b9d)


![a4_2](https://github.com/user-attachments/assets/465448b6-5541-4da3-8f6d-c1d206aa3aa9)



***Mobile device generated the highest revenue which constitutes to around 46% of the total revenue***


**Q5. Explore the channel type and quality of traffic**

```
WITH channel_analysis as 
(SELECT channel, SUM(page_views) as total_page_views, SUM(sessions) as total_sessions,
SUM(transactions) as total_transactions, SUM(revenue) as total_revenue
FROM website_analytics_data
GROUP BY channel
ORDER BY total_revenue DESC)

SELECT  *, ROUND((total_revenue/SUM(total_revenue) OVER())*100, 2) as total_revenue_percentage 
FROM channel_analysis
ORDER BY total_revenue_percentage DESC;

```

**Answer:**

![a5_1](https://github.com/user-attachments/assets/c01453f5-46f7-4869-a7b8-2f5135ef1f6b)

![a5_2](https://github.com/user-attachments/assets/1143a57e-001d-41f5-865d-6d4829c97c74)



***Organic search is by far the most popular and profitable marketing channel of all the channels. It generated a massive 63.5% of total revenue alone***

***This means that the webpages are ranking highly on the Organic search for the business keywords which implies that the SEO of the website is pretty good***



**Q6. Calculate the total revenue generated and total quantity sold per year for each product category and their respective cumulative sum**

```
WITH product_category_quantity_revenue as
(SELECT year, product_category, SUM(quantity_sold) as total_quantity_sold, SUM(revenue) as total_revenue
FROM website_analytics_data
GROUP BY year, product_category
ORDER BY year, total_revenue DESC)

SELECT *, SUM(total_revenue) OVER(PARTITION BY year ORDER by total_revenue DESC) as cumulative_sum 
FROM product_category_quantity_revenue;

```

**Answer:**

![a6_1](https://github.com/user-attachments/assets/9e9aa141-05b9-40b1-add1-b010ea10b9a9)


![a6_2](https://github.com/user-attachments/assets/e41ea91a-81c2-4612-89f1-0841c2db6ad1)


***Product categories – ‘Casual’ and ‘Semi Formal’ generated most of the revenue for both the  years***

***Revenue generated from each product category in 2020 is way more than that of 2019. Revenue generated in the year 2020 is almost 3 times that of revenue from 2019***



**Q7. In which year and month are the most new users visited the website (top 7 months)**

```
SELECT year, month,  SUM(new_users) as total_new_users
FROM website_analytics_data
GROUP BY year, month
ORDER BY total_new_users DESC
LIMIT 7;

```

**Answer:**

![a7_1](https://github.com/user-attachments/assets/41c6461d-af76-4ad4-8a71-99a96237aea5)


![a7_2](https://github.com/user-attachments/assets/da9c25c7-af58-4f77-92cb-ef7599593701)


***In 2019, the last quarter (months 10, 11, 12) have attracted most number of new users  - may be because of the year end sale and offers***

***In 2020, months 5, 6, and 7 (that is second quarter to begining of 3rd quarter) have the most new users***



**Q8. For each device, which is the most popular/used browser?**

```
WITH device_most_used_browser as 
(SELECT device, browser, SUM(page_views) as total_page_views 
FROM website_analytics_data
GROUP BY device, browser),

browser_rank as 
(SELECT *, RANK() OVER(PARTITION BY device ORDER BY total_page_views DESC) as rnk
FROM device_most_used_browser)

SELECT * FROM browser_rank
WHERE rnk = 1
ORDER BY total_page_views DESC;

```

**Answer:**

![a8_1](https://github.com/user-attachments/assets/52fa058e-9b34-4aa9-be9b-8ed80db0acdf)


![a8_2](https://github.com/user-attachments/assets/81bf9110-9b27-40c3-9520-4ba37506a41c)



***Mobile-Chrome is the most popular device-browser combination, followed by Desktop-Firefox***


**Q9. In each year, which is the least performing product category in terms of revenue generated and quantity sold?**

```
WITH product_category_quantity_revenue as 
(SELECT year, product_category, SUM(revenue) as total_revenue, SUM (quantity_sold) as total_quantity_sold
FROM website_analytics_data
GROUP BY year, product_category
ORDER BY total_revenue DESC),

yr_rnk as
(SELECT *, RANK() OVER(PARTITION BY year ORDER BY total_revenue ASC) as rnk
FROM product_category_quantity_revenue)

Select year, product_category, total_quantity_sold, total_revenue 
FROM yr_rnk 
WHERE rnk  = 1;

```

**Answer:**

![a9_1](https://github.com/user-attachments/assets/8f775c19-9d58-4eb0-ac6a-95ffa4599df1)


![a9_2](https://github.com/user-attachments/assets/03a86bd8-3ac4-4887-85aa-b6a13da3d395)



***In both the years, T-Shirt is the least selling product category***


**Q10. What are the top 3 product categories with the highest average bounce rate in 2020?**

```
SELECT year, product_category, ROUND(AVG(bounce_rate), 2) as avg_bounce_rate
FROM website_analytics_data
WHERE year = 2020 
GROUP BY year, product_category
ORDER BY avg_bounce_rate DESC
LIMIT 3;

```

**Answer:**

![a10_1](https://github.com/user-attachments/assets/71612615-64c7-4d6b-a620-13677e8084a1)


![a10_2](https://github.com/user-attachments/assets/48c24a21-3e0d-41d3-a43a-f045ca5d771e)



***In the year 2020, T-Shirt product category has the highest bounce rate with 63%. This is a major issue of concern, since the sales generated from the T-Shirt category are also very less***

***The Digital Marketing team has to review those T-Shirt product pages and rectify the issues which are responsible for increasing the bounce rates and bringing down the sales revenue***

