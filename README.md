# Analytics Description for AppleStore App Development Project

Introduction:
The AppleStore App Development Project is an exciting endeavor aimed at creating a cutting-edge mobile application that addresses a specific market need. To ensure the success and growth of the app, robust analytics play a pivotal role. This analytics description outlines the strategies, tools, and methodologies that will be employed to gather, analyze, and leverage data throughout the development process and beyond.

## Identify the Stakeholders
The main Stakeholder for this project is a group of aspiring app developers who need data-driven insights to decide what type of app to build so they are seeking answers to questions like;

1. What app categories are most popular?
2. What price should be set on the app?
3. How can they maximize user's ratings? 

## Objectives:


## Analytics Framework:

#### Data Collection:

Data is collected from 


# Analysis

## Connect the datasets
 
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
-- They both have a unique ID of 7197

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
No missing values

#### Find out the number of apps per genre
```
SELECT prime_genre, COUNT(*) as NumApps
FROM AppleStore
GROUP by prime_genre
order by NumApps DESC
```
Games have the highest number of Apps on ios which is 3862

#### Get an overview of the app ratings
```
SELECT min(user_rating) AS MinRating,
       max(user_rating) AS MaxRating,
       avg(user_rating) AS AvgRating
FROM AppleStore
```
Min rating is 0 
Max rating is 5

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
paid 3.72
free 3.38

Paid apps get slightly better reviews than free apps

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
<10-3.78, 10-30	- 4.12, >30-3.37

Apps with more language options have better ratings

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

#### Check the top-rated apps for each genre

```
SELECT	
	prime_genre,
    track_name
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

## Recommendations

1. Paid apps have better ratings: the analysis reveals that paid apps have higher ratings than their free counterparts and these could be due to several reasons:
   a. Users who paid for an app have higher engagement and have perceived the value which may lead to rating the app based on the user's experience.

2. Language Support: apps supporting 10 to 30 languages have better ratings. the number of languages is not ideally useful but focusing on and considering the right languages for the app is profitable.

3. finance and Book apps have low ratings: this suggests that these apps do not fully meet the needs of the users and this represents a market opportunity, because if the company can create a quality app in these categories that address the user's needs (better than the current offering) there is a potential for high users rating and market penetration. 
