# AWS CloudFormation

- CloudFormation is a declarative way of outlining AWS infrastructure for any resources

## Benefits of CloudFormation

- Infrastructure as code:
    - No resources are manually created, which is excellent for control
    - The code can be version controlled
    - Changes to the infrastructure are reviewed through code reviews
- Cost:
    - Each resource within the stack is tagged with an identifier, we can easily see how much a stack costs
    - We can estimate the cost of the resources using CloudFormation templates
    - Saving strategy: in dev, we can automate deletion of templates at 5PM and recreate the whole stack at 8AM in the morning
- Productivity:
    - Ability to destroy and re-create an infrastructure on the cloud on the fly
    - Automated generation of diagram for the templates
    - Declarative programming, no need to figure out ordering and orchestration
- Separation of concerns: create many CF stacks for applications and layers

## How CloudFormation Works

- Templates have to uploaded to S3 and then be references in CloudFormation
- To update a template we can't edit previous ones. We have to re-upload a new version of the template to AWS
- Stacks are identified by a name
- Deleting a stack deletes every single artifact that was created by CloudFormation

## Deploy CloudFormation Templates

- Manual way:
    - Editing templates in the CloudFormation Designer
    - Using the console to input parameters, etc.
- Automated way:
    - Templates are YAML files, we can edit them in a text editor
    - Using the AWS CLI we can deploy them
    - Recommended way for automated workflow

## CloudFormation Building Blocks

- Template components:
    - AWSTemplateFormatVersion: identifies capabilities of the template. Usually it is a date
    - Description: comments about the template
    - Resources (MANDATORY): AWS resources declared in the template
    - Parameters: dynamic input for the templates
    - Mappings: static variables for the templates
    - Outputs: references to what has be created
    - Conditionals: list of conditions ot perform resource creation
- Template helpers:
    - References
    - Functions

## CloudFormation Resources

- Resources are the of CF template and they are mandatory
- They represent the different AWS components that will be created and configured
- Resources are declared and reference each other
- AWS figures out creation, updates and deletes of resources
- Resource types identifiers are of the form of: `service-provider::service-name::data-type-name`. Example: `AWS::EC2::Instance`
- We can create a dynamic number of resources using CloudFormation Macros and Transform
- Almost every AWS service is supported. For those which are not supported, we can use CloudFormation Custom Resources as a workaround
- Referencing a resource from another resource can be done with `!Ref` function

## CloudFormation Parameters

- Parameters are a way to provide inputs to AWS CloudFormation template
- They are important if we want to:
    - Reuse templates across multiple projects
    - Provide some input when executing the template, since some template may be unknown before execution
- Parameters do have a type, preventing some errors before execution
- Parameters can be controller by all of these settings:
    - Types:
        - `String`
        - `Number`
        - `CommaDelimitedList`
        - `List<Type>`
        - SSM Parameters (get parameter value from SSM Parameter Store)
    - `Description`
    - `ConstraintDescription` (String)
    - `MinLength`/`MaxLength`
    - `MinValue`/`MaxValue`
    - `Default`
    - `AllowedValues` (array)
    - `AllowedPattern` (regex)
    - `NoEcho` (boolean; do not display his value anywhere, useful for passwords) 
- Example:
    ```yaml
    Parameters:
        InstanceType:
            Description: Chose an EC2 instance type
            Type: String
            AllowedValues:
            - t2.micro
            - t2.small
            - t2.medium
        Password:
            Description: Admin password
            Type: String
            NoEcho: True
    
    Resources:
        MyEC2Instance:
            Type: AWS::EC2::Instance
            Properties:
                InstanceType: !Ref InstanceType
                ImageId: ami-1233
    ```
- Referencing a parameter can be done with function `!Ref`
- Pseudo Parameters:
    - AWS offers us pseudo parameters in any CloudFormation template
    - These can be used at any time and are enabled by default
    - Examples:
        - `AWS::AccountId`
        - `AWS::Region`
        - `AWS::StackId`
        - `AWS::StackName`
        - `AWS::NoValue`

