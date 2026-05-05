
# ==ORDER==
```sql
###SQL Clause Order

#1 SELECT
-- Choose the columns you want to display.

#2 FROM
-- Choose the table you are getting data from.

#3 WHERE
-- Filter rows before anything is grouped.

#4 GROUP BY
-- Group rows together, usually with COUNT, AVG, SUM, MIN, or MAX.

#5 HAVING
-- Filter grouped results.

#6 ORDER BY
-- Sort the final results.

#7 LIMIT
-- Limit how many rows are returned.

SELECT neighborhood, COUNT(*) AS restaurant_count
FROM nomnom
WHERE health IS NOT NULL
GROUP BY neighborhood
HAVING COUNT(*) > 1
ORDER BY restaurant_count DESC
LIMIT 10;
```

# ==TYPES==
```SQL
INTEGER   → whole numbers
REAL      → decimal numbers
TEXT      → words/text
BLOB      → files/raw binary data
NULL      → no value

BOOLEAN   → usually 0 or 1
DATE      → usually stored as TEXT like '2026-05-04'
DATETIME  → usually stored as TEXT like '2026-05-04 14:30:00'
VARCHAR   → text, similar to TEXT in SQLite
CHAR      → short text, similar to TEXT in SQLite
DECIMAL   → decimal number, often money
NUMERIC   → number, integer or decimal
FLOAT     → decimal number, similar to REAL
DOUBLE    → decimal number, similar to REAL
```

# ==TABLE CHECKING==
```SQL
#1 List tables
.tables

#2 Show columns + types
PRAGMA table_info(<table_name>);

#3 Show table creation code
.schema <table_name>
```

# ==MODULE 1

## ==CREATE TABLE==
```sql
-- CREATE TABLE makes a new table.
-- Columns are written inside the parentheses.

CREATE TABLE awards (
  id INTEGER,
  name TEXT,
  recipient TEXT,
  award_name TEXT
);
```

## ==INSERT==
```SQL
-- INSERT INTO adds a new row to a table.
-- The values must match the columns listed.

INSERT INTO awards (id, name, recipient, award_name)
VALUES (1, 'Best Song', 'Taylor Swift', 'Grammy');
```
## ==ALTER==
```SQL
-- ALTER TABLE changes an existing table.
-- ADD COLUMN adds a new column to the table.

ALTER TABLE awards
ADD COLUMN year INTEGER;
```

## ==UPDATE==
```SQL
-- UPDATE changes existing data in a table.
-- WHERE chooses which row or rows to update.

UPDATE awards
SET recipient = 'Beyonce'
WHERE id = 1;
```

## ==DELETE==
```SQL
-- DELETE FROM removes rows from a table.
-- WHERE chooses which row or rows to delete.

DELETE FROM awards
WHERE id = 1;
```

## ==CONSTRAINTS==
```SQL
-- Constraints are rules for data in a table.
-- PRIMARY KEY uniquely identifies each row.
-- UNIQUE prevents duplicate values.
-- NOT NULL means the value cannot be empty.
-- DEFAULT gives a value automatically if none is provided.

CREATE TABLE awards (
  id INTEGER PRIMARY KEY,
  name TEXT UNIQUE,
  recipient TEXT NOT NULL,
  award_name TEXT DEFAULT 'Grammy'
);
```


# ==MODULE 2==

## ==SELECT SPECIFIC COLUMNS==
```SQL
-- SELECT specific columns
-- Instead of using *, you can choose only the columns you need.

SELECT name, genre, year
FROM movies;
```

## ==AS==
```SQL
-- AS
-- AS gives a column or table a temporary nickname.
-- This is useful for making results easier to read.

SELECT name AS movie_title
FROM movies;
```


## ==DISTINCT==
```sql
-- DISTINCT
-- DISTINCT removes duplicate values from the results.
-- This shows each genre only once.

SELECT DISTINCT genre
FROM movies;
```

## ==WHERE==
```SQL
-- WHERE
-- WHERE filters rows based on a condition.
-- This only shows movies released after 2010.

SELECT *
FROM movies
WHERE year > 2010;
```
## ==AND==
```sql
-- AND is used when both conditions must be true.
-- This finds romance movies from 1990 to 1999.
-- Comparison operators used with the `WHERE` clause are:

-- `=` equal to
-- `!=` not equal to
-- `>` greater than
-- `<` less than
-- `>=` greater than or equal to
-- `<=` less than or equal to

SELECT *
FROM movies
WHERE year BETWEEN 1990 AND 1999
  AND genre = 'romance';
```

