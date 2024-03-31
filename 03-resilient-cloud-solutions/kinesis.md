# Amazon Kinesis

- Kinesis is a managed alternative to Apache Kafka
- Great for application logs, metrics, IoT, clickstreams
- Great for real-time big 
- Data is automatically replicate to 3 AZs
- Kinesis offers 3 types of services:
    - Kinesis Streams: capture, process and store data streams
    - Kinesis Firehose: load streams into AWS data stores (S3, Redshift and OpenSearch)
    - Kinesis Data Analytics: analyzer data streams with SQL or Apache Flink
    - Kinesis Video Streams: capture, process and store video streams

## Kinesis Data Streams

### Overview

- Streams are divided in ordered shards/partitions
- Data retention is 1 day by default, can go up to 365 days
- Provides the ability to reprocess, replay data
- Multiple applications can consume the same stream
- Provides real-time processing with scale of throughput
- Once the data inserted in Kinesis, it can not be deleted (immutability)
- Data that shares the same partition key goes to the same shard (ordering)
- Producers:
    - Kinesis SDK
    - Kinesis Producer Library (KPL)
    - Kinesis Agent
    - 3rd party libraries
- Consumers:
    - Kinesis SDK
    - Kinesis Client Library (KCL)
    - Kinesis Connector Library
    - Managed consumers: AWS Lambda, Kinesis Data Firehose, Kinesis Data Analytics

### Capacity Modes

- Provisioned mode:
    - We choose the number of shards provisioned, scale them manually or using the API
    - Each shard gets 1 MB/s (or 1000 records per second)
    - Each shard gets 2 MB/s output speed (applicable for classic or fan-out consumer)
    - We pay per shard provisioned per hour
- On-demand mode:
    - We don't need to provision or manage the capacity
    - Default capacity provisioned (4 MB/s in or 4000 records per second)
    - Scales automatically based on observed throughput peak during the last 30 days
    - We pau per stream per hour and data in/out per GB

### Shards

- One stream is made of many shards
- Billing is per shard provisioned, we can have as many shards as we want
- Data can be sent in batches
- The number of shards can evolve over time (reshard/merge)
- Records are ordered per shard, not across shards

### Records

- Data blob: data being sent, serialized as bytes, can be up to 1 MB, can represent anything
- Record key: 
    - Sent alongside a record, helps to group records in shards (same key=same shard)
    - We should use a highly distributed key to avoid the hot partition problem
- Sequence number: uniq id for each record. Added by Kinesis after ingestion

### Security

- We control access/authorization using IAM policies
- Encryption in flights using HTTPS endpoints
- Encryption at rest using KMS
- We can implement our own encryption/decryption of data on client side
- VPC Endpoints are available for Kinesis to access within VPC
- We can monitor all the API calls using CloudTrail

### Limits

- Producer: 1MB/s or 1000 messages/s at write PER SHARD other we get `ProducerThroughputException`
- Consumer Classic: 
    - 2MB at read PER SHARD across all consumers
    - 5 API class per second PER SHARD across all consumers (if 3 different applications are consuming, possibility of throttling)

### Scaling Consumers

- `GetRecords.IteratorAgeMilliseconds` CloudWatch Metric:
    - The difference between current time and when the last record of the `GetRecords` call was written to the stream
    - Used tot rack the progress if the consumer (tracks read position)
    - If we have `IteratorAgeMilliseconds = 0`, then the records that are being read are completely caught up with the producer
    - `IteratorAgeMilliseconds > 0` means we are not processing the records fast enough

### Kinesis Client Library - KCL

- It is a Java library
- It uses DynamoDB to checkpoint offsets and to track other workers and share the work amongst them
- Great for reading in a distributed manner
- We can not have more KCL applications than shards (example: in case of 6 shards, we can have up to 6 application consumers)

## Amazon Kinesis Firehose

- Fully managed service, no administration required
- Offers **Near Real Time** performance:
    - Buffer interval: 0 seconds with no buffering, up to 900 seconds
    - Buffer size: minimum 1 MB, we can set a higher number
- We can load data into AWS destinations (S3, Redshift - COPY through S3, OpenSearch), into 3rd party destinations (such as Splunk, Datadog, New Relic, MongoDB, etc.) or into a custom destination using a HTTP Endpoint
- Offers automatic scaling
- Offers data transformation ability through AWS Lambda (ex: CSV => JSON)
- Supports compression when target is S3 (GZIP, ZIP and SNAPPY)
- We pay for the amount of data going through Firehose
- Data is written in batches to the destinations
- We can send failed or all data into a backup S3 bucket

## Kinesis Data Streams vs Firehose

- Kinesis Data Streams:
    - Requires to write custom code (producer/consumer)
    - Offers real time (~200 ms latency for classic) performance
    - We must manage scaling (shard splitting/merging)
    - Data storage for 1 to 365 days, data can be replayed, streams can have multiple consumers
- Kinesis Firehose:
    - Fully managed, we can send data to S3, Redshift, OpenSearch / 3rd party / custom HTTP endpoints
    - Serverless data transformation with Lambda
    - Offers near real time performance
    - Automatically scales
    - Offers no data storage, does not support replay capability

## Kinesis Data Analytics for SQL Applications

- We can perform real-time data analytics on Kinesis Streams using SQL
- We can add reference data from Amazon S3 to enrich streaming data
- Kinesis Data Analytics is a managed service, has auto scaling capabilities by default
- We can create streams out of real-time queries
- Payment: we pay for actual consumption rate
- Data can be sent into 2 destinations: 
    - Kinesis Data Streams
    - Kinesis Data Firehose
- Use cases:
    - Time-series analytics
    - Real-time dashboards
    - Real-time metrics

## Kinesis Data Analytics for Apache Flink

- **Note: This service has been renamed to Amazon Managed Service for Apache Flink!**
- We can use Flink (Java, Scala or SQL) to process and analyze streaming data
- We can read data from 2 data sources:
    - Kinesis Data Streams
    - Amazon MSK
- With this service we run any Apache Flink application on a managed cluster on AWS:
    - We get automatic provisioning of resources, parallel computation and automatic scaling
    - We get application backups: implemented as checkpoints and snapshots
- Flink is a lot more powerful than a simple SQL query language
- Flink does not supports reading data from Firehose (use Kinesis Analytics for SQL instead)

## Machine Learning on Kinesis Data Analytics

- Kinesis Data Analytics provides 2 machine learning algorithms:
    - `RANDOM_CUT_FOREST`:
        - It is a SQL function used for anomaly detection on numeric columns in a stream
        - Uses the recent history to compute and train the model
    - `HOTSPOTS`:
        - Locate and return information about relatively dense regions in our data