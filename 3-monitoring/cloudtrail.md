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