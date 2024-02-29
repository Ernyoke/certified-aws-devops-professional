# AWS CodeDeploy

- Used to deploy an application to one or more EC2 instances automatically
- Alternative of third party tools such as Ansible, Terraform, Puppet, etc.
- It is a managed service
- Each EC2 machine or on-premise instance must run the CodeDeploy Agent
- EC2 instances are grouped by deployment group (dev/test/prod)
- Provides lots of flexibility to define any kind of deployments
- CodeDeploy can be chained into CodePipeline and use artifacts from there
- CodeDeploy can re-use existing setup tools, works with any application, has autoscaling integration
- Supports AWS Lambda deployment as well, EC2 and ECS
- CodeDeploy does not provision resources

## Steps of Deployment

- The agent is continuously polling AWS CodeDeploy for work
- CodeDeploy sends `appspec.yml` file
- Application is pulled from the repository or S3
- The instance will run the deployment instructions
- CodeDeploy Agent will report the status of the deployment

## Deployment Groups

- With deployment groups we can deploy an application to an ASG, one ore multiple EC2 instances and on on-premise instances as well
- We can target multiple instances by tagging them and do a query on them based on tags

## Deployment Configurations

- Deployment types:
    - In-place
    - Blue/Green
- Deployment strategies:
    - OneAtATime
    - HalfAtATime
    - AllAtOnce
    - Custom configuration: we can specify how many healthy instances should we have at any point of the deployment
- Blue/Green: replaces every instance with new instances. The new instances are automatically assigned to the existing ASG target (**requires an ASG and a LB**)

## appspec.yml - Deep Dive

- Example: [appspec.yml](appspec.yml)
- Documentation [https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html
)
- Deployment hooks (lifecycle events):
    - **ApplicationStop**: occurs before the new revision of the application is downloaded. Use for defining a script which gracefully stops the application
    - **DownloadBundle**: downloads the current application revision from S3 and copies the revision to a temporary location. This event is reserved for CodeDeploy agent and can not be used to run scripts
    - **BeforeInstall**: runs before install script
    - **Install**: during this event, the CodeDeploy agent copies the revision files from the temporary location to the final destination folder. This event is reserved for CodeDeploy agent and can not be used to run scripts
    - **AfterInstall**: runs after install lifecycle event finished. This event can be used for tasks such as configuration or changing file permissions
    - **ApplicationStart**: this event can be used to restart the application stopped during ApplicationStop event
    - **ValidateService**: last deployment event. Can be used to validate if the deployment was successful
- In case we have load balancer, we can have the following additional hooks:
    - **BeforeBlockTraffic**
    - **BlockTraffic**
    - **AfterBlockTraffic**
    - **BeforeAllowTraffic**
    - **AllowTraffic**
    - **AfterAllowTraffic**
- Hook which can execute scripts can have the following properties:
    - `timeout`: specifies the max running duration for the script
    - `runas`: specifies the user which would be used to execute the script (example: `root`)
- Environment variables for hooks:
    - `APPLICATION_NAME`: the name of the application in CodeDeploy
    - `DEPLOYMENT_GROUP_NAME`: the name of the deployment group
    - `DEPLOYMENT_GROUP_ID`: the id of the deployment group in CodeDeploy
    - `LIFECYCLE_EVENT`: the name of the current deployment lifecycle

## CloudWatch Events

- Use cases:
    - We can use a CloudWatch event to pass a notification to a Slack channel whenever deployment fails
    - Pass CodeDeploy events to Kinesis Streams
    - Use CloudWatch alarms to automatically stop, terminate, reboot or recover Amazon EC2 instances when a deployment event occurs

## CloudWatch Logs

- In order to send the CodeDeploy logs to CloudWatch, we have to install the CloudWatch log agent on the specific machines

## CodeDeploy Triggers

- Similar to CloudWatch events
- They are internal to CodeDeploy, while alarms are CloudWatch specific
- Trigger integrate directly to AWS SNS

## Rollbacks

- Manual rollbacks: 
    - It can be done when no automatic rollback is set up
    - We can do a manual rollback by creating a new deployment with a previous revision
    - We might want to do manual rollbacks if the application is in an unknown state
- Automatic rollbacks:
    - Can be enabled on the deployment group
    - There are 2 automatic rollback options:
        - Roll back when a deployment fails
        - Roll back when alarm threshold is met: for example if the CPU hits a threshold after a successful deployment, we can set an automatic rollback for it
    - Alarms can be set to be ignored during deployment

## Retain Manually Added Files

- As part of the deployment process, the CodeDeploy agent removes from each instance all the files installed by the most recent deployment. If files that weren't part of a previous deployment appear in target deployment locations, we can choose what CodeDeploy does with them during the next deployment:
    - **Fail the deployment**: an error is reported and the deployment status is changed to Failed
    - **Overwrite the content**: the version of the file from the application revision replaces the version already on the instance
    - **Retain the content**: the file in the target location is kept and the version in the application revision is not copied to the instance

## CodeDeploy with On-Premise Instances

- Deploying with CodeDeploy to an On-Premise instance involves to following steps:
    1. Configure each on-premise instance, register it with CodeDeploy and tag the instance (tag will be only available to CodeDeploy)
    2. Deploy the application to the instances
- To register an on-premise instance:
    - Use an IAM user ARN to authenticate the instance: works for small number of instances
    - Use an IAM role ARN to authenticate the instance: uses STS for large number of instances. It is more secure and it is the recommended way

## CodeDeploy with Lambda

- Deployment configuration - traffic shifting:
    - All-at-Once
    - Canary: traffic is shifted in 2 increments, example 10% of traffic is shifted to the new version for 5 minutes. If everything is ok for 5 minutes, all the traffic will be shifted to the new version
    - Linear: traffic si shifted in equal increments, step by step, example 10% more of traffic in shifted after every 1 minute
- appspec.yml lifecycle events (hooks):
    - BeforeAllowTraffic: an existing Lambda function can be executed to check if everything is fine before allowing traffic to the newly deployed Lambda
    - AllowTraffic
    - AfterAllowTraffic: an existing Lambda function can be executed to check if everything is fine after allowing traffic to the newly deployed Lambda