## CloudFormation Mappings

- Mappings are fixed variables within a CF template
- They are handy to differentiate between different environments, regions, AMI types, etc.
- All the values are hardcoded within a template
- Mappings are great when we know in advance all the values that can be taken by an input
- They offer a safer control over a template
- To access a mapping, we can use `Fn::FinInMap` function:
    ```
    !FindInMap [MapName, TopLevelKey, SecondLevelKey]
    ```

## CloudFormation Output

- Output sections are optional
- Output values can be imported into other stacks
- We can also view the outputs in the AWS console or using the AWS CLI
- It is the best way to perform collaboration across stacks, different stacks can be handled an maintained by different people
- We can not delete a CloudFormation stack if its outputs are being references by another stack
- Output `Export` block: has to be specified in order the the output to be able to be imported in another template
- `!ImportValue` or `Fn::ImportValue`: imports an output into a stack 

## CloudFormation Conditions

- Conditions are used to control the creation of resources or outputs based on some conditions
- Conditions can reference other conditions, parameters or values
- Example:
    ```
    Condition:
        CreateProdResources: !Equals [ !Ref EnvType, prod ]
    ```
- Intrinsic (logical) functions for conditions:
    - `Fn::And`
    - `Fn::Equals`
    - `Fn::If`
    - `Fn::Not`
    - `Fn::Or`
- Using a condition: they can be applied to resources, outputs, etc.
- Example of using a condition on a resource:
    ```yaml
    Resources:
        MountPoint:
            TYpe: AWS::EC2::VolumeAttachment
            Condition: CreateProdResources
    ```

## Intrinsic Functions

- `Fn::Ref` (`!Ref`):
    - Parameters: returns the value of the parameter
    - Resources: returns the physical ID of the underlying resource
- `Fn::GetAtt` (`!GetAtt`):
    - Attributes are attached to any resources we create
    - We can retrieve exposed attributes of any resources using this function
- `Fn::FindInMap`:
    - We use this function to return a named value from a specific key
- `Fn::ImportValue`:
    - Import values that are exported by other templates
- `Fn::Join`:
    - Join values with a delimiter: `!Join[ delimiter, [ comma- delimited list of values ] ]`
- `Fn::Sub`::
    - Used for substitute values in strings
    - String must contain a `${VariableName}` which will be substituted
- `Fn::Base64`
- `Fn:Cidr`
- `Fn:GetAZs`

## CloudFormation Rollbacks

- Stack creation fails:
    - Default: everything rolls back, stack will be deleted (`OnFailure=ROLLBACK`)
    - We can disable the rollback in order to troubleshoot what happened (`OnFailure=DO_NOTHING`)
    - If we want to get rid of the whole stack on failure, we can set `OnFailure=DELETE`
- Stack update fails:
    - The stack automatically rolls back to the previous known working state
    - We can see the in the logs what happened and what are the error messages
- In case of a rollback failure happens we have to manually fix the issue and then use `ContinueUpdateRollback` API from Console

## CloudFormation Service Role

- It is an IAM role that allows CloudFormation to create/update/delete stacks on our behalf
- It can be used to give the ability to users to create/update/delete the stack resources even if they don't explicitly have permission to work with each resource from the stack
- The user must have `iam:PassRole` permission

## CloudFormation Capabilities

- In order to be able to create or update IAM roles during stack creation, we have to manually allow this
- `CAPABILITY_IAM`, `CAPABILITY_NAMED_IAM`: some stack templates might include resources that can affect permissions in your AWS account, for example, by creating new AWS Identity and Access Management (IAM). For those stacks, you must explicitly acknowledge this by specifying one of these capabilities
- `CAPABILITY_AUTO_EXPAND`: necessary when the template includes Macros or Nested Stacks to perform dynamic transformations
- `InsufficientCapabilitiesException`: this exception is returned if the capabilities where not allowed

