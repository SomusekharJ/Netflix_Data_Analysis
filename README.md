# Netflix_Data_Analysis

![NETFLIX](https://github.com/SomusekharJ/Netflix_Data_Analysis/blob/main/logo.png)

-- Table Schema

CREATE TABLE netflix (
    show_id      VARCHAR(10),
    type         VARCHAR(10),    
    title        VARCHAR(150),
    director     VARCHAR(208),    
    casts        VARCHAR(1000),
    country      VARCHAR(150),
    date_added   VARCHAR(50),
    release_year INT,
    rating       VARCHAR(10),
    duration     VARCHAR(15),
    listed_in    VARCHAR(100),
    description  VARCHAR(250)
);

-- Preview the dataset
SELECT * FROM netflix;

-- Count total content
SELECT COUNT(*) as total_content 
FROM netflix;

-- List all distinct content types
SELECT DISTINCT type FROM netflix;

-- 15 Business Problems & SQL Solutions

-- 1. Count the number of Movies vs TV Shows

SELECT type, COUNT(*) 
FROM netflix 
GROUP BY type;

-- 2. Find the most common rating for movies and TV shows

SELECT
    type,
    rating,
    COUNT(*)
FROM netflix
GROUP BY 1,2
ORDER BY 1,3 DESC;

-- 3. List all movies released in a specific year (e.g., 2020)

SELECT *
FROM netflix
WHERE type = 'Movie' AND release_year = 2020;

-- 4. Find the top 5 countries with the most content on Netflix

SELECT country, COUNT(*) AS total
FROM netflix
GROUP BY country
ORDER BY total DESC
LIMIT 5;

-- 5. Identify the longest movie

SELECT *
FROM netflix
WHERE type = 'Movie'
ORDER BY CAST(SPLIT_PART(duration, ' ', 1) AS INT) DESC
LIMIT 1;

-- 6. Find content added in the last 5 years

SELECT *
FROM netflix
WHERE CAST(SPLIT_PART(date_added, ',', -1) AS INT) >= EXTRACT(YEAR FROM CURRENT_DATE) - 5;

-- 7. Find all the movies/TV shows by director 'Rajiv Chilaka'

SELECT *
FROM netflix
WHERE director ILIKE '%Rajiv Chilaka%';

-- 8. List all TV shows with more than 5 seasons

SELECT *
FROM netflix
WHERE type = 'TV Show' AND CAST(SPLIT_PART(duration, ' ', 1) AS INT) > 5;

-- 9. Count the number of content items in each genre

SELECT UNNEST(STRING_TO_ARRAY(listed_in, ', ')) AS genre, COUNT(*) AS total
FROM netflix
GROUP BY genre
ORDER BY total DESC;

-- 10. Find each year and the average number of content releases in India on Netflix (Top 5 years)

WITH india_years AS (
    SELECT release_year, COUNT(*) AS total
    FROM netflix
    WHERE country ILIKE '%India%'
    GROUP BY release_year
)
SELECT release_year, total
FROM india_years
ORDER BY total DESC
LIMIT 5;

-- 11. List all movies that are documentaries

SELECT *
FROM netflix
WHERE type = 'Movie' AND listed_in ILIKE '%Documentaries%';

-- 12. Find all content without a director

SELECT *
FROM netflix
WHERE director IS NULL OR TRIM(director) = '';

-- 13. Find how many movies actor 'Salman Khan' appeared in last 10 years

SELECT *
FROM netflix
WHERE type = 'Movie'
  AND casts ILIKE '%Salman Khan%'
  AND release_year >= EXTRACT(YEAR FROM CURRENT_DATE) - 10;

-- 14. Find the top 10 actors who have appeared in the highest number of movies produced in India

WITH indian_movies AS (
    SELECT * FROM netflix
    WHERE type = 'Movie' AND country ILIKE '%India%'
),
actor_list AS (
    SELECT UNNEST(STRING_TO_ARRAY(casts, ', ')) AS actor
    FROM indian_movies
)
SELECT actor, COUNT(*) AS appearances
FROM actor_list
GROUP BY actor
ORDER BY appearances DESC
LIMIT 10;

-- 15. Categorize content based on the presence of 'kill' and 'violence' keywords in the description
--     Label as 'Bad' if either keyword exists, otherwise 'Good'. Count items in each category.

SELECT
  CASE
    WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
    ELSE 'Good'
  END AS category,
  COUNT(*) AS total
FROM netflix
GROUP BY category;
