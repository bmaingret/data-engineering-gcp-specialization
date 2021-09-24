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

Specific API to stream data one at a time directly into a preexisting BigQuery table. Careful: **there is a cost for streaming inserts**




### 5. Bigtable for High Throughput

## 6. Advanced BigQuery Functionality and Performance
