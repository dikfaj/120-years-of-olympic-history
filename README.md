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
1912|Summer|Stockholm             |
1920|Summer|Antwerpen             |
1924|Summer|Paris                 |
1924|Winter|Chamonix              |
1928|Summer|Amsterdam             |
1928|Winter|Sankt Moritz          |
1932|Summer|Los Angeles           |
1932|Winter|Lake Placid           |
1936|Summer|Berlin                |
1936|Winter|Garmisch-Partenkirchen|
1948|Summer|London                |
1948|Winter|Sankt Moritz          |
1952|Summer|Helsinki              |
1952|Winter|Oslo                  |
1956|Summer|Melbourne             |
1956|Summer|Stockholm             |
1956|Winter|Cortina d'Ampezzo     |
1960|Summer|Roma                  |
1960|Winter|Squaw Valley          |
1964|Summer|Tokyo                 |
1964|Winter|Innsbruck             |
1968|Summer|Mexico City           |
1968|Winter|Grenoble              |
1972|Summer|Munich                |
1972|Winter|Sapporo               |
1976|Summer|Montreal              |
1976|Winter|Innsbruck             |
1980|Summer|Moskva                |
1980|Winter|Lake Placid           |
1984|Summer|Los Angeles           |
1984|Winter|Sarajevo              |
1988|Summer|Seoul                 |
1988|Winter|Calgary               |
1992|Summer|Barcelona             |
1992|Winter|Albertville           |
1994|Winter|Lillehammer           |
1996|Summer|Atlanta               |
1998|Winter|Nagano                |
2000|Summer|Sydney                |
2002|Winter|Salt Lake City        |
2004|Summer|Athina                |
2006|Winter|Torino                |
2008|Summer|Beijing               |
2010|Winter|Vancouver             |
2012|Summer|London                |
2014|Winter|Sochi                 |
2016|Summer|Rio de Janeiro        |

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
1912 Summer|               28|
1920 Summer|               29|
1924 Summer|               45|
1924 Winter|               19|
1928 Summer|               46|
1928 Winter|               25|
1932 Summer|               47|
1932 Winter|               17|
1936 Summer|               49|
1936 Winter|               28|
1948 Summer|               58|
1948 Winter|               28|
1952 Summer|               66|
1952 Winter|               30|
1956 Summer|               70|
1956 Winter|               32|
1960 Summer|               82|
1960 Winter|               30|
1964 Summer|               93|
1964 Winter|               36|
1968 Summer|              110|
1968 Winter|               36|
1972 Summer|              119|
1972 Winter|               34|
1976 Summer|               90|
1976 Winter|               36|
1980 Summer|               80|
1980 Winter|               36|
1984 Summer|              138|
1984 Winter|               48|
1988 Summer|              155|
1988 Winter|               56|
1992 Summer|              167|
1992 Winter|               64|
1994 Winter|               67|
1996 Summer|              195|
1998 Winter|               72|
2000 Summer|              198|
2002 Winter|               76|
2004 Summer|              199|
2006 Winter|               78|
2008 Summer|              201|
2010 Winter|               81|
2012 Summer|              202|
2014 Winter|               88|
2016 Summer|              203|

### Which Sports were just played only once in the olympics?
```sql
WITH t1 AS (
SELECT 
  DISTINCT games,
  sport
FROM
  athlete_events ae
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
