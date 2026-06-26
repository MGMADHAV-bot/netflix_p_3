# netflix_p_3
-- NETFLIX PROJECT
# Netflix Movies and TV Shows Data Analysis using SQL

<img width="2226" height="678" alt="logo" src="https://github.com/user-attachments/assets/96797635-8919-45b9-ae80-aac9de890a60" />


## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives

- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.


CREATE TABLE netflix
	(
		show_id VARCHAR(6),
		type    VARCHAR(10),
		title   VARCHAR(150),
		director VARCHAR(208),
		casts    VARCHAR(1000),
		country	 VARCHAR(150),
		date_added VARCHAR(50),
		release_year INT,
		rating	 VARCHAR(10),
		duration VARCHAR(15),
		listed_in  VARCHAR(100),
		description VARCHAR(250)

	);

	SELECT * FROM netflix;



### 15. bussiness problems

### 1. count the number of movies vs tv shows

```sql

	SELECT 
		type,
		COUNT(*) AS total_content
		FROM netflix
		GROUP BY type
```

### 2. Find the most common ratings for movies and tv shows

```sql
	SELECT * FROM(
		SELECT
		type,
		rating,
		COUNT(*),
		RANK() OVER(PARTITION BY type ORDER BY COUNT(*) DESC) AS ranking	
		FROM netflix
	GROUP BY 1, 2
) AS t1
	WHERE 
		ranking = 1;
```

### 3. List all movies released in a specific year (e.g.,2020)

```sql
	SELECT * FROM netflix
	WHERE
	type = 'Movie'
	AND
	release_year = 2020
```

### 4. Find the top 5 countries with the most content on netflix

```sql
	SELECT 
	 	UNNEST(STRING_TO_ARRAY(country, ',')) AS new_country,
		 COUNT(show_id) AS total_conent
		 FROM netflix
		 GROUP BY 1
		 ORDER BY 2 DESC
		 LIMIT 5
```

### 5. identify the longest movie?

```sql
		SELECT * FROM netflix 
		WHERE 
		type = 'Movie'
		AND
		duration = (SELECT MAX(duration) FROM netflix)
```

### 6. Find the content added in the last 5 years

```sql
	 SELECT
	 *
	 FROM netflix
	 WHERE
	 	TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years'
```

### 7. Find all the movies/tv shows by director ' rajiv chilaka'.

```sql
	 SELECT * FROM netflix
	 WHERE   director ILIKE '%Rajiv chilaka%'
```

### 8 List all TV Shows with more than 5 seasons

```sql
	SELECT
	* 
	FROM netflix
	WHERE
	type = 'TV Show'
	AND
	SPLIT_PART(duration, ' ', 1):: numeric >5
```
	
### 9. count the number of conent items in each genre 

```sql
	SELECT
	UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
	COUNT(show_id) AS total_content
	FROM netflix
	GROUP BY 1
```

### 10. Find each year and the average numbers of content releae by india on netflix. 
### return top 5 year with highest avg content release

```sql
	SELECT 
		EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')) AS YEAR,
		COUNT(*) AS yearly_content,
		ROUND(
		COUNT(*):: NUMERIC/(SELECT COUNT(*) FROM netflix WHERE country = 'India') :: NUMERIC * 100
		,2) AS avg_conent_per_year
		FROM netflix
		WHERE country = 'India'
		GROUP BY 1
```

### 11. List all the movies that are doucmentries

```sql
	SELECT * FROM netflix
	WHERE
	 listed_in ILIKE '%documentaries%'
```

### 12. Find all content without a director

```sql
	SELECT * FROM netflix
	WHERE 
		director IS NULL
```

### 13. Find how many movies actor 'salman khan' apperd in last 10 years.

```sql
	SELECT * FROM netflix
	WHERE 
	casts ILIKE '%Salman Khan%'
	AND
	release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10
```

### 14. Find the top 10 actors who have apperd in the higest number of movies produced in india.

```sql
	SELECT 
	UNNEST(STRING_TO_ARRAY(casts, ',')) AS actors,
	COUNT(*) AS total_content
	FROM netflix
	WHERE country ILIKE '%india%'
	GROUP BY 1
	ORDER BY  2 DESC
	LIMIT 10
```

### 15. categorize the content based on the presence of the keywords 'kill' and 'violence' in
###  the description field. label content containing these keywords as 'bad ' and all other
### content as 'good'. count how many items fall into each category.

```sql
WITH new_table
AS
(
	SELECT
	*,
	CASE
	WHEN
		description ILIKE '%kill%' OR
		description ILIKE '%violence%' THEN 'Bad_Content'
		ELSE 'Good Content'
		END category
	FROM netflix
)
SELECT
	category,
	COUNT(*) AS total_content
	FROM new_table
	GROUP BY 1
```	

## Findings and Conclusion

- **Content Distribution:** The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
- **Common Ratings:** Insights into the most common ratings provide an understanding of the content's target audience.
- **Geographical Insights:** The top countries and the average content releases by India highlight regional content distribution.
- **Content Categorization:** Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.

