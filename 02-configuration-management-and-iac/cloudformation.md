# CloudFormation

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
    1. Resources: AWS resources declared in the template (MANDATORY)
    2. Parameters: dynamic input for the templates
    3. Mappings: static variables for the templates
    4. Outputs: references to what has be created
    5. Conditionals: list of conditions ot perform resource creation
    6. Metadata
- Template helpers:
    1. References
    2. Functions

## Parameters

- Parameters are a way to provide inputs to AWS CloudFormation template
- They are important if we want to:
    - Reuse templates across multiple projects
    - Provide some input when executing the template, since some template may be unknown before execution
- Parameters do have a type, preventing some errors before execution
- Parameters can be controller by all of these settings:
    - Types:
        - String
        - Number
        - CommaDelimitedList
        - List< Type >
        - AWS parameter (to help catch invalid values - match against existing values in AWS account)
    - Description
    - Constraints
    - Constraint Description (String)
    - Min/Max Length
    - Min/Max Value
    - Allowed Values (array)
    - Allowed Pattern (regex)
    - No Echo (boolean)
- Referencing a parameter can be done with function `!Ref`
- Pseudo Parameters:
    - AWS offers us pseudo parameters in any CloudFormation template
    - These can be used at any time and are enabled by default
    - Examples:
        - AWS::AccountId
        - AWS::Region
        - AWS::StackId
        - AWS::StackName
        - AWS::NoValue

## Resources

- Resources are the of CF template and they are mandatory
- They represent the different AWS components that will be created and configured
- Resources are declared and reference each other
- AWS figures out creation, updates and deletes of resources
- Resource types identifiers are of the form of: `AWS::aws-product-name::data-type-name`
- We can not create a dynamic amount of resources

## Mappings

- Mappings are fixed variables within a CF template
- They are handy to differentiate between different environments, regions, AMI types, etc.
- All the values are hardcoded within a template
- Mappings are great when we know in advance all the values that can be taken by an input
- They offer a safer control over a template
- To access a mapping, we can use `Fn::FinInMap` function:
    ```
    !FindInMap [MapName, TopLevelKey, SecondLevelKey]
    ```

## Output

- Output sections are optional
- Output values can be imported into other stacks
- We can also view the outputs in the AWS console or using the AWS CLI
- It enables a way to perform some cross stack collaborations, as we let experts handle their own part of the stack
- We can not delete a CF stack if its outputs are being references by another stack
- Output `Export` block: has to be specified in order the the output to be able to be imported in another template
- `!ImportValue` or `Fn::ImportValue`: imports an output into a stack 


## Conditions

- Conditions are used to control the creation of resources or outputs based on some conditions
- Conditions can reference other conditions, parameters or values
- Example:
    ```
    Condition:
        CreateProdResources: !Equals [ !Ref EnvType, prod ]
    ```
- Intrinsic (logical) functions for conditions:
    - Fn::And
    - Fn::Equals
    - Fn::If
    - Fn::Not
    - Fn::Or
- Using a condition: they can be applied to resources, outputs, etc.

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

## User Data in EC2 for CloudFormation

- We can have user data at EC2 instance launch through console
- This also can be included in CloudFormation
- **The entire script should be passed through the function Fn::base64**
- All the log of the user data will be stored in `/var/log/cloud-init-output.log`

## cfn-init

- `AWS::CloudFormation::Init` must be in the metadata section of a resource
- With the cfn-init script it helps make complex EC2 configurations readable
- The EC2 instance will query the CloudFormation service to get the init data
- Logs will be available in `/var/log/cfn-init.log` file

## cfn-signal and Wait Conditions

- Used to tell CloudFormation that the EC2 instance got properly configured after cfn-init script finished
- We have to run the cfn-signal script right after the cfn-init script finished. This will tell CloudFormation if the init script succeeded or not
- WaitConditions: tells CloudFormation to wait until it receives a signal from cfn-signal
    - We block the template until it receives the cfn-signal
    - We attach a `CreationPolicy`: how many signal we want to see and for how much we want to wait
