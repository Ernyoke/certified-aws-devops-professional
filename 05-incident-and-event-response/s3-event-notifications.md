# S3 Event Notifications

- Events in AWS S3: `S3:ObjectCreated`, `S3:ObjectRemoved`, `S3:ObjectRestored`, etc.
- Use case for these events: automatically react to these events, for example generate thumbnail for newly uploaded image
- Event notifications can be dispatched to:
    - SNS
    - SQS
    - Lambda Functions
- S3 events notifications are typically delivered in seconds, sometimes they can take minutes
- IAM permissions for Event Notifications:
    - In case of SNS: we crate a resource policy for SNS where we allow `SNS:Publish` from `s3.amazon.com` service principal
    - In case if SQS: similar to SNS
    - For Lambda we define a resource access policy for the function as well