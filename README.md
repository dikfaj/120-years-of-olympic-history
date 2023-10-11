# 120-years-of-olympic-history

### How many olympics games have been held?
```sql
SELECT
  COUNT(DISTINCT games) AS number_of_olympic
FROM
  athlete_events;
```

|number_of_olympic|
|-----------------|
|               51|

### List down all Olympics games held so far.
```sql
SELECT
  year,
  season,
  city
FROM 
  athlete_events ae
GROUP BY 1,2,3
ORDER BY 1;
```

|year|season|city                  |
|----|------|----------------------|
|1896|Summer|Athina                |
|1900|Summer|Paris                 |
|1904|Summer|St. Louis             |
1906|Summer|Athina                |
1908|Summer|London                |
....|......|.....................|
2008|Summer|Beijing               |
2010|Winter|Vancouver             |
2012|Summer|London                |
2014|Winter|Sochi                 |
2016|Summer|Rio de Janeiro        |

52 rows fetched. Shows some data.

### Mention the total no of nations who participated in each olympics game?
```sql
SELECT 
  games,
  COUNT(DISTINCT nr.region) AS number_of_nations
FROM 
  athlete_events ae 
JOIN
  noc_regions nr 
ON 
  ae.noc = nr.noc
GROUP BY 1;
```

games      |number_of_nations|
-----------|-----------------|
1896 Summer|               12|
1900 Summer|               31|
1904 Summer|               14|
1906 Summer|               20|
1908 Summer|               22|
...........|.................|
2008 Summer|              201|
2010 Winter|               81|
2012 Summer|              202|
2014 Winter|               88|
2016 Summer|              203|

51 rows fetched. Shows some data.

### Which Sports were just played only once in the olympics?
```sql
WITH t1 AS (
SELECT 
  DISTINCT games,
  sport
FROM
  athlete_events
),
t2 AS (
SELECT
  sport,
  COUNT(sport) AS no_of_games
FROM t1
GROUP BY 1
)

SELECT 
  t2.*, 
  t1.games
FROM
  t2
JOIN
  t1
ON
  t1.sport = t2.sport
WHERE
  t2.no_of_games = 1
ORDER BY
  t2.no_of_games,
  sport;
```
sport              |no_of_games|games      |
-------------------|-----------|-----------|
Aeronautics        |          1|1936 Summer|
Basque Pelota      |          1|1900 Summer|
Cricket            |          1|1900 Summer|
Croquet            |          1|1900 Summer|
Jeu De Paume       |          1|1908 Summer|
Military Ski Patrol|          1|1924 Winter|
Motorboating       |          1|1908 Summer|
Racquets           |          1|1908 Summer|
Roque              |          1|1904 Summer|
Rugby Sevens       |          1|2016 Summer|

### Fetch the total no of sports played in each olympic games.
```sql
SELECT
  games,
  COUNT(DISTINCT sport) AS number_of_sports
FROM
  athlete_events 
GROUP BY 1
ORDER BY 2 DESC;
```
games      |number_of_sports|
-----------|----------------|
2008 Summer|              34|
2000 Summer|              34|
2004 Summer|              34|
2016 Summer|              34|
2012 Summer|              32|
............|...............|
1960 Winter|               8|
1952 Winter|               8|
1936 Winter|               8|
1928 Winter|               8|
1932 Winter|               7|

51 rows fetched. Shows some data.

### Fetch details of the oldest athletes to win a gold medal.
```sql
WITH gold_by_age AS (
SELECT
  *,
DENSE_RANK() OVER(ORDER BY age DESC) AS age_rank
FROM
  athlete_events 
WHERE
  medal = 'Gold' AND age IS NOT NULL
ORDER BY age DESC
)

SELECT
  name,
  sex,
  age,
  team,
  noc,
  games,
  year,
  season,
  city,
  sport,
  medal
FROM
  gold_by_age
WHERE
  age_rank = 1;
```

name             |sex|age|team         |noc|games      |year|season|city     |sport   |medal|
-----------------|---|---|-------------|---|-----------|----|------|---------|--------|-----|
Charles Jacobus  |M  | 64|United States|USA|1904 Summer|1904|Summer|St. Louis|Roque   |Gold |
Oscar Gomer Swahn|M  | 64|Sweden       |SWE|1912 Summer|1912|Summer|Stockholm|Shooting|Gold |

### Fetch the top 5 athletes who have won the most gold medals.
```sql
SELECT
  name,
  COUNT(medal) AS no_of_gold_medal
FROM
  athlete_events 
WHERE
  medal = 'Gold'
GROUP BY name
ORDER BY no_of_gold_medal DESC
LIMIT 5;
```
name                          |no_of_gold_medal|
------------------------------|----------------|
Michael Fred Phelps, II       |              23|
Raymond Clarence "Ray" Ewry   |              10|
Frederick Carlton "Carl" Lewis|               9|
Paavo Johannes Nurmi          |               9|
Mark Andrew Spitz             |               9|

### Fetch the top 5 athletes who have won the most medals (gold/silver/bronze).
```sql
SELECT
  name,
  COUNT(medal) AS no_of_medal
FROM
  athlete_events
WHERE
  medal IS NOT NULL
GROUP BY name
ORDER BY no_of_medal DESC
LIMIT 5;
```
name                              |no_of_medal|
----------------------------------|-----------|
Michael Fred Phelps, II           |         28|
Larysa Semenivna Latynina (Diriy-)|         18|
Nikolay Yefimovich Andrianov      |         15|
Ole Einar Bjrndalen               |         13|
Takashi Ono                       |         13|

