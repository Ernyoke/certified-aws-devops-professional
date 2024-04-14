# S3 Event Notifications and Object Integrity

## S3 Event Notifications

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

## S3 Event Notifications with EventBridge

- All the S3 evens will end-up in EventBridge
- With EventBridge we can set up filters and rules for these events and connect them with all the destinations supported by EventBridge
- We can leverage all the features offered by EventBridge such as:
    - Advanced filtering with JSON rules
    - Have multiple destinations for events
    - Other capabilities such as archival, replay, reliable delivery
- EventBridge integration with an S3 bucket has to be enabled manually

## S3 Object Integrity

- S3 uses checksums to validate the integrity of uploaded objects
- It is using the MD5 checksum algorithm
- To make sure AWS accepts/reject an uploaded file, we can pass in the `Content-MD5` header with the calculated MD5 hash
- To validate an already upload object, we can use the ETag of the object:
    - ETag represents a version of the object, which is the MD5 hash if SS3-S3 encryption is enabled
    - We can get the ETag with `GetObjectMetadata` API call
- Other supported checksums: SHA-1, SHA-256, CRC32, CRC32C