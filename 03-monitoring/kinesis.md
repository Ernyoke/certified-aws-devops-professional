# AWS Kinesis

- Kinesis is a managed alternative to Apache Kafka
- Great for application logs, metrics, IoT, clickstreams
- Great for real-time big 
- Data is automatically replicate to 3 AZs
- Kinesis offers 3 types of services:
    - Kinesis Streams: low latency streaming ingest at scale
    - Kinesis Analytics: perform real-time analytics on streams using SQL
    - Kinesis Firehose: load streams into S3, Redshift and ElasticSearch

## Kinesis Streams Overview

- Streams are divided in ordered shards/partitions
- Data retention is 1 day by default, can go up to 7 days
- Provides the ability to reprocess, replay data
- Multiple applications can consume the same stream
- Provides real-time processing with scale of throughput
- Once the data inserted in Kinesis, it can not be deleted (immutability)

## Kinesis Streams Shards

- One stream is made of many shards
- Billing is per shard provisioned, we can have as many shards as we want
- Data can be sent in batches
- The number of shards can evolve over time (reshard/merge)
- Records are ordered per shard, not across shards

## Kinesis Stream Records

- Data blob: data being sent, serialized as bytes, can be up to 1 MB, can represent anything
- Record key: 
    - Sent alongside a record, helps to group records in shards (same key=same shard)
    - We should use a highly distributed key to avoid the hot partition problem
- Sequence number: uniq id for each record. Added by Kinesis after ingestion

## Kinesis Data Streams Limits

- Producer: 1MB/s or 1000 messages/s at write PER SHARD other we get `ProducerThroughputException`
- Consumer Classic: 
    - 2MB at read PER SHARD across all consumers
    - 5 API class per second PER SHARD across all consumers (if 3 different applications are consuming, possibility of throttling)
- Data retention: 25 hours data retention by default which can be expended to 7 days

## Kinesis Producers

- Kinesis SDK
- Kinesis Producer Library (KPL)
- Kinesis Agent
- CloudWatch Logs
- 3rd party libraries

## Kinesis Consumers

- Kinesis SDK
- Kinesis Client Library (KCL)
- Kinesis Connector Library
- AWS Lambda
- 3rd party libraries

## AWS Kinesis KCL

- It is a Java library
- It uses DynamoDB to checkpoint offsets and to track other workers and share the work amongst them
- Great for reading in a distributed manner
- We can not have more KCL applications than shards (example: in case of 6 shards, we can have up to 6 application consumers)

## AWS Kinesis Data Firehose

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