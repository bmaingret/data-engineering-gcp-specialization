# Modernizing Data Lakes and Data Warehouses with GCP

## Introduction to Data Engineering

Build data pipelines. Why?

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_pipeline.png)


### Challenges

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

### BigQuery as a data warehouse

* organized data
* queries on external data sources
* similar to rdbms with tables and views
* IAM
* on-demand storage and compute

Focus on getting insights from data and not managing infrastructure

### Data lakes and data warehouses

Data warehouse: store for that data is cleaned and transformed (vs data lake which is raw data)

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_lake_to_wh.png)

BigQuery can be used as data wh but also as only a query engine to Cloud SQL, Cloud Storage, Google Drive

### Transactional databases vs Data warehouse

RDBMS optimized for write to records vs WH optimized for columns read

### Serve other teams

* ML: features for ml training but also live and in production
* BI: improve query perf -> BI Engine (in-memory BI, column oriented)
* other data engineering teams: performance and availability requirements and monitoring -> stackdriver
  

### Data access and governance

* who should have access?
* PII

Data Catalog:
* managed data discovery (queriable metadata)
* data loss prevention api (classification and redaction)

### Production-ready pipelines

Cloud Composer -> managed Apache Airflow

### Summary

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_lake_wh.png)

[Google Cloud in 4 words](https://github.com/gregsramblings/google-cloud-4-words)
![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_4_words.png)


## Building a data lake

Consolidating data from various environments into on your environment.

Data is kept raw, can be files/blobs.

Data WH on the other hand:
* only load data into it when its use has been defined
* consistent format and schema

### Storage and ETL on GCP

GCP possibilities:

* Cloud SQL/Cloud Spanner
* Cloud Storage
* Firebase
* BigTable
  
#### How to chose? Some considerations 
* source of data
* destination of data
* amount of transformation
* volume of data

Amount of transformation?

* Extract and Load: data already in a format supported (e.g. Avro)
* Extract, Load & Transform : Similar but you need to do moderate cleaning transformation
* Extract, Transform & Load : You expect transformation to greatly reduce your data size, or data is binary/proprietary format

#### Cloud Storage

##### Overview

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
  
##### Security

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


#### Transactional and Data analytics workloads

![](/assets/02-Modernizing_Data_Lakes_and_Data_Warehouses_with_GCP_data_types.png)

**Transactional**: Simple queries, involving few records, storing operational data, 80% writes / 20% reads --> OLTP
**Analytical**: Complex queries, involving lots of records and multiple tables, storing consolidated data, 80% reads / 20% writes --> OLAP

Pipelines between the systems. Can take part of Cloud Storage as a staging area + BigQuery federated queries on CSV/Parquet/Apache ORC files.
