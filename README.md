# AppleStore



 -- Connect the datasets-- 
 
```
CREATE TABLE applestore_description_combined AS
select * From appleStore_description1
union all 
select * From appleStore_description2
union all 
select * From appleStore_description3
union all 
select * From appleStore_description4
```

## Exploratory Data Analysis(EDA)
-- Check the number of unique apps in both tables AppleStore and applestore_description_combined.

```
SELECT count(DISTINCT id) as UniqueAppIDs
From AppleStore
```

```
SELECT count(DISTINCT id) as UniqueAppIDs
From applestore_description_combined
```

#### Check for missing data
```
SELECT Count(*) As MIssingValues
from AppleStore
WHERE track_name is null or user_rating is null or prime_genre is null
```

repeat for applestore_description_combined

```
SELECT Count(*) As MIssingValues
from applestore_description_combined
WHERE app_desc is null 
```

#### Find out the number of apps per genre
```
SELECT prime_genre, COUNT(*) as NumApps
FROM AppleStore
GROUP by prime_genre
order by NumApps DESC
```

#### Get an overview of the app ratings

SELECT min(user_rating) AS MinRating,
       max(user_rating) AS MaxRating,
       avg(user_rating) AS AvgRating
FROM AppleStore

#### Get the distribution of app prices

```
SELECT
	(price/2) *2 As PriceBinStart,
    ((price/2) *2)*2 As PriceBinEnd,
COUNT (*) as NumApps
FROM AppleStore
GROUP by PriceBinStart
Order by PriceBinStart
```

## DATA ANALYSIS

#### Determine whether paid apps have higher ratings than free apps
```
SELECT CASE
 		when price > 0 then 'Paid'
        Else 'Free'
   End as App_Type,
    avg(user_rating) as Avg_Rating
  FROM AppleStore
    GROUP by App_Type
```
 
#### Then, Check if apps with more supporting languages have more ratings.

```
SELECT case 
		when lang_num < 10 then '<10languages'
        when lang_num BETWEEN 10 and 30 then '10-30languages'
    	else '>30laguages'
	end as language_bucket,
	avg(user_rating) as Avg_Rating
FROM AppleStore
group by language_bucket
order by  Avg_Rating DESC
```

#### check genres with low ratingsAppleStore

```
SELECT prime_genre, 
	avg(user_rating) as Avg_Rating
    from AppleStore
GROUP by prime_genre
ORDER by Avg_Rating ASC
limit 10
```

#### Check if there is a correlation between the length of the app description and the user's rating.

```
SELECT CASE 
	when length(b.app_desc) < 500 then 'short'
    when length(b.app_desc) between 500 and 1000 then 'medium'
    Else 'long'
  end as description_length_bucket,
  avg(a.user_rating) as average_rating
FROM
AppleStore as a 
join 
applestore_description_combined as b 
on 
a.id=b.id
group by description_length_bucket
order by average_rating DESC
```

#### check the top-rated apps for each genre

```
SELECT	
	prime_genre,
    track_name,
    user_rating
FROM (
		SELECT	
		prime_genre,
    	track_name,
    	user_rating,
  		RANK() OVER(PARTITION BY prime_genre order by user_rating desc, 
                    rating_count_tot desc) as rank
		FROM
  		AppleStore
  	 ) as a 
 WHERE
 a.rank = 1
```
