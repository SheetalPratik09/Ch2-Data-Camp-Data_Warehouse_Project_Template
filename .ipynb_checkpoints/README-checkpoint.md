# 🎵 Cloud Data Warehouse ETL Pipeline with Amazon Redshift

## 📌 Project Overview

This project builds an ETL (Extract, Transform, Load) pipeline to load music streaming data from Amazon S3 into Amazon Redshift and transform it into a star schema optimized for analytical queries on song play activity.

The pipeline:
1. Loads raw JSON data from S3 into staging tables in Redshift.
2. Transforms and inserts the data into fact and dimension tables.
3. Enables fast analytical queries on user listening behavior.

This project demonstrates core data engineering skills including cloud data warehousing, SQL schema design, and automated ETL pipelines using Python.

---

## 📊 Schema Design

A **star schema** is used to optimize analytical queries.

### ⭐ Fact Table

**songplays**  
Records of song play events (only `NextSong` actions)

| Column | Description |
|--------|------------|
| songplay_id | Primary key |
| start_time | Timestamp of song play |
| user_id | User identifier |
| level | User subscription level |
| song_id | Song identifier |
| artist_id | Artist identifier |
| session_id | Session number |
| location | User location |
| user_agent | Browser/device info |

### 📦 Dimension Tables

**users**

| user_id | first_name | last_name | gender | level |

**songs**

| song_id | title | artist_id | year | duration |

**artists**

| artist_id | name | location | latitude | longitude |

**time**

| start_time | hour | day | week | month | year | weekday |

---

## 🗂️ Datasets

All datasets are stored in Amazon S3 and provided by Udacity.

### 🎼 Song Dataset
- Location: `s3://udacity-dend/song_data`
- JSON files containing metadata about songs and artists
- Partitioned by first three letters of track ID

Example:
song_data/A/B/C/TRABCEI128F424C983.json


### 📝 Log Dataset
- Location: `s3://udacity-dend/log_data`
- JSON logs of user activity from music streaming app
- Partitioned by year and month

Example:
log_data/2018/11/2018-11-12-events.json



### 📄 Log Metadata
- Location: `s3://udacity-dend/log_json_path.json`
- Used by Redshift COPY command to correctly parse nested JSON structure

### ⚠️ Region Note

The S3 bucket `udacity-dend` is located in **us-west-2**, while the Redshift cluster is in **us-east-1**.  
Therefore, the COPY command explicitly specifies:
REGION 'us-west-2'


to enable cross-region data loading.

---

## ⚙️ ETL Pipeline Process

### Step 1: Create Tables

`create_tables.py` performs:
- Drop existing tables if present
- Create staging, fact, and dimension tables

### Step 2: Load Staging Tables

`etl.py` executes COPY commands:
- Loads log data into `staging_events`
- Loads song data into `staging_songs`

### Step 3: Insert Analytics Tables

Data is transformed and inserted into:
- `songplays` (fact table)
- `users`, `songs`, `artists`, `time` (dimension tables)

Transformations include:
- Timestamp conversion from epoch milliseconds
- Filtering only `NextSong` events
- Joining song and event data to match song and artist IDs

---

## ▶️ How to Run the Project

### 🔧 Prerequisites

- Python 3
- Amazon Redshift Serverless or Cluster
- IAM Role with:
  - AmazonS3ReadOnlyAccess
  - Redshift permissions

### 📁 Configuration

Update `dwh.cfg` with:

- Redshift endpoint
- Database name
- User
- IAM Role ARN

(Do NOT commit real passwords to GitHub)

---

### 🚀 Execution Steps

Run the scripts in this order:

```bash
python create_tables.py
python etl.py


If successful, data will be loaded and available for querying in Redshift.

SELECT COUNT(*) FROM staging_events;
SELECT COUNT(*) FROM staging_songs;

SELECT COUNT(*) FROM songplays;
SELECT COUNT(*) FROM users;
SELECT COUNT(*) FROM songs;
SELECT COUNT(*) FROM artists;
SELECT COUNT(*) FROM time;

SELECT * FROM songplays LIMIT 5;

All tables should contain data greater than zero rows.

| File               | Description                                          |
| ------------------ | ---------------------------------------------------- |
| `create_tables.py` | Drops and creates all staging and star schema tables |
| `etl.py`           | Loads data from S3 into staging and analytics tables |
| `sql_queries.py`   | Contains all SQL queries used by the ETL pipeline    |
| `dwh.cfg`          | Configuration file for Redshift and S3 paths         |
| `README.md`        | Project documentation                                |


🧼 Code Quality & Modularity
SQL logic is centralized in sql_queries.py
ETL logic is separated into reusable functions
Scripts follow logical step-by-step execution
Variable and function naming follows PEP8 conventions
Functions include docstrings explaining their purpose
This structure supports easy debugging, maintenance, and future scaling.


✅ Conclusion
This project demonstrates how to build a cloud-based analytical data warehouse using:
Amazon S3 (Data Lake)
Amazon Redshift (Cloud Data Warehouse)
Python-based ETL pipelines
Star schema modeling for analytics
It provides a strong foundation for building scalable analytics platforms in real-world data engineering workflows.









