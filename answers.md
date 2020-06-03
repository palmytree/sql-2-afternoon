## Joins

1. Get all invoices where the `unit_price` on the `invoice_line` is greater than \$0.99.
```sql
SELECT * FROM invoice i
JOIN invoice_line l
ON i.invoice_id = l.invoice_id
WHERE l.unit_price > 0.99;
```
2. Get the `invoice_date`, customer `first_name` and `last_name`, and `total` from all invoices.
```sql
SELECT c.first_name, c.last_name, i.invoice_date, i.total
FROM invoice i
JOIN customer c ON i.customer_id = c.customer_id;
```
3. Get the customer `first_name` and `last_name` and the support rep's `first_name` and `last_name` from all customers.
```sql
SELECT (c.first_name, c.last_name) AS Customer_Name, (e.first_name, e.last_name) AS Rep_Name
FROM customer c
JOIN employee e ON c.support_rep_id = e.employee_id;
```
4. Get the album `title` and the artist `name` from all albums.
```sql
SELECT m.title, a.name AS artist
FROM album m
JOIN artist a on m.artist_id = a.artist_id; 
```
5. Get all playlist_track track_ids where the playlist `name` is Music.
```sql
SELECT track_id
FROM playlist_track
WHERE playlist_id IN (
  SELECT playlist_id
  FROM playlist
  WHERE name = 'Music'
  );
```
6. Get all track `name`s for `playlist_id` 5.
```sql
SELECT name
FROM track
WHERE track_id IN (
  SELECT track_id
  FROM playlist_track
  WHERE playlist_id = 5
  );
```
7. Get all track `name`s and the playlist `name` that they're on ( 2 joins ).
```sql
SELECT p.name AS playlist, t.name AS track
FROM playlist p
JOIN playlist_track pt ON pt.playlist_id = p.playlist_id
JOIN track t ON t.track_id = pt.track_id;
```
8. Get all track `name`s and album `title`s that are the genre `Alternative & Punk` ( 2 joins ).
```sql
SELECT a.title AS album, t.name AS track, g.name AS genre
FROM album a
JOIN track t ON t.album_id = a.album_id
JOIN genre g ON t.genre_id = g.genre_id
WHERE g.name = 'Alternative & Punk';
```

### Black Diamond

- Get all tracks on the playlist(s) called Music and show their name, genre name, album name, and artist name.
  - At least 5 joins.
```sql
SELECT 
p.name AS playlist, 
t.name AS track, 
m.title AS album,
a.name AS artist,
g.name AS genre
FROM playlist p
JOIN playlist_track pt ON pt.playlist_id = p.playlist_id
JOIN track t ON t.track_id = pt.track_id
JOIN album m ON t.album_id = m.album_id
JOIN artist a ON m.artist_id = a.artist_id
JOIN genre g ON g.genre_id = t.genre_id
WHERE p.name = 'Music';
```

## Nested Queries

1. Get all invoices where the `unit_price` on the `invoice_line` is greater than \$0.99.

```sql
SELECT * FROM invoice
WHERE invoice_id IN (
  SELECT invoice_id
  FROM invoice_line
  WHERE unit_price > 0.99);
```

2. Get all playlist tracks where the playlist name is Music.

```sql
SELECT * FROM playlist_track
WHERE playlist_id IN (
  SELECT playlist_id
  FROM playlist
  WHERE name = 'Music');
```

3. Get all track names for `playlist_id` 5.

```sql
SELECT name FROM track
WHERE track_id IN (
  SELECT track_id
  FROM playlist_track
  WHERE playlist_id = 5);
```

4. Get all tracks where the `genre` is Comedy.

```sql
SELECT * FROM track
WHERE genre_id IN (
  SELECT genre_id
  FROM genre
  WHERE name = 'Comedy');
```

5. Get all tracks where the `album` is Fireball.

```sql
SELECT * FROM track
WHERE album_id IN (
  SELECT album_id
  FROM album
  WHERE title = 'Fireball');
```

6. Get all tracks for the artist Queen ( 2 nested subqueries ).

```sql
SELECT * FROM track
WHERE album_id IN (
  SELECT album_id
  FROM album
  WHERE artist_id IN (
    SELECT artist_id
    FROM artist
    WHERE name = 'Queen'
  	)
 	);
```

## Updating Rows

1. Find all customers with fax numbers and set those numbers to `null`.

```sql
UPDATE customer
SET fax = null
WHERE fax IS NOT null;
```

2. Find all customers with no company (null) and set their company to `"Self"`.