## CloudFormation `DeletionPolicy`

- We can put a DeletionPolicy on any resource to control what happens when the CloudFormation template is deleted or the resource is removed from the template
- Extra safety measure to preserve and backup resources
- `DeletionPolicy=Delete`:
    - Default for all resources, excluding RDS db cluster resources, where the default is snapshot 
    - To delete an S3 bucket, we have to delete everything first from the bucket
- `DeletionPolicy=Retain`:
    - To be specify on resource to preserve/backup in case of CloudFormation deletes
    - Works for any resource and nested stacks
- `DeletionPolicy=Snapshot`:
    - Works on EBS volumes, ElastiCache cluster, ElastiCache ReplicationGroup, RDS database instance, RDS database cluster, Redshift cluster

## CloudFormation Stack Policies

- During a CloudFormation stack update, all update actions are allowed on all resources (default)
- A Stack Policy is a JSON document that defines the update actions allowed on specific resources during the stack update
- Protect resources from unintentional updates
- When we set a Stack Policy, all resources in the Stack are protected by default
- We have to specify an explicit `ALLOW` for the resources we want to be updatable

## CloudFormation Termination Protection

- To prevent accidental deletes on CloudFormation Stacks, we can use Termination Protection option

## Custom Resources

- We can define custom resources to address any of the following use cases:
    - An AWS resource is not yet covered (new service for example)
    - Track an on-premise resource
    - Have a custom script running during create/update/delete, for example empty an S3 bucket
    - Fetch an AMI id (the old way)
- Custom resources are backed by either Lambda functions (most common) or an SNS topic
- To define a custom resource we have to use the `ServiceToken` property which has to be an ARN to a Lambda function or SNS topic
- There will be an event passed to the Lambda which contains the request type (create, update, delete) and the response url (the url for callback from the function). We can also pass some parameters as resource properties (key-value pairs) to the Lambda

## Dynamic References

- Used to reference external values stored in SSM Parameter Store and Secrets Manager
- CloudFormation retrieves the value of the reference during create/update/delete operations
- Dynamic references can be:
    - `ssm` for plaintext stored in SSM Parameter Store
    - `ssm-secure` for secure string stored in SSM Parameter Store
    - `secretsmanager` for secret values stored in Secrets Manager
- For RDS Databases, if we have `ManageMasterUserPassword` field set to true, CloudFormation will automatically create a secret password in Secrets Manager

## User Data in EC2 for CloudFormation

- We can have user data at EC2 instance launch through console
- This also can be included in CloudFormation
- **The entire script should be passed through the function Fn::base64**
- All the log of the user data will be stored in `/var/log/cloud-init-output.log`

## CloudFormation Helper Scripts

- The problems with Use Data are the following:
    - It is a bash script, what if we have a very large configuration?
    - What if we want to evolve the state of the EC2 instance without terminating it and creating a new one?
    - How do we know if the User Data script was successfully completed?
- Solution: **CloudFormation Helper Scripts** : Python scripts that come directly on Amazon Linux AMIs, or they can be installed using `yum` or `dnf` on non-Amazon Linux AMIs
- CloudFormation Helper Scripts are to following: `cfn-init`, `cfn-signal`, `cfn-get-metadata`, `cfn-hup`

### cnf-init

- `AWS::CloudFormation::Init`:
    - A block of configuration that belongs to `Metadata` block
    - Contains the following:
        - Packages: used to download and install pre-packaged apps and components of Linux/Windows
        - Groups: define user groups
        - Users: define users and to which group they belong
        - Sources: download files and archives and place them on the EC2 instance
        - Files: create files on the EC2 instance using inline text or pull files from an URL
        - Commands: series of shell commands
        - Services: launch a list of services
- cnf-init is used ti retrieve and interpret the resource metadata, installing packages, creating files and starting services
- With the cfn-init script it helps make complex EC2 configurations readable
- The EC2 instance will query the CloudFormation service to get the init data
- Logs will be available in `/var/log/cfn-init.log` file

