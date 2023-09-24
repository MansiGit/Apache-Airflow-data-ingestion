<img src="https://github.com/MansiGit/Apache-Airflow-data-ingestion/raw/main/airflow.svg" width="100" height="100" alt="Apache Airflow">


# Airflow Data ingestion pipeline

## Introduction:
I've created this project to demo a real-time data ingestion pipeline using Apache Airflow. Details:
1. Set up Airflow and SQLite database using Docker
2. Use Python airflow library to write a DAG to transform & load data from a csv to a Sqlite table.
3. Scheduled the DAG to run every 1 hr to pick up new data.

## DAG Details
### Transform raw data:
* Function [**`transform_data`**](https://github.com/MansiGit/Apache-Airflow-data-ingestion/blob/5b74d8ed201f778e564ab0399d377023642941c4/dags/data_ingestion_dag/main.py#L33) processes data for a given date and time. 
* **Data Source:** Reads data from CSV files, including 'booking,' 'client,' and 'hotel' datasets.
* **Standardization:** Ensures consistent date formatting for clarity and accuracy.
* **Currency Conversion:** Converts costs from Euros to British Pounds for uniformity.
* **Data Refinement:** Removes unnecessary columns to simplify the dataset.
* **Structured Output:** Saves the processed data in a well-organized format.

### Load transformed data:
* Function [**`load_data`**](https://github.com/MansiGit/Apache-Airflow-data-ingestion/blob/5b74d8ed201f778e564ab0399d377023642941c4/dags/data_ingestion_dag/main.py#L72) loads data for a specified date and time
Database Connection: It connects to a SQLite database located at "/usr/local/airflow/db/datascience.db."
* **Table Creation:** If the "booking_record" table doesn't exist, it creates it with a predefined schema, defining column names and data types.
* **Data Reading:** Processed data is read from a CSV file based on the provided date and time.
* **Data Insertion:** The read data is then appended to the "booking_record" table in the database.

### DAG Definition:
* Initialize default arguments for the DAG, including the owner and start date.
* Define the DAG named 'booking_ingestion' with a description and scheduling interval.
* Create two PythonOperator tasks, task_1 and task_2, representing the data transformation and loading steps, respectively. These tasks are linked in a sequence, where task_2 depends on the successful completion of task_1.

## Table schema
```
    CREATE TABLE IF NOT EXISTS booking_record (
        client_id INTEGER NOT NULL,
        booking_date TEXT NOT NULL,
        room_type TEXT(512) NOT NULL,
        hotel_id INTEGER NOT NULL,
        booking_cost NUMERIC,
        currency TEXT,
        age INTEGER,
        client_name TEXT(512),
        client_type TEXT(512),
        hotel_name TEXT(512)
```


## Step by step instructions

1. Prepare the database first `docker-compose up airflow-init`

This is going to create db/airflow.db sqlite database. 
Airflow WebServer and Scheduler are created
    ![Apache Webserver](image.png)
    ![Apache Scheduler](image-1.png)

2. Add raw data for current execution date and hour to be ingested

3. Launch Airflow `docker-compose up`

Wait for scheduler and webserver to get healthy, then go to `localhost:8081` 

```python
username: admin
password: airflow
```

4. Enable the DAG and watch it ingest data.
![Data Ingestion](image-2.png)
