# CodeDeploy

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
- Deployment hooks:
    - ApplicationStop: script for stop currently running application
    - DownloadBundle: downloads the current application bundle from S3
    - BeforeInstall: runs before install script
    - Install: install script
    - AfterInstall: runs after install script is finished
    - ApplicationStart: script for starting the application
    - ValidateService: script for validating the started application
- Every hook can have a:
    - `timeout`: specifies the max running duration for the script
    - `runas`: specifies the user which would be used to execute the script (example: `root`)