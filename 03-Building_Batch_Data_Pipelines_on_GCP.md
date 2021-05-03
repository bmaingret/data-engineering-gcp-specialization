# Building Batch Data Pipelines on GCP

- [1. Transforming data](#1-transforming-data)
  - [1.1. Comparisons EL, ELT, ETL](#11-comparisons-el-elt-etl)
  - [1.2. Data quality processing](#12-data-quality-processing)
  - [1.3. Beyond Dataflow and BigQuery](#13-beyond-dataflow-and-bigquery)
  - [1.4. Data Lineage and Data Catalog](#14-data-lineage-and-data-catalog)
- [2. Executing Spark on Cloud Dataproc](#2-executing-spark-on-cloud-dataproc)
  - [2.1. Hadoop ecosystem](#21-hadoop-ecosystem)
  - [2.2. Hadoop on Dataproc](#22-hadoop-on-dataproc)
  - [2.3. GCS versus HDFS](#23-gcs-versus-hdfs)
  - [2.4. Optimizations](#24-optimizations)
    - [2.4.1. Dataproc storage](#241-dataproc-storage)
    - [2.4.2. Dataproc templates and autoscaling](#242-dataproc-templates-and-autoscaling)
    - [2.4.3. Dataproc monitoring](#243-dataproc-monitoring)
- [3. Managed Data Pipelines](#3-managed-data-pipelines)
  - [3.1. Building Batch data pipeline with Cloud Data Fusion](#31-building-batch-data-pipeline-with-cloud-data-fusion)
  - [3.2. Orchestrating work with Cloud Composer (Apache Airflow)](#32-orchestrating-work-with-cloud-composer-apache-airflow)
- [4. Cloud Dataflow](#4-cloud-dataflow)
  - [4.1. Dataflow Pipelines](#41-dataflow-pipelines)
    - [4.1.1. Transforming data with PTransforms](#411-transforming-data-with-ptransforms)
    - [4.1.2. Aggregating data with GroupByKey and Combine](#412-aggregating-data-with-groupbykey-and-combine)
    - [4.1.3. Side inputs](#413-side-inputs)
    - [4.1.4. Windows of data](#414-windows-of-data)
  - [4.2. Dataflow templates](#42-dataflow-templates)

## 1. Transforming data

### 1.1. Comparisons EL, ELT, ETL

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

### 1.2. Data quality processing

![](/assets/03-Building_Batch_Data_Pipelines_on_GCP_data_quality.png)


### 1.3. Beyond Dataflow and BigQuery

Alternatives for transformation

* Cloud Dataflow (Apache Beam): streaming data
* Cloud Dataproc: managed Apache Hadoop/Spark
* Cloud Data Fusion: UI for designing transformation that can be deployed on Dataproc

When?

* Latency, througput: Bigtable
* Reuse Spark pipeline: Dataproc
* Visual pipeline building: Data Fusion

### 1.4. Data Lineage and Data Catalog

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


## 3. Managed Data Pipelines

### 3.1. Building Batch data pipeline with Cloud Data Fusion

Full-managed, no-code tool to build data pipeline.

Use Dataproc under the hood (supports for Dataflow is work in progress.):

* Create ephemeral execution environments in Cloud Dataproc (MapReduce, Spark, Spark Streaming).
* Use existing Cloud dataproc clusters

Cost only when pipelines are run. Data Fusion instances don't incur  cost.

Supports for metadata data lineage

* Dataset
* Columns

Extensible

* Plugins
* Templates


Pipelines

* DAG
* allow non-linear

Explore data in Wrangler

* apply transformations
* create pipeline and schedule

### 3.2. Orchestrating work with Cloud Composer (Apache Airflow)

* Serverless
* Multiple instances are possible
* Storage in GCS

Workflows

* Written in Python
* DAG
* Based on `Operator` (Python, GCP, AWS, Azure)

Scheduling

* Periodic: through code
* Event-driven by a Cloud Function. Multiple triggers are available (GCS, HTTP, Pub/Sub, Firebase,...)

Monitor

* historical log for DAG runs
* Overview per DAG and task

Logs are also available in Stackdriver.

## 4. Cloud Dataflow

![](/assets/03-Building_Batch_Data_Pipelines_on_GCP_beam.png)

* Based on Apache BEAM (= Batch + strEAM).
* Allow for the same code to be used both for batch and stream.
* Fully serverless
* Prefered if no existing Spark
* Data: pCollections of element, immutable, stored as serialized byte strings
* Transformation: pTransform each element 

![](/assets/03-Building_Batch_Data_Pipelines_on_GCP_beam2.png)

### 4.1. Dataflow Pipelines

* Define WHAT
* Dataflow will decide HOW, dynamically at each step
* Optimized graph for best execution path

![](/assets/03-Building_Batch_Data_Pipelines_on_GCP_dataflow.png)

* PCollection in are not transformed in place
* Directed graph of steps
* Can be run locally or on workers

#### 4.1.1. Transforming data with PTransforms

* `Map` for 1:1 relationship
* `FlatMap` for others
* `ParDo` implements parallel processing on each element using a `DoFn`

#### 4.1.2. Aggregating data with GroupByKey and Combine

`GroupByKey` usually follows a `Map` step. Careful on data skew at scale, since each group is worked on on a worker.

`CoGroupByKey` similar but work on two or more key-value pairs.

`Combine*` are the reduce steps, using pre-built or custom functions (can be based on `CombineFn`). Can be applied to 

* a PCollection of values: `CombineGlobally`
* a grouped Key-value pair: `CombinePerKey`

`Combine` can be parallelized on multiple workers and usually is more efficient than `GroupByKey`.

`Flatten` works similarly to a union of similar tables.

`Partition` splits PCollections into smaller ones.

#### 4.1.3. Side inputs 

Side Input is a smaller dataset than can fits into memory and can be accessed for each processed elements during `ParDo`.

#### 4.1.4. Windows of data

Default window is a global window that starts and ends with the bounded PCollection. 

![](/assets/03-Building_Batch_Data_Pipelines_on_GCP_bounded.png)

For unbounded PCollection, you can use time-based windows (also possible for bounded collections).
![](/assets/03-Building_Batch_Data_Pipelines_on_GCP_unbounded.png)

### 4.2. Dataflow templates

* Allows for configurable jobs to be launched by users.
* Several templates are provided by Google, and allows for custom templates
* Can be started programmatically
* Template Metadata allows for template documentation
