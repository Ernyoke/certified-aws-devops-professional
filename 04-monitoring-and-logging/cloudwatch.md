# CloudWatch

## CloudWatch Metrics

- CloudWatch provides metrics for every services in AWS
- A metric is a variable to monitor (CPUUtilization, NetworkIn, etc.)
- Metrics belong to **namespaces**
- A **dimension** is an attribute of a metric (instance id, environment, etc)
- We can have up to 30 dimensions per metric
- Metrics have a **timestamp**
- We can create a CloudWatch Dashboards with metrics

### CloudWatch Metric Streams

- Used to continually stream CloudWatch metrics to a destination of our choice with near-real-time delivery and low latency
- Destination can be the following:
    - Amazon Kinesis Data Firehose and then its destinations
    - Directly into 3rd party service providers such as Datadog, Dynatrace, New Relic, Splunk, Sumo Logic, etc.

### CloudWatch EC2 Detailed Monitoring

- EC2 instance metrics are reporting every 5 minutes
- With detailed monitoring we may get samplings every 1 minute
- Detailed monitoring also can improve the scalability of our application: use detailed monitoring if we want to more prompt scale of our ASG
- Maximum retention period for metrics is 15 months. Data points with a period less than 60 second are available for 3 hours, data points with period of 60 seconds are available for 15 days, 5 minute data points for 63 days and 1 hour data points for 15 months

### CloudWatch Builtin Metrics

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

### CloudWatch Custom Metrics

- Possibility to define and send our own custom metrics to CloudWatch
- Ability to use dimensions (attributes) to segment metrics
- Metric resolution (`StorageResolution` API parameter):
    - Standard: 1 minute
    - High resolution: 1/5/10/30 seconds
- To send a metric to CloudWatch, we can use the `PutMetricData` API

    ```
    aws cloudwatch put-metric-data --metric-name <metric-name> --namespace <namespace> --value <value> --dimensions InstanceId=1-23456789,Instance-Type=m1.small --profile <profile> --region <region>
    ```

- CloudWatch accepts metric data points up to two weeks in the past and two hours in the future
- We should use exponential back-off in case of throttle errors

### Export CloudWatch Metrics

- `GetMetricStatistics` API:

    ```
    aws cloudwatch get-metric-statistics --namespace <namespace>  --metric-name <metric name> --dimensions=<dimensions> --statistics Maximum --start-time 2019-08-10T00:00:00 --end-time <end-time> --period <period> --profile <profile> --region <region>
    ```

### CloudWatch Anomaly Detection

- Used to continuously analyze metrics to determine normal baselines and surface anomalies using machine learning algorithms
- It creates a model based on the metric's past data
- Shows us which values in the graph are out of the normal range
- Allows us to create alarms based on expect values (instead of using a static threshold)
- Provides the ability to exclude time periods or events from being trained

## CloudWatch Logs

- Applications can send logs to CloudWatch using the SDK
- CloudWatch Log sources:
    - SDK, CloudWatch Logs Agent, CloudWatch Unified Agent
    - Elastic Beanstalk: collection of logs from the application
    - ECS: collections of logs from the containers
    - AWS Lambda: collection from function logs
    - VPC FLow Logs
    - API Gateway
    - CloudTrail based on filter
    - Route53: log DNS queries
- CloudWatch logs can be exported to:
    - Amazon S3 (batch exporting for archival)
    - Kinesis Data Streams
    - Kinesis Data Firehose
    - AWS Lambda
    - Amazon OpenSearch
- Logs storage architecture:
    - **Log groups**: arbitrary name, usually representing an application
    - **Log stream**: instances within application/log files/ containers
- We can set log expiration policies
- Using the AWS CLI we can tail CloudWatch logs
- To send logs to CloudWatch, we have to make sure IAM permissions are correct
- Logs can be encrypted using KMS

### CloudWatch Logs Insights

- Can be used to search and analyze log data stored in CloudWatch
- Example: find specific IP inside logs, count occurrences of `ERROR` messages
- Provides a purpose-build query language:
    - Automatically discovers fields from AWS services and JSON log events
    - We can fetch desired event fields, filter based on conditions, calculate aggregate statistics, sort events, limit number of events
- We can query multiple Log Groups at a time even if they are in different AWS accounts
- CloudWatch Logs Insights is a query engine, can do queries on historical data

### Exporting Logs

- S3 Export:
    - It is a batch export
    - Exports are started as task which can take up to 12 hours to finish
    - The API call for starting a task is `CreateExportTask`
- CloudWatch Logs Subscriptions:
    - Subscriptions are used to access real-time feed of log events from CloudWatch and have it delivered to other services such as Kineses Streams, Kinesis Firehose or AWS Lambda for custom processing
    - **Subscription Filter**: filter which logs events to deliver to the destination
    - With subscription filters we can aggregate logs from different accounts and regions into a single account. To accomplish this we must use a **Cross-Account Subscription**

