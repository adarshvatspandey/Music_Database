🎵 Music Store Database Project (PostgreSQL)

This project is a Music Store Database Management System developed using PostgreSQL in pgAdmin. It is designed for beginners to understand how relational databases work by managing and analyzing music store data.

Tables Used in the Project

PROJECT DASHBOARD PREVIEW
![image](https://github.com/adarshvatspandey/Music_Database/blob/348033be0220e793ac26bb6ebfeb96798e5b0d3c/Analysis.png)


🎤 Artist
Stores information about music artists such as artist ID and name.

💿 Album
Contains album details including album ID, title, and the associated artist.

🎶 Track
Holds detailed information about each song, including track name, album, genre, duration, and price.

🎧 Genre
Defines categories of music such as rock, pop, jazz, etc.

📀 MediaType
Specifies the format of the track (e.g., MP3, WAV, etc.).

👤 Customer
Contains customer details like name, email, country, and contact information.

🧑‍💼 Employee
Stores information about employees working in the music store, including roles and reporting structure.

🧾 Invoice
Represents customer purchases, including invoice ID, customer ID, billing details, and total amount.

📄 Invoice_Line
Contains detailed line items of each invoice such as track purchased, quantity, and unit price.

📁 Playlist
Stores playlist details created in the system.

🔗 Playlist_Track
Acts as a bridge table connecting playlists and tracks (many-to-many relationship).

Key Objectives of the Project

To understand database normalization and relationships

To practice SQL queries (JOIN, GROUP BY, ORDER BY, etc.)

To analyze customer purchasing behavior

To identify popular tracks, artists, and genres

To explore real-world database structure (Music Store system)

# SQL Music Store Analysis

## 📌 Project Overview

This project analyzes a Music Store database using SQL to answer business-related questions. The queries range from basic data exploration to advanced customer and sales analysis using joins, subqueries, CTEs, and window functions.

---

# Question Set 1 – Easy

## Q1. Who is the senior-most employee based on job title?

### SQL Query

```sql
SELECT title, last_name, first_name
FROM employee
ORDER BY levels DESC
LIMIT 1;
```

### Insight

Identifies the highest-ranking employee in the organization.

---

## Q2. Which countries have the most invoices?

### SQL Query

```sql
SELECT COUNT(*) AS total_invoices,
       billing_country
FROM invoice
GROUP BY billing_country
ORDER BY total_invoices DESC;
```

### Insight

Shows which countries generate the highest number of invoices.

---

## Q3. What are the top 3 invoice totals?

### SQL Query

```sql
SELECT total
FROM invoice
ORDER BY total DESC
LIMIT 3;
```

### Insight

Returns the three highest invoice values.

---

## Q4. Which city generates the highest revenue?

### SQL Query

```sql
SELECT billing_city,
       SUM(total) AS invoice_total
FROM invoice
GROUP BY billing_city
ORDER BY invoice_total DESC
LIMIT 1;
```

### Insight

Identifies the city contributing the most revenue.

---

## Q5. Who is the best customer?

### SQL Query

```sql
SELECT customer.customer_id,
       first_name,
       last_name,
       SUM(total) AS total_spending
FROM customer
JOIN invoice
ON customer.customer_id = invoice.customer_id
GROUP BY customer.customer_id, first_name, last_name
ORDER BY total_spending DESC
LIMIT 1;
```

### Insight

Finds the customer with the highest overall spending.

---

# Question Set 2 – Moderate

## Q1. Find all Rock Music listeners.

### SQL Query

```sql
SELECT DISTINCT email,
       first_name,
       last_name
FROM customer
JOIN invoice
ON customer.customer_id = invoice.customer_id
JOIN invoice_line
ON invoice.invoice_id = invoice_line.invoice_id
WHERE track_id IN (
    SELECT track_id
    FROM track
    JOIN genre
    ON track.genre_id = genre.genre_id
    WHERE genre.name = 'Rock'
)
ORDER BY email;
```

### Insight

Lists customers who purchased Rock music tracks.

---

## Q2. Top 10 Rock artists by number of tracks.

### SQL Query

```sql
SELECT artist.artist_id,
       artist.name,
       COUNT(artist.artist_id) AS number_of_songs
FROM track
JOIN album
ON album.album_id = track.album_id
JOIN artist
ON artist.artist_id = album.artist_id
JOIN genre
ON genre.genre_id = track.genre_id
WHERE genre.name = 'Rock'
GROUP BY artist.artist_id, artist.name
ORDER BY number_of_songs DESC
LIMIT 10;
```

### Insight

Ranks artists based on the number of Rock tracks they have produced.

---

## Q3. Tracks longer than the average song length.

### SQL Query

```sql
SELECT name,
       milliseconds
FROM track
WHERE milliseconds > (
    SELECT AVG(milliseconds)
    FROM track
)
ORDER BY milliseconds DESC;
```

### Insight

Displays songs that are longer than the average track duration.

---

# Question Set 3 – Advanced

## Q1. Find how much each customer spent on the best-selling artist.

### SQL Query

```sql
WITH best_selling_artist AS (
    SELECT artist.artist_id,
           artist.name AS artist_name,
           SUM(invoice_line.unit_price * invoice_line.quantity) AS total_sales
    FROM invoice_line
    JOIN track
    ON track.track_id = invoice_line.track_id
    JOIN album
    ON album.album_id = track.album_id
    JOIN artist
    ON artist.artist_id = album.artist_id
    GROUP BY artist.artist_id, artist.name
    ORDER BY total_sales DESC
    LIMIT 1
)

SELECT c.customer_id,
       c.first_name,
       c.last_name,
       bsa.artist_name,
       SUM(il.unit_price * il.quantity) AS amount_spent
FROM invoice i
JOIN customer c
ON c.customer_id = i.customer_id
JOIN invoice_line il
ON il.invoice_id = i.invoice_id
JOIN track t
ON t.track_id = il.track_id
JOIN album alb
ON alb.album_id = t.album_id
JOIN best_selling_artist bsa
ON bsa.artist_id = alb.artist_id
GROUP BY c.customer_id,
         c.first_name,
         c.last_name,
         bsa.artist_name
ORDER BY amount_spent DESC;
```

### Insight

Shows how much each customer spent on the highest-selling artist.

---

## Q2. Most popular music genre for each country.

### SQL Query

```sql
WITH popular_genre AS (
    SELECT COUNT(invoice_line.quantity) AS purchases,
           customer.country,
           genre.name,
           ROW_NUMBER() OVER(
               PARTITION BY customer.country
               ORDER BY COUNT(invoice_line.quantity) DESC
           ) AS row_no
    FROM invoice_line
    JOIN invoice
    ON invoice.invoice_id = invoice_line.invoice_id
    JOIN customer
    ON customer.customer_id = invoice.customer_id
    JOIN track
    ON track.track_id = invoice_line.track_id
    JOIN genre
    ON genre.genre_id = track.genre_id
    GROUP BY customer.country, genre.name
)

SELECT *
FROM popular_genre
WHERE row_no = 1;
```

### Insight

Determines the most purchased music genre in each country.

---

## Q3. Highest-spending customer in each country.

### SQL Query

```sql
WITH customer_with_country AS (
    SELECT customer.customer_id,
           customer.first_name,
           customer.last_name,
           invoice.billing_country,
           SUM(invoice.total) AS total_spending,
           ROW_NUMBER() OVER(
               PARTITION BY invoice.billing_country
               ORDER BY SUM(invoice.total) DESC
           ) AS row_no
    FROM customer
    JOIN invoice
    ON customer.customer_id = invoice.customer_id
    GROUP BY customer.customer_id,
             customer.first_name,
             customer.last_name,
             invoice.billing_country
)

SELECT *
FROM customer_with_country
WHERE row_no = 1;
```

### Insight

Identifies the top-spending customer in every country.

---

## 🛠️ SQL Concepts Used

* SELECT Statements
* Aggregate Functions (SUM, COUNT, AVG)
* GROUP BY & ORDER BY
* INNER JOIN
* Subqueries
* Common Table Expressions (CTEs)
* Window Functions
* Business Analytics Queries
* Customer & Sales Analysis

## 📊 Key Business Insights

* Identified the most valuable customers.
* Determined top-performing cities and countries.
* Analyzed music preferences by genre and location.
* Evaluated artist performance and revenue contribution.
* Discovered customer spending behavior across markets.



