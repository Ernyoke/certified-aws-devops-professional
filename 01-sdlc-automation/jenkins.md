# Jenkins on AWS

- Jenkins is an open source CICD tool
- It can replace CodeBuild, CodePipeline and CodeDeploy
- It must be deployed in master/slave configuration. This would require Multi-AZ deploy on EC2
- All projects must have a `Jenkinsfile` for configuration, similar to `buildspec.yml`
- Jenkins can be extended on AWS with the help of many plugins

## Jenkins Integration with CodePipeline

- Jenkins can be integrated as a stage in CodePipeline. For example it can replace CodeBuild by doing the build of the project

## Jenkins on AWS - Whitepaper

- Reference: [https://d1.awsstatic.com/whitepapers/DevOps/Jenkins_on_AWS.pdf](https://d1.awsstatic.com/whitepapers/DevOps/Jenkins_on_AWS.pdf)

## Jenkins Plugins

- **Amazon EC2** plugin: if Jenkins notices that our build cluster is overloaded, it will start instances using the EC2 API and automatically connect them as Jenkins agents. When the load goes down, excess EC2 instances will be terminated
- **AWS CodeBuild** plugin: official AWS plugin. Jenkins will send all the build jobs to CodeBuild
- **Amazon Elastic Container Service** plugin: Jenkins agents will be launched in AWS ECS
- **AWS CodePipeline** plugin: use Jenkins and CodePipeline together
- **Artifact Manager on S3**: keep artifacts and pipeline stats on S3
