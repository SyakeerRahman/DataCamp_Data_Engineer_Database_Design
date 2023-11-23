# DataCamp_Data_Engineer_Database_Design

Course Description
A good database design is crucial for a high-performance application. Just like you wouldn't start building a house without the benefit of a blueprint, you need to think about how your data will be stored beforehand. Taking the time to design a database saves time and frustration later on, and a well-designed database ensures ease of access and retrieval of information. While choosing a design, a lot of considerations have to be accounted for. In this course, you'll learn how to process, store, and organize data in an efficient way. You'll see how to structure data through normalization and present your data with views. Finally, you'll learn how to manage your database and all of this will be done on a variety of datasets from book sales, car rentals, to music reviews.

### 1. Processing, Storing, and Organizing Data

Start your journey into database design by learning about the two approaches to data processing, OLTP and OLAP. In this first chapter, you'll also get familiar with the different forms data can be stored in and learn the basics of data modeling.

- [ ] OLTP and OLAP
- [X] OLAP vs. OLTP

<img width="638" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/9e3dea7f-a78c-46d2-b51d-03b56d313b6c">

- [X] Which is better?

<img width="344" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/d6f147d7-432b-4c62-9d90-6f9700b9adbb">

- [ ] Storing data



- [X] Name that data type!

<img width="641" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/d00f4fa0-3bcf-421a-b6a0-d75304321553">

- [X] Ordering ETL Tasks

You have been hired to manage data at a small online clothing store. Their system is quite outdated because their only data repository is a traditional database to record transactions.

You decide to upgrade their system to a data warehouse after hearing that different departments would like to run their own business analytics. You reason that an ELT approach is unnecessary because there is relatively little data (< 50 GB).

<img width="509" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/404929fc-0ca2-4919-a972-1d9892f538a2">

- [X] Recommend a storage solution

<img width="649" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/653664ac-2ae0-470c-9070-99395997ab5f">

- [ ] Database design
- [X] Classifying data models

<img width="648" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/0d57a91a-1ca4-4637-a327-b85bd31e1088">

- [ ] Deciding fact and dimension tables

Imagine that you love running and data. It's only natural that you begin collecting data on your weekly running routine. You're most concerned with tracking how long you are running each week. You also record the route and the distances of your runs. You gather this data and put it into one table called Runs with the following schema:

runs
duration_mins - float
week - int
month - varchar(160)
year - int
park_name - varchar(160)
city_name - varchar(160)
distance_km - float
route_name - varchar(160)
After learning about dimensional modeling, you decide to restructure the schema for the database. Runs has been pre-loaded for you.

```ruby
-- Create a route dimension table
CREATE TABLE route(
	route_id INTEGER PRIMARY KEY,
    park_name VARCHAR(160) NOT NULL,
    city_name VARCHAR(160) NOT NULL,
    distance_km FLOAT NOT NULL,
    route_name VARCHAR(160) NOT NULL
);
-- Create a week dimension table
CREATE TABLE week(
	week_id INTEGER PRIMARY KEY,
    week INTEGER NOT NULL,
    month VARCHAR(160) NOT NULL,
    year INTEGER NOT NULL
);
```

- [X] Querying the dimensional model

Here it is! The schema reorganized using the dimensional model: 

![image](https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/74586532-5ea4-45e4-bad8-bb06f1557dd3)

Let's try to run a query based on this schema. How about we try to find the number of minutes we ran in July, 2019? We'll break this up in two steps. First, we'll get the total number of minutes recorded in the database. Second, we'll narrow down that query to week_id's from July, 2019.

```ruby
SELECT 
	-- Get the total duration of all runs
	SUM(duration_mins)
FROM 
	runs_fact
-- Get all the week_id's that are from July, 2019
INNER JOIN week_dim ON runs_fact.week_id = week_dim.week_id
WHERE month = 'July' and year = '2019';
```

### 2. Database Schemas and Normalization

In this chapter, you will take your data modeling skills to the next level. You'll learn to implement star and snowflake schemas, recognize the importance of normalization and see how to normalize databases to different extents.

