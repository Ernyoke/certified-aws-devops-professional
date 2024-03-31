# Amazon EKS - Elastic Kubernetes Service

- It is a way to launch managed Kubernetes clusters on AWS
- Kubernetes is an open-source system for automatic deployment, scaling and management of containerized (usually Docker) applications
- It is an alternative to ECS, having similar goal but different API
- EKS supports EC2 launch mode if we want to deploy worker nodes or Fargate to deploy serverless containers
- Use case for EKS: if our company already is using Kubernetes on-premises on on another cloud and wants to migrate to AWS
- Kubernetes is cloud-agnostic, can be used in any cloud (Azure, GCP, etc.)

## EKS Node Types

- Managed Node Groups:
    - Creates and manages Nodes (EC2 instances) for us
    - Nodes are part of an ASG managed by EKS
    - Supports On-Demand or Spot Instances
- Self-Managed Nodes:
    - Nodes are created by us and registered to the EKS cluster
    - We can the use prebuilt AMI - Amazon EKS Optimized AMI; or we can build our own AMI
    - Supports On-Demand and Spot Instances
- AWS Fargate:
    - No maintenance required; no nodes to manage

## Data Volumes

- To attach a volume we need to specify a `StorageClass` in the manifest on our EKS cluster
- This will leverage a Container Storage Interface (CSI) compliant driver
- We have support for:
    - Amazon EBS
    - Amazon EFS (only type which works with Fargate)
    - Amazon FSx for Lustre
    - Amazon FSx for NetApp ONTAP

## Control Plane Logging

- Send EKS Control Plane audit and diagnostic logs to CloudWatch Logs
- EKS Control Plane log entry types:
    - API Server (`api`)
    - Audit (`audit`)
    - Authenticator (`authenticator`)
    - Controller Manager (`controllerManager`)
    - Scheduler (`scheduler`)
- We have the ability to select which kind of logs we want to send to CloudWatch Logs

## Nodes and Container Logging

- We can capture node, pod and container logs and sent them to CloudWatch Logs
- We have to use the CloudWatch Agent to send metrics to CloudWatch
- To get the logs we can use FluentBit or Fluentd log drivers
- Container logs are stored on a Node directory `/var/log/container`
- We can use CloudWatch Container Insights to get a dashboarding monitoring solution for nodes, pods, tasks and services