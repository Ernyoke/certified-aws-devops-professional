# AWS Organizations

- It is a global service allowing to manage multiple AWS accounts from a organization
- The main account is the master account, this can not be changed
- Managed accounts are called member accounts
- Member accounts can be part of a single organization, they can be migrated to other organizations
- AWS Organizations provides consolidated billing across all accounts - single payment method
- Pricing benefits from aggregated usage
- API is available to automate AWS account creation

## `OrganizationAccountAccessRole`

- If we create a member account using the API from the Organizations service, the `OrganizationAccountAccessRole` will be automatically created in the member account
- Grants full administrator permissions in the member account to the management account
- Can be used to perform admin tasks from the management account
- Can be assumed by IAM users in the management account
- It is automatically added all new member accounts created with AWS Organizations
- Must be created manually if we invite an existing account

## Multi Account Strategies

- Create accounts per department, per cost center, per dev/test/prod, based on regulatory restrictions (using SCP), for better isolation, to have separate per-account service limits, isolated accounts fro logging, etc.
- Multi accounts vs one account using multi VPC
- We can have tagging standards for billing purposes
- We can have CloudTrail on all accounts, aggregate logs to a central S3 account
- We can establish cross account roles for admin purposes

## Organization Units (OU)

- It is a way to group multiple service accounts

## Feature Modes

- Consolidated billing features:
    - Consolidated Billing across all accounts - single payment method
    - Pricing benefits from aggregated usage (volume discount fec EC2, S3, etc.)
- All Features (Default):
    - Includes consolidated billing features and SCP
    - Invited accounts must approve enabling all features
    - Provides the ability to apply an SCP to prevent member accounts from leaving the org
    - Once enabled, we cannot go back to Consolidated Billing Features only

## Reserved Instances

- For billing purposes, the consolidated billing feature of AWS Organizations treats all the accounts in the organization as one account
- This means that all accounts in the organization can receive the hourly cost benefits of Reserved Instances that are purchased by any other account
- The payer account (Management account) of an org can turn off Reserved Instance (RI) discount and Savings Plan discount sharing for any accounts in the org, including the payer account
- To share an RI or Savings Plan discount with an account, both accounts must have sharing turned on

## Service Control Policies (SCP)

- Can be used to whitelist or blacklist IAM actions which can be applied to OU or account level
- These restrictions do not apply to the master account
- SCPs are applied to all users and roles of the member accounts, including their root account
- SCPs do not affect service-linked roles. Service-linked roles enable AWS services to integrate with AWS Organizations and can't be restricted by SCPs
- SCP must have an explicit Allow (does not allow anything by default)
- Use cases:
    - Restrict access to certain services
    - Enforce PCI compliance by disable certain services

## Moving Accounts

- To migrate accounts from one organization to another:
    1. Remove the member account from the old organization
    2. Send an invite to the new organization
    3. Accept the invite of the new organization from the member account
- Migrate the master account:
    1. Remove the member accounts from the organizations using the procedure above
    2. Delete the old organization
    3. Repeat the process above to invite the old master account to the new organization

## Multi Account with AWS

- Any cross account action requires to define IAM trust
- IAM roles can be assumed cross account (STS - `AssumeRole`)
- CodePipeline: cross account invocation of CodeDeploy
- AWS Config Aggregators
- CloudWatch Events - Event Bus: we can send events across accounts
- CloudFormation - StackSets
- CloudWatch Logs: cross account log sharing with subscriptions

## AWS Services that We Can Use with AWS Organizations

- With AWS Organizations we can perform account management activities at scale by consolidating multiple AWS accounts into a single organization
- We can leverage the multi-account management services available in AWS Organizations with select AWS services to perform tasks on all accounts that are members of your organization
- **Trusted access**: we can enable a compatible AWS service to perform operations across all of the AWS accounts in our organization
- **Delegated administrator for AWS services**: a compatible AWS service can register an AWS member account in the organization as an administrator for the organization's accounts in that service
- Services:
    - AWS CloudFormation Stacksets: a user in the management account or a delegated administrator account can create a stack set with service-managed permissions that deploys stack instances to accounts in our organization
    - AWS CloudTrail: a user in a management account or delegated administrator account can create an organization trail or event data store that logs all events for all accounts in the organization
    - AWS Config: we can get an organization-wide view of our compliance status. We can also use AWS Config API operations to manage AWS Config rules and conformance packs across all AWS accounts in your organization
    - AWS Control Tower: we can set up a landing zone, a multi-account environment for all of your AWS resources
    - Amazon EventBridge: we can enable sharing of all Amazon EventBridge events, formerly Amazon CloudWatch Events, across all accounts in your organization
    - AWS Firewall Manager: we can centrally configure and manage AWS WAF rules across the accounts in your organization
    - Amazon GuardDuty: we can designate a member account to view and manage GuardDuty for all of the accounts in your organization
    - AWS IAM Identity Center: users can sign in to the AWS access portal with their corporate credentials and access resources in their assigned management account or member accounts
    - AWS Trusted Advisor: run Trusted Advisor checks for all of the AWS accounts in your organization
    - etc.