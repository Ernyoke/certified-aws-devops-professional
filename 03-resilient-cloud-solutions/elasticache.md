# Amazon ElastiCache

- ElastiCache is a service offering managed Redis or Memcached instances
- Caches are in-memory databases with really high performance and low latency
- Caches help reduce load from databases for read intensive workloads
- AWS takes cares of OS maintenance/patching, optimizations, setup, configuration, monitoring, failure recovery and backups for cache instances

## ElastiCache Solution Architectures

- Database Cache:
    - Applications query ElastiCache. If the data is not available, it will be retrieved from RDS and stored in ElastiCache as well
    - Used to relieve load from RDS
    - The cache must have an invalidation strategy to make sure only the most current data is stored
- User Session Store for Stateful Applications:
    - User logs into an application
    - The application writes the session data into ElastiCache
    - In case the user hits another instance running the application, the application can retrieve the session from the cache

## Comparison between Redis and Memcached

| Redis                                                   | Memcached                                           |
| ------------------------------------------------------- | --------------------------------------------------- |
| Multi AZ with automatic failover                        | Uses Multi-node for partitioning of data (sharding) |
| Read Replicas to scale reads and have high availability | No high availability                                |
| Data durability using AOF persistance                   | No persistance of data                              |
| Backup and restore features                             | No backup and restore                               |
| Supports *Sets* a *Sorted Sets*                         | Has a multi-threaded architecture                   |

## ElastiCache Replication

- **Cluster Mode Disabled**:
    - One primary node, up to 5 replicas
    - Replication is asynchronous
    - Primary node is used for reads and writes
    - Replicas can be used for reads only
    - We have on shard, all the nodes have all the data
    - We have guarding against data loss  if a node fails
    - Multi-AZ is enabled by default for failover
    - Helpful to scale read performance
    - We have 2 kinds of scaling:
        - Horizontal: Scale out/in by adding or removing read replicas (max 5 read replicas)
        - Vertical:
            - Scale up/down by having larger or a smaller node type
            - ElastiCache will internally create a new node groups, then data replication will happen, finally the DNS will be updated
- **Cluster Mode Enabled**:
    - Data is partitioned across multiple shards
    - The data is divided between shards
    - Helpful to scale writes
    - Each shard has a primary and up to 5 replica nodes (same concept as *Cluster Mode Disabled*)
    - Multi-AZ capability os ON by default
    - We get up to 500 nodes per cluster:
        - 500 shards with a single master
        - 250 shards with 1 master and 1 replica
        - ...
        - 83 shards with one master and 5 replicas

## Redis Auto Scaling

- We can have auto scaling when we have Cluster Mode Enabled
- We can automatically increase/decrease the desired shards or replicas
- Auto scaling supports both **Target Tracking** and **Scheduled Scaling Policies**
- To connect to the cluster it is recommended to use the Cluster's **Configuration Endpoint**

## Redis Connection Endpoints

- Standalone Node:
    - One endpoint for read and write operations
- Cluster Mode Disabled:
    - 1 Primary Endpoint for all the operations
    - Multiple Reader Endpoints: evenly split read operations across all the read replicas
    - Node Endpoint: read operations for individual nodes
- Cluster Mode Enabled:
    - Configuration Endpoint: for all read/write operations that support Cluster Mode Enabled command
    - Node Endpoints: for read operations only