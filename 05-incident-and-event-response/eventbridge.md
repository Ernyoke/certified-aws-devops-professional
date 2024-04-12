# CloudWatch Events

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