## ==LIKE==
```sql
-- LIKE
-- LIKE is used to search for a pattern in text.
-- % means any number of characters.
-- This finds movies with names that start with 'The'.

SELECT *
FROM movies
WHERE name LIKE 'The%';

-- LIKE with % on both sides
-- This finds movies with the word 'man' anywhere in the name.

SELECT *
FROM movies
WHERE name LIKE '%man%';

-- LIKE with _
-- The underscore _ means exactly one character.
-- This finds names like 'Cars' or 'Mars' because _ replaces one letter.

SELECT *
FROM movies
WHERE name LIKE '_ars';
```
## ==IS NULL==
```SQL
-- IS NULL
-- IS NULL checks if a value is missing or empty.
-- Use this instead of = NULL.

SELECT *
FROM movies
WHERE imdb_rating IS NULL;
```

## ==IS NOT NULL==
```SQL
-- IS NOT NULL
-- IS NOT NULL checks if a value exists.

SELECT *
FROM movies
WHERE imdb_rating IS NOT NULL;
```
#3 ==BETWEEN==
```SQL
-- BETWEEN
-- BETWEEN filters values inside a range.
-- It includes both the starting and ending values.

SELECT *
FROM movies
WHERE year BETWEEN 1990 AND 1999;

===============================================================================

-- BETWEEN includes both ends.
-- Text is compared from the first character.
-- This means name BETWEEN 'A' AND 'J' is the same as name >= 'A' AND name <= 'J'
-- So 'J' is included, but 'Jaws' is not.

SELECT *
FROM movies
WHERE name BETWEEN 'A' AND 'J';
```

## ==AND==
```sql
-- AND
-- AND means both conditions must be true.
-- This finds romance movies released from 1990 to 1999.

SELECT *
FROM movies
WHERE year BETWEEN 1990 AND 1999
  AND genre = 'romance';
```

## ==OR== 
```sql
-- OR
-- OR means at least one condition must be true.
-- This finds movies after 2014 or movies with the action genre.

SELECT *
FROM movies
WHERE year > 2014
   OR genre = 'action';
```

## ==ORDER BY== 
```SQL
-- ORDER BY ASC
-- ASC sorts from lowest to highest or oldest to newest.
-- ASC is the default, so writing it is optional.

SELECT *
FROM movies
ORDER BY year ASC;


-- ORDER BY DESC
-- DESC sorts from highest to lowest or newest to oldest.

SELECT *
FROM movies
ORDER BY year DESC;
```

## ==LIMIT==
```sql
-- LIMIT
-- LIMIT controls how many rows are shown.
-- This shows only the first 5 results.

SELECT *
FROM movies
LIMIT 5;

-- LIMIT with ORDER BY
-- This shows the 5 newest movies.

SELECT *
FROM movies
ORDER BY year DESC
LIMIT 5;
```

## ==CASE==
```SQL
-- CASE
-- CASE creates custom categories in the results.
-- It works like an if/else statement.

SELECT name,
  CASE
    WHEN imdb_rating > 8 THEN 'Great'
    WHEN imdb_rating > 6 THEN 'Good'
    ELSE 'Okay'
  END AS rating_category
FROM movies;

-- CASE explanation
-- WHEN checks a condition.
-- THEN gives the result if the condition is true.
-- ELSE gives a result if none of the conditions are true.
-- END finishes the CASE statement.
-- AS names the new column.

SELECT name,
  CASE
    WHEN year < 2000 THEN 'Old Movie'
    ELSE 'New Movie'
  END AS movie_age
FROM movies;
```





# ==EXTRAS==


# ==CAST==
# If the type is TEXT and you want to convert to INTEGER
```sql
SELECT *
FROM pokedex_raw
WHERE CAST(attack AS INTEGER) BETWEEN 50 AND 100;
```





# ==Adding two values in a table==

### Format

```sql
INSERT INTO table_name (column1, column2, column3)
VALUES
  (value1, value2, value3),
  (value1, value2, value3);
```

### Example

```sql
INSERT INTO friends (id, name, birthday)
VALUES
  (2, 'Erl', '2001-10-25'),
  (3, 'CJ', '2001-10-24');
```

# ==Editing a value==

### Format

