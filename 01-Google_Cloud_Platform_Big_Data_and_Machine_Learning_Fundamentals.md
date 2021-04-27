# Google Cloud Platform Big Data and Machine Learning Fundamentals

- [1. Introduction to Google Cloud Platform](#1-introduction-to-google-cloud-platform)
- [2. Choosing the right approach](#2-choosing-the-right-approach)
  - [2.1. Compute](#21-compute)
  - [2.2. Storage](#22-storage)
  - [2.3. Big Data / ML](#23-big-data--ml)
  - [2.4. User's story](#24-users-story)
- [3. Recommending Products using Cloud SQL and Spark](#3-recommending-products-using-cloud-sql-and-spark)
  - [3.1. Recommendation systems](#31-recommendation-systems)
  - [3.2. Migrating workloads to the cloud](#32-migrating-workloads-to-the-cloud)
    - [3.2.1. Set-up Apache Spark job](#321-set-up-apache-spark-job)
    - [3.2.2. Tuning  clusters](#322-tuning--clusters)
    - [3.2.3. Move storage off-clusters to Cloud Storage](#323-move-storage-off-clusters-to-cloud-storage)
- [4. Predict Visitor Purchases Using BigQuery ML](#4-predict-visitor-purchases-using-bigquery-ml)
  - [4.1. Introduction to BigQuery](#41-introduction-to-bigquery)
  - [4.2. Explore and analyze large datasets with SQL](#42-explore-and-analyze-large-datasets-with-sql)
  - [4.3. Ingest and store datasets into BigQuery](#43-ingest-and-store-datasets-into-bigquery)
  - [4.4. Use BigQuery GIS to analyze geographic data](#44-use-bigquery-gis-to-analyze-geographic-data)
  - [4.5. Apply machine learning using SQL with BigQuery ML](#45-apply-machine-learning-using-sql-with-bigquery-ml)
- [5. Real-time IoT Dashboards with Pub/Sub, Dataflow, and Data Studio](#5-real-time-iot-dashboards-with-pubsub-dataflow-and-data-studio)
  - [5.1. Modern data pipeline challenges](#51-modern-data-pipeline-challenges)
  - [5.2. Message-oriented architectures](#52-message-oriented-architectures)
  - [5.3. Designing serverless streaming pipelines with Apache Beam](#53-designing-serverless-streaming-pipelines-with-apache-beam)
  - [5.4. Visualize insights with Data Studio dashboards](#54-visualize-insights-with-data-studio-dashboards)
- [6. 6.Machine Learning on Unstructured Data](#6-6machine-learning-on-unstructured-data)

## 1. Introduction to Google Cloud Platform

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_overview.png)

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_google_research.png)

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_google_product.png)

## 2. Choosing the right approach

### 2.1. Compute

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_compute.png)

* Compute Engine: Infrastructure aaS
* Google Kubernetes Engine: Cointainer aaS
* App Engine: Platform aaS
* Cloud Function: Function aaS

### 2.2. Storage

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_storage.png)


### 2.3. Big Data / ML

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_data.png)

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_ml_pipeline.png)

### 2.4. User's story

[American Cancer Society: Analyzing breast cancer images faster and better with machine learning](https://cloud.google.com/customers/american-cancer-society/)

Key challenges:
* Compute power requirements to handle large and images

GCP stack:
* Cloud ML Engine
* Cloud Storage
* Compute Engine for preproc and orchestration

Pipeline:
* Image prepocessing
* Features with auto-encoder
* Features cluster (TF)

End result is cluster assignments for each tile, ready for further manual analysis.

## 3. Recommending Products using Cloud SQL and Spark

### 3.1. Recommendation systems

Recommendation systems are the bread and butter of today's internet.

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_recommendations_systems.png)

Explicit feedback (article rating) and implicit feedback (time spent on an article web page).

When and where to compute the recommendations.

### 3.2. Migrating workloads to the cloud

* Batch processing.
* SparkML -> managed Spark ML
* Hadoop -> managed Hadoop
* MySQL -> Cloud SQL

Why Cloud SQL?

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_cloud_sql.png)

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_gcp_data.png)


Hadoop/Spark?

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_hadoop_ecosystem.png)

Cloud Dataproc: managed Hadoop/Spark clusters

#### 3.2.1. Set-up Apache Spark job

1. Big Data > Dataproc
2. Create cluster
3. Submit a job
   1. Java class
   2. Jar files

#### 3.2.2. Tuning  clusters

Plan on-premise cluster usages and schedules jobs accordingly vs on cloud where clusters are started/run depending on the jobs that need to be run.

* Cloud Dataproc Autoscaling
* Preemptible VMs: non exclusive VM for batch, fault-tolerant workloads

All these require data to be stored off clusters.

#### 3.2.3. Move storage off-clusters to Cloud Storage

High bandwidth and network capacity allow for off-cluster storage.

Allows to use the same data from different resources.

Usually cheaper.

## 4. Predict Visitor Purchases Using BigQuery ML

### 4.1. Introduction to BigQuery

* peta-byte scale ready
* full-managed, serverless
* encryption for data at rest, and security at row/column levels
  
![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_bigquery_architecture.png)

Two different services:
* BigQuery Storage Service
* BigQuery Query Service

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_bigquery_services.png)


### 4.2. Explore and analyze large datasets with SQL

Supports standard SQL.

Supports for Views.

Alternative: Dataprep UI tool.

### 4.3. Ingest and store datasets into BigQuery

Batch and streaming data ingest from various sources.

Support for querying directly several sources without first ingesting data.

Limitations on row size (1MB).

BigTable: consider for higher throughput

Cloud Dataflow: consider if transformation/aggregation are needed midflight

BigQuery supports ARRAY and STRUCT as data types, helping to consolidate multiple data sources keeping structure.


### 4.4. Use BigQuery GIS to analyze geographic data

Additional geographic functions.

BigQuery Geo Viz.

### 4.5. Apply machine learning using SQL with BigQuery ML

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_models_structured_data.png)

Model functions available through BigQuery:

* linear reg
* binary/multi-class logref
* evaluation metrics against an evaluation dataset
* feature distribution analysis
* model weights inspection
* 
![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_e2e_bqml.png)

## 5. Real-time IoT Dashboards with Pub/Sub, Dataflow, and Data Studio

### 5.1. Modern data pipeline challenges

* variety of input sources
* volume of data
* near real-time constraints
* unstructured sources

### 5.2. Message-oriented architectures

Decoupling producers and consumers.

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_pipeline.png)

### 5.3. Designing serverless streaming pipelines with Apache Beam

* Support batch and streaming
* SDK available with builtins transformations
* available templates
* portable over multiple execution envs
* extensible

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_dataflow.png)

### 5.4. Visualize insights with Data Studio dashboards

Datastudio connects with BigQuery

* Create a new reports and add data sources
* Explore directly from BigQuery

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_datastudio_taxi.png)

## 6. 6.Machine Learning on Unstructured Data

Start with pre-built models offered by GCP especially when small datasets.

Expand with AutoML to customized prebuilt models.

AutoML is built with Neural Architectural Search.

Example with Vision API

![](/assets/01-Google_Cloud_Platform_Big_Data_and_Machine_Learning_Fundamentals_automl_vs_visionapi.png)
