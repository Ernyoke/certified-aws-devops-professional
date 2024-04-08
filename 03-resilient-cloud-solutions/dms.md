# DMS - Database Migration Service

- Allows us to quickly and securely migrate databases to AWS
- It is resilient and self healing
- The source database remains available during migration
- Supports:
    - Homogeneous migrations: example Oracle to Oracle
    - Heterogeneous migrations: example Microsoft SQL Server to Aurora
- Supports Continuous Data Replication using CDC
- To use DMS we must create an EC2 instance to perform the replication tasks

## DMS Sources and Targets

- Sources:
    - On-premises and EC2 instance database: Oracle, MS SQL Server, MySQL, MariaDB, PostgreSQL, MongoDB, SAP, DB2
    - Azure: Azure SQL Database
    - Amazon RDS: all including Aurora
    - Amazon S3
    - DocumentDB
- Targets:
    - On-premises or databases running on EC2 instance: Oracle, MS SQL Server, MySQL, MariaDB, PostgreSQL, SAP
    - Any DB in Amazon RDS
    - Redshift, DynamoDB, S3
    - OpenSearch Service
    - Kinesis Data Streams
    - Apache Kafka
    - DocumentDB and Amazon Neptune
    - Redis and Babelfish

## AWS Schema Conversion Tool (SCT)

- Used to convert database schemas from one engine to another
- Example: 
    - OLTP: SQL Server or Oracle to MySQL, Postgres, Aurora
    - OLAP: Teradata or Oracle to Redshift
- SCT is not required to be used in case we are migrating to the same DB engine

## Mutli-AZ Deployments with DMS

- In case Multi-AZ is enabled, DMS provisions and maintains a synchronous standby replica in a different AZ
- Advantages:
    - Provides data redundancy
    - Eliminates I/O freezes
    - Minimizes latency spikes

## Replication Task Monitoring

- Task Status:
    - Indicates the condition of the task (Created, Running, Stopped)
    - Task Status Bar: gives an estimation of the Task's progress
- Table State:
    - Includes the current states of the tables
    - We get information about number of inserts, deletions and updates for each table

## CloudWatch Metrics from DMS

- Host Metrics:
    - Performance and utilization statistics for the replication host
    - Example: `CPUUtilization`, `FreeableMemory`, `FreeStorageSpace`, `WriteIOPS`
- Replication Task Metrics:
    - Statistics for Replication Task including incoming and committed changes, latency between the replication host and both source and target databases
    - Example: `FullLoadThroughputBandwidthTarget`, `FullLoadThroughputRowsTarget`, `CDCIncomingChanges`, `CDCLatencySource`, `CDCLatencyTarget`, `CDCChangesMemorySource`, `CDCChangesMemoryTarget`
- Table Metrics:
    - Statistics for tables that are in the process of being migrated