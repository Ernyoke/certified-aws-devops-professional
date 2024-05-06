# AWS App2Container - A2C

- App2Container (A2C) is a command-line tool for modernizing .NET and Java applications into containerized applications
- A2C analyzes and builds an inventory of all applications running in virtual machines on-premises or in the cloud
- We simply select the application we want to containerize, and A2C packages the application artifact and identified dependencies into container images, configures the network ports, and generates the ECS task and Kubernetes pod definitions

## Features

- App2Container generates ECS task definitions and Kubernetes deployment YAML for the containerized application following the AWS best practices for security and scalability by integrating with various AWS services such as ECR, ECS, and EKS
- App2Container creates CI/CD pipelines for Amazon DevOps services such as CodeBuild and CodeDeploy to build and deploy containers
- If we have existing CI/CD tooling (for example, Azure DevOps and Jenkins), then we can integrate A2C provided artifacts – dockerfile, ECS task definition, EKS deployment YAML - into our existing CI/CD workflows
- App2Container generates CloudFormation templates that configure required compute, network, and security infrastructure to seamlessly deploy containerized applications in AWS