```sql
UPDATE customer
SET company = 'Self'
WHERE company IS null;
```

3. Find the customer `Julia Barnett` and change her last name to `Thompson`.

```sql
UPDATE customer
SET last_name = 'Thompson'
WHERE first_name = 'Julia'
AND last_name = 'Barnett'
RETURNING *;
```

4. Find the customer with this email `luisrojas@yahoo.cl` and change his support rep to `4`.

```sql
UPDATE customer
SET support_rep_id = 4
WHERE email = 'luisrojas@yahoo.cl'
RETURNING *;
```

5. Find all tracks that are the genre `Metal` and have no composer. Set the composer to `"The darkness around us"`.

```sql
UPDATE track
SET composer = 'The darkness around us'
WHERE composer IS null
AND genre_id IN (
  SELECT genre_id
  FROM genre
  WHERE name = 'Metal')
RETURNING *;
```

6. Refresh your page to remove all database changes.

## Group by

1. Find a count of how many tracks there are per genre. Display the genre name with the count.
```sql
SELECT g.name AS genre, count(t.name)
FROM genre g
JOIN track t ON t.genre_id = g.genre_id
GROUP BY g.name
ORDER BY count DESC;
```
2. Find a count of how many tracks are the `"Pop"` genre and how many tracks are the `"Rock"` genre.
```sql
SELECT g.name AS genre, count(t.name)
FROM genre g
JOIN track t ON t.genre_id = g.genre_id
WHERE g.name IN ('Pop', 'Rock')
GROUP BY g.name;
```
3. Find a list of all artists and how many albums they have.
```sql
SELECT a.name AS artist, count(m.album_id)
FROM artist a
JOIN album m ON m.artist_id = a.artist_id
GROUP BY a.name
ORDER BY count DESC;
```

## Distinct

1. From the `track` table find a unique list of all `composer`s.
```sql
SELECT DISTINCT composer FROM track;
```
2. From the `invoice` table find a unique list of all `billing_postal_code`s.
```sql
SELECT DISTINCT billing_postal_code FROM invoice;
```
3. From the `customer` table find a unique list of all `company`
```sql
SELECT DISTINCT company FROM customer;
```

## Delete

2. Delete all `'bronze'` entries from the table.
```sql
DELETE FROM practice_delete
WHERE type = 'bronze'
RETURNING *;
```
3. Delete all `'silver'` entries from the table.
```sql
DELETE FROM practice_delete
WHERE type = 'silver'
RETURNING *;
```
4. Delete all entries whose value is equal to `150`.
```sql
DELETE FROM practice_delete
WHERE value = 150
RETURNING *;
```

## eCommerce Sim

### Summary

Let's simulate an e-commerce site. We're going to need users, products, and orders.

- users need a name and an email.
- products need a name and a price
- orders need a ref to product.
- All 3 need primary keys.

### Instructions

- Create 3 tables following the criteria in the summary.
```sql
CREATE TABLE users(
  user_id SERIAL PRIMARY KEY,
  first_name VARCHAR(100),
  last_name VARCHAR(100),
  email VARCHAR(200)
);
CREATE TABLE products(
  product_id SERIAL PRIMARY KEY,
  name VARCHAR(200),
  price DECIMAL(10,2)
);
CREATE TABLE order_line(
  order_line_id SERIAL PRIMARY KEY,
  order_id INT,
  product_id INT REFERENCES products(product_id),
  quantity INT
);
CREATE TABLE orders(
  order_id SERIAL PRIMARY KEY,
  user_id INT REFERENCES users(user_id),
 	order_date DATE
);
ALTER TABLE order_line
ADD CONSTRAINT fk FOREIGN KEY (order_id) REFERENCES orders (order_id);
```
- Add some data to fill up each table.
  - At least 3 users, 3 products, 3 orders.

<details>