- [ ] Star and snowflake schema
- [X] Running from star to snowflake

Remember your running database from last chapter? 

![image](https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/2b474e1d-c092-4ee8-8af4-03e8960b28bc)

After learning about the snowflake schema, you convert the current star schema into a snowflake schema. To do this, you normalize route_dim and week_dim. Which option best describes the resulting new tables after doing this?

The tables runs_fact, route_dim, and week_dim have been loaded.

<img width="353" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/fdbad944-7913-457e-86b3-cad5d0c93b84">

- [X] Adding foreign keys

Adding foreign keys
Foreign key references are essential to both the snowflake and star schema. When creating either of these schemas, correctly setting up the foreign keys is vital because they connect dimensions to the fact table. They also enforce a one-to-many relationship, because unless otherwise specified, a foreign key can appear more than once in a table and primary key can appear only once.

The fact_booksales table has three foreign keys: book_id, time_id, and store_id. In this exercise, the four tables that make up the star schema below have been loaded. However, the foreign keys still need to be added.
![image](https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/445dc6fa-5fc4-44ba-acdd-3a9e745ab354)

```ruby
-- Add the book_id foreign key
ALTER TABLE fact_booksales ADD CONSTRAINT sales_book
    FOREIGN KEY (book_id) REFERENCES dim_book_star (book_id);
    
-- Add the time_id foreign key
ALTER TABLE fact_booksales ADD CONSTRAINT sales_time
    FOREIGN KEY (time_id) REFERENCES dim_time_star (time_id);
    
-- Add the store_id foreign key
ALTER TABLE fact_booksales ADD CONSTRAINT sales_store
    FOREIGN KEY (store_id) REFERENCES dim_store_star (store_id);
```

- [X] Extending the book dimension

In the video, we saw how the book dimension differed between the star and snowflake schema. The star schema's dimension table for books, dim_book_star, has been loaded and below is the snowflake schema of the book dimension. 

![image](https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/5ebeb96c-ffde-4e9c-bf65-e3ca498c487f)

In this exercise, you are going to extend the star schema to meet part of the snowflake schema's criteria. Specifically, you will create dim_author from the data provided in dim_book_star.

```ruby
-- Create a new table for dim_author with an author column
CREATE TABLE dim_author (
    author varchar(256)  NOT NULL
);

-- Insert authors 
INSERT INTO dim_author
SELECT DISTINCT author FROM dim_book_star;

-- Add a primary key 
ALTER TABLE dim_author 
ADD COLUMN author_id SERIAL PRIMARY KEY;

-- Output the new table
SELECT * FROM dim_author;
```

- [ ] Normalized and denormalized databases
- [X] Querying the star schema

The novel genre hasn't been selling as well as your company predicted. To help remedy this, you've been tasked to run some analytics on the novel genre to find which areas the Sales team should target. To begin, you want to look at the total amount of sales made in each state from books in the novel genre.

Luckily, you've just finished setting up a data warehouse with the following star schema:

![image](https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/ee7c0be0-efe3-441b-99b9-bee2228299d9)

The tables from this schema have been loaded. Note that you should not use aliases in FROM and JOIN statements.

```ruby
-- Output each state and their total sales_amount
SELECT dim_store_star.state, SUM(sales_amount)
FROM fact_booksales
	-- Join to get book information
    JOIN dim_book_star ON fact_booksales.book_id = dim_book_star.book_id
	-- Join to get store information
    JOIN dim_store_star ON fact_booksales.store_id = dim_store_star.store_id
-- Get all books with in the novel genre
WHERE  
    dim_book_star.genre = 'novel'
-- Group results by state
GROUP BY
    dim_store_star.state;
```

- [X] Querying the snowflake schema

Imagine that you didn't have the data warehouse set up. Instead, you'll have to run this query on the company's operational database, which means you'll have to rewrite the previous query with the following snowflake schema:

![image](https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/13724a07-41c5-4147-888c-8ea027c00ff1)

