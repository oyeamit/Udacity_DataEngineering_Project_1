# Project: Data Modeling with Postgres

**Sparkify**, *a startup*, is interested in understanding what songs users are listening to on their new music streaming app. This project aims to apply data modeling with Postgres and build an ETL pipeline using Python on the collected data from the above-mentioned music streaming app.


### Quick Start

1.  Run `create_tables.py` to create database and respective tables.
2.  Run `test.ipynb` to confirm the creation of your tables with the correct columns. Make sure to click "*Restart kernel*" to close the connection to the database after running this notebook.
3.  Run `etl.py` to process the entire dataset.

### Structure

The file explorer is accessible using the button in left corner of the The repository contains the following elements:

-   `data/` contains song and log files. `data/song_data/` contains details regarding metadata about a song and the artist. `data/log_data/` contains details on user activity in JSON format
-   `sql_queries.py` defines all the SQL queries required for the creation of the database schema and ETL pipeline.
-   `create_tables.py` creates the Sparkify database and tables.
-   `etl.py` defines the ETL pipeline. It reads and transforms the song and log JSON files from the local directory and stores them into the Postgres database. `etl.ipynb` contains the same code and is used for prototype designing during the initial stage.
-   `test.ipynb` displays the first few rows of each table to let us check the database for Sanity Tests.

### Dataset

The dataset has two kind of files. 

 1. Each file stored on the path `data/song_data/` contains information about the metadata of the song. Each file has excatly one JSON object. A sample JSON object looks as below:
```
{
 "num_songs":1,
 "artist_id":"AR8ZCNI1187B9A069B",
 "artist_latitude":null,
 "artist_longitude":null,
 "artist_location":"",
 "artist_name":"Planet P Project",
 "song_id":"SOIAZJW12AB01853F1",
 "title":"Pink World",
 "duration":269.81832,
 "year":1984
}
```
 2. Each file stored on the path `data/log_data/` will contains information regarding the user activity on Sparkify. Each file has many JSON object. A sample JSON object looks as below:
```
{
   "artist":null,
   "auth":"Logged In",
   "firstName":"Walter",
   "gender":"M",
   "itemInSession":0,
   "lastName":"Frye",
   "length":null,
   "level":"free",
   "location":"San Francisco-Oakland-Hayward, CA",
   "method":"GET",
   "page":"Home",
   "registration":1540919166796.0,
   "sessionId":38,
   "song":null,
   "status":200,
   "ts":1541105830796,
   "userAgent":"\"Mozilla\/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/36.0.1985.143 Safari\/537.36\"",
   "userId":"39"
}
```

### Database schema

Database schema is created using a **star** schema optimized for queries on song play analysis.

![Database star schema](db_star-schema.jpeg)


The database contains the following **fact** table:
-   *songplays* - user song plays
```
songplays (
        songplay_id SERIAL PRIMARY KEY,
        start_time timestamp NOT NULL,
        user_id int NOT NULL,
        level varchar,
        song_id varchar,
        artist_id varchar,
        session_id int,
        location varchar,
        user_agent varchar
        );
```
*songplays* has foreign keys to the following **dimension** tables:

-   *users*
```
users (
	user_id int PRIMARY KEY,
	first_name varchar,
	last_name varchar,
	gender varchar,
	level varchar
);
```
-   *songs*
```
songs (
	song_id varchar PRIMARY KEY,
	title varchar,
	artist_id varchar,
	year int,
	duration numeric
	);
```
-   *artists*
```
artists (
	artist_id varchar PRIMARY KEY,
	name varchar,
	location varchar,
	latitude numeric,
	longitude numeric
);
```
-   *time*
```
time (
	start_time timestamp PRIMARY KEY,
	hour int,
	day int,
	week int,
	month int,
	year int,
	weekday int
);
```
### Example queries:
```
# load sql extention in jupyter notebook
%load_ext sql
# connect to postgres database
%sql postgresql://student:student@127.0.0.1/sparkifydb


# Total Number of song played after Nov 12, 2018
%sql SELECT COUNT(*) FROM songplays WHERE start_time > '2018-11-12';
## 5152


# Total number of songs played by user 'Noah Chavez'
%sql SELECT COUNT(*) FROM songplays \
    WHERE songplays.user_id = \
        (SELECT user_id FROM users \
             WHERE first_name = 'Noah' \
                 AND last_name = 'Chavez');
## count
## 7
```