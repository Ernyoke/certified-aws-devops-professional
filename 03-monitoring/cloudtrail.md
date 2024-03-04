# CloudTrail

- Provides governance, compliance and audit for an AWS account
- CloudTrail is enabled by default!
- Provides a history of events/API calls made within an AWS account
- It can put logs into CloudWatch Logs
- If a resource is deleted, we have to look into CloudTrail first
- CloudTrails shows the past 90 days of activity
- The default UI only shows create, modify and delete events
- CloudTrail Trail features:
    - Provides detailed list of all events
    - Ability to store these events in S3 for further analysis
    - It can be region specific or global
- CloudTrail logs are encrypted with SSE-S3 encryption by default when they are stored into S3. There is a possibility to use SSE-KMS encryption
- A CloudTrail log entry contains information about:
    - Who made the request
    - When was the request made and from where
    - What was requested
    - What was the response
- CloudTrail may have a 15 minutes delay to deliver log files into the S3 bucket


## Log Integrity

- We can validate the integrity of the logs file using the AWS CLI
- The AWS CLI allows us to detect the following type of changes:
    - Modification or deletion of CloudTrail log files
    - Modification or deletion of CloudTrail digest files
    - Modification or deletion of both of the above
- Validate logs command:
    ```
    aws cloudtrail validate-logs --start-time <time> --trail-arn arn:aws:cloudtrail:us-east-2:123456:trail/my-trail-name --verbose --profile aws-devops
    ```

## Cross Account Logging

- Reference: [https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)
- Steps to enable cross account logging:
    1. Turn on CloudTrail in the account where the destination bucket will belong
    2. Update the bucket policy to grant cross-account permission to CloudTrail
    3. Turn on CloudTrail on the other accounts. Configure CloudTrail to use the same bucket from the destination account