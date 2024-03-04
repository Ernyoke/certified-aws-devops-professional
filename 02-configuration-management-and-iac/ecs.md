# ECS - Elastic Container System

## ECS Clusters

- ECS Clusters are logical grouping of EC2 instances
- EC2 instances run the ECS agent which is a Docker container
- The ECS agents register the instance to the ECS cluster
- The EC2 instances run a special AMI made specifically for ECS

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

## ECR - Elastic Container Registry

- ECR is a private Docker image repository
- Access to ECR is controller through IAM
- In order to push a Docker image to ECR, we have to do the following commands
    - AWS CLI v1: `$(aws ecr get-login --no-include-email --region eu-west1)`
    - AWS CLI V2: `aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin 12345.dkr.ecr.eu-west-1.amazonaws.com`
    - Build the image: `docker build -d demo .`
    - Tag the image `docker tag demo:latest ...`
    - Push the image: `docker push 1234:dkr.ecr.eu-west-1.amazonaws.com/demo:latest`
- Pull an image from ECR:
    - Login with the same command above
    - Pull the image: `docker pull 1234:dkr.ecr.eu-west-1.amazonaws.com/demo:latest`

## Fargate

- It is a serverless service for running containers in the cloud
- We just have to create task definitions and AWS will run our containers

## Elastic Beanstalk + ECS

- We can run Elastic Beanstalk in single and multi Docker container mode
- Multi Docker helps run multiple containers per EC2 instance in EB
- EB multi container mode will create:
    - ECS cluster
    - EC2 instances, configured to use the ECS cluster
    - Load Balancer (in high availability mode)
    - Task definitions and execution
- Requires a config file named **Dockerrun.aws.json** which has to be placed a the root of the source code

## ECS - IAM Roles

- EC2 instance: needs an EC2 service policy for being able to interact with ECS. The policy attached is usually the one managed by AWS named `AmazonEC2ContainerServiceEC2Role`
- Task definitions can have a task role, which provides the Docker container tasks to interact with other AWS services

## ECS - Auto Scaling

- Service Auto Scaling (optional): we can configure the minimum number of tasks, maximum number of tasks and the desired number of task. It is similar to EC2 auto scaling
- Scaling policies:
    - Target tracking
    - Step scaling: requires alarms to increase and decrease the number of tasks

## ECS - Integration with CloudWatch

- For a task definition we can define a log drive at task creation
- With a log driver we can integrate the task logs with CloudWatch Logs
- For the log driver we can select the log group, log stream prefix and the AWS region
- There is no CloudWatch agent required to be installed for ECS
- **CloudWatch Container Insights**: sends per container metrics to CloudWatch. It collects, aggregates and summarizes compute utilization such as CPU, memory, disk, networking information

## ECS CI/CD Pipeline

- Reference tutorials: 
    - [https://ecsworkshop.com/](https://ecsworkshop.com/)
    - [https://docs.aws.amazon.com/codepipeline/latest/userguide/ecs-cd-pipeline.html](https://docs.aws.amazon.com/codepipeline/latest/userguide/ecs-cd-pipeline.html)
