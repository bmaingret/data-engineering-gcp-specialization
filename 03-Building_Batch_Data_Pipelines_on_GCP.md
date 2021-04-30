# Building Batch Data Pipelines on GCP

- [1. EL, ELT, ETL](#1-el-elt-etl)
- [2. Executing Spark on Cloud Dataproc](#2-executing-spark-on-cloud-dataproc)
  - [2.1. Hadoop ecosystem](#21-hadoop-ecosystem)
  - [2.2. Hadoop on Dataproc](#22-hadoop-on-dataproc)
  - [2.3. GCS versus HDFS](#23-gcs-versus-hdfs)
  - [2.4. Optimizations](#24-optimizations)
    - [2.4.1. Dataproc storage](#241-dataproc-storage)
    - [2.4.2. Dataproc templates and autoscaling](#242-dataproc-templates-and-autoscaling)
    - [2.4.3. Dataproc monitoring](#243-dataproc-monitoring)

## 1. Transforming data

### Comparisons EL, ELT, ETL

Extract-Load

* Cloud Storage -> BigQuery
* Batch load of historical data
* Only if data are clean and correct

Extract-Load-Transform

* Cloud Storage -> BigQuery
* Transform using views or to other tables
* If unsure about how data is going to be used
* When transformation can be expressed in SQL 

Extract-Transform-Load

* Many sources are supported
* Transform using Dataflow -> BigQuery
* Data streaming / Continuous flow

### Data quality processing

![](/assets/03-Building_Batch_Data_Pipelines_on_GCP_data_quality.png)


### Beyond Dataflow and BigQuery

Alternatives for transformation

* Cloud Dataflow (Apache Beam): streaming data
* Cloud Dataproc: managed Apache Hadoop/Spark
* Cloud Data Fusion: UI for designing transformation that can be deployed on Dataproc

When?

* Latency, througput: Bigtable
* Reuse Spark pipeline: Dataproc
* Visual pipeline building: Data Fusion

### Data Lineage and Data Catalog

Keep track of information about data --> Metadata: use tags (key:value) on datasets, tables and views

* where it came from
* transformation processes
* current location/condition

## 2. Executing Spark on Cloud Dataproc

### 2.1. Hadoop ecosystem

![](/assets/03-Building_Batch_Data_Pipelines_on_GCP_hadoop.png)

Spark allows to better use Hadoop resources and provides in-memory processing. Works as a declarative programming language: describe what you want to do, and not how so that Spark can find the best way to accomplish it with the best usage of resources.

### 2.2. Hadoop on Dataproc

Run existing Spark jobs on Dataproc to leverage usual cloud advantages:

* scalable, autoscalable
* managed
* pay as you use
* flexible: allow for multiple environments/versions
* easily add software
* integration with GCP storage products

### 2.3. GCS versus HDFS

Initially Hadoop was built on top of MapReduce where the paradigm was that data was local to the processing unit.

Separation of compute and storage allows for more flexibility, better scaling, cost efficiency... Possible thanks to high bw network.

Keep in mind

* avoid small read, use larger block sizes
* avoid iterating on nested directory

Upload (push) frequently data to GCP using DistCp and pull rarely used data, all using DistCp.

### 2.4. Optimizations

* Localisation
* GCP network configuration
* Number of file < 10k, increase file size, and block size
* On GCP, smaller SSD will work slower
* Enough VM?
* Flexible cloud doesn't oppose capacity calculations
* More VM means more maximum storage 

#### 2.4.1. Dataproc storage

Local HDFS when

* lots of metadata operations (small file, multiple directories/partitions)
* modification/move/rename directory (GCS is not friend with these operations since objects are immutable)
* lots of appends operation
* heavy I/O
* very low I/O latency requirements (<10 ms)

Drawbacks

* Storage more expensive in Dataproc than GCS (and other GC storage options)
* Storage not compatible with other GCP services
* Data needs to be persisted somewhere else to be able to remove clusters
* Makes using ephemeral clusters and specific job clusters more difficult (data needs to be moved)

GCS works well as initial and final source of data : one read, one write

#### 2.4.2. Dataproc templates and autoscaling

Workflow template (YAML file) -> DAG, then can 

* create cluster
* select existing cluster
* submit jobs
* delete clusters

Can take advantage of autoscaling during job execution. Pay attention to:

* starting nodes (improve efficiency)
* maximum nodes
* scale up/down factor and cooldown period: how fast cluster will adapt

#### 2.4.3. Dataproc monitoring

--> Stackdriver

Use labels to search logs faster (only logs for creation)

Set the log level according to your needs

* StackDriver
* Spark context


## Managed Data Pipelines

### Building Batch data pipeline with Cloud Data Fusion

### Orchestrating work with Cloud Composer (Apache Airflow)

## Cloud Dataflow

### Dataflow Pipelines

#### Designing pipeline


#### Transforming data with PTransforms

#### Aggregating data with GroupByKey and Combine

#### Side inputs and windows of data

### Dataflow templates

### Dataflow SQL
