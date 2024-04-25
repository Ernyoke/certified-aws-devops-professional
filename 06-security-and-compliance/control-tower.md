# AWS Control Tower

- It is an easy way to set up and govern a secure and compliant multi-account AWS environment based on best practices
- Benefits:
    - Automate the set up of our environments in a few clicks
    - Automate ongoing policy management using guardrails
    - Detect policy violations and remediate them
    - Monitor compliance through and interactive dashboard
- AWS Control Tower runs on top of AWS Organizations:
    - It will automatically set up an organization to organize accounts and implement SCPs (Service Control Policies)

## Account Factory

- Automates account provisioning and deployments
- Enables us to create pre-approved baselines and configuration options for AWS accounts in our organization (eg. VPC default configuration, subnets, regions, etc.)
- Uses AWS Service Catalog to provision new AWS accounts

## Detect and Remediate Policy Violations

- Guardrails:
    - Provide ongoing governance for our Control Tower environment
    - We can have 2 kinds of guardrails:
        - **Preventive (using SCPs)**: for example disallow creation of access keys for the root user
        - **Detective (using AWS Config)**: for example detect wether MFA for the root user is enabled
- Guardrail levels:
    - Mandatory:
        - Automatically enabled and enforced by AWS Control Tower
        - Example: disallow public read access to the Log Archive account
    - Strongly Recommended:
        - Based on AWS best practices (optional)
        - Example: enable encryption for EBS volumes attached to EC2 instances
    - Elective:
        - Commonly used by enterprises (optional)
        - Example: disallow delete actions without MFA on S3 buckets