The tables in this schema have been loaded. Remember, our goal is to find the amount of money made from the novel genre in each state.


- [X] Updating countries

Going through the company data, you notice there are some inconsistencies in the store addresses. These probably occurred during data entry, where people fill in fields using different naming conventions. This can be especially seen in the country field, and you decide that countries should be represented by their abbreviations. The only countries in the database are Canada and the United States, which should be represented as USA and CA.

In this exercise, you will compare the records that need to be updated in order to do this task on the star and snowflake schema. dim_store_star and dim_country_sf have been loaded.

```ruby
-- Output records that need to be updated in the star schema
SELECT * FROM dim_store_star
WHERE country != 'USA' AND country !='CA';
```

- [X] Extending the snowflake schema

The company is thinking about extending their business beyond bookstores in Canada and the US. Particularly, they want to expand to a new continent. In preparation, you decide a continent field is needed when storing the addresses of stores.

Luckily, you have a snowflake schema in this scenario. As we discussed in the video, the snowflake schema is typically faster to extend while ensuring data consistency. Along with dim_country_sf, a table called dim_continent_sf has been loaded. It contains the only continent currently needed, North America, and a primary key. In this exercise, you'll need to extend dim_country_sf to reference dim_continent_sf.

```ruby
-- Add a continent_id column with default value of 1
ALTER TABLE dim_country_sf
ADD continent_id int NOT NULL DEFAULT(1);

-- Add the foreign key constraint
ALTER TABLE dim_country_sf ADD CONSTRAINT country_continent
   FOREIGN KEY (continent_id) REFERENCES dim_continent_sf (continent_id);
   
-- Output updated table
SELECT * FROM dim_country_sf;
```

- [ ] Normal forms

      
- [X] Converting to 1NF

In the next three exercises, you'll be working through different tables belonging to a car rental company. Your job is to explore different schemas and gradually increase the normalization of these schemas through the different normal forms. At this stage, we're not worried about relocating the data, but rearranging the tables.

A table called customers has been loaded, which holds information about customers and the cars they have rented.

<img width="332" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/048e14f3-8787-46c1-aeb2-56a63005c774">

```ruby
-- Create a new table to hold the cars rented by customers
CREATE TABLE cust_rentals (
  customer_id INT NOT NULL,
  car_id VARCHAR(128) NULL,
  invoice_id VARCHAR(128) NULL
);

-- Drop a column from customers table to satisfy 1NF
ALTER TABLE customers
DROP COLUMN cars_rented,
DROP COLUMN invoice_id;
```

- [X] Converting to 2NF

Let's try normalizing a bit more. In the last exercise, you created a table holding customer_ids and car_ids. This has been expanded upon and the resulting table, customer_rentals, has been loaded for you. Since you've got 1NF down, it's time for 2NF.

<img width="343" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/cb1ae67d-d5bc-4c4c-b1b7-d5acfc177a2b">

```ruby
-- Create a new table to satisfy 2NF
CREATE TABLE cars (
  car_id VARCHAR(256) NULL,
  model VARCHAR(128),
  manufacturer VARCHAR(128),
  type_car VARCHAR(128),
  condition VARCHAR(128),
  color VARCHAR(128)
);

-- Drop columns in customer_rentals to satisfy 2NF
ALTER TABLE customer_rentals
DROP COLUMN model,
DROP COLUMN manufacturer, 
DROP COLUMN type_car,
DROP COLUMN condition,
DROP COLUMN color;
```

- [X] Converting to 3NF

Last, but not least, we are at 3NF. In the last exercise, you created a table holding car_idss and car attributes. This has been expanded upon. For example, car_id is now a primary key. The resulting table, rental_cars, has been loaded for you.

<img width="342" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/46f98413-ca88-4ed5-9c2b-3bb9dff2cbd1">

```ruby
-- Create a new table to satisfy 3NF
CREATE TABLE car_model(
  model VARCHAR(128),
  manufacturer VARCHAR(128),
  type_car VARCHAR(128)
);

-- Drop columns in rental_cars to satisfy 3NF
ALTER TABLE rental_cars
DROP COLUMN model,
DROP COLumn color;
```

