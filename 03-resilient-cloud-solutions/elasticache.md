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