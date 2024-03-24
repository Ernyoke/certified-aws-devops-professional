# SSM - AWS Systems Manager

- Used for managing a fleet of EC2 instances and on-premise systems at scale
- Offers operational insights about the state of the infrastructure
- Provides easy problem detection
- Offers patching automation for enhanced compliance
- Works for both Windows and Linux
- It has integration with CloudWatch metrics and dashboards
- It has integration with AWS Config

## SSM Features

- **Resource Groups**
- Operations Management:
    - Explorer
    - OpsCenter
    - CloudWatch Dashboard
- Shared Resources:
    - **Documents**
- Change Management:
    - Change Manager
    - **Automation**
    - Change Calendar
    - **Maintenance Windows**
- Application Management:
    - Application Manager
    - AppConfig
    - **Parameter Store**
- Node Management:
    - Fleet Manager
    - Compliance
    - **Inventory**
    - Hybrid Activations
    - **Session Manager**
    - **Run Command**
    - **State Manager**
    - **Patch Manager**
    - Distributor

## How Systems Manager Works?

- We need to install the SSM agent on the systems we want to control
- The agent is by default installed on Amazon Linux and some Ubuntu AMIs
- If an instance can not be controlled by the agent, the issue is probably with the agent on the IAM role is missing to allow SSM actions

## AWS Tags and Resource Groups

- We can add text key/value pairs to many AWS resources
- Tags are commonly used with EC2 instances
- Tags can be used for resource grouping, automation, cost allocation
- AWS Resource Groups are used to create, view and manage logical groups of resources
- They allow creation of logical groups of resources such as : 
    - Applications
    - Different layers of an application stack
    - Differentiate a prod and development environment
- There are 2 types of resource groups:
    - Tag based
    - CloudFormation stack based

## AWS SSM Documents

- SSM Documents are written in JSON or YAML
- In SSM Documents we define parameters and actions. Documents are then executed by a specific service
- Documents are either managed by AWS or created and managed by users
- Documents can be 4 different types:
    - Command
    - Automation
    - Policy
    - Session
- Example of AWS SSM Document:

```yaml
schemaVersion: "2.2"
description: "Hello World"
parameters: 
    parameters: 
        type: "String"
        description: "Some text to print"
        default: "none"
mainSteps: 
    - 
        action: "aws:runShellScript"
        name: "runShellScript"
        inputs: 
            workingDirectory: "{{.}}"
            runCommand:
            - "echo 'hello world'"
            - "echo {{ parameters }}"
```

- AWS Documents are used to run commands with SSM Run Command
- They are also used with State Manager, Patch Manager, Automation
- AWS Documents can retrieve data from Parameter Store

## SSM Run Command

- Used for execute documents (scripts) or just to run simple commands
- Commands are executed across multiple instances (using resource groups)
- Documents are either managed by AWS or created and managed by users
- Run Command has builtin rate control/error control - progressively roll out commands to instances, in case of errors stop executing commands
- Integrates with IAM and CloudTrail
- SSM Run Command requires no SSH access to instances
- The output of the command can be shown in the AWS SSM Run Command Console, or it can be sent to an S3 bucket or to CloudWatch Logs
- Run Command can be evoked with Even Bridge

## SSM Automation

- Systems Manager Automation simplifies common maintenance and deployment tasks of Amazon EC2 instances and other AWS resources. Automation enables to do the following:
    - Build automations to configure and manage instances and AWS resources
    - Create custom runbooks or use pre-defined runbooks maintained by AWS
    - Receive notifications about Automation tasks and runbooks by using Amazon EventBridge
    - Monitor Automation progress and details by using the AWS Systems Manager console
- Automation Runbook:
    - SSM Documents of type Automation
    - Define actions to be performed on our EC2 instances or other AWS resources
    - Runbooks may be pre-defined (by AWS) or custom
- Automation can be triggered:
    - Manually by using the AWS console, AWS CLI or SDK
    - By Amazon EventBridge
    - On schedule using Maintenance Windows
    - By AWS Config rules remediations
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

## SSM Parameter Store

- Used for securely storing configuration and secrets in AWS
- It can have optional encryption using KMS
- Serverless, scalable, durable, easy SDK
- Version tracking of configurations and secrets
- Configuration management happens using IAM policies
- We get notification with Amazon EventBridge
- Has full integration with CloudFormation

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

### SSM Parameter Tiers

- **Standard**:
    - Total number of parameters: 10K
    - Max size of a parameter: 4KB
    - Pricing: free
    - **Parameter policies: NO**
- **Advanced**:
    - Total number of parameters: 100K
    - Max size of a parameter: 8KB
    - Pricing: 0.05$ per advanced parameter per month
    - **Parameter policies: YES**

### SSM Parameters Policies for Advanced Parameters

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

