# AWS Config

- Helps with auditing and recording compliance of AWS resources
- Helps record configurations and changes over time
- Provides the ability of storing the configuration data into S3 where it can be analyzed by Athena
- Problems AWS Config Solves:
    - Check if there is unrestricted SSH access to a security group
    - Check if buckets have public access
    - Find out how did an ALB configuration change over time
- We can receive alerts (SNS notifications) for any change
- AWS Config is a per region service, but it can be aggregated across regions and accounts

## AWS Config Resource

- Ability to view the compliance of a resource over time
- Ability to view configuration of a resource over time
- View CloudTrails API calls if enabled

## AWS Config Rules

- AWS provides a set of managed config rules (over 75) which can be used by the users
- Users can also make custom config rules (a rule must be defined using AWS Lambda)
- Example of custom rules user can make:
    - Evaluate if each EBS disk is of type GP2
    - Evaluate if each EC2 instance is of type t2.micro
- Rules can be evaluated/triggered:
    - For each config change (Configuration change)
    - At regular time intervals (Periodic)
- Evaluation of rules can trigger CloudWatch events if the rule is non-compliant
- Rules can have auto remediations: if a resource is not compliant, the is an option to trigger auto remediation, example: stop instances with non-approved tags
- **AWS Config Rules do not prevent actions from happening (no deny)**
- Custom rules:
    - We can create custom rules which will be managed by us
    - In order to create a custom rule, we have to create a Lambda function which will check if a resource is compliant or not
    - Trigger types for custom rules are same as for managed rules:
        - Configuration change
        - Periodic
    - Scope of the trigger: we can define for which resource does the rule apply, example database instance, EC2 instance, etc. We can use tags as well instead of resource types

## AWS Config Automation

- We can configure config to stream configuration changes and notifications to an SNS topic
- AWS Config sends notifications for the following events:
    - Configuration item change for resource
    - Configuration history delivered for an account
    - Configuration snapshot was started and delivered for an account
    - Compliance state of resources
    - Evaluation started for a rule
    - Config failed to deliver the notification for an account
- Config rules have integration with CloudWatch Events
- Remediation capability in Config Rules: rules have a remediation action option, where we can run a SSM document to address a non-compliant warning

## AWS Config Multi-Account Multi-Region Data Aggregation

- A aggregator is an AWS Config resource type that collects AWS Config configuration and compliance data from the following:
    - Multiple accounts and multiple regions
    - Single account and multiple regions
    - An organization in AWS Organizations and all the accounts in that organization

## CloudWatch vs CloudTrail vs Config

- CloudWatch
    - Performance monitoring (metrics, CPU, network, etc.) and dashboards
    - Events and alerting
    - Log aggregation and analysis
- CloudTrail
    - Record API calls made within an account
    - Define trails for specific resources
    - It is a global service
- Config
    - Record configuration changes
    - Evaluate resources against compliance rules
    - Get timeline of changes and compliance