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
/* 🎵 MUSIC DATABASE ANALYTICS PROJECT – BUSINESS INSIGHT QUERIES */

/* =========================================================
Q1: Revenue concentration risk (Customer dependency analysis)

Business Question:
Do a small percentage of customers generate most of the revenue?

Outcome:
Identifies whether platform revenue depends on top customers (Pareto 80/20 rule).

Your Contribution:
Applied aggregation + window functions to measure revenue distribution across customer segments.
========================================================= */

WITH customer_revenue AS (
    SELECT 
        customer_id,
        SUM(total) AS total_spent
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


/* =========================================================
Q2: Top revenue-generating artists

Business Question:
Which artists contribute most to platform revenue?

Outcome:
Helps marketing team focus promotions and recommendations on high-value artists.

Your Contribution:
Built revenue model using joins across invoice, track, album, and artist tables.
========================================================= */

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


/* =========================================================
Q3: Country-wise revenue efficiency

Business Question:
Which countries generate highest revenue per customer?

Outcome:
Helps optimize marketing budget and regional expansion strategy.

Your Contribution:
Created KPI (Revenue per Customer) using aggregation and grouping.
========================================================= */

SELECT 
    c.country,
    SUM(i.total) AS total_revenue,
    COUNT(DISTINCT c.customer_id) AS total_customers,
    SUM(i.total) * 1.0 / COUNT(DISTINCT c.customer_id) AS revenue_per_customer
FROM customer c
JOIN invoice i ON c.customer_id = i.customer_id
GROUP BY c.country
ORDER BY revenue_per_customer DESC;


/* =========================================================
Q4: Song engagement analysis (Recommendation system insight)

Business Question:
Which songs have highest user engagement?

Outcome:
Used to improve recommendation engine and playlist ranking.

Your Contribution:
Built engagement KPI using total plays vs unique listeners.
========================================================= */

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


/* =========================================================
Q5: Monthly customer retention trend

Business Question:
Is user engagement increasing or decreasing over time?

Outcome:
Helps track platform growth and customer retention performance.

Your Contribution:
Performed time-series aggregation using monthly invoice analysis.
========================================================= */

SELECT 
    DATE_TRUNC('month', invoice_date) AS month,
    COUNT(DISTINCT customer_id) AS active_customers
FROM invoice
GROUP BY DATE_TRUNC('month', invoice_date)
ORDER BY month;



---

## 🧰 Tools & Technologies
- PostgreSQL 🐘  
- SQL (Joins, Aggregations, Window Functions)  
- Data Analysis  
- Business Intelligence Thinking  

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
