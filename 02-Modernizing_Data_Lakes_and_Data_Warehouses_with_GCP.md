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
- [2. Building a Data Lake](#2-building-a-data-lake)
  - [2.1. Storage and ETL on GCP](#21-storage-and-etl-on-gcp)
  - [2.2. Cloud Storage](#22-cloud-storage)
    - [2.2.1. Overview](#221-overview)
    - [2.2.2. Security](#222-security)
  - [2.3. Transactional and Data analytics workloads](#23-transactional-and-data-analytics-workloads)
- [3. Building a Data Warehouse](#3-building-a-data-warehouse)
  - [3.1. BigQuery for Data Warehouse](#31-bigquery-for-data-warehouse)
    - [3.1.1. Structure](#311-structure)
    - [3.1.2. IAM](#312-iam)
    - [3.1.3. Queries](#313-queries)
    - [3.1.4. Loading data](#314-loading-data)
  - [3.2. Schema design](#32-schema-design)
    - [3.2.1. Explore public schemas](#321-explore-public-schemas)
    - [3.2.2. Efficient schema design](#322-efficient-schema-design)
      - [3.2.2.1. Nested and repeated fields](#3221-nested-and-repeated-fields)
  - [3.3. Partitioning and Clustering in BigQuery](#33-partitioning-and-clustering-in-bigquery)
    - [3.3.1. Partitioning](#331-partitioning)
    - [3.3.2. Clustering](#332-clustering)
  - [3.4. Managing BigQuery Reservations](#34-managing-bigquery-reservations)

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


## 2. Building a Data Lake

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
  
How to chose? Some considerations 

* source of data
* destination of data
* amount of transformation
* volume of data

Amount of transformation?

* Extract and Load: data already in a format supported (e.g. Avro)
* Extract, Load & Transform : Similar but you need to do moderate cleaning transformation
* Extract, Transform & Load : You expect transformation to greatly reduce your data size, or data is binary/proprietary format

### 2.2. Cloud Storage

#### 2.2.1. Overview

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
  
#### 2.2.2. Security

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


### 2.3. Transactional and Data analytics workloads

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_data_types.png)

**Transactional**: Simple queries, involving few records, storing operational data, 80% writes / 20% reads --> OLTP
**Analytical**: Complex queries, involving lots of records and multiple tables, storing consolidated data, 80% reads / 20% writes --> OLAP

Pipelines between the systems. Can take part of Cloud Storage as a staging area + BigQuery federated queries on CSV/Parquet/Apache ORC files.


## 3. Building a Data Warehouse

### 3.1. BigQuery for Data Warehouse

#### 3.1.1. Structure

* ```project.dataset.table```
* All datasets belong to a project
* All tables have a schema

#### 3.1.2. IAM

* Access control only on datasets: all tables in datasets are accessible
* Create new dataset with views on others datasets (must be in the same region)

#### 3.1.3. Queries

* Query BigQuery Storage (datasets & tables)
* Cloud Storage/External storage
* Results are stored in temporary tables for 24 hours (no charge)
* Output can also be stored in permanent table
* Query price based on storage/slots
* Price is separated between storage and queries

#### 3.1.4. Loading data

**Batch load**

* Supports for CSV, JSON, AVRO, Parquet, ORC, Datastore backup
* Data Transfer Service: pre-built jobs for import from existing services
* Can be triggered through Cloud Functions or use scheduled queries
* Use staging tables to import data data, transform, then load to warehouse tables

**History**

* Up to 7 days history, through snapshots.
* Fully queriable (SELECT)
* Deleted tables recoverable only if not new table with same ID (careful CREATE or REPLACE, and streaming)

**Data Transfer Service**

* Connectors and transformation templates 
* Max frequency = once a day
* Supports data staging, processing and backfills

**Limitation**

* Only 1000 DML updates per table per day (Insert, Update, Delete, Merge) -> use RDBMS if more is needed

**Custom transformation**

* Support for User-Defined functions in SQL and JS --> less optimization if JS
* Can be permanent, stored in your datasets

### 3.2. Schema design

#### 3.2.1. Explore public schemas

**TABLES** ```__TABLES__```

* project_id
* dataset_id
* row counts
* size
* last modified datetime

**INFORMATION_SCHEMA** ```INFORMATION_SCHEMA```

* ```INFORMATION_SCHEMA.COLUMNS```
  * among others, information on partitioning and clustering of columns
* ```INFORMATION_SCHEMA.SCHEMATA```
  * Exploring tables and datasets inside a project

Can allow to version dataset schemas.

#### 3.2.2. Efficient schema design

**[Normalize](https://en.wikipedia.org/wiki/Database_normalization)** data

*  increase orderliness
*  easier querying and processing
*  relational data
*  save storage space
*  increase coherence.

**Denormalize** data 

* further increase orderliness
* Data must have been normalized first
* Data is flatten allowing for duplicate values in order to
* gain processing performance

Denormalizing should happened before loading into bigquery.

Can have a negative impact for group by on columns with one-to-many relationships. In this case we can use nested fields and repeated fields to have the best of both worlds.

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_denormalize.png)


##### 3.2.2.1. Nested and repeated fields

Minimize JOIN (occurring in typical RDBMS with multiple tables) and repetition (occurring in data lake for instance).

**Structs** acts like pre-joined table 
**Arrays** acts like pre-joined table on one-to-many field. Same data type within the array.

Structs can be nested. Structs and Arrays can be used independently or conjointly.

### 3.3. Partitioning and Clustering in BigQuery

#### 3.3.1. Partitioning

Reduce data read -> reduce cost and improve efficiency

Three ways of partitioning tables:

* On any column of type ```DATE``` or ```TIMESTAMP```
* Ingestion time
* Integer-typed column with specific range and increments


Possible to require a filter on partition.

Best practice: isolate partitioned field on the left-hand side of where clauses

#### 3.3.2. Clustering

BigQuery sorts data based on values in clustering columns, and create blocks of adjacent rows.

Set-up at table creation time, order of columns is important when clustering multiple columns.

As a table gets modifier, it can get unsorted --> need to recluster. BigQuery now automatically reclusters.

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_cluster_partition.png)

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_cluster_partioning_2.png)


### 3.4. Managing BigQuery Reservations

* BigQuery commitmets are dedicated BigQuery slots with flat-rate pricing
* 100 slot increment
* regional resources

Example
![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_slots.png)

Hard to monitor Jobs and Slot usages --> BigQuery System Tables Reports and leverage `INFORMATION_SCHEMA` that offers  detailed real-time data about slot usage at job level.

BigQuery System Table reports offers a public data studio dashboard and requests that can be customized.
