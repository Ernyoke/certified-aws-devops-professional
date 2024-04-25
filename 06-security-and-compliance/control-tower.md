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

## Landing Zones

- They are automatically provisioned, secure, and compliant multi-account environments based on AWS best practices
- A Landing Zone consists of:
    - AWS Organization: create and manage multi-account structure
    - Account Factory: easily configure new accounts to adhere to config and policies
    - Organizational Units (OU): group and categorize accounts based on purpose
    - Service Control Policies (SCPs): enforce fine-grained permissions and restrictions
    - IAM Identity Center: centrally manage user access to accounts and services
    - Guardrails: rules and policies to enforce security, compliance and best practices
    - AWS Config: monitor and assess our resources' compliance with Guardrails

## Account Factory Customization (AFC)

- Used to automatically customize resources in new and existing accounts created through Account Factory
- For this we can use a Custom Blueprint:
    - It is a CloudFormation template that defines the resources and configurations we want to customize in the account
    - It is defined in the form of a Service Catalog product
    - Recommended to be stored in a Hub Account (don't use Management Account), which stores all the Custom Blueprints
    - We can also use predefined blueprints created by AWS partners
- Only one blueprint can be deployed to the account!
- When we create new accounts with Account Factory, a new event is created in EventBridge
- Migrate accounts to Control Tower:
    1. We move the account into an unregistered OU which is the safe zone
    2. We move the account into our organization
    3. We create an IAM role which allows to manage this account
    4. We deploy the Config Conformance Packs to this account to make sure it is compliant with our organization

## Customizations for AWS Control Tower (CfCT)

- It is a GitOps-style customization framework created by AWS
- Helps us to add customizations to our Landing Zone using custom CloudFormation templates and SCPs
- It will automatically deploy resources to new AWS accounts created using Account Factory
- Note: CfCT is different from AFC (Account Factory Customization)