🎵 Music Store Database Project (PostgreSQL)

This project is a Music Store Database Management System developed using PostgreSQL in pgAdmin. It is designed for beginners to understand how relational databases work by managing and analyzing music store data.

Tables Used in the Project

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


