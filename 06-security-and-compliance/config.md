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

## AWS Config Resource

- Ability to view the compliance of a resource over time
- Ability to view configuration of a resource over time
- View CloudTrails API calls if enabled

## Remediations

- Rules have a remediation action option, where we can run a SSM Automation document to address a non-compliant warning
- We can use AWS-managed automation document or create a custom automation document
- We can set Remediations Retries if the resource is still non-compliant after auto remediation

## Notifications

- We can use EventBridge to trigger notifications when AWS resources are non-compliant
- We can configure Config to stream configuration changes and notifications to an SNS topic
- AWS Config sends notifications for the following events:
    - Configuration item change for resource
    - Configuration history delivered for an account
    - Configuration snapshot was started and delivered for an account
    - Compliance state of resources
    - Evaluation started for a rule
    - Config failed to deliver the notification for an account

## Configuration Recorder

- Stores the configuration of our AWS resources as Configuration Items
- **Configuration Item**: a point-in-time view of the various attributes of an AWS resource. Created whenever AWS Config detects a change to the resource (eg. attributes, relationships, config events)
- We can create custom configuration recorders to save record only the resources we specify
- To be able to use Config, we must create a recorder so AWS Config can track our resources (it is automatically created when we enable AWS Config from the Console or CLI)

## Aggregators

- A aggregator is an AWS Config resource type that collects AWS Config configuration and compliance data from the following:
    - Multiple accounts and multiple regions
    - Single account and multiple regions
    - An organization in AWS Organizations and all the accounts in that organization
- The aggregator is created in one central account
- If we use AWS Organizations, we don't need for individual authorization of the accounts
- AWS Config Rules must be deployed in each individual account

## Conformance Pack

- A conformance pack is a collection of AWS Config Rules and Remediation Actions
- Packs are created using YAML files
- They are deployed to an AWS account and region or across an AWS Organization
- We can use pre-built packs or create our won Custom Conformance Packs
- Custom Conformance Packs can have custom rules backed by Lambda Functions
- We can designate a Delegated Administrator to deploy Conformance Packs to our AWS Organization (can be a member account)

## Organizational Rules

- They are AWS Config Rules that we can manage across all accounts within an AWS Organization

|                  | Organizational Rules                                                                   | Conformance Packs                                                                          |
|------------------|----------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------|
| Scope            | AWS Organization                                                                       | AWS Accounts and Organization                                                              |
| Evaluation Type  | Evaluate resources against predefined rules that are defined and enforced at Org level | Evaluate resources against predefined rules that are defined and enforced at Account level |
| Rules Count      | One rule                                                                               | Many Rules at a time                                                                       |
| Compliance Level | Managed at Organization level                                                          | Managed at account level                                                                   |

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