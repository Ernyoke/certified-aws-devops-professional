# Amazon ElasticSearch (Amazon ES)

- Managed version of ElasticSearch (open source project)
- Needs to run on servers (not a serverless offering)
- Use cases:
    - Log analytics
    - Real-time application monitoring
    - Security analytics
    - Full text search
    - Clickstream analytics
    - Indexing

## ELK Stack - ElasticSearch, Logstash, Kibana

- ElasticSearch: provides search and indexing capability
- Kibana: 
    - Provides real-time dashboards on top of the data that sits in ES
    - Alternative to CloudWatch dashboards (more advanced capabilities)
- Logstash:
    - Log ingestion mechanism, uses the Logstash Agent
    - Alternative to CloudWatch Logs
    - We can decide on log retention and granularity

