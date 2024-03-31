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

## Kinesis Data Streams Overview

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

## Kinesis Data Stream Capacity Modes

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

## Kinesis Data Streams Shards

- One stream is made of many shards
- Billing is per shard provisioned, we can have as many shards as we want
- Data can be sent in batches
- The number of shards can evolve over time (reshard/merge)
- Records are ordered per shard, not across shards

## Kinesis Data Stream Records

- Data blob: data being sent, serialized as bytes, can be up to 1 MB, can represent anything
- Record key: 
    - Sent alongside a record, helps to group records in shards (same key=same shard)
    - We should use a highly distributed key to avoid the hot partition problem
- Sequence number: uniq id for each record. Added by Kinesis after ingestion

## Kinesis Data Streams Security

- We control access/authorization using IAM policies
- Encryption in flights using HTTPS endpoints
- Encryption at rest using KMS
- We can implement our own encryption/decryption of data on client side
- VPC Endpoints are available for Kinesis to access within VPC
- We can monitor all the API calls using CloudTrail

## Kinesis Data Streams Limits

- Producer: 1MB/s or 1000 messages/s at write PER SHARD other we get `ProducerThroughputException`
- Consumer Classic: 
    - 2MB at read PER SHARD across all consumers
    - 5 API class per second PER SHARD across all consumers (if 3 different applications are consuming, possibility of throttling)

## Kinesis CLient Library - KCL

- It is a Java library
- It uses DynamoDB to checkpoint offsets and to track other workers and share the work amongst them
- Great for reading in a distributed manner
- We can not have more KCL applications than shards (example: in case of 6 shards, we can have up to 6 application consumers)

## Amazon Kinesis Firehose

- Fully managed service, no administration required
- Offers **Near Real Time** (60 seconds latency minimum for non full batches) performance
- We cal load data into Redshift, S3, ElasticSearch, Splunk
- Offers automatic scaling
- Offers data transformation ability through AWS Lambda (ex: CSV => JSON)
- Supports compression when target is S3 (GZIP, ZIP and SNAPPY)
- We pay for the amount of data going through Firehose

## Kinesis Data Streams vs Firehose

- Streams:
    - Requires to write custom code (producer/consumer)
    - Offers real time (~200 ms latency for classic) performance
    - We must manage scaling (shard splitting/merging)
    - Data storage for 1 to 7 days, data can be replayed, streams can have multiple consumers
- Firehose:
    - Fully managed, we can send data to S3, Splunk, Redshift, ElasticSearch
    - Serverless data transformation with Lambda
    - Offer near real time performance (lower buffer time is 1 minute)
    - Automatically scales
    - Offers no data storage, no replayability

## AWS Kinesis Data Analytics

- We can perform real-time data analytics on Kinesis Streams using SQL
- Kinesis Data Analytics is a managed service, has auto scaling capabilities by default
- Can create streams out of real-time queries