### cfn-signal and Wait Conditions

- Used to tell CloudFormation that the EC2 instance got properly configured after `cfn-init` script finished
- We have to run the `cfn-signal` script right after the `cfn-init` script finished. This will tell CloudFormation if the init script succeeded or not
- WaitConditions: tells CloudFormation to wait until it receives a signal from `cfn-signal`
    - We block the template until it receives a signal from `cfn-signal`
    - We attach a `CreationPolicy`: how many signal we want to see and for how much we want to wait
- In case a wait condition did not receive the required number of signals from an EC2 instance:
    - We have to ensure the AMI has the CloudFormation scripts installed
    - We have to verify the `cfn-init` and `cfn-signal` commands were successfully run on the instance. We can view the logs such as `/var/log/cloud-init.log` or `/var/log/cnf-init.log`
    - We can retrieve the logs by logging into the instance, but we have to disable rollback on failure in order for CloudFormation to not delete the instance
    - The instance must have connection to the internet. If the instance is in a VCP, it should be able to connect to the internat using a NAT gateway

### cfn-hup

- Can be used to tell our EC2 instance to look for Metadata changes every 15 (default, can be changed by specifying an `interval`) minutes and apply the Metadata configuration
- Allows to make configuration updates on our running Amazon EC2 instances through the `UpdateStack` API action
- It relies on`cfn-hup` configurations stored in `/etc/cnf/cnf-hup.conf` and `/etc/cfn/hooks.d/cnf-auto-reloader.conf`

## CloudFormation Nested Stacks

- They allow to isolate repeated patterns, common components in separate stacks and call them from other stack
- Nested stacks are considered to be best practice
- To update a nested stack, alway update the parent (root stack)
- Nested stacks can have nested stacks in themselves
- Cross Stacks vs Nested Stacks:
    - Cross Stacks:
        - Helpful when stacks have different lifecycles
        - The use outputs exported by other stacks
    - Nested Stacks:
        - Helpful when components must be re-used, example a properly configured Application Load Balancer

## CloudFormation - `DependsOn`

- It is a way to define a specific resource should be created after another one was already created

## CloudFormation StackSets

- Used for create/update/delete stacks across multiple accounts and regions, all in a single operation
- An administrator account has to create StackSets, while the target accounts will create/update/delete the stack instances from the StackSet
- When we update a StackSet in the admin account, all the associated target accounts will receive this update
- Permission models:
    - Self-managed Permissions: 
        - Create the IAM roles (with established trusted relationship) in both administrator and target accounts
        - Deploy to any target account in which we have permissions to create IAM role
    - Service-managed Permissions:
        - Deploy to accounts managed by AWS Organizations
        - StackSets create the IAM Roles on our behalf (enable trusted access with AWS Organizations)
        - We must enable all features in AWS Organizations
        - Ability to deploy to any new accounts added to our organization in the future
- StackSets in AWS Organizations:
    - Provide the ability to automatically deploy stack instances when new accounts are added to the organizations
    - We can delegate StackSets administration to member accounts from AWS Organizations
    - Trusted access with AWS Organization must be enabled before delegated administrators can deploy to AWS Organizations managed accounts

## CloudFormation ChangeSets

- When we update a stack, we need to know what will change before the changes themselves are applied
- ChangeSets wont tell us in advance if the update will be successful
- For Nested Stacks we will see the changes across all the stacks

## CloudFormation Drift Detection

- Drift detection operation on a stack determines whether the stack has drifted (changed) from its expected template configuration
- Returns detailed information about the drift status of each resource in the stack that supports drift detection
- StackSets Drift Detection:
    - We can use CloudFormation drift detection on StackSets as well
    - Performs drift detection on the stack associated with each stack instance in the StackSet
    - If the current state of a resource in a stack varies from the expected state:
        - The stack is considered drifted
        - The stack instance that the stack is associated with is considered drifted
        - The StackSet is considered drifted
    - Drift detection identifies unmanaged changes that happen outside of CFN
    - Changes made through CloudFormation to a stack directly (not at the StackSet level) are not considered drifted (it is not a best practice)
    - We can stop drift detection on a StackSet


