# AWS CodePipeline

- It is a continuous delivery tool and a visual workflow
- We can define sources: GitHub, CodeCommit, S3
- We can define build tools such as CodeBuild, Jenkins, etc.
- We use third party tools for testing
- We can deploy to BeanStalk, CloudFormation, ECS, S3, CodeDeploy
- We can invoke Lambda Functions or Step Functions
- It is made of stages:
    - Each stage can have sequential and/or parallel actions
    - Example of stages: Build, Test, Deploy, LoadTest
    - Stages can have manual approval steps

## CodePipeline Concepts

- **Pipeline**: is a workflow construct that describes how software changes go through a release process. Each pipeline is made up of a series of stages
- **Stage**:  is a logical unit we can use to isolate an environment and to limit the number of concurrent changes in that environment. Each stage contains actions that are performed on the application artifacts
- **Action**: is a set of operations performed on application code and configured so that the actions run in the pipeline at a specified point. Valid CodePipeline action types are *source*, *build*, *test*, *deploy*, *approval*, and *invoke*

## CodePipeline Artifacts

- Each pipeline stage can create artifacts
- Artifacts are stored in S3 and passed on to the next stage
- We can have a central S3 bucket for all the pipelines
- Artifacts are encrypted by default with an AWS managed key. We can create our own key in KSM if we want
- Artifacts are passed between CodePipeline stages
- Final artifacts can be uploaded to a different bucket. This can be achieved with a parallel stage to the final stage of the pipeline

## CodePipeline Troubleshooting

- For CodePipeline Pipeline/Action/Stage Execution State Changes we can use CloudWatch Events (EventBridge). Example:
    - We can create events for failed pipelines
    - We can create events for cancelled stages
- If CodePipeline fails a stage, the pipeline stops, and we cam get information in the console
- If the pipeline can perform an action, we should make sure the IAM Service Role attached does have enough IAM permissions
- AWS CloudTrail can be used to audit AWS API calls

## Stage Actions

- Stage actions can be parallel and sequential
- Parallel actions are part of the same action group while sequential changes are port of different action groups
- Pipelines defined as JSON:
    - Stages can be defined with the `runOrder` value
    - `runOrder` is an integer, stage group with lesser value are executed first
    - Parallel actions have the same `runOrder`

## Events/Webhooks/Polling

- Events: 
    - Preferred way to start a pipeline
    - Example: an event from EvenBridge starts the pipeline
    - Example: from GitHub we can use CodeStar Source Connection to trigger an event and start the pipeline
- Webhooks:
    - Older way of triggering the start of a pipeline
    - A script can use a HTTP webhook to start a pipeline
- Polling:
    - Regular checks from third party integration (such as Github)
    - No recommended, they are not as great as events

## CodePipeline Action Type Constraints for Artifacts

- Reference: [https://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html](https://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)

## Manual Approval Stage

- It is an action group which requires a manual approval from somebody
- We can send a notification to a SNS topic in case of a manual approval
- Action Type Constraint for manual approval:
    - Owner is AWS
    - Action is Manual

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
- Works with CloudFormation StackSets to deploy across multiple AWS accounts and AWS regions
- Actions modes:
    - Create or Replace a Change Set, Execute a Change Set
    - Create or Update a Stack, Delete a Stack, Replace a Failed Stack
- Template Parameter Overrides:
    - We can specify a JSON object to override parameter values during runtime
    - We can retrieve a parameter value from CodePipeline Input Artifact
    - All parameter names must be present in template
    - Override types:
        - Static: use template configuration file (recommended)
        - Dynamic: use parameter overrides
- Capabilities: used to allow whether we want to allow CloudFormation to create IAM resources on our behalf. Capability options:
    - `CAPABILITY_IAM`
    - `CAPABILITY_NAMED_IAM`
    - `CAPABILITY_AUTO_EXPAND`: used for nested stacks
- Create CodePipeline with CloudFormation: [https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-cloudformation.html](https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-cloudformation.html)
- CodePipeline-Nested-CFN: [https://github.com/aws-samples/codepipeline-nested-cfn](https://github.com/aws-samples/codepipeline-nested-cfn)

## Best Practices

- We can use parallel actions within a stage using `RunOrder`
- We can deploy to pre-prod before deploying to prod with a manual approval step
- Any kind of event in CodePipeline will be detected in EventBridge => we can intercept events, such as deployment failures
- Running code from CodePipeline: we can use the `Invoke` action
- `Invoke` action supports invoking Step Functions

## Multi Region Deployments

- Actions in our pipeline can be in different regions
- S3 Artifact Stores must be defined in each region where we have actions
- CodePipeline must have read/write access to these buckets
- CodePipeline handles the copying of input arguments from one AWS region to other when performing cross-region actions (example: deploying CloudFormation templates)