```sql
UPDATE table_name
SET column_name = new_value
WHERE condition;
```

### Example

```sql
UPDATE friends
SET name = 'Storm'
WHERE id = 1;
```

# ==Updating multiple rows with different values==

### Format
```sql
UPDATE table_name
SET column_name = CASE
  WHEN condition THEN new_value
  WHEN condition THEN new_value
END
WHERE condition;
```
## Example

```sql
UPDATE friends
SET email = CASE
  WHEN id = 1 THEN 'storm@codecademy.com'
  WHEN id = 3 THEN 'cj.flores@academy.opswerks.com'
END
WHERE id IN (1, 3);

SELECT * FROM friends;

```

# Make SQLite query output readable
```sql
#1 Turn headers on
.headers on

#2 Use column mode
.mode column

#3 Optional: set column widths
.width 5 15 10 10 5 5 5 5 15

#4 Run your query again
SELECT * FROM pokedex_raw;
```


# TO CREATE NEW TABLE AND COPY THE DATA FROM THE OLD TABLE
```sql
CREATE TABLE pokedex_test_new (
  pokemon_id TEXT PRIMARY KEY,
  pokemon_name TEXT UNIQUE NOT NULL,
  pokemon_type1 TEXT NOT NULL,
  pokemon_type2 TEXT DEFAULT 'None'
);

INSERT INTO pokedex_test_new (
  pokemon_id,
  pokemon_name,
  pokemon_type1,
  pokemon_type2
)
SELECT
  pokemon_id,
  pokemon_name,
  pokemon_type1,
  pokemon_type2
FROM pokedex_test;

DROP TABLE pokedex_test;

ALTER TABLE pokedex_test_new
RENAME TO pokedex_test;

```

# TO VIEW CREATED TABLES
```sql
.tables
```

# TO COPY SPECIFIC DATA FROM TABLE A TO TABLE B
```sql
INSERT INTO pokedex_test (
  pokemon_id,
  name,
  type1,
  type2
)
SELECT
  pokemon_id,
  name,
  type1,
  type2
FROM pokedex_raw
WHERE pokemon_id IN (1, 9, 15, 19, 20);
```

# SORT IN ASC ORDER
```sql
SELECT *
FROM students
ORDER BY name ASC;
```

# TO COUNT THE ITEMS IN A TABLE
```sql
SELECT COUNT(*)
FROM students;
```

# TO CHECK CREATED CONSTRAINTS
```sql
PRAGMA table_info(pokedex_clean);
```

# TO COPY A DATA AND SKIP DUPLICATED ROWS
```sql
INSERT OR IGNORE INTO pokedex_clean (
  pokemon_id,
  name,
  type1,
  type2,
  hp,
  attack,
  defense,
  evolution_stage,
  evolves_to
)
SELECT DISTINCT
  pokemon_id,
  name,
  type1,
  type2,
  hp,
  attack,
  defense,
  evolution_stage,
  evolves_to

FROM pokedex_messy;

```



# GROUP ACTIVITY 2

# CAST

```SQL
##CORRELATE CAST TO BY ORDER, WHEN THE ATTACK TYPE IS TEXT AND CONVERTING THIS TO INTEGER


SELECT *  
FROM pokedex_raw  
ORDER BY CAST(attack AS INTEGER) ASC;
```


What are the top 3 Grass type pokemon with the highest hp?

Write your answers in your own words before submitting:

1. How does filtering affect the number of rows returned?
Filtering affects the results by only showing data that you need. Filtering limits rows based on the conditions that you have set. This filters all the unnecessary data that doesn't match what you're looking for.
2. How does sorting affect how results are viewed?
Sorting the results shows a more organized table, it is easier to check the data, and view the values in either ascending or descending. Sorting organizes the rows without changing how many rows are returned.
3. What is the purpose of limiting results?
Limiting results helps a cleaner output row. Limiting controls how many rows will be returned to you.
4. How do multiple conditions affect query results?
In a multiple conditions query, this refines the searching of data with the use of logic like "AND"and "OR". This makes it flexible to look for the data that you want.



# ============================================================================================================================================================================================================================================================