## Deploying Lambda Functions using CloudFormation

- Inline: we can define the Lambda code inside the CF template
    - The code for the Lambda function should be defined under `Code ZipFile` key which should specify that its content is a multi line content (use the pipe operator for this `|`)
    - Restrictions: 
        - Code should be limited in length 
        - We can not have dependencies
- Zip: we can zip the function and its dependencies and upload the archive into an S3 bucket
    - The code for the lambda can be references under `Code S3Bucket` tag
    - We have to specify the bucket and location for the zip in S3
    - We can also reference the version of the archive by `S3ObjectVersion`

## Status Codes

- `CREATE_COMPLETE`: Successful creation of one or more stacks
- `CREATE_IN_PROGRESS`: Ongoing creation of one or more stacks
- `CREATE_FAILED`: Unsuccessful creation of one or more stacks. Possible reasons for a failed creation include insufficient permissions to work with all resources in the stack, parameter values rejected by an AWS service, or a timeout during resource creation
- `DELETE_COMPLETE`
- `DELETE_FAILED`
- `DELETE_IN_PROGRESS`
- `REVIEW_IN_PROGRESS`: Ongoing creation of one or more stacks with an expected `StackId` but without any templates or resources
- `ROLLBACK_COMPLETE`: Successful removal of one or more stacks after a failed stack creation or after an explicitly canceled stack creation. Any resources that were created during the create stack operation are deleted
- `UPDATE_COMPLETE`: Successful update of one or more stacks
- `UPDATE_COMPLETE_CLEANUP_IN_PROGRESS`: Ongoing removal of old resources for one or more stacks after a successful stack update
- `UPDATE_ROLLBACK_FAILED`: Unsuccessful return of one or more stacks to a previous working state after a failed stack update. When in this state, we can delete the stack or continue rollback. We might need to fix errors before our stack can return to a working state. Or, we can contact AWS Support to restore the stack to a usable state
    - Explanation: 
        - [AWS Blog](https://aws.amazon.com/blogs/devops/continue-rolling-back-an-update-for-aws-cloudformation-stacks-in-the-update_rollback_failed-state/#:~:text=There%20are%20other%20reasons%20an,dependent%20resource%20did%20not%20stabilize.&text=In%20the%20AWS%20CloudFormation%20console,then%20choose%20Continue%20Update%20Rollback.)
        - [Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-continueupdaterollback.html)
        - [Troubleshooting](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/troubleshooting.html#troubleshooting-errors-update-rollback-failed)
- `IMPORT_IN_PROGRESS`: The import operation is currently in progress
- `IMPORT_COMPLETE`
- `IMPORT_ROLLBACK_IN_PROGRESS`

## CloudFormation Troubleshooting

- `DELETE_FAILED`:
    - Some resources could not have been deleted
    - In case of S3 buckets, the buckets have to be emptied before deletion
    - We have to empty the buckets manually, or use a Custom Resource with a Lambda Function
    - Security Groups cannot be deleted until all attachments are removed
    - `DeletionPolicy=Retain` will skip the deletion of the resource that is failing to be removed
- `UPDATE_ROLLBACK_FAILED`:
    - Can be caused by resources changed outside of CloudFormation, insufficient permissions, Auto Scaling Group that does not receive enough signals, etc.
    - Solution is to manually fix the errors and then issue the following API call `ContinueUpdateRollback`
- StackSets Troubleshooting:
    - A stack operation failed and the stack instance status is `OUTDATED`:
        - Might be caused by insufficient permissions in a target account for creating resources that are specified in the template
        - The template could be trying to create global resources that must be unique such as S3 buckets
        - The admin account does not have a trust relationship with the target account
        - We might have reached a limit or a quota in the target account