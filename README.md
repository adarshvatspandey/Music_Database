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
- 
/* Q1: Who is the senior most employee based on job title? */

SELECT title, last_name, first_name 
FROM employee
ORDER BY levels DESC
LIMIT 1


/* Q2: Which countries have the most Invoices? */

SELECT COUNT(*) AS c, billing_country 
FROM invoice
GROUP BY billing_country
ORDER BY c DESC


/* Q3: What are top 3 values of total invoice? */

SELECT total 
FROM invoice
ORDER BY total DESC


/* Q4: Which city has the best customers? We would like to throw a promotional Music Festival in the city we made the most money. 
Write a query that returns one city that has the highest sum of invoice totals. 
Return both the city name & sum of all invoice totals */

SELECT billing_city,SUM(total) AS InvoiceTotal
FROM invoice
GROUP BY billing_city
ORDER BY InvoiceTotal DESC
LIMIT 1;


/* Q5: Who is the best customer? The customer who has spent the most money will be declared the best customer. 
Write a query that returns the person who has spent the most money.*/

SELECT customer.customer_id, first_name, last_name, SUM(total) AS total_spending
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
GROUP BY customer.customer_id
ORDER BY total_spending DESC
LIMIT 1;




/* Question Set 2 - Moderate */

/* Q1: Write query to return the email, first name, last name, & Genre of all Rock Music listeners. 
Return your list ordered alphabetically by email starting with A. */

/*Method 1 */

SELECT DISTINCT email,first_name, last_name
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id
WHERE track_id IN(
	SELECT track_id FROM track
	JOIN genre ON track.genre_id = genre.genre_id
	WHERE genre.name LIKE 'Rock'
)
ORDER BY email;


/* Method 2 */

SELECT DISTINCT email AS Email,first_name AS FirstName, last_name AS LastName, genre.name AS Name
FROM customer
JOIN invoice ON invoice.customer_id = customer.customer_id
JOIN invoiceline ON invoiceline.invoice_id = invoice.invoice_id
JOIN track ON track.track_id = invoiceline.track_id
JOIN genre ON genre.genre_id = track.genre_id
WHERE genre.name LIKE 'Rock'
ORDER BY email;


/* Q2: Let's invite the artists who have written the most rock music in our dataset. 
Write a query that returns the Artist name and total track count of the top 10 rock bands. */

SELECT artist.artist_id, artist.name,COUNT(artist.artist_id) AS number_of_songs
FROM track
JOIN album ON album.album_id = track.album_id
JOIN artist ON artist.artist_id = album.artist_id
JOIN genre ON genre.genre_id = track.genre_id
WHERE genre.name LIKE 'Rock'
GROUP BY artist.artist_id
ORDER BY number_of_songs DESC
LIMIT 10;


/* Q3: Return all the track names that have a song length longer than the average song length. 
Return the Name and Milliseconds for each track. Order by the song length with the longest songs listed first. */

SELECT name,miliseconds
FROM track
WHERE miliseconds > (
	SELECT AVG(miliseconds) AS avg_track_length
	FROM track )
ORDER BY miliseconds DESC;




/* Question Set 3 - Advance */

/* Q1: Find how much amount spent by each customer on artists? Write a query to return customer name, artist name and total spent */

/* Steps to Solve: First, find which artist has earned the most according to the InvoiceLines. Now use this artist to find 
which customer spent the most on this artist. For this query, you will need to use the Invoice, InvoiceLine, Track, Customer, 
Album, and Artist tables. Note, this one is tricky because the Total spent in the Invoice table might not be on a single product, 
so you need to use the InvoiceLine table to find out how many of each product was purchased, and then multiply this by the price
for each artist. */

WITH best_selling_artist AS (
	SELECT artist.artist_id AS artist_id, artist.name AS artist_name, SUM(invoice_line.unit_price*invoice_line.quantity) AS total_sales
	FROM invoice_line
	JOIN track ON track.track_id = invoice_line.track_id
	JOIN album ON album.album_id = track.album_id
	JOIN artist ON artist.artist_id = album.artist_id
	GROUP BY 1
	ORDER BY 3 DESC
	LIMIT 1
)
SELECT c.customer_id, c.first_name, c.last_name, bsa.artist_name, SUM(il.unit_price*il.quantity) AS amount_spent
FROM invoice i
JOIN customer c ON c.customer_id = i.customer_id
JOIN invoice_line il ON il.invoice_id = i.invoice_id
JOIN track t ON t.track_id = il.track_id
JOIN album alb ON alb.album_id = t.album_id
JOIN best_selling_artist bsa ON bsa.artist_id = alb.artist_id
GROUP BY 1,2,3,4
ORDER BY 5 DESC;


