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