### 3. Database Views

Get ready to work with views! In this chapter, you will learn how to create and query views. On top of that, you'll master more advanced capabilities to manage them and end by identifying the difference between materialized and non-materialized views.

- [ ] Database views
- [X] Tables vs. views

<img width="655" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/fdf3387f-22dd-43cc-b88d-c522115d9f19">

- [X] Viewing views

Because views are very useful, it's common to end up with many of them in your database. It's important to keep track of them so that database users know what is available to them.

The goal of this exercise is to get familiar with viewing views within a database and interpreting their purpose. This is a skill needed when writing database documentation or organizing views.

```ruby
-- Get all non-systems views
SELECT * FROM information_schema.views
WHERE table_schema NOT IN ('pg_catalog', 'information_schema');
```

- [X] Creating and querying a view

Have you ever found yourself running the same query over and over again? Maybe, you used to keep a text copy of the query in your desktop notes app, but that was all before you knew about views!

In these Pitchfork reviews, we're particularly interested in high-scoring reviews and if there's a common thread between the works that get high scores. In this exercise, you'll make a view to help with this analysis so that we don't have to type out the same query often to get these high-scoring reviews.

```ruby
-- Create a view for reviews with a score above 9
CREATE VIEW high_scores AS
SELECT * FROM REVIEWS
WHERE score > 9;

-- Count the number of self-released works in high_scores
SELECT COUNT(*) FROM high_scores
INNER JOIN labels ON high_scores.reviewid = labels.reviewid
WHERE label = 'self-released';
```

- [ ] Managing views
- [X] Creating a view from other views

Views can be created from queries that include other views. This is useful when you have a complex schema, potentially due to normalization, because it helps reduce the JOINS needed. The biggest concern is keeping track of dependencies, specifically how any modifying or dropping of a view may affect other views.

In the next few exercises, we'll continue using the Pitchfork reviews data. There are two views of interest in this exercise. top_15_2017 holds the top 15 highest scored reviews published in 2017 with columns reviewid,title, and score. artist_title returns a list of all reviewed titles and their respective artists with columns reviewid, title, and artist. From these views, we want to create a new view that gets the highest scoring artists of 2017.

```ruby
-- Create a view with the top artists in 2017
CREATE VIEW top_artists_2017 AS
-- with only one column holding the artist field
SELECT artist_title.artist FROM artist_title
INNER JOIN top_15_2017
ON artist_title.reviewid = top_15_2017.reviewid;

-- Output the new view
SELECT * FROM top_artists_2017;
```

- [X] Granting and revoking access

Access control is a key aspect of database management. Not all database users have the same needs and goals, from analysts, clerks, data scientists, to data engineers. As a general rule of thumb, write access should never be the default and only be given when necessary.

In the case of our Pitchfork reviews, we don't want all database users to be able to write into the long_reviews view. Instead, the editor should be the only user able to edit this view.

```ruby
-- Revoke everyone's update and insert privileges
REVOKE UPDATE, INSERT ON long_reviews FROM public; 

-- Grant the editor update and insert privileges 
GRANT UPDATE, INSERT ON long_reviews TO editor; 
```

- [X] Updatable views

In a previous exercise, we've used the information_schema.views to get all the views in a database. If you take a closer look at this table, you will notice a column that indicates whether the view is updatable.

Which views are updatable?

<img width="226" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/0c1e2f61-b248-4fe1-b71a-9646ed266fd2">

- [X] Redefining a view

Unlike inserting and updating, redefining a view doesn't mean modifying the actual data a view holds. Rather, it means modifying the underlying query that makes the view. In the last video, we learned of two ways to redefine a view: (1) CREATE OR REPLACE and (2) DROP then CREATE. CREATE OR REPLACE can only be used under certain conditions.

The artist_title view needs to be appended to include a column for the label field from the labels table.