<summary> <code> My mockaroo data </code> </summary>

  ```sql
  insert into users (first_name, last_name, email) values ('Clarinda', 'Osipov', 'cosipov0@mozilla.com');
  insert into users (first_name, last_name, email) values ('Delcina', 'Esby', 'desby1@furl.net');
  insert into users (first_name, last_name, email) values ('Drusie', 'Tremayle', 'dtremayle2@bloomberg.com');
  insert into users (first_name, last_name, email) values ('Shaylyn', 'Capinetti', 'scapinetti3@twitpic.com');
  insert into users (first_name, last_name, email) values ('Bertina', 'Abbati', 'babbati4@quantcast.com');
  insert into users (first_name, last_name, email) values ('Charisse', 'Kwiek', 'ckwiek5@patch.com');
  insert into users (first_name, last_name, email) values ('Georgianna', 'Vawton', 'gvawton6@yahoo.com');
  insert into users (first_name, last_name, email) values ('Karolina', 'Lock', 'klock7@hao123.com');
  insert into users (first_name, last_name, email) values ('Shaine', 'Heistermann', 'sheistermann8@abc.net.au');
  insert into users (first_name, last_name, email) values ('Cristen', 'Boston', 'cboston9@harvard.edu');

  insert into products (name, price) values ('Carbonated Water - Cherry', 9.71);
  insert into products (name, price) values ('Pork - Back Ribs', 11.6);
  insert into products (name, price) values ('Sprouts - China Rose', 12.85);
  insert into products (name, price) values ('Flour - Strong', 9.08);
  insert into products (name, price) values ('Capicola - Hot', 8.84);
  insert into products (name, price) values ('Wine - Vovray Sec Domaine Huet', 7.66);
  insert into products (name, price) values ('Snapple - Mango Maddness', 11.82);
  insert into products (name, price) values ('Drambuie', 6.2);
  insert into products (name, price) values ('Puree - Raspberry', 3.42);
  insert into products (name, price) values ('Beef - Striploin Aa', 12.8);
  insert into products (name, price) values ('Tomatoes', 9.32);
  insert into products (name, price) values ('Coffee - Flavoured', 11.25);
  insert into products (name, price) values ('Pizza Pizza Dough', 3.23);
  insert into products (name, price) values ('Wine - White, Chardonnay', 10.83);
  insert into products (name, price) values ('Veal - Inside, Choice', 8.43);
  insert into products (name, price) values ('Foil - 4oz Custard Cup', 16.9);
  insert into products (name, price) values ('Jam - Blackberry, 20 Ml Jar', 10.8);
  insert into products (name, price) values ('Aspic - Amber', 6.27);
  insert into products (name, price) values ('Squash - Pepper', 3.47);
  insert into products (name, price) values ('Jerusalem Artichoke', 19.18);

  insert into orders (user_id, order_date) values (3, '2019-08-11 10:29:23');
  insert into orders (user_id, order_date) values (5, '2020-03-25 20:24:42');
  insert into orders (user_id, order_date) values (3, '2019-08-14 11:46:28');
  insert into orders (user_id, order_date) values (10, '2020-04-19 07:53:57');
  insert into orders (user_id, order_date) values (4, '2019-08-01 22:29:38');
  insert into orders (user_id, order_date) values (2, '2019-12-26 10:33:50');
  insert into orders (user_id, order_date) values (2, '2020-04-16 01:04:42');
  insert into orders (user_id, order_date) values (1, '2019-12-19 19:49:47');
  insert into orders (user_id, order_date) values (6, '2019-06-21 02:50:28');
  insert into orders (user_id, order_date) values (7, '2019-07-23 14:57:37');
  insert into orders (user_id, order_date) values (7, '2019-11-27 14:38:42');
  insert into orders (user_id, order_date) values (4, '2019-12-09 06:59:50');
  insert into orders (user_id, order_date) values (1, '2020-01-05 14:50:03');
  insert into orders (user_id, order_date) values (9, '2019-07-16 19:22:19');
  insert into orders (user_id, order_date) values (1, '2020-02-25 13:05:56');
  insert into orders (user_id, order_date) values (6, '2019-12-27 23:59:56');
  insert into orders (user_id, order_date) values (5, '2020-01-11 06:12:18');
  insert into orders (user_id, order_date) values (7, '2019-10-04 09:39:20');
  insert into orders (user_id, order_date) values (5, '2020-02-16 20:39:04');
  insert into orders (user_id, order_date) values (4, '2020-01-14 19:21:07');

  insert into order_line (order_id, product_id, quantity) values (14, 20, 13);
  insert into order_line (order_id, product_id, quantity) values (16, 16, 1);
  insert into order_line (order_id, product_id, quantity) values (13, 20, 5);
  insert into order_line (order_id, product_id, quantity) values (13, 9, 10);
  insert into order_line (order_id, product_id, quantity) values (1, 20, 12);
  insert into order_line (order_id, product_id, quantity) values (20, 5, 20);
  insert into order_line (order_id, product_id, quantity) values (14, 13, 7);
  insert into order_line (order_id, product_id, quantity) values (19, 13, 19);
  insert into order_line (order_id, product_id, quantity) values (17, 8, 12);
  insert into order_line (order_id, product_id, quantity) values (10, 7, 13);
  insert into order_line (order_id, product_id, quantity) values (17, 10, 4);
  insert into order_line (order_id, product_id, quantity) values (14, 7, 7);
  insert into order_line (order_id, product_id, quantity) values (20, 2, 9);
  insert into order_line (order_id, product_id, quantity) values (5, 12, 13);
  insert into order_line (order_id, product_id, quantity) values (1, 17, 13);
  insert into order_line (order_id, product_id, quantity) values (1, 6, 20);
  insert into order_line (order_id, product_id, quantity) values (18, 20, 17);
  insert into order_line (order_id, product_id, quantity) values (6, 18, 15);
  insert into order_line (order_id, product_id, quantity) values (1, 19, 14);
  insert into order_line (order_id, product_id, quantity) values (7, 3, 11);
  insert into order_line (order_id, product_id, quantity) values (12, 16, 5);
  insert into order_line (order_id, product_id, quantity) values (16, 11, 5);
  insert into order_line (order_id, product_id, quantity) values (13, 10, 10);
  insert into order_line (order_id, product_id, quantity) values (12, 3, 18);
  insert into order_line (order_id, product_id, quantity) values (20, 15, 16);
  insert into order_line (order_id, product_id, quantity) values (14, 15, 12);
  insert into order_line (order_id, product_id, quantity) values (7, 12, 8);
  insert into order_line (order_id, product_id, quantity) values (16, 2, 8);
  insert into order_line (order_id, product_id, quantity) values (19, 7, 2);
  insert into order_line (order_id, product_id, quantity) values (12, 12, 2);
  insert into order_line (order_id, product_id, quantity) values (7, 13, 17);
  insert into order_line (order_id, product_id, quantity) values (20, 18, 18);
  insert into order_line (order_id, product_id, quantity) values (3, 9, 12);
  insert into order_line (order_id, product_id, quantity) values (6, 9, 1);
  insert into order_line (order_id, product_id, quantity) values (20, 19, 3);
  insert into order_line (order_id, product_id, quantity) values (3, 14, 18);
  insert into order_line (order_id, product_id, quantity) values (7, 10, 6);
  insert into order_line (order_id, product_id, quantity) values (15, 18, 3);
  insert into order_line (order_id, product_id, quantity) values (12, 14, 15);
  insert into order_line (order_id, product_id, quantity) values (14, 8, 12);
  insert into order_line (order_id, product_id, quantity) values (3, 1, 14);
  insert into order_line (order_id, product_id, quantity) values (8, 6, 6);
  insert into order_line (order_id, product_id, quantity) values (11, 15, 13);
  insert into order_line (order_id, product_id, quantity) values (7, 15, 18);
  insert into order_line (order_id, product_id, quantity) values (4, 6, 13);
  insert into order_line (order_id, product_id, quantity) values (8, 17, 16);
  insert into order_line (order_id, product_id, quantity) values (8, 20, 13);
  insert into order_line (order_id, product_id, quantity) values (8, 14, 18);
  insert into order_line (order_id, product_id, quantity) values (7, 11, 19);
  insert into order_line (order_id, product_id, quantity) values (20, 2, 11);
  insert into order_line (order_id, product_id, quantity) values (10, 15, 14);
  insert into order_line (order_id, product_id, quantity) values (20, 10, 6);
  insert into order_line (order_id, product_id, quantity) values (5, 11, 2);
  insert into order_line (order_id, product_id, quantity) values (2, 17, 4);
  insert into order_line (order_id, product_id, quantity) values (13, 19, 16);
  insert into order_line (order_id, product_id, quantity) values (9, 12, 17);
  insert into order_line (order_id, product_id, quantity) values (12, 12, 18);
  insert into order_line (order_id, product_id, quantity) values (4, 14, 17);
  insert into order_line (order_id, product_id, quantity) values (4, 1, 5);
  insert into order_line (order_id, product_id, quantity) values (11, 19, 17);
  insert into order_line (order_id, product_id, quantity) values (2, 4, 12);
  insert into order_line (order_id, product_id, quantity) values (19, 20, 12);
  insert into order_line (order_id, product_id, quantity) values (3, 7, 19);
  insert into order_line (order_id, product_id, quantity) values (20, 7, 10);
  insert into order_line (order_id, product_id, quantity) values (6, 14, 12);
  insert into order_line (order_id, product_id, quantity) values (19, 16, 13);
  insert into order_line (order_id, product_id, quantity) values (15, 4, 8);
  insert into order_line (order_id, product_id, quantity) values (15, 13, 3);
  insert into order_line (order_id, product_id, quantity) values (1, 20, 1);
  insert into order_line (order_id, product_id, quantity) values (9, 7, 16);
  insert into order_line (order_id, product_id, quantity) values (13, 19, 13);
  insert into order_line (order_id, product_id, quantity) values (3, 3, 18);
  insert into order_line (order_id, product_id, quantity) values (1, 18, 4);
  insert into order_line (order_id, product_id, quantity) values (19, 1, 9);
  insert into order_line (order_id, product_id, quantity) values (12, 16, 13);
  insert into order_line (order_id, product_id, quantity) values (9, 1, 5);
  insert into order_line (order_id, product_id, quantity) values (9, 9, 15);
  insert into order_line (order_id, product_id, quantity) values (5, 9, 15);
  insert into order_line (order_id, product_id, quantity) values (17, 12, 6);
  insert into order_line (order_id, product_id, quantity) values (18, 5, 6);
  insert into order_line (order_id, product_id, quantity) values (18, 10, 17);
  insert into order_line (order_id, product_id, quantity) values (16, 15, 12);
  insert into order_line (order_id, product_id, quantity) values (1, 7, 14);
  insert into order_line (order_id, product_id, quantity) values (17, 7, 3);
  insert into order_line (order_id, product_id, quantity) values (2, 14, 6);
  insert into order_line (order_id, product_id, quantity) values (3, 20, 10);
  insert into order_line (order_id, product_id, quantity) values (12, 1, 2);
  insert into order_line (order_id, product_id, quantity) values (13, 19, 18);
  insert into order_line (order_id, product_id, quantity) values (9, 18, 14);
  insert into order_line (order_id, product_id, quantity) values (17, 7, 20);
  insert into order_line (order_id, product_id, quantity) values (16, 2, 11);
  insert into order_line (order_id, product_id, quantity) values (7, 16, 18);
  insert into order_line (order_id, product_id, quantity) values (17, 3, 17);
  insert into order_line (order_id, product_id, quantity) values (4, 11, 13);
  insert into order_line (order_id, product_id, quantity) values (17, 14, 4);
  insert into order_line (order_id, product_id, quantity) values (17, 7, 19);
  insert into order_line (order_id, product_id, quantity) values (19, 9, 18);
  insert into order_line (order_id, product_id, quantity) values (3, 17, 10);
  insert into order_line (order_id, product_id, quantity) values (6, 14, 20);
  insert into order_line (order_id, product_id, quantity) values (1, 4, 17);
  ```
  </details>

