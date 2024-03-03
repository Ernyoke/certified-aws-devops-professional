# AWS CodeDeploy

- It is a serviced used to automate application deployments
- Alternative of third party tools such as Ansible, Terraform, Puppet, etc.
- It is a managed service
- New application versions can be deployed to EC2 instances, on-premises servers, Lambda functions, ECS services
- CodeDeploy provides automated rollback capability in case of a failed deployment
- In case of EC2 machines/servers, each machine or on-premise instance must run the CodeDeploy Agent
- EC2 instances are grouped by deployment group (dev/test/prod)
- CodeDeploy can be chained into CodePipeline and use artifacts from there
- CodeDeploy can re-use existing setup tools, works with any application, has autoscaling integration
- CodeDeploy does not provision resources

## Steps of a Deployment

- The agent is continuously polling AWS CodeDeploy for work
- CodeDeploy sends `appspec.yml` file
- Application is pulled from the repository or S3
- The instance will run the deployment instructions
- CodeDeploy Agent will report the status of the deployment

## Deploy to EC2/On-Premises Platform

- CodeDeploy supports 2 kind of deployments:
    - In-place deployments
    - Blue/Green deployments
- The instance where we want to deploy must have the CodeDeploy Agent installed
- We can define the deployment speed:
    - `AllAtOnce`
    - `HalfAtATime`
    - `OneAtATime`
    - Custom configuration: we can specify how many healthy instances should we have at any point of the deployment
- Blue/Green: replaces every instance with new instances. The new instances are automatically assigned to the existing ASG target (**requires an ASG and a LB**)
- CodeDeploy Agent:
    - It must be running on the EC2 instance as a pre-requisite
    - It can be installed and updated automatically if we are using Systems Manager
    - The Ec2 instance must have sufficient permissions to access Amazon S3 to get deployment bundles
- CodeDeploy with EC2 - In-place Deployments:
    - We have to use EC2 tags or ASG to identify instances where we want to deploy
    - If we are using a load balancer, the traffic to the instance is stopped before the instance is updated and started again after the update finished
    - Deployment hooks:
        - In case we use a Load Balancer: `BeforeBlockTraffic` -> `BlockTraffic` -> `AfterBlockTraffic` -> ... (see `appspec.yml` bellow)
- CodeDeploy with EC2 - Blue/Green Deployments:
    - There are 2 ways to provision EC2 instances:
        - *Manually*: we create the instances manually and identify them by tags during deployment
        - *Automatic*: we have an ASG (blue). CodeDeploy will create a new ASG with the same settings (Green). New instances will be created with the ASG, old instances will be terminated
    - A load balancer is required for Blue/Green deployments!
    - Instance termination:
        - `BlueInstanceTerminationOption`: we can specify wether to terminate the original EC2 instance after a successful deployment
        - `Action Terminate`: we can specify a wait time for termination
        - `Action Keep Alive`: instances are kept running but deregistered from the load balancer and deployment group
- CodeDeploy with On-Premise Instances:
    - Deploying with CodeDeploy to an On-Premise instance involves to following steps:
        1. Configure each on-premise instance, register it with CodeDeploy and tag the instance (tag will be only available to CodeDeploy)
        2. Deploy the application to the instances
    - To register an on-premise instance:
        - Use an IAM user ARN to authenticate the instance: works for small number of instances
        - Use an IAM role ARN to authenticate the instance: uses STS for large number of instances. It is more secure and it is the recommended way

## Deployment Groups

- With deployment groups we can deploy an application to an ASG, one ore multiple EC2 instances and on on-premise instances as well
- We can target multiple instances by tagging them and do a query on them based on tags

## Deploy to ECS

- CodeDeploy can help automate the deployment of a new ECS Task Definition
- Only works for Blue/Green deployments
- Application Load Balancer is required for traffic-shifting strategies. NLB supports immediate switch only
- Deployment traffic-shifting strategies:
    - Linear: grow traffic every N minutes. Options:
        - `ECSLinear10PercentEvery3Minutes`
        - `ECSLinear10PercentEvery10Minutes`
        - Custom
    - Canary: try X percent then 100%. Options:
        - `ECSCanary10Percent5Minutes`
        - `ECSCanary10Percent30Minutes`
    - AllAtOnce: switch immediately two the new target group