# ==MODULE 3==

	
# ==COUNT==
```SQL
# COUNT()

-- COUNT() is used to count rows or values in a table.

SELECT COUNT(*)
FROM fake_apps;

-- COUNT(*) counts every row in the table.
-- Use this when you want the total number of rows/apps.


============================================================

# Tips / Tricks

-- Use COUNT(*) when counting all rows.
-- This is usually the simplest and fastest choice.

SELECT COUNT(*)
FROM fake_apps;


-- Use COUNT(column_name) when you only want to count rows
-- where that column is NOT NULL.

SELECT COUNT(price)
FROM fake_apps;


-- Use WHERE to count only specific rows.

SELECT COUNT(*)
FROM fake_apps
WHERE price = 0;


-- Give the count a cleaner column name using AS.

SELECT COUNT(*) AS total_apps
FROM fake_apps;


-- Count rows that match text using LIKE.

SELECT COUNT(*) AS free_name_apps
FROM fake_apps
WHERE name LIKE '%free%';


-- Remember:
-- COUNT(*) counts all rows.
-- COUNT(column) skips NULL values.
-- WHERE filters before counting.
-- AS renames the result column.
```


# SUM
```SQL
# SUM()

-- SUM() adds all values in one numeric column.
-- Use it when you want the total of a column.

SELECT SUM(downloads)
FROM fake_apps;

-- This adds every value in the downloads column.
-- Result = total downloads from all apps.


============================================================

# SUM() with price

SELECT SUM(price)
FROM fake_apps;

-- This adds every value in the price column.
-- Result = total price of all apps combined.


============================================================

# SUM() with WHERE

SELECT SUM(downloads)
FROM fake_apps
WHERE price = 0;

-- This adds downloads only for apps where price is 0.
-- Useful for total downloads of free apps.


============================================================

# Tips / Tricks

-- SUM() needs a specific numeric column.
-- You cannot use SUM(*).

-- Correct:
SELECT SUM(downloads)
FROM fake_apps;

-- Wrong:
SELECT SUM(*)
FROM fake_apps;


-- SUM() works best with number columns:
-- INTEGER = whole numbers
-- REAL = decimal numbers

-- In this table:
-- downloads = INTEGER, good for SUM()
-- price = REAL, good for SUM()
-- name = TEXT, not good for SUM()
-- category = TEXT, not good for SUM()


-- Use AS to rename the result column.

SELECT SUM(downloads) AS total_downloads
FROM fake_apps;


-- Remember:
-- COUNT(*) = count rows
-- SUM(column) = add values in one numeric column
-- WHERE filters rows before adding
```

# AVERAGE
```SQL
SELECT AVG(downloads)
FROM fake_apps;

```

# ROUND
```sql
SELECT ROUND(price, 0)
FROM fake_apps;


SELECT name, ROUND(price, 0)
FROM fake_apps;


SELECT ROUND(AVG(price), 2)
FROM fake_apps;
```

# GROUP BY I
```SQL
SELECT year,
   AVG(imdb_rating)
FROM movies
GROUP BY year
ORDER BY year;



SELECT price, COUNT(*) 
FROM fake_apps
GROUP BY price;


SELECT price, COUNT(*) 
FROM fake_apps
WHERE downloads > 20000
GROUP BY price;




```

# GROUP BY II
```SQL
SELECT ROUND(imdb_rating),
   COUNT(name)
FROM movies
GROUP BY 1
ORDER BY 1;

SELECT category, 
   price,
   AVG(downloads)
FROM fake_apps
GROUP BY category, price;


SELECT category, 
   price,
   AVG(downloads)
FROM fake_apps
GROUP BY 1, 2;


1,2,3,4,5 HERE REPRESENTS THE ORDER OF VARIABLES IN THE SELECT LINE


###GROUP BY

# GROUP BY

-- GROUP BY combines rows with the same value.
-- Used with COUNT(), SUM(), AVG(), MAX(), MIN().

SELECT category, COUNT(*)
FROM fake_apps
GROUP BY category;

-- Counts apps per category.


============================================================

# GROUP BY with SUM()

SELECT category, SUM(downloads)
FROM fake_apps
GROUP BY category;

-- Adds downloads per category.


============================================================

# GROUP BY multiple columns

SELECT category, price, AVG(downloads)
FROM fake_apps
GROUP BY category, price;

-- Groups by category AND price.


============================================================

# GROUP BY shortcut numbers

SELECT category, price, AVG(downloads)
FROM fake_apps
GROUP BY 1, 2;

-- 1 = category
-- 2 = price


============================================================

# Tips

-- GROUP BY = group rows
-- COUNT/SUM/AVG = calculate per group
-- ORDER BY = sort final result
-- DESC = highest to lowest

-- Use AS for cleaner column names.

SELECT category, SUM(downloads) AS total_downloads
FROM fake_apps
GROUP BY category
ORDER BY total_downloads DESC;
```