- Run queries against your data.
  - Get all products for the first order.
  ```sql
  SELECT * FROM orders o
  JOIN order_line ol ON o.order_id = ol.order_id
  JOIN products p ON p.product_id = ol.product_id
  WHERE o.order_id = 1;
  ```
  - Get all orders.
  ```sql
  SELECT * FROM orders;
  ```
  - Get the total cost of an order ( sum the price of all products on an order ).
  ```sql
  SELECT o.order_id, sum(p.price * ol.quantity) FROM orders o
  JOIN order_line ol ON o.order_id = ol.order_id
  JOIN products p ON p.product_id = ol.product_id
  WHERE o.order_id = 1
  GROUP BY o.order_id;
  ```
- Add a foreign key reference from orders to users.
```sql
-- SEE CREATION
```
- Update the orders table to link a user to each order.
```sql
-- SEE CREATION
```
- Run queries against your data.
  - Get all orders for a user.
  ```sql
  SELECT *
  FROM users u
  JOIN orders o ON o.user_id = u.user_id
  WHERE u.user_id = 1
  GROUP BY u.user_id, o.order_id;
  ```
  - Get how many orders each user has.
  ```sql
  SELECT u.first_name, u.last_name, count(o.order_id) AS num_orders
  FROM users u
  JOIN orders o ON o.user_id = u.user_id
  GROUP BY u.user_id;
  ```

### Black Diamond

- Get the total amount on all orders for each user.
```sql
SELECT u.first_name, u.last_name, sum(p.price * ol.quantity) AS total_spend
FROM users u
JOIN orders o ON o.user_id = u.user_id
JOIN order_line ol ON ol.order_id = o.order_id
JOIN products p ON p.product_id = ol.product_id
GROUP BY u.user_id
ORDER BY total_spend DESC;
```