```ruby
-- Redefine the artist_title view to have a label column
CREATE OR REPLACE VIEW artist_title AS
SELECT reviews.reviewid, reviews.title, artists.artist, labels.label
FROM reviews
INNER JOIN artists
ON artists.reviewid = reviews.reviewid
INNER JOIN labels
ON labels.reviewid = artists.reviewid;

SELECT * FROM artist_title;
```

- [X] Materialized views



- [X] Materialized versus non-materialized

Materialized and non-materialized are two distinct categories of views. In this exercise, you will organize their differences and similarities.
<img width="663" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/7c102fa5-cfc9-4ae7-9791-77e0e8ff3707">

- [X] Creating and refreshing a materialized view

The syntax for creating materialized and non-materialized views are quite similar because they are both defined by a query. One key difference is that we can refresh materialized views, while no such concept exists for non-materialized views. It's important to know how to refresh a materialized view, otherwise the view will remain a snapshot of the time the view was created.

In this exercise, you will create a materialized view from the table genres. A new record will then be inserted into genres. To make sure the view has the latest data, it will have to be refreshed.

```ruby
-- Create a materialized view called genre_count 
CREATE MATERIALIZED VIEW genre_count AS
SELECT genre, COUNT(*) 
FROM genres
GROUP BY genre;

INSERT INTO genres
VALUES (50000, 'classical');

-- Refresh genre_count
REFRESH MATERIALIZED VIEW genre_count;

SELECT * FROM genre_count;
```

- [X] Managing materialized views

<img width="552" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/042e19cd-1bba-4043-be6d-da04f6f246ba">

### 4. Database Management

This final chapter ends with some database management-related topics. You will learn how to grant database access based on user roles, how to partition tables into smaller pieces, what to keep in mind when integrating data, and which DBMS fits your business needs best.

- [ ] Database roles and access control
- [X] Create a role

A database role is an entity that contains information that define the role's privileges and interact with the client authentication system. Roles allow you to give different people (and often groups of people) that interact with your data different levels of access.

Imagine you founded a startup. You are about to hire a group of data scientists. You also hired someone named Marta who needs to be able to login to your database. You're also about to hire a database administrator. In this exercise, you will create these roles.

```ruby
-- Create a data scientist role
CREATE ROLE data_scientist;

-- Create a role for Marta
CREATE ROLE marta lOGIN

-- Create an admin role
CREATE ROLE admin WITH CREATEDB CREATEROLE;
```

- [X] GRANT privileges and ALTER attributes

Once roles are created, you grant them specific access control privileges on objects, like tables and views. Common privileges being SELECT, INSERT, UPDATE, etc.

Imagine you're a cofounder of that startup and you want all of your data scientists to be able to update and insert data in the long_reviews view. In this exercise, you will enable those soon-to-be-hired data scientists by granting their role (data_scientist) those privileges. Also, you'll give Marta's role a password.

```ruby
-- Grant data_scientist update and insert privileges
GRANT UPDATE, INSERT ON long_reviews TO data_scientist;

-- Give Marta's role a password
ALTER ROLE marta WITH PASSWORD 's3cur3p@ssw0rd';
```

- [X] Add a user role to a group role

There are two types of roles: user roles and group roles. By assigning a user role to a group role, a database administrator can add complicated levels of access to their databases with one simple command.

For your startup, your search for data scientist hires is taking longer than expected. Fortunately, it turns out that Marta, your recent hire, has previous data science experience and she's willing to chip in the interim. In this exercise, you'll add Marta's user role to the data scientist group role. You'll then remove her after you complete your hiring process.

```ruby
-- Add Marta to the data scientist group
GRANT data_scientist TO marta;

-- Celebrate! You hired data scientists.

-- Remove Marta from the data scientist group
REVOKE data_scientist FROM marta;
```

- [ ] Table partitioning
- [X] Reasons to partition

<img width="659" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/30a9a079-198a-4a65-9369-6894e6c8bcc1">

- [X] Partitioning and normalization