- The ECS Task Definition and the new container images must be already created before CodeDeploy executes
- Reference to the new ECS Task Revision (`TaskDefintion`) and the load balancer information (`LoadBalancerInfo`) are specified in teh `appspec.yml` file
- CodeDeploy Agent is not required
- We can define a second listener on the load balancer (Test Listener) where we can send test traffic for the new target group
- Deployment Hooks:
    - They cannot be scripts they have to be Lambda Functions
    - Hooks: `BeforeInstall` -> `Install` (cannot run script) -> `AfterInstall` -> `AllowTestTraffic` (cannot run script) -> `AfterAllowTestTraffic` -> `BeforeAllowTraffic` -> `AllowTraffic` (cannot run script) -> `AfterAllowTraffic`

## Deploy to Lambda

- CodeDeploy can help automate traffic shifting for Lambda Aliases:
    - *All-at-Once*
    - *Canary*: traffic is shifted in 2 increments, example 10% of traffic is shifted to the new version for 5 minutes. If everything is ok for 5 minutes, all the traffic will be shifted to the new version
    - *Linear*: traffic si shifted in equal increments, step by step, example 10% more of traffic in shifted after every 1 minute
- Traffic shifting is fully integrated with AWS SAM framework
- `appspec.yml` lifecycle events (hooks):
    - `BeforeAllowTraffic`: an existing Lambda function can be executed to check if everything is fine before allowing traffic to the newly deployed Lambda
    - `AllowTraffic`: cannot run scripts (Lambda functions)
    - `AfterAllowTraffic`: an existing Lambda function can be executed to check if everything is fine after allowing traffic to the newly deployed Lambda

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
    - **BeforeBlockTraffic** (can run scripts)
    - **BlockTraffic** (cannot run scripts)
    - **AfterBlockTraffic** (can run scripts)
    - **BeforeAllowTraffic** (can run scripts)
    - **AllowTraffic** (cannot run scripts)
    - **AfterAllowTraffic** (can run scripts)
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

## Troubleshooting

- Deployment Error: `InvalidSignatureException` - Signature expired
    - For CodeDeploy to perform its operation, it is required accurate time references
    - If the date and time of an EC2 instance are not set correctly, they might not match the signature date of our deployment request
    - Solution: synchronize time
- Deployment or all Lifecycle Events are skipped (EC2/On-prem):
    - Errors:
        - "The overall deployment failed because too many individual instances failed deployment"
        - "Too few healthy instances are available for deployment"
        - Error code: `HEALTH_CONSTRAINTS`
    - Solution:
        - We should make sure CodeDeploy Agent is installed and running
        - We should make sure the CodeDeploy Service Role or IAM instance profile have the required permissions
        - In case of a HTTP Proxy, configure CodeDeploy with `:proxy_uri` parameter
        - Make sure there is no date and time mismatch between CodeDeploy and CodeDeploy Agent
- If a CodeDeploy deployment to an ASG is in progress and a scale-out event happens, the previous version is deployed to the new instance
- By default, after a successful deployment, CodeDeploy will start a follow-on deployment to update any outdated instance
- `AllowTraffic` step is failing after a B/G deployment:
    - Reason: incorrectly configured health checks on ELB
    - Solution: review and correct any errors in ELB health checks configuration

## Retain Manually Added Files

- As part of the deployment process, the CodeDeploy agent removes from each instance all the files installed by the most recent deployment. If files that weren't part of a previous deployment appear in target deployment locations, we can choose what CodeDeploy does with them during the next deployment:
    - **Fail the deployment**: an error is reported and the deployment status is changed to Failed
    - **Overwrite the content**: the version of the file from the application revision replaces the version already on the instance
    - **Retain the content**: the file in the target location is kept and the version in the application revision is not copied to the instance