/* Q2: We want to find out the most popular music Genre for each country. We determine the most popular genre as the genre 
with the highest amount of purchases. Write a query that returns each country along with the top Genre. For countries where 
the maximum number of purchases is shared return all Genres. */

/* Steps to Solve:  There are two parts in question- first most popular music genre and second need data at country level. */

/* Method 1: Using CTE */

WITH popular_genre AS 
(
    SELECT COUNT(invoice_line.quantity) AS purchases, customer.country, genre.name, genre.genre_id, 
	ROW_NUMBER() OVER(PARTITION BY customer.country ORDER BY COUNT(invoice_line.quantity) DESC) AS RowNo 
    FROM invoice_line 
	JOIN invoice ON invoice.invoice_id = invoice_line.invoice_id
	JOIN customer ON customer.customer_id = invoice.customer_id
	JOIN track ON track.track_id = invoice_line.track_id
	JOIN genre ON genre.genre_id = track.genre_id
	GROUP BY 2,3,4
	ORDER BY 2 ASC, 1 DESC
)
SELECT * FROM popular_genre WHERE RowNo <= 1


/* Method 2: : Using Recursive */

WITH RECURSIVE
	sales_per_country AS(
		SELECT COUNT(*) AS purchases_per_genre, customer.country, genre.name, genre.genre_id
		FROM invoice_line
		JOIN invoice ON invoice.invoice_id = invoice_line.invoice_id
		JOIN customer ON customer.customer_id = invoice.customer_id
		JOIN track ON track.track_id = invoice_line.track_id
		JOIN genre ON genre.genre_id = track.genre_id
		GROUP BY 2,3,4
		ORDER BY 2
	),
	max_genre_per_country AS (SELECT MAX(purchases_per_genre) AS max_genre_number, country
		FROM sales_per_country
		GROUP BY 2
		ORDER BY 2)

SELECT sales_per_country.* 
FROM sales_per_country
JOIN max_genre_per_country ON sales_per_country.country = max_genre_per_country.country
WHERE sales_per_country.purchases_per_genre = max_genre_per_country.max_genre_number;


/* Q3: Write a query that determines the customer that has spent the most on music for each country. 
Write a query that returns the country along with the top customer and how much they spent. 
For countries where the top amount spent is shared, provide all customers who spent this amount. */

/* Steps to Solve:  Similar to the above question. There are two parts in question- 
first find the most spent on music for each country and second filter the data for respective customers. */

/* Method 1: using CTE */

WITH Customter_with_country AS (
		SELECT customer.customer_id,first_name,last_name,billing_country,SUM(total) AS total_spending,
	    ROW_NUMBER() OVER(PARTITION BY billing_country ORDER BY SUM(total) DESC) AS RowNo 
		FROM invoice
		JOIN customer ON customer.customer_id = invoice.customer_id
		GROUP BY 1,2,3,4
		ORDER BY 4 ASC,5 DESC)
SELECT * FROM Customter_with_country WHERE RowNo <= 1


/* Method 2: Using Recursive */

WITH RECURSIVE 
	customter_with_country AS (
		SELECT customer.customer_id,first_name,last_name,billing_country,SUM(total) AS total_spending
		FROM invoice
		JOIN customer ON customer.customer_id = invoice.customer_id
		GROUP BY 1,2,3,4
		ORDER BY 2,3 DESC),

	country_max_spending AS(
		SELECT billing_country,MAX(total_spending) AS max_spending
		FROM customter_with_country
		GROUP BY billing_country)

SELECT cc.billing_country, cc.total_spending, cc.first_name, cc.last_name, cc.customer_id
FROM customter_with_country cc
JOIN country_max_spending ms
ON cc.billing_country = ms.billing_country
WHERE cc.total_spending = ms.max_spending
ORDER BY 1;
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


/* source: www.youtube.com/@RishabhMishraOfficial */

/* Thank You :) */
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
