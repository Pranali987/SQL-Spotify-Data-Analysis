# Spotify Advanced SQL Project and Query Optimization P-6
Project Category: Advanced
[Click Here to get Dataset](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset)

![Spotify Logo](https://github.com/najirh/najirh-Spotify-Data-Analysis-using-SQL/blob/main/spotify_logo.jpg)

## Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using **SQL**. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and advanced), and optimizing query performance. The primary goals of the project are to practice advanced SQL skills and generate valuable insights from the dataset.

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
## Project Steps

### 1. Data Exploration
Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:
- `Artist`: The performer of the track.
- `Track`: The name of the song.
- `Album`: The album to which the track belongs.
- `Album_type`: The type of album (e.g., single or album).
- Various metrics such as `danceability`, `energy`, `loudness`, `tempo`, and more.

### 4. Querying the Data
After the data is inserted, various SQL queries can be written to explore and analyze the data. Queries are categorized into **easy**, **medium**, and **advanced** levels to help progressively develop SQL proficiency.

#### Easy Queries
- Simple data retrieval, filtering, and basic aggregations.
  
#### Medium Queries
- More complex queries involving grouping, aggregation functions, and joins.
  
#### Advanced Queries
- Nested subqueries, window functions, CTEs, and performance optimization.

### 5. Query Optimization
In advanced stages, the focus shifts to improving query performance. Some optimization strategies include:
- **Indexing**: Adding indexes on frequently queried columns.
- **Query Execution Plan**: Using `EXPLAIN ANALYZE` to review and refine query performance.

## Technology Stack
- **Database**: PostgreSQL
- **SQL Queries**: DDL, DML, Aggregations, Joins, Subqueries, Window Functions
- **Tools**: pgAdmin 4 (or any SQL editor), PostgreSQL (via Homebrew, Docker, or direct installation)

## How to Run the Project
1. Install PostgreSQL and pgAdmin (if not already installed).
2. Set up the database schema and tables using the provided normalization structure.
3. Insert the sample data into the respective tables.
4. Execute SQL queries to solve the listed problems.
5. Explore query optimization techniques for large datasets.
  
---

## 15 Practice Questions

### Easy Level
1. Retrieve the names of all tracks that have more than 1 billion streams
```sql
select * from spotify
where stream > 1000000000;
```
2. List all albums along with their respective artists.
 ```sql
select distinct album, artist
from spotify
order by 1;
```  
3. Get the total number of comments for tracks where `licensed = TRUE`.
```sql
select sum(comments) as total_comments
from spotify
where licensed = 'true';
```
4. Find all tracks that belong to the album type `single`.
```sql
select * 
from spotify
where album_type = 'single';
```
5. Count the total number of tracks by each artist.
```sql
select 
	artist, --- 1
	count(track) as total_songs --- 2
from spotify
group by artist
order by total_songs asc;
```

### Medium Level
1. Calculate the average danceability of tracks in each album.
```sql
select 
	album,
	avg(danceability) as Average
from spotify
group by album
order by 2 desc;
```
2. Find the top 5 tracks with the highest energy values.
```sql
select 
	track,
	max(energy) as Highest_energy
from spotify
group by track
order by 2 desc
limit 5;
```
3. List all tracks along with their views and likes where `official_video = TRUE`.
```sql
select 
	track,
	sum(views) as views,
	sum(likes) as likes
from spotify
where official_video = 'true'
group by track
order by 2 desc
limit 5;
```
4. For each album, calculate the total views of all associated tracks.
```sql
select 
	album,
	track,
	sum(views) as total_views
from spotify
group by album,track
order by 3 desc;
```
5. Retrieve the track names that have been streamed on Spotify more than YouTube.
```sql
SELECT *
FROM (
    SELECT 
        track,
        COALESCE(SUM(CASE WHEN most_played_on = 'Youtube' THEN stream END), 0) AS stream_on_Youtube,
        COALESCE(SUM(CASE WHEN most_played_on = 'Spotify' THEN stream END), 0) AS stream_on_Spotify
    FROM spotify 
    GROUP BY track
) AS t1
WHERE 
    stream_on_Spotify > stream_on_Youtube
    AND 
    stream_on_Youtube <> 0;
```

### Advanced Level
1. Find the top 3 most-viewed tracks for each artist using window functions.
```sql
with ranking_artist as
(
select 
	artist,
	track,
	sum(views) as total_view,
	dense_rank() over(partition by artist order by sum(views) desc) as rank
from spotify 
group by artist, track
order by 1,3 desc
)
select * from ranking_artist
where rank<=3
```
2. Write a query to find tracks where the liveness score is above the average.
```sql
SELECT 
    track,
	artist,
    liveness
FROM 
    spotify
WHERE 
    liveness > (SELECT AVG(liveness) FROM spotify);

```
3. **Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.**
```sql
WITH AlbumEnergy AS (
    SELECT 
        album,
        MAX(energy) AS max_energy,
        MIN(energy) AS min_energy
    FROM 
        spotify
    GROUP BY 
        album
)
SELECT 
    album,
    max_energy,
    min_energy,
    (max_energy - min_energy) AS energy_difference
FROM 
    AlbumEnergy
order by 2 desc;

```
   
4. Find tracks where the energy-to-liveness ratio is greater than 1.2.
```sql
SELECT 
    track,
    energy,
    liveness,
    (energy / liveness) AS energy_to_liveness_ratio
FROM 
    spotify
WHERE 
    (energy / liveness) > 1.2;
```
5. Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.
```sql
SELECT 
    track,
    views,
    likes,
    SUM(likes) OVER (ORDER BY views DESC) AS cumulative_likes
FROM 
    spotify
ORDER BY 
    views DESC;
```