# HAVING
```sql
SELECT price, 
   ROUND(AVG(downloads)),
   COUNT(*)
FROM fake_apps
GROUP BY price
HAVING COUNT(*) > 10;


###HAVING

# HAVING

-- HAVING filters grouped results.
-- Use it after GROUP BY.

SELECT price, COUNT(*)
FROM fake_apps
GROUP BY price
HAVING COUNT(*) > 10;

-- Groups apps by price.
-- Then keeps only prices with more than 10 apps.


============================================================

# WHERE vs HAVING

-- WHERE filters rows before grouping.
-- HAVING filters groups after grouping.

SELECT category, AVG(downloads)
FROM fake_apps
WHERE price > 0
GROUP BY category
HAVING AVG(downloads) > 10000;


============================================================

# Tips

-- WHERE = filter rows
-- GROUP BY = make groups
-- HAVING = filter groups
-- HAVING comes after GROUP BY
-- Often used with COUNT(), SUM(), AVG(), MAX(), MIN()



```

# ==MODULE 4==

# MULTIPLE TABLES
```SQL


```

# COMBINING TABLES WITH SQL / JOIN
```SQL
-- Checkpoint 1

SELECT *
FROM orders
JOIN subscriptions
  ON orders.subscription_id = subscriptions.subscription_id;

-- Checkpoint 2

SELECT *
FROM orders
JOIN subscriptions
  ON orders.subscription_id = subscriptions.subscription_id
WHERE subscriptions.description = 'Fashion Magazine';

```

# INNER JOIN
```sql
SELECT COUNT(*)
FROM newspaper
JOIN online
ON newspaper.id = online.id;
```
# LEFT JOIN
```sql
SELECT *
FROM newspaper
LEFT JOIN online
  ON newspaper.id = online.id
WHERE online.id IS NULL;


###LEFT JOIN

#1 Basic LEFT JOIN

SELECT *
FROM newspaper
LEFT JOIN online
  ON newspaper.id = online.id;

-- LEFT JOIN keeps all rows from the left table: newspaper
-- If there is a match in online, it shows the joined data
-- If there is no match, online columns become NULL


#2 Find rows with no match

SELECT *
FROM newspaper
LEFT JOIN online
  ON newspaper.id = online.id
WHERE online.id IS NULL;

-- Shows rows from newspaper that do NOT have a match in online


#3 Quick meaning

-- LEFT JOIN = keep everything from the left table
-- Matching rows from right table are added
-- No match on right side = NULL


#4 Tips

-- LEFT table = the table after FROM
-- RIGHT table = the table after LEFT JOIN
-- ON = tells SQL how to match rows
-- WHERE right_table.id IS NULL = find missing matches
-- Good for: finding unmatched records

```

# CROSS JOIN
```SQL
SELECT shirts.shirt_color,
   pants.pants_color
FROM shirts
CROSS JOIN pants;


select count(*) from newspaper where start_month <=3 and end_month >=3;


SELECT * FROM newspaper CROSS JOIN months;


select * from newspaper cross join months where start_month <= month and end_month >= month;


select month, count(*) from newspaper cross join months where start_month <=month and end_month >= month group by month;


###CROSS JOIN

#1 Basic CROSS JOIN

SELECT *
FROM shirts
CROSS JOIN pants;

-- CROSS JOIN combines every row from the first table
-- with every row from the second table.
-- It does NOT need an ON condition.


#2 Example

-- If shirts has 3 rows
-- and pants has 2 rows
-- result = 3 × 2 = 6 rows


#3 CROSS JOIN with selected columns

SELECT shirts.shirt_color,
       pants.pants_color
FROM shirts
CROSS JOIN pants;

-- Shows every shirt color matched with every pants color.


#4 CROSS JOIN with WHERE

SELECT *
FROM newspaper
CROSS JOIN months
WHERE start_month <= month
  AND end_month >= month;

-- CROSS JOIN first makes all combinations.
-- WHERE filters only the matching/valid rows.


#5 CROSS JOIN with GROUP BY

SELECT month, COUNT(*)
FROM newspaper
CROSS JOIN months
WHERE start_month <= month
  AND end_month >= month
GROUP BY month;

-- Counts how many newspaper subscribers were active each month.


#6 Tips

-- CROSS JOIN = all possible combinations
-- No ON needed
-- Total rows = table1 rows × table2 rows
-- Use WHERE to filter combinations
-- Be careful: CROSS JOIN can create many rows
```


