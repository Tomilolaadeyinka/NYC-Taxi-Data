### NYC-Taxi-Data-End-to-End-Data-Pipeline

This project involves building an end-to-end data pipeline to process and analyze NYC taxi data using cloud technologies, batch processing, and data engineering principles. The pipeline integrates different stages of data processing, including ETL (Extract, Transform, Load), data warehousing, and analytics.

## Project Overview

The aim of this project is to process green taxi data for the year 2021, transforming it into a structured dataset that can be used for further analysis. The pipeline is built using tools and services like Google Cloud Platform (GCP), BigQuery, Cloud Storage, and Apache Airflow for orchestration. The final output of the pipeline is stored in a data warehouse (BigQuery) for easy querying and analysis.

## Data Pipeline Stages
 1. ** Data Extraction (Extract) **
Source Data: The source of the data is the NYC Green Taxi dataset, which contains information on trips made by green taxis in New York City, including pickup and drop-off locations, timestamps, passenger count, and fare amounts.

Cloud Storage: The data is extracted and loaded into Google Cloud Storage (GCS) buckets for further processing.

Code : 

```bash
from google.cloud import storage

Initialize a GCS client
`client = storage.Client()

Define the bucket and file to upload
`bucket_name = 'my_bucket'
file_name = 'nyc_green_taxi_data.csv'
bucket = client.get_bucket(bucket_name)

Upload the file to GCS
`blob = bucket.blob(file_name)
blob.upload_from_filename('local_path_to_file/nyc_green_taxi_data.csv')
```

# 2. Data Transformation (Transform)

The data is transformed using Apache Beam and PySpark for cleaning and reshaping the data. We ensure that the dataset is structured and ready for analysis by removing missing values, handling duplicates, and converting data types.

Code :

``from pyspark.sql import SparkSession
from pyspark.sql.functions import col

Initialize Spark session
`spark = SparkSession.builder \
    .appName("NYC Green Taxi ETL") \
    .getOrCreate()

Load the data from GCS
`data_path = "gs://my_bucket/nyc_green_taxi_data.csv"
df = spark.read.option("header", "true").csv(data_path)

Data cleaning
`df_cleaned = df.dropna() \
               .dropDuplicates() \
               .withColumn("pickup_datetime", col("pickup_datetime").cast("timestamp"))

df_cleaned.show(5)

# 3. Data Loading (Load)

The cleaned data is then loaded into BigQuery for storage and future analysis. We use Apache Airflow to automate the ETL pipeline and schedule the data loading jobs.

Code :

``from google.cloud import bigquery

Initialize BigQuery client
`bq_client = bigquery.Client()

Define the dataset and table names
`dataset_id = 'my_project.my_dataset'
table_id = 'nyc_taxi_data_2021'

Load the transformed data into BigQuery
`df_cleaned.write \
    .format('bigquery') \
    .option('table', f'{dataset_id}.{table_id}') \
    .mode('overwrite') \
    .save()

# 4. Orchestration with Apache Airflow

Apache Airflow is used for scheduling and orchestrating the ETL workflow. We define the steps of the data pipeline as tasks in an Airflow DAG (Directed Acyclic Graph), which ensures that the process runs in a specified order and monitors task status.

Code :

``from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime

def extract_data():
    # Logic to extract data
    pass

def transform_data():
    # Logic to transform data
    pass

def load_data():
    # Logic to load data into BigQuery
    pass

Define the Airflow DAG
`dag = DAG('nyc_taxi_etl', description='ETL pipeline for NYC Green Taxi data',
          schedule_interval='@daily', start_date=datetime(2025, 1, 1), catchup=False)

Define the tasks
`task1 = PythonOperator(task_id='extract_data', python_callable=extract_data, dag=dag)
task2 = PythonOperator(task_id='transform_data', python_callable=transform_data, dag=dag)
task3 = PythonOperator(task_id='load_data', python_callable=load_data, dag=dag)

Set task dependencies
task1 >> task2 >> task3

# 5. Data Warehouse and Analytics

Once the data is loaded into BigQuery, users can run SQL queries for further analysis. In this project, the data is used for generating insights into taxi trips, such as fare amounts, trip durations, and passenger counts.

Example Query:

SELECT 
    pickup_datetime,
    dropoff_datetime,
    passenger_count,
    fare_amount
FROM 
    `my_project.my_dataset.nyc_taxi_data_2021`
WHERE 
    fare_amount > 10
ORDER BY 
    pickup_datetime DESC
LIMIT 100;

# Technologies Used

Apache Airflow: Used for scheduling and orchestrating the ETL pipeline.

PySpark: For data transformation, cleaning, and reshaping.

Google Cloud Storage: For storing raw data files.

Google BigQuery: For storing processed data in a data warehouse for analytics.

Apache Beam: For handling large-scale data processing


## Steps to Run the Project

# 1. Set Up Google Cloud Environment:

Create a Google Cloud project and enable the necessary APIs (BigQuery, Cloud Storage, etc.).

Authenticate using ``gcloud auth login.

# 2. Clone the Repository:

``git clone https://github.com/your-username/nyc-taxi-data-pipeline.git
cd nyc-taxi-data-pipeline

# 3. Install Dependencies:

``pip install -r requirements.txt

# 4. Run the Pipeline:

You can execute the pipeline manually or schedule it using Apache Airflow.

Ensure that you configure the BigQuery and GCS credentials in your environment.

# 5. Query the Data in BigQuery:

Once the data is loaded, you can run SQL queries in BigQuery to analyze it.



    
