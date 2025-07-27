# Netflix_Data_Analysis

![NETFLIX](https://github.com/SomusekharJ/Netflix_Data_Analysis/blob/main/logo.png)

Netflix Movies and TV Shows Data Analysis using SQL

Overview

This project offers an in-depth SQL-based analysis of Netflix’s content catalog, including both movies and TV shows. The main objective is to solve business problems and extract actionable insights using PostgreSQL queries and a clean data model.

Objectives
	•	Analyze the distribution and popularity of content types.
	•	Evaluate ratings, release patterns, and content distribution by geography.
	•	Categorize and filter content based on genres, directors, actors, and keywords.
	•	Leverage SQL techniques including CTE, Window Functions, and String Manipulation.

Dataset

The dataset used in this project is publicly available on Kaggle:
	•	Source: Netflix Titles Dataset
	•	Format: CSV

Schema (PostgreSQL)

DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix (
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);

Business Questions and SQL Solutions

1. Number of Movies vs TV Shows

SELECT type, COUNT(*)
FROM netflix
GROUP BY type;

2. Most Common Rating per Content Type

WITH RatingCounts AS (
    SELECT type, rating, COUNT(*) AS count
    FROM netflix
    GROUP BY type, rating
),
Ranked AS (
    SELECT *, RANK() OVER (PARTITION BY type ORDER BY count DESC) AS rnk
    FROM RatingCounts
)
SELECT type, rating AS most_common_rating
FROM Ranked
WHERE rnk = 1;

3. Movies Released in 2020

SELECT * FROM netflix WHERE release_year = 2020;

4. Top 5 Countries with Most Content

SELECT country, COUNT(*) AS count
FROM (
    SELECT UNNEST(STRING_TO_ARRAY(country, ',')) AS country
    FROM netflix
) AS countries
WHERE country IS NOT NULL
GROUP BY country
ORDER BY count DESC
LIMIT 5;

5. Longest Movie by Duration

SELECT *
FROM netflix
WHERE type = 'Movie'
ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC
LIMIT 1;

6. Content Added in Last 5 Years

SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';

7. Content by Director ‘Rajiv Chilaka’

SELECT *
FROM (
    SELECT *, UNNEST(STRING_TO_ARRAY(director, ',')) AS dir
    FROM netflix
) AS t
WHERE dir = 'Rajiv Chilaka';

8. TV Shows with More Than 5 Seasons

SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;

9. Content Count per Genre

SELECT genre, COUNT(*) AS count
FROM (
    SELECT UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre
    FROM netflix
) AS genres
GROUP BY genre;

10. Top 5 Years with Highest Avg Content from India

SELECT release_year, COUNT(*) AS count
FROM netflix
WHERE country = 'India'
GROUP BY release_year
ORDER BY count DESC
LIMIT 5;

11. All Movies That Are Documentaries

SELECT *
FROM netflix
WHERE listed_in ILIKE '%Documentaries%';

12. Content with No Director

SELECT * FROM netflix WHERE director IS NULL;

13. Movies Featuring ‘Salman Khan’ in Last 10 Years

SELECT *
FROM netflix
WHERE casts ILIKE '%Salman Khan%'
  AND release_year >= EXTRACT(YEAR FROM CURRENT_DATE) - 10;

14. Top 10 Actors by Indian Movies

SELECT actor, COUNT(*) AS count
FROM (
    SELECT UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor
    FROM netflix
    WHERE country = 'India'
) AS a
GROUP BY actor
ORDER BY count DESC
LIMIT 10;

15. Categorize Content by “Kill” or “Violence”

SELECT category, COUNT(*)
FROM (
    SELECT CASE
        WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
        ELSE 'Good'
    END AS category
    FROM netflix
) AS categories
GROUP BY category;

Conclusion
	•	Diversity: Netflix has a broad mix of genres, languages, and content types.
	•	Data Insights: Ratings, genres, and countries give valuable signals for content strategy.
	•	SQL Skills: This project demonstrates practical use of window functions, arrays, CTEs, type casting, and string manipulation in PostgreSQL.

About Me

This project is part of my data analyst portfolio.
	•	LinkedIn
	•	YouTube
	•	Instagram
	•	Discord Community

Feel free to fork, share, or use this project for your own learning!