- Wait condition did not receive the required number of signals from an EC2 instance:
    - We have to ensure the AMI has the CloudFormation scripts installed
    - We have to verify the cfn-init and cfn-signal commands were successfully run on the instance
    - We can retrieve the logs by logging into the instance, but we have to disable rollback on failure in order for the CF to not delete the instance
    - Instance must have connection to the internet. If the instance is in a VCP, it should be able to connect to the internat using a NAT gateway

## CloudFormation Rollbacks

- Stack creation fails:
    - Default: everything rolls back, stack will be deleted (`OnFailure=ROLLBACK`)
    - We can disable the rollback in order to troubleshoot what happened (`OnFailure=DO_NOTHING`)
    - If we want to get rid of the whole stack on failure, we can set `OnFailure=DELETE`
- Stack update fails:
    - The stack automatically rolls back to the previous known working state
    - We can see the in the logs what happened and what are the error messages

## Nested Stacks

- They allow to isolate repeated patterns, common components in separate stacks and call them from other stack
- Nested stacks are considered to be best practice
- To update a nested stack, alway update the parent (root stack)

## ChangeSets

- When we update a stack, we need to know what will change before the changes themselves are applied
- Change sets wont say if the update will be successful

## Retain Data on Delete

- We can put a DeletionPolicy on any resource to control what happens when the CloudFormation template is deleted
- `DeletionPolicy=Retain`:
    - To be specify on resource to preserve/backup in case of CloudFormation deletes
    - Works for any resource and nested stacks
- `DeletionPolicy=Snapshot`:
    - Works on EBS volumes, ElastiCache cluster, ElastiCache ReplicationGroup, RDS database instance, RDS database cluster, Redshift cluster
- `DeletionPolicy=Delete`:
    - Default for all resources, excluding RDS db cluster resources, where the default is snapshot 
    - To delete an S3 bucket, we have to delete everything first from the bucket

## Termination Protection

- To prevent accidental deletes on CloudFormation templates, we have to use Termination Protection

## SSM Parameters

- Type of parameter: `AWS::SSM:Parameter::Value< String >`
- Parameter will be fetched from the path specified by `Default` key
- In parameters tab in CloudFormation we can see a resolved value for the SSM parameters
- AWS provides some default public SSM parameters which can be referenced (example: latest EC2 AMI)

## CloudFormation - `DependsOn`

- It is a way to define a specific resource should be created after another one was already created

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

## Custom Resources

- We can define custom resources to address any of the following use cases:
    - An AWS resource is not yet covered (new service for example)
    - Track an on-premise resource
    - Empty an S3 bucket
    - Fetch an AMI id (the old way)
- Custom resources are Lambda function which will be invoked at every create, update or delete event
- There will be an event passed to the Lambda which contains the request type (create, update, delete) and the response url (the url for callback from the function). We can also pass some parameters as resource properties (key-value pairs) to the Lambda

## Drift Detection

- Drift detection operation on a stack determines whether the stack has drifted from its expected template configuration
- Returns detailed information about the drift status of each resource in the stack that supports drift detection

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

## Capabilities

- In order to be able to create IAM roles during stack creation, we have to manually allow this
- `CAPABILITY_IAM`, `CAPABILITY_NAMED_IAM`: some stack templates might include resources that can affect permissions in your AWS account, for example, by creating new AWS Identity and Access Management (IAM). For those stacks, you must explicitly acknowledge this by specifying one of these capabilities
- `InsufficientCapabilitiesException`: this exception is returned if the capabilities where not allowed

## cfn-hup

- `cfn-hup` helper is a daemon that detects changes in resource metadata and runs user-specified actions when a change is detected
- Allows to make configuration updates on our running Amazon EC2 instances through the `UpdateStack` API action
- Default listening time for update: 15 minutes
- Can be changed by specifying a `interval`

## Stack Policies

- To protect stack resources from update actions, we can define a stack policy
- When we set a stack policy on a stack, any update not explicitly allowed is denied by default 