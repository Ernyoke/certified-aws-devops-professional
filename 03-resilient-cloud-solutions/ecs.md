# ECS - Elastic Container Service

## ECS Clusters

- ECS Clusters are logical grouping of EC2 instances
- EC2 instances run the ECS agent which is a Docker container
- The ECS agents register the instance to the ECS cluster
- The EC2 instances run a special AMI made specifically for ECS

## ECS Launch Types

- EC2 Launch Type:
    - We must provision and maintain the infrastructure (EC2 instances)
    - Each EC2 instance must run the ECS Agent to register itself to the ECS Cluster
    - AWS takes care to starting/stopping containers
- Fargate Launch Type:
    - We do not provision the infrastructure (no EC2 instances to manage)
    - It is considered to by serverless offering
    - We just have to create task definitions and AWS will run our containers

## ECS - IAM Roles for ECS

- EC2 Instance Profile (EC2 LaunchType only):
    - Used by the ECS agent
    - The ECS agent Makes API calls to the ECS service
    - The ECS agent can send container logs to CloudWatch
    - It has to pull Docker images from ECR
    - It can reference sensitive data from Secrets Manager or SSM Parameter Store
- ECS Task Role (valid for both EC2 and Fargate LaunchTypes):
    - IAM Role attached to the ECS tasks
    - Allows each task to have a specific role
    - We can use different roles different ECS Services we run
    - Task Roles are defined in the task definition

## ECS Load Balancer Integration

- Application Load Balancer is supported for most use cases
- Network Load Balancer is recommended for high throughput/high performance use case, **or to pair with AWS PrivateLink** (used for private integration with the API Gateway)
- Classic Load Balancer is supported but not recommended. It does not provide any advanced features and it does not support Fargate

## ECS Task Definition

- Task definitions are metadata in JSON format containing information how to run a Docker container
- It contains information about:
    - Image name
    - Port Binding for container and host
    - Memory and CPU required
    - Environment variables
    - Networking information

## ECS Service

- ECS services define how many tasks should run and how they should be running
- They ensure that the number of tasks desired are running across fleet of EC2 instances
- ECS services can be linked to an Elastic Load Balancer (NLB/ALB)

## ECS Data Volumes (EFS)

- We can use Amazon EFS file systems to mount them to ECS tasks
- Works with both EC2 and Fargate launch types
- Tasks running in any AZ will share the same data in EFS file system
- Fargate + EFS =  Serverless
- Use cases for EFS usage: persistent multi-AZ shared storage for containers
- Amazon S3 cannot be mounted as a file system! (sort of, this is what the exam expects)

## ECS - Auto Scaling

- Service Auto Scaling (optional): we can configure the minimum number of tasks, maximum number of tasks and the desired number of task. It is similar to EC2 auto scaling
- We can scale based on the following metrics:
    - ECS Service Average CPU Utilization
    - ECS Service Average Memory Utilization
    - ALB Request Count Per Target - metric coming from the ALB
- Scaling policies:
    - **Target Tracking**: scale based on target value for a specific CloudWatch metroc
    - **Step Scaling**: requires alarms to increase and decrease the number of tasks
    - **Scheduled Scaling**: scale based on a predefined date/time
- ECS Service Auto Scaling (task level) != EC2 Auto Scaling (instance level)
- Fargate Auto Scaling is much easier to manage
- Auto Scaling EC2 instances:
    - We can accommodate ECS service scaling by adding new EC2 instances to the cluster
    - This can be accomplished in multiple ways:
        - **Auto Scaling Group Scaling**:
            - Scale our ASG based on CPU utilization
            - Add EC2 instances over time
        - **ECS Cluster Capacity Provider**:
            - Used to automatically provision and scale the infrastructure for our ECS tasks
            - Capacity Providers are paired with Auto Scaling Groups
            - Recommended way to scaling EC2 instances used for an ECS cluster

## ECS - Logging

- Logging with `awslogs` driver:
    - Containers can send application logs directly to CloudWatch Logs
    - We need to turn on the `awslogs` log driver for this
    - This is done with the `logConfiguration` parameter in the Task Definition
    - For the log driver we can select the log group, log stream prefix and the AWS region
    - There is no CloudWatch agent required to be installed for ECS
    - With the Fargate Launch Tpe:
        - We have to make sure the Task Execution role has the required permissions to send logs to CloudWatch
        - There is support for other drivers: `splunk`, `awsfirelens`
    - With the EC2 Launch Type:
        - We can use the CloudWatch Unified Agent and ECS Container Agent
        - We enable logging using `ECS_AVAILABLE_LOGGING_DRIVERS` in `/etc/ecs/ecs.config`
        - Containers in the EC2 instances must have permissions to push logs in case of CloudWatch
- ECS Logging with Sidecar Container:
    - Accomplished using a sidecar container which is responsible for collecting logs from all other containers and files on the file system and send those logs to a log aggregator


## CloudWatch Container Insight

- If enabled, Container Insights sends per container metrics to CloudWatch. It collects, aggregates and summarizes compute utilization such as CPU, memory, disk, networking information

## Elastic Beanstalk + ECS

- We can run Elastic Beanstalk in single and multi Docker container mode
- Multi Docker helps run multiple containers per EC2 instance in EB
- EB multi container mode will create:
    - ECS cluster
    - EC2 instances, configured to use the ECS cluster
    - Load Balancer (in high availability mode)
    - Task definitions and execution
- Requires a config file named **Dockerrun.aws.json** which has to be placed a the root of the source code

## ECS CI/CD Pipeline

- Reference tutorials: 
    - [https://ecsworkshop.com/](https://ecsworkshop.com/)
    - [https://docs.aws.amazon.com/codepipeline/latest/userguide/ecs-cd-pipeline.html](https://docs.aws.amazon.com/codepipeline/latest/userguide/ecs-cd-pipeline.html)
