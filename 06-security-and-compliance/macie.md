# Amazon Macie

- A security service that uses machine learning to automatically discover, classify, and protect sensitive data in AWS
- Macie recognizes sensitive data such as personally identifiable information (PII) or intellectual property
- Amazon Macie allows you to achieve the following:
    - Identify and protect various data types, including PII, PHI, regulatory documents, API keys, and secret keys
    - Verify compliance with automated logs that allow for instant auditing
    - Identify changes to policies and access control lists
    - Receive notifications when data and account credentials leave protected zones
    - Detect when large quantities of business-critical documents are shared internally and externally

## Concepts

- Data source is the origin or location of a set of data:
    - AWS CloudTrail event logs and errors, including Amazon S3 object-level API activity
    - Amazon S3 objects: we can integrate Macie with our S3 buckets and/or specify S3 prefixes
- User, in the context of Macie, a user is the AWS Identity and Access Management (IAM) identity that makes the request
- There are certain file formats that Macie does not support, such as wav files
- Once Macie begins monitoring your data, it uses several automatic content classification methods to identify and prioritize your sensitive and critical data and to accurately assign business value to our data
- Each classification has a designated risk level between 1 and 10, with 10 being the highest risk and 1 being the lowest
- Classification methods:
    - Content Type Classification
    - File Extension Classification
    - Theme Classification
    - Regex Classification
    - PII Classification
    - Support Vector Machine–Based Classifier