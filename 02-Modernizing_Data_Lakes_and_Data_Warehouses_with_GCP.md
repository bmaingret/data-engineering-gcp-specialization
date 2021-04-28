# Modernizing Data Lakes and Data Warehouses with GCP

- [1. Introduction to Data Engineering](#1-introduction-to-data-engineering)
  - [1.1. Challenges](#11-challenges)
  - [1.2. BigQuery as a data warehouse](#12-bigquery-as-a-data-warehouse)
  - [1.3. Data lakes and data warehouses](#13-data-lakes-and-data-warehouses)
  - [1.4. Transactional databases vs Data warehouse](#14-transactional-databases-vs-data-warehouse)
  - [1.5. Serve other teams](#15-serve-other-teams)
  - [1.6. Data access and governance](#16-data-access-and-governance)
  - [1.7. Production-ready pipelines](#17-production-ready-pipelines)
  - [1.8. Summary](#18-summary)
- [2. Building a data lake](#2-building-a-data-lake)
  - [2.1. Storage and ETL on GCP](#21-storage-and-etl-on-gcp)
    - [2.1.1. How to chose? Some considerations](#211-how-to-chose-some-considerations)
    - [2.1.2. Cloud Storage](#212-cloud-storage)
      - [2.1.2.1. Overview](#2121-overview)
      - [2.1.2.2. Security](#2122-security)
    - [2.1.3. Transactional and Data analytics workloads](#213-transactional-and-data-analytics-workloads)

## 1. Introduction to Data Engineering

Build data pipelines. Why?

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_pipeline.png)


### 1.1. Challenges

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_challenges.png)

**Access to data**

* Spread accross tools
* unstructured
* siloed

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_siloed.png)

**Data accuracy and quality**

* Missing data
* date/time issues
* proprietary format

**Availability of computational resources**

* manage on-demand capacity
* missmatch and unefficient

**Query performance**

* concurrency
* manage software and hardware
* different engines

### 1.2. BigQuery as a data warehouse

* organized data
* queries on external data sources
* similar to rdbms with tables and views
* IAM
* on-demand storage and compute

Focus on getting insights from data and not managing infrastructure

### 1.3. Data lakes and data warehouses

Data warehouse: store for that data is cleaned and transformed (vs data lake which is raw data)

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_lake_to_wh.png)

BigQuery can be used as data wh but also as only a query engine to Cloud SQL, Cloud Storage, Google Drive

### 1.4. Transactional databases vs Data warehouse

RDBMS optimized for write to records vs WH optimized for columns read

### 1.5. Serve other teams

* ML: features for ml training but also live and in production
* BI: improve query perf -> BI Engine (in-memory BI, column oriented)
* other data engineering teams: performance and availability requirements and monitoring -> stackdriver
  

### 1.6. Data access and governance

* who should have access?
* PII

Data Catalog:
* managed data discovery (queriable metadata)
* data loss prevention api (classification and redaction)

### 1.7. Production-ready pipelines

Cloud Composer -> managed Apache Airflow

### 1.8. Summary

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_lake_wh.png)

[Google Cloud in 4 words](https://github.com/gregsramblings/google-cloud-4-words)
![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_4_words.png)


## 2. Building a data lake

Consolidating data from various environments into on your environment.

Data is kept raw, can be files/blobs.

Data WH on the other hand:
* only load data into it when its use has been defined
* consistent format and schema

### 2.1. Storage and ETL on GCP

GCP possibilities:

* Cloud SQL/Cloud Spanner
* Cloud Storage
* Firebase
* BigTable
  
#### 2.1.1. How to chose? Some considerations 
* source of data
* destination of data
* amount of transformation
* volume of data

Amount of transformation?

* Extract and Load: data already in a format supported (e.g. Avro)
* Extract, Load & Transform : Similar but you need to do moderate cleaning transformation
* Extract, Transform & Load : You expect transformation to greatly reduce your data size, or data is binary/proprietary format

#### 2.1.2. Cloud Storage

##### 2.1.2.1. Overview

* Persistent storage
* Object store but allows for some file system like control

Composed of 

* Buckets (Single global namespace!): container for objects
* Objects: data + metadata

Bucket properties

* Location
* Availability: zone, multi zones, region, multi regions
* Frequency of access: nearline, coldline, etc.
  
File system-like

* Path like: '/' only characters even if looks like filesystem
* However you cannot directly list files in a directory (directory doesn't exist) -> regex filename

Object management

* Retention policy
* Versioning
* Lifecycle management (e.g. move after X days to coldline)
  
##### 2.1.2.2. Security

Security through Cloud IAM and Access Lists

* Project roles: create/delete buckets
* Bucket roles: reader, writer, owner, set acl policy
ACL can be disabled

Data encryption 

* Data is encrypted at rest
* Keys managed by Google (Google Managed Encryption Keys and Cloud Key Managed Service)
* Keys can be managed by customer as well (Customer Managed Encryption Key) in Cloud Key Management Services
* Keys can be managed externally (Customer Supplied Encryption Key)
* Data can be stored before upload (in addition to Google data encryption)

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_keys.png)

Data audit

* Lock on data (bucket/obejct)
* Log on access
* Retention policy


#### 2.1.3. Transactional and Data analytics workloads

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_data_types.png)

**Transactional**: Simple queries, involving few records, storing operational data, 80% writes / 20% reads --> OLTP
**Analytical**: Complex queries, involving lots of records and multiple tables, storing consolidated data, 80% reads / 20% writes --> OLAP

Pipelines between the systems. Can take part of Cloud Storage as a staging area + BigQuery federated queries on CSV/Parquet/Apache ORC files.
