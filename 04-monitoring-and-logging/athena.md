# Amazon Athena

- It is serverless query service that can be used to analyze data stored in an S3 bucket
- Uses standard SQL language to query file (built on Presto)
- Supports CSV, JSON, ORC, Avro and Parquet
- Pricing: $5 per TB of data scanned
- Commonly used with Amazon Quicksight for reporting/dashboards
- Use cases: business intelligence, analytics, reporting, analyzing VPC Flow Logs, ELB Logs, CloudTrail trails, etc.

## Performance Improvements

- Use columnar data for cost-savings (scan less data). Examples: Parquet or ORC formats. We can use Glue to convert data to these formats
- Compress data for smaller retrievals (bzip, gzip, lz4, snappy, zlip, zstd)
- Partition datasets in S3 for easy querying on virtual columns
- User larger files (>128 MB) to minimize overhead

## Federated Queries

- Allows us to run SQL queries across data stored in relational, non-relational, object and custom data sources (AWS or on-premises)
- This can be accomplished with Data Source Connectors that run AWS Lambda to execute Federated Queries
- Results will be stored in S3 buckets