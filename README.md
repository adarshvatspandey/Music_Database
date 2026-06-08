# 🎵 Music Streaming Analytics & Business Insights using PostgreSQL

## 📌 Project Overview
This project is a SQL-based data analysis of a music streaming platform (similar to Spotify). It explores user behavior, artist performance, revenue distribution, and geographical trends using PostgreSQL.

The main objective is to convert raw transactional music data into meaningful business insights that can support decision-making in marketing, product strategy, and recommendation systems.
![image](https://github.com/adarshvatspandey/Music_Database/blob/bab9cad58af41b6e6e9fba031e470f4716b985a0/image.jpeg)
![image](https://github.com/adarshvatspandey/Music_Database/blob/bab9cad58af41b6e6e9fba031e470f4716b985a0/ERD%20ForDatabase.png)
---

## 🧠 Project Objective
The goal of this project is to:
- Analyze customer listening behavior
- Identify top-performing artists and songs
- Understand revenue contribution across users and countries
- Measure user engagement and retention patterns
- Support business decisions using data-driven insights

---

## 🗂️ Dataset Description
The project is built using relational music streaming data consisting of:

- 👤 Customers → user information and location
- 🎵 Tracks → song details and metadata
- 🎤 Artists → music creators
- 📀 Albums → grouped songs
- 💳 Invoices → customer purchases/transactions
- 📄 Invoice Lines → detailed song-level transactions
- 🌍 Genres → classification of music types

---

## 🧠 Business Story (Insight-Driven Analysis)

Instead of only analyzing tables, this project focuses on **business storytelling through data**.

---

## 💰 Revenue Insight: “Power Users Drive the Business”

The analysis shows that a small percentage of customers generate the majority of revenue.

👉 This indicates strong dependency on high-value customers.

**Business Outcome:**
- Focus on VIP customer retention  
- Build loyalty programs  
- Reduce churn among top spenders  

---

## 🎤 Artist Insight: “Hit Artists Dominate Revenue”

A few top artists contribute most of the platform’s revenue, while many others contribute very little.

👉 This shows a highly concentrated content ecosystem.

**Business Outcome:**
- Promote top artists in recommendations  
- Optimize homepage content strategy  
- Invest in popular artist collaborations  

---

## 🌍 Geography Insight: “Unequal Market Value”

Revenue varies significantly across countries, with some regions contributing more revenue per customer.

👉 This shows that not all markets are equally profitable.

**Business Outcome:**
- Focus marketing on high-value countries  
- Expand in underperforming but potential markets  
- Localize content strategy  

---

## 🎧 Engagement Insight: “Some Songs Drive Repeat Listening”

Certain songs show significantly higher replay rates and engagement scores.

👉 This indicates strong user preference and content stickiness.

**Business Outcome:**
- Improve recommendation engine  
- Promote high-engagement songs  
- Identify viral content early  

---

## 📈 Retention Insight: “User Activity Fluctuates Over Time”

Monthly user activity is not stable and changes over time.

👉 This highlights the importance of retention strategies.

**Business Outcome:**
- Build re-engagement campaigns  
- Improve personalized playlists  
- Track user lifecycle behavior
---
# 🎵 Music Streaming Analytics & Business Insights using PostgreSQL

---
The project demonstrates how SQL can be used not just for querying data, but for **business decision-making and storytelling**.

---

# 🧠 Business Problem
The platform wants to understand:
- Who are the most valuable customers?
- Which artists and songs drive revenue?
- Which countries are most profitable?
- How is user engagement distributed?
- Is revenue dependent on a small group of users?

---

# 🎵 Music Streaming Analytics & Business Insights using PostgreSQL

## 📌 Project Overview
This project analyzes a music streaming platform dataset using PostgreSQL to uncover insights about customer behavior, artist performance, revenue generation, geographical trends, and user engagement. The objective is to transform raw transactional data into actionable business insights that can help improve marketing strategies, customer retention, and content recommendations.

---

## 🎯 Business Objective
The music streaming platform wants to answer the following key questions:

- Who are the most valuable customers?
- Which artists generate the highest revenue?
- Which countries contribute the most to business growth?
- Which songs drive the highest engagement?
- How does customer activity change over time?

By answering these questions, the business can make data-driven decisions to improve revenue, user retention, and platform growth.

---

## 💰 Revenue Concentration Analysis

### Business Question
Do a small percentage of customers generate most of the platform revenue?

### SQL Query

```sql
WITH customer_revenue AS (
    SELECT customer_id, SUM(total) AS total_spent
    FROM invoice
    GROUP BY customer_id
),
ranked AS (
    SELECT *,
           NTILE(10) OVER (ORDER BY total_spent DESC) AS decile
    FROM customer_revenue
)
SELECT 
    SUM(CASE WHEN decile = 1 THEN total_spent ELSE 0 END) * 100.0 /
    SUM(total_spent) AS top_10_percent_revenue_share
FROM ranked;
```

### 📊 Business Insight
The analysis reveals whether revenue is concentrated among a small group of high-value customers.

### 🎯 Business Outcome
- Identify VIP customers
- Create loyalty programs
- Improve customer retention strategies
- Reduce dependency on customer acquisition

---

## 🎤 Artist Revenue Analysis

### Business Question
Which artists contribute the most revenue to the platform?

### SQL Query

```sql
SELECT 
    ar.name AS artist_name,
    SUM(il.unit_price * il.quantity) AS total_revenue
FROM invoice_line il
JOIN track t ON il.track_id = t.track_id
JOIN album al ON t.album_id = al.album_id
JOIN artist ar ON al.artist_id = ar.artist_id
GROUP BY ar.name
ORDER BY total_revenue DESC
LIMIT 10;
```

### 📊 Business Insight
A small number of artists typically generate a large portion of revenue and engagement.

### 🎯 Business Outcome
- Promote top-performing artists
- Improve recommendation algorithms
- Increase artist-based marketing campaigns
- Drive higher user engagement

---

## 🌍 Country Revenue Efficiency Analysis

### Business Question
Which countries generate the highest revenue per customer?

### SQL Query

```sql
SELECT 
    c.country,
    SUM(i.total) AS total_revenue,
    COUNT(DISTINCT c.customer_id) AS total_customers,
    SUM(i.total) * 1.0 / COUNT(DISTINCT c.customer_id) AS revenue_per_customer
FROM customer c
JOIN invoice i ON c.customer_id = i.customer_id
GROUP BY c.country
ORDER BY revenue_per_customer DESC;
```

### 📊 Business Insight
Revenue contribution varies significantly across different countries.

### 🎯 Business Outcome
- Identify high-value markets
- Optimize marketing budgets
- Support international expansion strategies
- Improve regional targeting

---

## 🎧 Song Engagement Analysis

### Business Question
Which songs generate the highest user engagement?

### SQL Query

```sql
SELECT 
    t.name AS track_name,
    COUNT(il.quantity) AS total_plays,
    COUNT(DISTINCT i.customer_id) AS unique_listeners,
    COUNT(il.quantity) * 1.0 / COUNT(DISTINCT i.customer_id) AS engagement_score
FROM invoice_line il
JOIN track t ON il.track_id = t.track_id
JOIN invoice i ON il.invoice_id = i.invoice_id
GROUP BY t.name
ORDER BY engagement_score DESC;
```

### 📊 Business Insight
Certain songs attract significantly higher replay rates and user engagement.

### 🎯 Business Outcome
- Improve recommendation systems
- Optimize playlist creation
- Identify viral content early
- Increase platform stickiness

---

## 📈 Customer Activity Trend Analysis

### Business Question
How does customer activity change over time?

### SQL Query

```sql
SELECT 
    DATE_TRUNC('month', invoice_date) AS month,
    COUNT(DISTINCT customer_id) AS active_customers
FROM invoice
GROUP BY DATE_TRUNC('month', invoice_date)
ORDER BY month;
```

### 📊 Business Insight
Monthly active users fluctuate over time, indicating changes in engagement and retention.

### 🎯 Business Outcome
- Monitor customer retention
- Measure platform growth
- Improve engagement campaigns
- Support strategic planning

---

## 🧠 Key Business Findings

- A small percentage of customers generate a large share of total revenue.
- Top artists drive most platform earnings and engagement.
- Revenue per customer differs significantly by country.
- Some songs create substantially higher engagement than others.
- User activity changes over time and requires continuous retention efforts.

---

## 💼 Business Impact

This project helps transform raw music streaming data into:

- Revenue Optimization Strategies
- Customer Segmentation Analysis
- Artist Performance Tracking
- Geographic Market Intelligence
- Recommendation System Improvements
- User Retention & Engagement Insights

---

## 🛠️ Tools & Technologies

- PostgreSQL
- SQL
- Joins
- Aggregations
- Window Functions
- Common Table Expressions (CTEs)
- Business Intelligence & Data Analytics

---

## 🏁 Conclusion

This project demonstrates how SQL can be used to solve real business problems in the music streaming industry. Through data analysis and business storytelling, it identifies key drivers of revenue, engagement, and customer behavior, enabling smarter strategic decisions and sustainable business growth.

---

## 🚀 Project Impact
This project demonstrates how raw music streaming data can be transformed into:

- 💰 Revenue optimization strategies  
- 👥 Customer segmentation (VIP vs Regular users)  
- 🎤 Artist performance tracking  
- 🌍 Market expansion strategy  
- 🎧 Recommendation system improvement  
- 📈 User retention analytics  

---

## 🏁 Conclusion
This project is not just about SQL queries—it is a **business intelligence case study**. It shows how data can uncover hidden patterns in customer behavior, revenue distribution, and content performance to drive smarter business decisions in a music streaming platform.
