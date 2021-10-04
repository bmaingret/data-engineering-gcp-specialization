# Building Resilient Streaming Analytics Systems on GCP

- [1. Processing Streaming Data](#1-processing-streaming-data)
- [2. Serverless Messaging with Cloud Pub/Sub](#2-serverless-messaging-with-cloud-pubsub)
- [3. Cloud Dataflow Streaming Features](#3-cloud-dataflow-streaming-features)
- [4. High-Throughput BigQuery and Bigtable Streaming Features](#4-high-throughput-bigquery-and-bigtable-streaming-features)
  - [4.1. Streaming into BigQuery](#41-streaming-into-bigquery)
  - [4.2. Streaming into Cloud Bigtable](#42-streaming-into-cloud-bigtable)
- [5. Advanced BigQuery Functionality and Performance](#5-advanced-bigquery-functionality-and-performance)
  - [5.1. BigQuery : advanced functionality](#51-bigquery--advanced-functionality)
  - [5.2. BigQuery : performance considerations](#52-bigquery--performance-considerations)

## 1. Processing Streaming Data
![](/assets/04-Building_Resilient_Streaming_Analytics_Systems_on_GCP/04-Building_Resilient_Streaming_Analytics_Systems_on_GCP_streaming_data.png)

*Why stream?* Sometimes only real-time information is useful (think security, finance, health, etc.).

In addition to being time constrained, streaming data is also unbounded: the data set is never complete. It also often associated with Big DAta (the 3 Vs).



## 2. Serverless Messaging with Cloud Pub/Sub

** Highly available, scalable and asynchronous messaging bus**

**Publish/Suscribe patterns**

* Multiple publishers can post to the same topic
* Multiple subscribers can get a post from the same subscription
* A topic can have multiple scubscriptions
* A subscriptions subscribes to only one topic

**Push and Pull delivery**: in both an ACK is required at the subscription for each message. If ack not received before the ack deadline, message is resent. When there are multiple subscribers, only one need to acknowledge the message.

![](/assets/04-Building_Resilient_Streaming_Analytics_Systems_on_GCP/04-Building_Resilient_Streaming_Analytics_Systems_on_GCP_push_pull.png)

Keep in mind:

* No guarantees on latency
* Order not preserved
* Duplication may happen

Dataflow can be used to deduplicate message during a window.

## 3. Cloud Dataflow Streaming Features

**Windowing**

* Fixed windows: time based finite chunks, non overlapping
* Sliding windows: same but overlapping, with a minimum gap
* Sessions windows: minimum gap, and timing dependent on another element

**Watermark**

Apache beam allows the use of a watermark to account for little lag time and decide to which window allocate the data and/or to discard the data.

POssible to define custom triggers based on watermarks, counts, 

## 4. BigQuery Streaming Features

Specific API to stream data one at a time directly into a preexisting BigQuery table, different from a load job. Careful: **there is a cost for streaming inserts**

### 5. Bigtable for High Throughput

BigTable requires to have a good understanding of the input data and how it will be queried.

BigTable over BigQuery

* ms latency vs seconds
* Non-structured key-value data, <10MB vs transactions, highly structured data, SQL-like requirements

e.g. Machine Learning data: financial, marketing, purchasing, IoT sensors, monitoring.

**How it works?**

Works in cluster (managed services, 3 replicas by default), based on tables stored in Colossus. Data can be manipulated directly or through pointers to move data.

Auto-learning access patterns to balance data over clusters (best with large quantity of data, with constant access).

**Simplified for speed**

* Based on only one row key and multiple columns
* Hence not all data and operations are efficient (scan, sort, search)

**Example: Flights of the world**

Best row key (depend on the most common type of requests): custom row key based on origin, arrival and date (think about earliest or latest) --> only a scan to find flights from/to between dates

**Column Families**: allows to get partial data from a row instead of a full row

**Data modification**

* *Deletion*: marked for deletion but row can still exists
* *Update*: *deletion* and new row appended at the end
* Finally compaction effectively remove rows marked for deletion

**Data organization for performance**

* Groupe data: fewer cells in a row is better
* Use column families
* Use row keys to place identical/similar data in adjacent rows
* Replications allows to redirect queries to specific clusters
* Use Key Visualizer to analyze read/write patterns

## 6. Advanced BigQuery Functionality and Performance

*London Bike Share dataset*

* 24+ millions bike share trips
* 2 tables: cycle trips and cycle station locations

*Analysis goal*

* finding fastest bike share commuters
* finding stations with fastest and lowest bike turnover time
* bike maintenance based on usage metrics



### Geographic Information System functions

*Finding fastest bike share commuters*

* Using GIS functions to convert spatial data and calculate distance
* Can visualized in BigQuery GeoViz

Location data always needs to be converted into WKT.

### WITH clauses vs permanenet tables

Use *WITH* to organize queries. 

Why not permanent tables:

* BigQuery actually optimizes query in a way that can make them perform better
* Keep readability a priority

### Analytical window functions

*Finding stations with fastest and lowest bike turnover time*

Find the previous end date of rental to get the time at station.

* Organized and filter the data with *OVER(PARTITION BY)*
* Use *LAG* to get the previous end date 



### Ranking functions + ARRAY

*Bike maintenance based on usage metrics*

Using *RANK* to get the current trip number (probably could use a *COUNT*)

Using ARRAY to keep details about aggregation results.

### Performance considerations

![](/assets/04-Building_Resilient_Streaming_Analytics_Systems_on_GCP/04-Building_Resilient_Streaming_Analytics_Systems_on_GCP_optimizations.png)

* Limit read columns
* Filter early and often
* Join with largest table on left
* Group by on low cardinality
* Use approx grouping functions when possible
* Order on the outermost query
* Partitions tables on time data