### Live Tail

- CloudWatch Logs feature which can be used for tailing log streams in real time
- Supports filters
- Can be used for debugging
- Live tail sessions recommended to be closed after usage, we can inquire additional costs (1 hour is free every day)

### CloudWatch Metric Filters

- We can create CloudWatch metrics from logs by creating metric filters
- A metric filter is a filter pattern which will be applied to log messages
- We can create alarms out of metric filters
- Metric Filters do not retroactively filter data. Filters only publish metric data points for events that happen after the filter was created

## CloudWatch Unified Agent

- CloudWatch agent variants: CloudWatch Logs Agent; CloudWatch Unified Agent (newer, recommended)
- Newer logging agent, can be used for sending metrics and logs as well to CloudWatch from EC2 instances and on-premises
- We can store configurations for the agent in SSM Parameter store to be reusable across multiple instances
- The unified agent can send `statsd` to send information to CloudWatch
- With the agent we can send memory and internal CPU metrics to CloudWatch
- Metrics that can be collected from a server/EC2 instance:
    - CPU metrics (active, guest, idle, system, user, steal)
    - Disk metrics (free, used, total); Disk IO
    - RAM (free, inactive, used, total, cached)
    - Netstat (number of TCP and UDP connections, net packets/bytes)
    - Processes (total, dead, blocked, idle, running, sleep)
    - Swap Space (free, used, used percentage)

## All kind of Logs

- Application Logs:
    - Logs that are produced by application code
    - Contains custom log messages, stack traces, etc.
    - Written to a local file on the filesystem
    - Usually stream to CloudWatch Logs using the CloudWatch agent on EC2
    - If using Lambda, direct integration with CloudWatch Logs is available
    - If using ECS or Fargate, direct integration with CloudWatch Logs is available and can be enabled
    - If using Elastic Beanstalk, direct integration with CloudWatch can be enabled by setting the necessary IAM roles
- Operating system logs (Event Logs, System Logs):
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

## CloudWatch Alarms

- Alarms are used to trigger notifications for any metric
- Alarms can go to Auto Scaling, EC2 actions, SNS notifications
- Different ways of computing alarms: sampling, percentage max, min, etc.
- Alarm states:
    - `OK`
    - `INSUFFICIENT_DATA`
    - `ALARM`
- Period:
    - Length of time in seconds to evaluate the metric
    - High resolution custom metrics: we can choose between 10, 30 second or multiples of 60 secods
- Targets:
    - Actions on EC2 instances: Stop, terminate, reboot or recover EC2 instances
    - Trigger auto scaling actions
    - Send notifications to SNS
- Alarms can be created based in CloudWatch Logs metrics filter
- CloudWatch does not test or validate the actions that are assigned
- In order to test an alarm, we can use the following command:

    ```
    aws cloudwatch set-alarm-state --alarm-name "name" --state-values ALARM --state-reason "testing"
    ```

### Composite Alarms

- CloudWatch Alarms are based on a single metric
- With Composite Alarms we can create alarms by monitoring the state of other multiple alarms
- `ADN` or `OR` conditions are supported
- Helpful to reduce "alarm noise" by creating complex composite alarms

### EC2 Instance Recovery

- Status Check:
    - Instance status: check the EC2 VM
    - System status: check the underlying hardware
- Recovery: in case the alarm is breached we can start an instance recovery
- When a recovery happens we get the same private, public, EIP, metadata, placement group

### Billing Alarms

- We can create an alarm based on spending
- We can send notifications to SNS topics if the billing alarms is triggered
- We can create billing alarms for specific service

## CloudWatch Dashboard

- Great way to setup dashboards for quick access to key metrics
- Dashboards are global
- Dashboards can include graphs from different regions
- We can change the time zone and time range of the dashboards
- We can setup automatic refresh (10s, 1m, 2m, 5m, 15m)
- Pricing:
    - 3 dashboards (up to 50 metrics) for free
    - $3/dashboard/month

## CloudWatch Synthetics Canary

- Synthetics Canary are configurable scripts that will monitor APIs and URLs
- These scripts meant to reproduce what a customer would do in order to find issues before the app is deployed to production
- They can be also used to check the availability and latency of our endpoints
- They can store load time data and screenshots of the UI
- They have integration with CloudWatch Alarms
- The scripts can be written in Node.js or Python
- Provides programmatic access to a headless Chrome browser
- They can be run once or on a regular basis
- Canary Blueprints:
    - Heartbeat Monitor: load URL, store screenshot and an HTTP archive file
    - API Canary: test basic read and write functions of a REST API
    - Broken Link Checker: check all links inside a page
    - Visual Monitoring: compare a screenshot taken during a canary run with a baseline screenshot
    - Canary Recorder: used with CloudWatch Synthetics Recorder - used to record actions on a website and automatically generate a test script for that
    - GUI Workflow Builder: verifies that actions can be taken on a webpage (example: test a webpage with a login form)