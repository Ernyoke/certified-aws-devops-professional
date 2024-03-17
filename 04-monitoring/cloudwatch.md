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
    aws cloudwatch get-metric-statistics --namespace <namespace>  --metric-name <metric name> --dimensions=<dimensions> --statistics Maximum --start-time 2019-08-10T00:00:00 --end-time <end-time> --period <period> --profile <profile> --region <region>
    ```

## CloudWatch Alarms

- Alarms are used to trigger notifications for any metric
- Alarms can go to Auto Scaling, EC2 actions, SNS notifications
- Different ways of computing alarms: sampling, percentage max, min, etc.
- Alarm states:
    - OK
    - INSUFFICIENT_DATA
    - ALARM
- Period:
    - Length of time in seconds to evaluate the metric
    - High resolution custom metrics: we can only choose between 10 or 30 second
- Targets:
    - Stop, terminate, reboot or recover EC2 instances
    - Trigger auto scaling actions
    - Send notifications to SNS
- Alarms can be created based in CloudWatch Logs metrics filter
- CloudWatch does not test or validate the actions that are assigned
- In order to test an alarm, we can use the following command:

    ```
    aws cloudwatch set-alarm-state --alarm-name "name" --state-values ALARM --state-reason "testing"
    ```

## Billing Alarms

- We can create an alarm based on spending
- We can send notifications to SNS topics if the billing alarms is triggered
- We can create billing alarms for specific service

## CloudWatch Logs

- Applications can send logs to CloudWatch using the SDK
- CloudWatch can collect logs from:
    - Elastic Beanstalk: collection of logs from the application
    - ECS: collections of logs from the containers
    - AWS Lambda: collection from function logs
    - VPC FLow Logs
    - API Gateway
    - CloudTrail based on filter
    - CloudWatch log agent
    - Route53: log DNS queries
- CloudWatch logs can be saved to:
    - Batch exporter to S3 archival
    - Stream to ElasticSearch cluster for further analysis
- Logs storage architecture:
    - Log groups: arbitrary name, usually representing an application
    - Log stream: instances within application/log files/ containers
- We can set log expiration policies
- Using the AWS CLI we can tail CloudWatch logs
- To send logs to CloudWatch, we have to make sure IAM permissions are correct
- Logs can be encrypted using KMS

## CloudWatch Unified Agent

- Newer logging agent, can be used for sending metrics and logs as well to CloudWatch from EC2 instances and on-premises
- We can store configurations for the agent in SSM Parameter store to be reusable across multiple instances
- The unified agent can send `statsd` to send information to CloudWatch
- With the agent we can send memory and internal CPU metrics to CloudWatch

## CloudWatch Metric Filters

- We can create CloudWatch metrics from logs by creating metric filters
- A metric filter is a filter pattern which will be applied to log messages
- We can create alarms out of metric filters

## CloudWatch Logs Subscriptions

- Subscriptions are used to access real-time feed of log events from CloudWatch and have it delivered to other services such as Kineses Streams, Kinesis Firehose or AWS Lambda for custom processing
- Data can be loaded and analyzed in real time using Lambda and Kinesis Streams. Kinesis Firehose is a near real time service, data will be loaded in real time into it, but the service will not process it in real time
- Reference: [https://aws.amazon.com/blogs/big-data/power-data-ingestion-into-splunk-using-amazon-kinesis-data-firehose/](https://aws.amazon.com/blogs/big-data/power-data-ingestion-into-splunk-using-amazon-kinesis-data-firehose/)

## All kind of Logs

- Application Logs:
    - Logs that are produced by application code
    - Contains custom log messages, stack traces, etc.
    - Written to a local file on the filesystem
    - Usually stream to CloudWatch Logs using the CloudWatch agent on EC2
    - If using Lambda, direct integration with CloudWatch Logs is available
    - If using ECS or Fargate, direct integration with CloudWatch Logs is available and can be enabled
- Operating system logs:
    - Logs that are generated by the OS
    - Informing us about the system behavior
    - Usually streamed to CloudWatch Logs using a CloudWatch agent
- Access logs:
    - List of all the requests for individual files that people have requested from a site
    - Example: `/var/log/apache/access.log`

## AWS Managed Logs

- Load Balancer access logs: can be sent directly to S3
- CloudTrail Logs: can be sent either to S3 or CloudWatch Logs
- VPC Flow Logs: can be sent either to S3 or CloudWatch Logs
- Route53 Access Logs: can be only sent to CloudWatch Logs (can be exported with a subscription)
- S3 Access Logs: can be sent directly to S3
- CloudFront Access Logs: can be sent directly to S3

## CloudWatch Events

- Source + Rule => Target should change in some ways
- Schedule: Cron jobs
- Event Pattern: rule to react to a service doing something, example: CodePipeline state change
- CloudWatch Events can trigger Lambda Functions, can send notifications to SNS, SQS, Kinesis Messages
- A event creates a small JSON document to give information about the change

## CloudWatch Events Integration with CloudTrail API

- All events that are delivered via CloudTrail have `AWS API Call via CloudTrail` as the value for detail-type
- To create a rule that triggers on an action by an AWS service that does not emit events, we can base the rule on API calls made by that service

## CloudWatch Events cs S3 Event Notifications

- S3 Event Notifications: we can use the Amazon S3 Event Notifications feature to receive notifications when certain events happen in an S3 bucket
- S3 can publish notifications when the following events happen:
    - New object created
    - Object is removed
    - Object is restored from Glacier
    - Reduced Redundancy Storage object lost
    - Replication event
- S3 event notification destinations are:
    - SNS
    - SQS
    - Lambda
- We can use CloudWatch Events to catch some events which can not be handled by S3 Event Notifications
- We have to have a CloudTrail trail in order to have S3 object level events in CloudWatch
- CloudWatch Events provide bucket level events, while S3 Events provide object level events only

## CloudWatch Dashboard

- Great way to setup dashboards for quick access to key metrics
- Dashboards are global
- Dashboards can include graphs from different regions
- We can change the time zone and time range of the dashboards
- We can setup automatic refresh (10s, 1m, 2m, 5m, 15m)
- Pricing:
    - 3 dashboards (up to 50 metrics) for free
    - $3/dashboard/month