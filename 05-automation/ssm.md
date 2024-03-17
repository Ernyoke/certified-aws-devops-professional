# SSM - AWS Systems Manager

- Used for manage EC2 and on-premise systems at scale
- Offers operational insights about the state of the infrastructure
- Provides easy problem detection
- Offers patching automation for enhanced compliance
- Works for both Windows and Linux
- It has integration with CloudWatch metrics and dashboards
- It has integration with AWS Config

## SSM Features

- Resource Groups
- Insights:
    - Insights Dashboard
    - Inventory: discover and audit the software installed
    - Compliance
- Parameter Store
- Action:
    - Automation: shut down EC2, create AMIs
    - Run Command
    - Session Manager
    - Patch Manager
    - Maintenance Windows
    - State Manager: define and maintain configuration of OS an applications

## How Systems Manager Works?

- We need to install the SSM agent on the systems we want to control
- The agent is by default installed on Amazon Linux and some Ubuntu AMIs
- If an instance can not be controlled by the agent, the issue is probably with the agent on the IAM role is missing to allow SSM actions
- SSM Hybrid Activation: [https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-managedinstances.html])(https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-managedinstances.html)
    - Used form setup on-premise instances in order to be managed by SSM
    - When a hybrid activation is created, SSM provides an **Activation Code** and an **Activation ID** which will be used to set up the SSM agent on the on-premise instance
    - The instance id for EC2 instances starts with `i-` and for on-premise instances it starts with `mi-`
    - Managed on-premise instances can be tagged in SSM

## Resource Groups

- They are used to group resources together
- There are 2 types of resource groups:
    - Tag based
    - CloudFormation stack based

## SSM Run Command

- Allows to run commands on instances managed by SSM
- Commands which we would want to execute on VMs are specified in SSM Documents
- Documents are either managed by AWS or created and managed by users
- Documents can be 4 different types:
    - Command
    - Automation
    - Policy
    - Session

## SSM Parameter Store

- Used for securely storing configuration and secrets in AWS
- It can have optional encryption using KMS
- Serverless, scalable, durable, easy SDK
- Version tracking of configurations and secrets
- Configuration management using IAM policies
- Notification with CloudWatch events
- Integration with CloudFormation

### SSM Parameter Store Hierarchy

- Example of hierarchy:

    - /my-department/
        - my-app/
            - dev/
                - db-url
                - db-password
            - prod/
                - db-url
                - db-password
        - other-app
    - /other-department

### SSM Tiers

- Standard:
    - Total number of parameters: 10K
    - Max size of a parameter: 4KB
    - Pricing: free
    - Parameter policies: NO

- Advanced:
    - Total number of parameters: 100K
    - Max size of a parameter: 8KB
    - Pricing: 0.05$ per advanced parameter per month
    - Parameter policies: YES

### Policies for Advanced Parameters

- Allow assigning a TTL for a parameter to force updating or deleting sensitive data
- We can assign multiple policies at a time

### SSM Parameter Store CLI

- Get parameters
    ```
    aws ssm get-parameters --names /my-app/dev/db-url /my-app/dev/db-password
    ```

- Get parameters decrypted
    ```
    aws ssm get-parameters --names /my-app/dev/db-url /my-app/dev/db-password --with-decryption
    ```

- Get parameters by path: gets multiple parameters recursively
    ```
    aws ssm get-parameters-by-path --path /my-app/ --recursive
    ```

## SSM Patch Manager

- Used to automatically patch running instances
- Predefined patch baselines: defined by AWS, tells Patch Manager how to patch by default a given OS
- Custom baseline: 
    - We can create our own list of patches which can be applied to a fleet
    - We can set up rules for patching, we can create a list of approved and rejected patches
    - We can set up manual approval for patches
    - Patch sources: we can add a repository for being able to distribute patches

## Maintenance Windows

- It is a scheduled job (Cron job) in order to specify when the patch baselines can be applied to instances
- We can set the duration of windows (up tp 24 hours)
- We can set a period before patching in order to stop initiating new tasks
- Register targets: we have to register a target for which the patching has to be done. Targets can be based on tags, resource groups or single resource
- Register a run command: we have to set a command (example RunPatchBaseline) which will execute the patches

## SSM Inventory

- Used to collect data about running instances
- The data it collects can be the following:
    - Applications
    - AWS Components
    - Network configurations
    - Windows Updates
    - Instance Detail Information
    - Services
    - Etc.

## AWS Systems Manager Automation

- Systems Manager Automation simplifies common maintenance and deployment tasks of Amazon EC2 instances and other AWS resources. Automation enables to do the following:
    - Build automations to configure and manage instances and AWS resources
    - Create custom runbooks or use pre-defined runbooks maintained by AWS
    - Receive notifications about Automation tasks and runbooks by using Amazon EventBridge
    - Monitor Automation progress and details by using the AWS Systems Manager console
- Automation use cases:
    - Perform common IT tasks:
        - Use the `AWS-StopEC2InstanceWithApproval` runbook to request that one or more AWS Identity and Access Management (IAM) users approve the instance stop action. After the approval is received, Automation stops the instance
        - Use the `AWS-StopEC2Instance` runbook to automatically stop instances on a schedule by using Amazon EventBridge or by using a maintenance window task
        - Use the `AWS-UpdateCloudFormationStackWithApproval` runbook to update resources that were deployed by using CloudFormation template. The update applies a new template
    - Safely perform disruptive tasks in bulk:
        - Use the `AWS-RestartEC2InstanceWithApproval` runbook to target an AWS resource group that includes multiple instances
    - Simplify complex tasks:
        - Use the `AWS-UpdateLinuxAmi` and `AWS-UpdateWindowsAmi` runbooks to create golden AMIs from a source AMI. We can run custom scripts before and after updates are applied. We can also include or exclude specific packages from being installed
        - Use the `AWSSupport-ExecuteEC2Rescue` runbook to recover impaired instances. An instance can become unreachable for a variety of reasons, including network misconfigurations, RDP issues, or firewall settings
    - Enhance operations security
- WhitePaper: Building a Secure, Approved AMI Factory Process Using Amazon EC2 Systems Manager (SSM), AWS Marketplace, and AWS Service Catalog [https://d1.awsstatic.com/whitepapers/aws-building-ami-factory-process-using-ec2-ssm-marketplace-and-service-catalog.pdf](https://d1.awsstatic.com/whitepapers/aws-building-ami-factory-process-using-ec2-ssm-marketplace-and-service-catalog.pdf)

## AWS Session Manager

- Allows to start a secure shell on a VM
- **Does not use SSH access and bastion hosts**
- Only works for EC2 for now
- Log actions done through secure shells to S3 and CloudWatch Logs
- IAM permissions: access SSM + write to S3 + write to CloudWatch
- CloudTrail can intercept StartSessions events
- AWS Secure Shell compared to SSH:
    - No need to open port 22 at all anymore
    - No need fot a bastion host
    - All commands are logged to S3/CloudWatch
    - Access to Secure Shell is done through User IAM, not SSH keys


