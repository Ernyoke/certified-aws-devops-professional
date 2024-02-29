# AWS CodePipeline

- It is a continuous delivery tool and a visual workflow
- We can define sources: GitHub, CodeCommit, S3
- We can define build tools such as CodeBuild, Jenkins, etc.
- We use third party tools for testing
- We can deploy to BeanStalk, CloudFormation, ECS or CodeDeploy
- It is made of stages:
    - Each stage can have sequential and/or parallel actions
    - Example of stages: Build, Test, Deploy, LoadTest
    - Stages can have manual approval steps

## CodePipeline Concepts

- **Pipeline**: is a workflow construct that describes how software changes go through a release process. Each pipeline is made up of a series of stages
- **Stage**:  is a logical unit we can use to isolate an environment and to limit the number of concurrent changes in that environment. Each stage contains actions that are performed on the application artifacts
- **Action**: is a set of operations performed on application code and configured so that the actions run in the pipeline at a specified point. Valid CodePipeline action types are source, build, test, deploy, approval, and invoke

## CodePipeline Artifacts

- Each pipeline stage can create artifacts
- Artifacts are stored in S3 and passed on to the next stage
- We can have a central S3 bucket for all the pipelines
- Artifacts are encrypted by default with an AWS managed key. We can create our own key in KSM if we want
- Artifacts are passed between CodePipeline stages
- Final artifacts can be uploaded to a different bucket. This can be achieved with a parallel stage to the final stage of the pipeline

## Manual Approval

- It is an action group which requires a manual approval for somebody
- We can send a notification to a SNS topic in case of a manual approval

## Stage Actions

- Stage actions can be parallel and sequential
- Parallel actions are part of the same action group while sequential changes are port of different action groups
- Pipelines defined as JSON:
    - Stages can be defined with the `runOrder` value
    - `runOrder` is an integer, stage group with lesser value are executed first
    - Parallel actions have the same `runOrder`

## CodePipeline Use Cases

- Reference: [https://docs.aws.amazon.com/codepipeline/latest/userguide/best-practices.html](https://docs.aws.amazon.com/codepipeline/latest/userguide/best-practices.html)
- Action providers:
    - Approvals:
        - Manual Approval
    - Build:
        - CodeBuild
        - AWS Jenkins
    - Deploy:
        - CloudFormation
        - CodeDeploy
        - Elastic Beanstalk
        - Service Catalog
        - ECS, ECS (Blue/Green)
        - S3
        - Lambda
    - Sources:
        - CodeCommit
        - ECR
        - S3
        - Github
    - Test:
        - CodeBuild
        - AWS Device Farm
        - AWS Jenkins
        - Other external services

## CodePipeline Integration with AWS Lambda

- Reference: [https://docs.aws.amazon.com/codepipeline/latest/userguide/actions-invoke-lambda-function.html](https://docs.aws.amazon.com/codepipeline/latest/userguide/actions-invoke-lambda-function.html)

## Deploy CloudFormation Templates

- CodePipeline can deploy CloudFormation templates as well
- CodePipeline actions for CloudFormation:
    - Create or update stack
    - Delete stack
    - Replace a failed stack
    - Create or replace change set
    - Execute change set
- Capabilities: used to allow whether we want to allow CloudFormation to create IAM resources on our behalf. Capability options:
    - `CAPABILITY_IAM`
    - `CAPABILITY_NAMED_IAM`
    - `CAPABILITY_AUTO_EXPAND`: used for nested stacks
- Create CodePipeline with CloudFormation: [https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-cloudformation.html](https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-cloudformation.html)
- CodePipeline-Nested-CFN: [https://github.com/aws-samples/codepipeline-nested-cfn](https://github.com/aws-samples/codepipeline-nested-cfn)