# Netflix_Data_Analysis

![NETFLIX](https://github.com/SomusekharJ/Netflix_Data_Analysis/blob/main/logo.png)

📊 Netflix Movies and TV Shows Data Analysis using SQL


⸻

📌 Overview

This project performs a detailed analysis of Netflix’s Movies and TV Shows data using SQL (PostgreSQL). It addresses real-world business questions and provides deep insights using optimized queries. The data was imported into a PostgreSQL database and queried accordingly.

⸻

🎯 Objectives
	•	Understand the content mix of Netflix (Movies vs TV Shows).
	•	Identify rating trends across content types.
	•	Analyze content by release year, country, genre, and duration.
	•	Perform actor/director-based exploration.
	•	Perform content categorization based on sensitive keywords.

⸻

🗃️ Dataset Source
	•	Netflix Shows Dataset on Kaggle

⸻

🛠️ Schema

DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
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


⸻

💼 Business Problems & SQL Solutions

1. Count the Number of Movies vs TV Shows

SELECT type, COUNT(*) FROM netflix GROUP BY type;

2. Most Common Rating for Movies and TV Shows

WITH RatingCounts AS (
    SELECT type, rating, COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT *, RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rnk
    FROM RatingCounts
)
SELECT type, rating AS most_frequent_rating
FROM RankedRatings
WHERE rnk = 1;

3. List All Movies Released in 2020

SELECT * FROM netflix WHERE release_year = 2020;

4. Top 5 Countries with Most Content

SELECT country, COUNT(*) AS total
FROM (
    SELECT UNNEST(STRING_TO_ARRAY(country, ',')) AS country
    FROM netflix
) AS countries
GROUP BY country
ORDER BY total DESC
LIMIT 5;

5. Longest Movie

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
WHERE TRIM(dir) = 'Rajiv Chilaka';

8. TV Shows with More Than 5 Seasons

SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;

9. Count of Content in Each Genre

SELECT genre, COUNT(*) AS total
FROM (
    SELECT UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre
    FROM netflix
) AS genres
GROUP BY genre;

10. Top 5 Years with Highest Content Released in India

SELECT release_year, COUNT(*) AS total_releases
FROM netflix
WHERE country = 'India'
GROUP BY release_year
ORDER BY total_releases DESC
LIMIT 5;

11. All Movies That Are Documentaries

SELECT *
FROM netflix
WHERE listed_in ILIKE '%Documentaries%';

12. Content Without a Director

SELECT *
FROM netflix
WHERE director IS NULL;

13. Movies Featuring ‘Salman Khan’ in Last 10 Years

SELECT *
FROM netflix
WHERE casts ILIKE '%Salman Khan%'
  AND release_year >= EXTRACT(YEAR FROM CURRENT_DATE) - 10;

14. Top 10 Actors in Indian Movies

SELECT actor, COUNT(*) AS appearances
FROM (
    SELECT UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor
    FROM netflix
    WHERE country = 'India'
) AS a
GROUP BY actor
ORDER BY appearances DESC
LIMIT 10;

15. Categorize Content as ‘Good’ or ‘Bad’ Based on Keywords

SELECT category, COUNT(*)
FROM (
    SELECT
        CASE
            WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categories
GROUP BY category;


⸻

🧾 Conclusion
	•	Netflix offers a large and diverse content library.
	•	Analysis shows trends in content types, rating patterns, country-wise content creation, and sensitive content identification.
	•	SQL is a powerful tool for drawing insights from real-world datasets.

⸻

👨‍💻 Author - Somu Sekhar

This project showcases my practical SQL skills and PostgreSQL integration experience. Built on real-world data for interview, data analysis, and portfolio use.

⸻

📲 Let’s Connect!
	•	LinkedIn: Your LinkedIn
	•	GitHub: Your GitHub
	•	Instagram: Your Instagram
	•	YouTube: Your Channel
	•	Portfolio: Your Website/Portfolio

⸻

Thank you for reading! Feel free to reach out for feedback, collaboration, or guidance.