# UNION
```sql
SELECT * 
FROM newspaper 
UNION 
SELECT * 
FROM online;


###UNION

#1 Basic UNION

SELECT *
FROM online
UNION
SELECT *
FROM newspaper;

-- UNION stacks results from two SELECT queries.
-- It puts rows from the second query under rows from the first query.


#2 Important rule

-- Both SELECT statements must have the same number of columns.
-- The column types should also match.

SELECT first_name, last_name, email
FROM online
UNION
SELECT first_name, last_name, email
FROM newspaper;


#3 UNION removes duplicates

SELECT email
FROM online
UNION
SELECT email
FROM newspaper;

-- If the same email appears in both tables,
-- UNION shows it only once.


#4 UNION ALL

SELECT email
FROM online
UNION ALL
SELECT email
FROM newspaper;

-- UNION ALL keeps duplicates.
-- Use it if you want every row from both tables.


#5 Tips

-- UNION = stack results
-- UNION removes duplicate rows
-- UNION ALL keeps duplicate rows
-- Same number of columns required
-- Column order must match
-- Use when tables have similar columns

```

# WITH
```sql
WITH previous_query AS (
SELECT customer_id,
COUNT(subscription_id) AS 'subscriptions'
FROM orders
GROUP BY customer_id
)

  

SELECT customers.customer_name ,previous_query.subscriptions from previous_query join customers on previous_query.customer_id = customers.customer_id;


###WITH

#1 Basic WITH

WITH previous_query AS (
  SELECT customer_id,
         COUNT(subscription_id) AS subscriptions
  FROM orders
  GROUP BY customer_id
)

SELECT *
FROM previous_query;

-- WITH creates a temporary result/table.
-- previous_query is the temporary table name.
-- You can use it in the main query after.


#2 WITH + JOIN

WITH previous_query AS (
  SELECT customer_id,
         COUNT(subscription_id) AS subscriptions
  FROM orders
  GROUP BY customer_id
)

SELECT customers.customer_name,
       previous_query.subscriptions
FROM previous_query
JOIN customers
  ON previous_query.customer_id = customers.customer_id;

-- First query counts subscriptions per customer_id.
-- Main query joins that result with customers.
-- Final output shows customer name and subscription count.


#3 Tips

-- WITH = save a query temporarily
-- Also called a CTE
-- CTE = Common Table Expression
-- Use it to make long queries cleaner
-- The temporary table only exists for that query
-- Good for breaking complex queries into steps

```

# MODULE 4 TIPS
```SQL
###WHERE vs HAVING
-- Use WHERE to filter rows before grouping
WHERE CAST(attack AS INTEGER) > 50

-- Use HAVING to filter groups after grouping
HAVING COUNT(*) > 2
```


# DUPLICATION
### TO AVOID DUPLICATION ASSUMING THAT THE DATA DUPLICATES WITH THE SAME COLUMNS
```SQL
SELECT *
FROM pokedex_raw
INNER JOIN pokedex_ceji
ON pokedex_raw.name = pokedex_ceji.name
WHERE pokedex_raw.pokemon_id IN (
  SELECT MIN(pokemon_id)
  FROM pokedex_raw
  GROUP BY name
);

The first query uses `select *`, so it returns **all columns** from both tables, but it also adds a `where` condition with a subquery to keep only the row with the smallest `pokemon_id` for each Pokémon name.
This is useful when you want **all available information** from both tables but still want to avoid duplicate Pokémon names, such as duplicate Pikachu rows.


```


```sql
SELECT DISTINCT
  pokedex_raw.name,
  pokedex_raw.type1,
  pokedex_raw.attack,
  pokedex_ceji.category
FROM pokedex_raw
INNER JOIN pokedex_ceji
ON pokedex_raw.name = pokedex_ceji.name;

The second query uses `select distinct`, so it only removes duplicate rows based on the specific columns you selected: `name`, `type1`, `attack`, and `category`.
This is useful when you do **not need every column** and only want a clean result showing selected information from both tables.
```

