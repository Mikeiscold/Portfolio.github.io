# Portfolio.github.io
CREATE TABLE appleStore_description_combined as
SELECT * FROM appleStore_description1
Union ALL
SELECT* from appleStore_description2
UNION ALL
SELECT * from appleStore_description3
UNION ALL
select * from appleStore_description4


SELECT COUNT(DISTINCT id) as UNIQUEAPPIDs
FROM AppleStore
SELECT COUNT(DISTINCT id) as UNIQUEAPPIDs
FROM appleStore_description_combined

-- Check for missing values in key fields
SELECT COUNT(*) as MissingVALUES
FROM AppleStore
WHERE track_name is NULL or user_rating is null or prime_genre is null


SELECT COUNT(*) as MissingVALUES
FROM appleStore_description_combined
WHERE app_desc is NULL 
-- find the number of app per genre
SELECT prime_genre, Count(*) as NumApps
FROM AppleStore
GROUP by prime_genre
ORDER BY NumApps DESC
-- Get an overview fo the apps rating

SELECT min(user_rating) as MinRating,
       max(user_rating) as MaxRating,
       avg(user_rating) as AvgRating
FROM AppleStore

--Determine whether paid app has more rating than free apps

SELECT CASE
           WHEN price > 0 then 'paid'
           ELSE 'free'
           END as App_Type,
           avg(user_rating) as Avg_Rating
FROM AppleStore
GROUP by App_Type
-- check if app with more supported languages has higher ratings

SELECT CASE
           WHEN lang_num < 10 THEN '<10 lanuages'
           WHEN lang_num BETWEEN 10 and 30 THEN '<10-30 lanuages'
           ELSE '>30 Languages'
           end as language_bucket,
           avg(user_rating) as Avg_Rating
FROM AppleStore
GROUP BY  language_bucket
ORDER by Avg_Rating DESC

-- check genre with low rating
SELECT prime_genre,
       Avg(user_rating) as Avg_Rating
       FROM AppleStore
       GROUP by prime_genre
       ORDER by Avg_rating ASC
       Limit 10
       
-- Check if there is correlation between the lenght of the app desciption and user rating

SELECT CASE
           WHEN length(b.app_desc) <500 THEN 'short'
           WHEN length(b.app_desc) BETWEEN 500 AND 1000 THEN 'medium'
           ELSE 'long'
           End as description_lenght_bucket,
           avg(a.user_rating) as average_rating
          
     FROM 
          AppleStore as a 
     JOIN 
         appleStore_description_combined as b
     on
        a.id = b.id
     GROUP BY 
             description_lenght_bucket
     order by 
             average_rating DESC
  -- Checking for top-rated apps in each genre
  SELECT
        prime_genre,
        user_rating,
        track_name
  FROM ( 
        SELECT
       prime_genre,
       user_rating,
       track_name,
       RANK() OVER(PARTITION by prime_genre order by user_rating DESC, rating_count_tot DESC) AS rank
       FROM
           AppleStore
      ) as a 
      WHERE
   a.rank = 1
       
