# Spotify Advanced SQL Project and Query Optimization P-6
Project Category: Advanced

## Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using **SQL**. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and advanced), and optimizing query performance. The primary goals of the project are to practice advanced SQL skills and generate valuable insights from the dataset.

**TABLE CREATION**
```sql
-- create table
DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```

**EXPLORING DATA**
```sql
select count(*) from spotify;

select count(distinct artist) from spotify;

select count(distinct album) from spotify;

select distinct album_type from spotify;

select max(duration_min) from spotify;

select * from spotify where duration_min=0;

delete from spotify where duration_min = 0;
select * from spotify where duration_min=0;
```
## EASY BUSINESS PROBLEMS
**1 Retrieve the names of all tracks that have more than 1 billion streams.**
select track from spotify where stream > 1000000000;

**2 List all albums along with their respective artists.**
```sql
select * from spotify;
select distinct album, artist from spotify;
```
**3 Get the total number of comments for tracks where licensed = TRUE.**
```sql
select * from spotify;
select track, sum(comments) as total_comments from spotify
where licensed = true
group by track;
```
**4 Find all tracks that belong to the album type single.**
```sql
select * from spotify;

select 
	distinct track 
from spotify
where 
	album_type = 'single';
```
**5 Count the total number of tracks by each artist.**
```sql
select 
	distinct artist, 
	count(track)
from spotify 
group by 1
order by 2;
```
## MEDIUM LEVEL BUSINESS PROBLEMS
**1 Calculate the average danceability of tracks in each album.**
```sql
select * from spotify;
select count(distinct album) from spotify;
select 
	album,
	avg(danceability) as avg_danceability
from spotify
group by 1;
```
**2 Find the top 5 tracks with the highest energy values.**
```sql
select * from spotify;
select 
	track, 
	energy
from spotify 
order by energy desc limit 5; 
```
**3 List all tracks along with their views and likes where official_video = TRUE.**
```sql
select * from spotify;
select 
	track,
	sum(views) as total_views,
	sum(likes) as total_likes
from spotify
where official_video = 'true'
group by 1
order by 2 desc limit 5;
```
**4 For each album, calculate the total views of all associated tracks.**
```sql
select * from spotify;
select 
	album,
	track,
	sum(views) as total_views
from spotify
group by 1,2;
```
**5 Retrieve the track names that have been streamed on Spotify more than YouTube.**
```sql
select * from spotify

select * from
(select
	track,
	coalesce(sum(case when most_played_on = 'Youtube' then stream end),0) as streamed_on_youtube,
	coalesce(sum(case when most_played_on = 'Spotify' then stream end),0) as streamed_on_spotify
from spotify
group by 1) as t1
where streamed_on_spotify > streamed_on_youtube
and streamed_on_youtube <> 0;
```
## ADVANCE LEVEL BUSINESS PROBLEMS
**1 Find the top 3 most-viewed tracks for each artist using window functions.**
```sql
select * from spotify;
select count(distinct artist) from spotify;

with ranking_artist
as
(select
	artist,
	track,
	sum(views) as total_views,
	dense_rank() over(partition by artist order by sum(views) desc) as rank
from spotify
group by 1,2)

select * from ranking_artist
where rank <=3;
```
**2 Write a query to find tracks where the liveness score is above the average.**
```sql
select * from spotify;

select 
	track,
	artist,
	liveness
from spotify 
	where liveness >= (select 
							avg(liveness) 
						from spotify);
```
**3 Use a WITH clause to calculate the difference between the highest and lowest energy values for tracks in each album.**
```sql
select * from spotify;

with cte
as
(select
	album,
	max(energy) as highest_energy,
	min(energy) as lowest_energy
from spotify
group by 1)
select
	album,
	highest_energy - lowest_energy as energy_diff
from cte
order by 2 desc
```
**4 Find tracks where the energy-to-liveness ratio is greater than 1.2.**
```sql
select * from spotify

with cte
as
(select
	track,
	energy/liveness as energy_liveniness_ratio
from spotify)
select 
	track,
	energy_liveniness_ratio
from cte
where energy_liveniness_ratio > 1.2;
```
**5 Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.**
```sql
select 
    track,
    views,
    likes,
    sum(likes) over (order by views desc) AS cumulative_likes
from spotify;

```
## END OF PROJECT

