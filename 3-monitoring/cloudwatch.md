# CloudWatch

## CloudWatch Metrics

- CloudWatch provides metrics for every services in AWS
- A metric is a variable to monitor (CPUUtilization, NetworkIn, etc.)
- Metrics belong to namespaces
- A dimension is an attribute of a metric (instance id, environment, etc)
- We can have up to 10 dimensions per metric
- Metrics have a timestamp
- We can create a CloudWatch Dashboards with metrics

## CloudWatch EC2 Detailed Monitoring

- EC2 instance metrics are reporting every 5 minutes
- With detailed monitoring we may get samplings every 1 minute
- Detailed monitoring also can improve the scalability of our application: use detailed monitoring if we want to more prompt scale of our ASG
- Maximum retention period for metrics is 15 months. Data points with a period less than 60 second are available for 3 hours, data points with period of 60 seconds are available for 15, 5 minute data points for 63 days and 1 hour data points for 15 months

## CloudWatch Builtin Metrics

- EC2:
    - CPU Utilization
    - Disk Reads, Read Operations and Writes: only available for instances with instance store
    - Network In, Out, Packets In, Packets Out
    - Status Check and Failed Status Check
    - CPU Credit Usage and Balance: for T2, T3 burstable instances
    - We don't get built in metrics for RAM and running processes
- EBS:
    - Read Bandwidth, Write BandWith
    - Read Throughput, Write Throughput
    - Average Queue Length: how many operations are queued for the EBS volume
    - Time Spent Idle
    - Average Read/Write Size
    - Burs Balance
    - We don't get built in metrics for disk space used
- ASG:
    - Minimum, Maximum Group Size
    - Desired Capacity
    - In Service Instances
    - Pending/Standby Instances
    - Terminating Instances
    - Total Instances
    - Group and aggregate metrics can be enabled
- LB:
    - Target Response Time
    - Requests: number of requests handled
    - HTTP 5XXS, 4XXs
    - Target Connection Errors
    - Active Connection Count
    - New Connection Count
    - Consumed Load Balancer Capacity Units: used for billing
- RDS:
    - CPU Utilization, Credit Balance, Credit Usage
    - Database Connections
    - Bin Log Disk Usage
    - Free Memory
    - Swap Usage

## CloudWatch Custom Metrics

- Possibility to define and send our own custom metrics to CloudWatch
- Ability to use dimensions (attributes) to segment metrics
- Metric resolution:
    - Standard: 1 minute
    - High resolution: up to 1 second (StorageResolution API parameter)
- To send a metric to CloudWatch, we can use the `PutMetricData` API

    ```
    aws cloudwatch put-metric-data --metric-name <metric-name> --namespace <namespace> --value <value> --dimensions InstanceId=1-23456789,Instance-Type=m1.small --profile <profile> --region <region>
    ```

- We should use exponential back-off in case of throttle errors

## Export CloudWatch Metrics

- `GetMetricStatistics` API:

    ```
    aws cloudwatch get-metric-statistics --namespace <namespace>  --metric-name <metric name> --dimentsions=<dimensions> --statistics Maximum --start-time 2019-08-10T00:00:00 --end-time <end-time> --period <period> --profile <profile> --region <region>
    ```