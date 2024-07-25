# Website Analytics


![web_analytics_img](https://github.com/user-attachments/assets/4012c1a9-29f1-4ec6-aec6-861a4b6a972f)


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

![a1_1](https://github.com/user-attachments/assets/a937f2dc-77c8-4b66-8997-4816e21cb332)

![a1_2](https://github.com/user-attachments/assets/c00d7c51-24e3-41d5-a9ea-b56aabfde6e9)


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

![a2_1](https://github.com/user-attachments/assets/3c68cee9-1fa1-403b-9782-dce53a35b697)


![a2_2](https://github.com/user-attachments/assets/d0d3dbed-43bc-4fda-9a4b-eac96507a6ee)


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

![a3_1](https://github.com/user-attachments/assets/b03490b1-b38f-4b95-9e7e-e3e046ac1fd3)


![a3_2](https://github.com/user-attachments/assets/ff87fed2-0ae1-49d2-979a-cf84bb2de0ec)


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

![a4_1](https://github.com/user-attachments/assets/ee9cef50-6685-47bb-96e8-738e752c7c2f)

![a4_2](https://github.com/user-attachments/assets/28693f95-2e49-4557-aea7-835786c63a2b)


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

![a5_1](https://github.com/user-attachments/assets/6e970e1f-4a06-4063-bf35-8f1d31bd8e82)

![a5_2](https://github.com/user-attachments/assets/1fc48479-a3a8-43bc-8ece-5eec4d257135)


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

![a6_1](https://github.com/user-attachments/assets/cf25d831-c4b1-43d2-b927-417976561ff0)

![a6_2](https://github.com/user-attachments/assets/5b4ca072-9900-4495-be52-a455d53f0300)


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

![a7_1](https://github.com/user-attachments/assets/3f3623b2-27a7-4c66-882d-08cdc9ff7046)

![a7_2](https://github.com/user-attachments/assets/4266eb6c-f637-45b1-923b-6e532a9c7ed4)


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

![a8_1](https://github.com/user-attachments/assets/7366ad32-4491-4c45-b814-1beea905ef94)

![a8_2](https://github.com/user-attachments/assets/65fb4eb8-e7dc-4f44-8f31-cea799168066)


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

![a9_1](https://github.com/user-attachments/assets/84492c20-0742-4134-a72d-fa009d06a95e)

![a9_2](https://github.com/user-attachments/assets/7f2ff1fe-74a1-4d83-97fa-d9470e6054f7)


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

![a10_1](https://github.com/user-attachments/assets/935799b1-38a9-46da-a2e9-385486a97272)

![a10_2](https://github.com/user-attachments/assets/85fbb67a-f93d-48f0-a011-ec26fd5aeac8)


***In the year 2020, T-Shirt product category has the highest bounce rate with 63%. This is a major issue of concern, since the sales generated from the T-Shirt category are also very less***

***The Digital Marketing team has to review those T-Shirt product pages and rectify the issues which are responsible for increasing the bounce rates and bringing down the sales revenue***

