# Secrets and License Manager

## AWS Secrets Manager

- Provides management, rotation and retrieval of secrets throughout their lifecycle
- Has tight integration with RDS Aurora, PostgreSQL, etc.
- Pricing: $0.4 per secret per month and $0.5 per 10K API calls with a 30 day free trial
- Provides storage for the following types of secrets:
    - Credentials for RDS databases
    - Credentials for Redshift clusters
    - Credentials for DocumentDB
    - Credentials for other databases
    - Other types of secrets (ex. API key)
- Keys can be encrypted with default or managed KMS Keys
- Secrets rotation:
    - Can be enabled or disabled
    - We can select a rotation interval (between 60 days and 1 year)
    - We have to assign a Lambda function which will rotate the secret

## License Manager

- Used for manage licenses in an account
- Can manage Microsoft, Oracle, SAP, etc. licenses
- We can define rules for the license software. These rules will be attached to resources and by this we will be able to monitor the usage these licenses
- We can alert users if licenses are overused