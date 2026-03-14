# NYC Taxi Data Pipeline with Apache Airflow

## Overview

This project implements an **ETL data pipeline** for processing NYC taxi trip data using **Apache Airflow**.
The pipeline automatically downloads raw taxi trip data, cleans and transforms the dataset, and loads it into a **MySQL data warehouse** using a **star schema** design.

The objective of this project is to demonstrate a **Data Engineering workflow** including data ingestion, data cleaning, feature engineering, and data warehouse loading.

---

## Architecture

Data Pipeline Workflow

```
NYC Taxi Dataset
        │
        ▼
download_taxi_data
        │
        ▼
clean_taxi_data
        │
        ▼
transform_taxi_data
        │
        ▼
load_taxi_model
        │
        ▼
MySQL Data Warehouse
```

The pipeline is orchestrated using Apache Airflow DAGs.

---

## Technologies Used

* Apache Airflow (Workflow orchestration)
* Docker (Containerized environment)
* Pandas (Data processing and transformation)
* MySQL (Data warehouse)
* SQLAlchemy (Database connection)
* Python

---

## Dataset

The dataset used in this project is sourced from NYC Open Data.

Source:
https://data.cityofnewyork.us/resource/t29m-gskq.csv

The dataset contains information about taxi trips such as:

* pickup time
* dropoff time
* trip distance
* fare amount
* passenger count
* payment type

---

## Pipeline Tasks

### 1. Download Taxi Data

Task: `download_taxi_data`

This step downloads the taxi dataset from NYC Open Data and saves it locally as a CSV file.

Key operations:

* HTTP request to dataset API
* Save CSV file locally
* Push file path via Airflow XCom

Output file:

```
/tmp/nyc_taxi_raw.csv
```

---

### 2. Clean Taxi Data

Task: `clean_taxi_data`

This step performs data cleaning using Pandas.

Cleaning rules include:

* Remove rows with null values
* Remove invalid fare amounts
* Remove invalid trip distances

Output file:

```
/tmp/nyc_taxi_clean.csv
```

---

### 3. Transform Taxi Data

Task: `transform_taxi_data`

This step performs feature engineering and filtering.

New features created:

* trip_duration_minutes
* speed_mph
* fare_per_mile
* pickup_hour
* pickup_day_of_week
* is_weekend

Invalid trips are removed, such as:

* unrealistic speeds (> 80 mph)
* trips shorter than 1 minute

Output file:

```
/tmp/nyc_taxi_transformed.csv
```

---

### 4. Load Data Warehouse

Task: `load_taxi_model`

The transformed data is loaded into a MySQL data warehouse.

The schema follows a **star schema design**.

Tables created:

Dimension Tables:

* dim_time
* dim_payment

Fact Table:

* fact_trips

The fact table stores trip-level metrics such as:

* fare_amount
* trip_distance
* trip_duration_minutes
* speed_mph
* fare_per_mile
* passenger_count

Data is loaded using Pandas `to_sql()` with chunked inserts.

---

## Data Warehouse Schema

Star Schema

```
          dim_time
             │
             │
dim_payment ─── fact_trips
```

Fact Table Grain:

One row per taxi trip.

---

## How to Run the Project

### 1. Start Docker Containers

```
docker compose up -d
```

### 2. Access Airflow

Open browser:

```
http://localhost:8080
```

### 3. Enable DAG

Enable DAG:

```
download_nyc_taxi
```

### 4. Run the Pipeline

Trigger the DAG manually from the Airflow UI.

---

## Example Output

After running the pipeline, the following tables will be created in MySQL:

```
dim_time
dim_payment
fact_trips
```

---

## Learning Objectives

This project demonstrates:

* Building ETL pipelines
* Workflow orchestration using Apache Airflow
* Data cleaning and transformation with Pandas
* Designing a star schema for analytics
* Loading data into a relational data warehouse

---

## Author

Project developed for Data Engineering coursework.

Repository:
https://github.com/Dachannarak/Airflow_taxi