- Used to automatically patch running instances with OS updates, application updates, security patches, etc.
- Patching can happen on-demand or on a schedule with Maintenance Windows
- Patch Baselines: 
    - Defines which patches should and should not be installed on an instance
    - We have the ability to create custom Patch Baselines
    - Patches can be auto-approved withing days of their release
    - By default, only critical patches and security patches are installed
- Patch Groups:
    - We can associate a set of instances with a specific Patch Baseline
    - Instances should defined with the tag key `Patch Group`
    - An instance can only be part of one Patch Group
    - A Patch Group can only be registered with one Patch Baseline
- Patch Baseline types:
    - Pre-defined Patch Baseline:
        - Managed by AWS for different Operating Systems
        - `AWS-RunPatchBaseline` SSM Document can be used to run a Patch Baseline
    - Custom Patch Baseline:
        - We can create our own Patch Baseline and choose which patches to auto-approve
        - We can specify custom and alternative patch repositories

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

## SSM Session Manager

- Allows to start a secure shell on a VM (EC2 or on-premises)
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
- IAM Permissions:
    - Control which user/groups can access Session Manager and to which instance they can connect
    - We can use tags to restrict access to only specific EC2 instances
- Optionally, we can restrict commands an user can run in a session
- SSM Session Manager with VPC Endpoints:
    - Used to connect to EC2 instances which are in a private subnet without internet access
    - Required endpoints and ports:
        - `com.amazonaws.region.ssm` to allow Inbound 443
        - `com.amazonaws.region.ssmmessages` to allow Inbound 443
        - For the EC2 instance we must allow Outbound 443
        - In case of KMS usage we need a VPC endpoint for `com.amazonaws.region.kms`
        - For CloudWatch Logs we need a VPC endpoint for `com.amazonaws.region.logs`
        - In case of S3 usage we can use a VPC Gateway Endpoint for `com.amazonaws.region.s3` (requires route table update)

## SSM Default Host Management Configuration (DHMC)

- When it is enabled, it automatically configures our EC2 instances as managed instances without the use of EC2 Instance Profile (without IAM role)
- Requires an **Instance Identity Role**: a type of IAM role with no permission beyond identifying the EC2 instance to AWS Services such as Systems Manager
- EC2 instances must have **IMDSv2 enabled** and **SSM Agent installed**
- Once the system is managed by SSM, this will automatically enable Session Manager, Patch Manager and Inventory
- This service must be enabled per AWS region

## SSM Hybrid Environments (Hybrid Activations)

- We can use SSM to manage on-premises servers, IoT devices, edge devices and virtual machines (example VMs provided by other cloud providers)
- It requires from us to create a Hybrid Activation
- When a Hybrid Activation is created, SSM provides an **Activation Code** and an **Activation ID** which will be used to set up the SSM agent on the on-premise instance
- The instance id for EC2 instances starts with `i-` and for on-premise instances it starts with `mi-`
- Managed on-premise instances can be tagged in SSM

## IoT Greengrass Instance Activation

- We can manage IoT Greengrass Core devices using SSM
- We have to install SSM Agent on Greengrass Core devices, they will register themselves as managed node in SSM
- SSM Agent can be installed manually or deployed as a Greengrass Component (pre-built software module that we deploy directly to Greengrass Core devices)
- Once the SSM Agent is installed on the device, we must define a Token Exchange Role (IAM Role for IoT core devices)
- We must add permission to the Token Exchange Role to communicate with SSM
- Use cases: easily update and maintain OS and software updates across a fleet of Greengrass Core devices

## SSM Automations - Use Cases

- Reduce costs by automatically start and stop EC2 instances and RDS DB instances
- Reduce costs by automatically downsize EC2 instances and RDS DB instances
- Build a golden AMI (EC2 Image Builder is a better option)
- SSM Automations is integrated with AWS Config => remediate non-compliant resources

## SSM Compliance

- Used for scanning our fleet of managed nodes for patch compliance and configuration inconsistencies
- Displays current data about:
    - Patches in Patch Manager
    - Associations in State Manager
- Compliance data can be aggregated across multiple accounts/regions by syncing data to an S3 bucket using Resource Data Sync. This data can be analyzed by Athena and QuickSight
- Compliance can be sent do Security Hub

## SSM OpsCenter

- Allows us to view, investigate and remediate issues in one place (we don't need to navigate across different AWS services)
- Contains: security issues, performance issues (example DynamoDB throttle), failures (example ASG failed to launch an instance)
- The goal of OpsCenter is to reduce the meantime to resolve issues
- OpsCenter contains **OpsItems**:
    - Operational issues or interruptions that need investigation and remediation
    - These can be events, resources, AWS Config changes, CloudTrail logs, etc.
    - Provides recommended Runbooks to resolve issues
- Supports both EC2 instances and on-premises managed nodes