### Fetch the top 5 most successful countries in olympics. Success is defined by no of medals won.
```sql
SELECT
  nr.region,
COUNT(medal) AS no_of_medal
FROM
  athlete_events ae 
JOIN
  noc_regions nr
ON
  ae.noc = nr.noc 
WHERE
  medal IS NOT NULL
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```
region |no_of_medal|
-------|-----------|
USA    |       5637|
Russia |       3947|
Germany|       3756|
UK     |       2068|
France |       1777|

### List down total gold, silver and broze medals won by each country.
```sql
SELECT
  nr.region,
  COUNT(CASE WHEN medal = 'Gold' THEN 1 END) AS no_of_gold_medal,
  COUNT(CASE WHEN medal = 'Silver' THEN 1 END) AS no_of_silver_medal,
  COUNT(CASE WHEN medal = 'Bronze' THEN 1 END) AS no_of_bronze_medal
FROM 
  athlete_events ae 
JOIN 
  noc_regions nr 
ON 
  ae.noc = nr.noc
WHERE medal IS NOT NULL 
GROUP BY 1
ORDER BY 2 DESC 
LIMIT 5;
```

region |no_of_gold_medal|no_of_silver_medal|no_of_bronze_medal|
-------|----------------|------------------|------------------|
USA    |            2638|              1641|              1358|
Russia |            1599|              1170|              1178|
Germany|            1301|              1195|              1260|
UK     |             678|               739|               651|
Italy  |             575|               531|               531|


### List down total gold, silver and broze medals won by each country corresponding to each olympic games.
```sql
SELECT
  games,
  nr.region,
  COUNT(CASE WHEN medal = 'Gold' THEN 1 END) AS no_of_gold_medal,
  COUNT(CASE WHEN medal = 'Silver' THEN 1 END) AS no_of_silver_medal,
COUNT(CASE WHEN medal = 'Bronze' THEN 1 END) AS no_of_bronze_medal
FROM
  athlete_events ae 
JOIN
  noc_regions nr
ON ae.noc = nr.noc
WHERE medal IS NOT NULL 
GROUP BY 1,2
ORDER BY 1 ASC, 3 DESC, 4 DESC, 5 DESC;
```

games      |region                     |no_of_gold_medal|no_of_silver_medal|no_of_bronze_medal|
-----------|---------------------------|----------------|------------------|------------------|
1896 Summer|Germany                    |              25|                 5|                 2|
1896 Summer|USA                        |              11|                 7|                 2|
1896 Summer|Greece                     |              10|                18|                20|
1896 Summer|France                     |               5|                 4|                 2|
1896 Summer|UK                         |               3|                 3|                 3|
1896 Summer|Hungary                    |               2|                 1|                 3|
..........|...........................|.................|..................|..................|
2016 Summer|Dominican Republic         |               0|                 0|                 1|
2016 Summer|Morocco                    |               0|                 0|                 1|
2016 Summer|Portugal                   |               0|                 0|                 1|
2016 Summer|United Arab Emirates       |               0|                 0|                 1|
2016 Summer|Finland                    |               0|                 0|                 1|

1640 rows fetched, show some data

--Which countries have never won gold medal but have won silver/bronze medals?
```sql
WITH medal_count AS (SELECT
  nr.region,
  COUNT(CASE WHEN medal = 'Gold' THEN 1 END) AS no_of_gold_medal,
  COUNT(CASE WHEN medal = 'Silver' THEN 1 END) AS no_of_silver_medal,
  COUNT(CASE WHEN medal = 'Bronze' THEN 1 END) AS no_of_bronze_medal
FROM
  athlete_events ae 
JOIN
  noc_regions nr
ON
  ae.noc = nr.noc
WHERE medal IS NOT NULL 
GROUP BY 1)

SELECT
  region,
  no_of_gold_medal,
  no_of_silver_medal,
  no_of_bronze_medal
FROM
  medal_count
WHERE
  no_of_gold_medal = 0 AND (no_of_silver_medal >=1 OR no_of_bronze_medal >= 1)
ORDER BY 3 DESC, 4 DESC;
```

region            |no_of_gold_medal|no_of_silver_medal|no_of_bronze_medal|
------------------|----------------|------------------|------------------|
Paraguay          |               0|                17|                 0|
Iceland           |               0|                15|                 2|
Montenegro        |               0|                14|                 0|
Malaysia          |               0|                11|                 5|
Namibia           |               0|                 4|                 0|
..................|...............|...................|.................|
Mauritius         |               0|                 0|                 1|
Barbados          |               0|                 0|                 1|
Togo              |               0|                 0|                 1|
Guyana            |               0|                 0|                 1|
Macedonia         |               0|                 0|                 1|
Iraq              |               0|                 0|                 1|

37 rows fetced, show some data.

### --In which Sport/event, Indonesia has won highest medals.
```sql
SELECT
  sport,
  COUNT(medal) AS medal_count
FROM 
	athlete_events ae 
WHERE 
	team = 'Indonesia' AND medal IS NOT NULL
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1
```
sport    |medal_count|
---------|-----------|
Badminton|         14|