In the video, you saw the differences between the two types of partitioning: vertical and horizontal partitioning. As you'd expect, the names suggest how these different strategies work.

It might be a bit challenging to distinguish normalization, which you saw in previous chapters, from partitioning.

<img width="657" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/6b4c2639-6618-44c4-83af-5d2b96dedf19">

- [X] Creating vertical partitions

Creating vertical partitions
In the video, you learned about vertical partitioning and saw an example.

For vertical partitioning, there is no specific syntax in PostgreSQL. You have to create a new table with particular columns and copy the data there. Afterward, you can drop the columns you want in the separate partition. If you need to access the full table, you can do so by using a JOIN clause.

In this exercise and the next one, you'll be working with the example database called pagila. It's a database that is often used to showcase PostgreSQL features. The database contains several tables. We'll be working with the film table. In this exercise, we'll use the following columns:

film_id: the unique identifier of the film
long_description: a lengthy description of the film

```ruby
-- Create a new table called film_descriptions
CREATE TABLE film_descriptions (
    film_id INT,
    long_description TEXT
);

-- Copy the descriptions from the film table
INSERT INTO film_descriptions
SELECT film_id, long_description FROM film;
    
-- Drop the descriptions from the original table
ALTER TABLE film 
DROP COLUMN long_description;

-- Join to view the original table
SELECT * FROM film_descriptions 
JOIN film USING(film_id);
```

- [X] Creating horizontal partitions

In the video, you also learned about horizontal partitioning.

The example of horizontal partitioning showed the syntax necessary to create horizontal partitions in PostgreSQL. If you need a reminder, you can have a look at the slides.

In this exercise, however, you'll be using a list partition instead of a range partition. For list partitions, you form partitions by checking whether the partition key is in a list of values or not.

To do this, we partition by LIST instead of RANGE. When creating the partitions, you should check if the values are IN a list of values.

We'll be using the following columns in this exercise:

film_id: the unique identifier of the film
title: the title of the film
release_year: the year it's released

```ruby
-- Create a new table called film_partitioned
CREATE TABLE film_partitioned (
  film_id INT,
  title TEXT NOT NULL,
  release_year TEXT
)
PARTITION BY LIST (release_year);

-- Create the partitions for 2019, 2018, and 2017
CREATE TABLE film_2019
	PARTITION OF film_partitioned FOR VALUES IN ('2019');

CREATE TABLE film_2018
	PARTITION OF film_partitioned FOR VALUES IN ('2018');

CREATE TABLE film_2017
	PARTITION OF film_partitioned FOR VALUES IN ('2017');

-- Insert the data into film_partitioned
INSERT INTO film_partitioned
SELECT film_id, title, release_year FROM film;

-- View film_partitioned
SELECT * FROM film_partitioned;
```

- [] Data integration



- [X] Data integration do's and dont's

<img width="629" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/a4d7e9f4-defd-4977-b4dc-ca5f567de4f9">

- [X] Analyzing a data integration plan

You're a data analyst in a hospital that wants to make sure there is enough cough medicine should an epidemic break out. For this, you need to combine the historical health records with the upcoming appointments to see if you can detect a pattern similar to the last cold epidemic. Then, you need to make sure there is sufficient stock available or if the stock should be increased. To help tackle this problem, you created a data integration plan.

Which risk is not clearly indicated on the data integration plan?

![image](https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/4b171778-3e65-421b-b3a6-0ff4ad912333)

<img width="644" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/b7ca7b8a-5e41-4228-b81a-c8bcf411ebb6">

- [ ] Picking a Database Management System (DBMS)


- [X] SQL versus NoSQL

<img width="632" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/9ab1d823-2b13-42dd-9530-7c8edcfb712f">

- [ ] Choosing the right DBMS

<img width="628" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/7e7b51f7-7419-4479-82c3-a5c1571e046f">
<img width="628" alt="image" src="https://github.com/SyakeerRahman/DataCamp_Data_Engineer_Database_Design/assets/105381652/f75594fd-1e54-4750-ade7-f816f88f44df">

