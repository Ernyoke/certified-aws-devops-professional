# AWS CodeBuild

- Fully managed build service
- Alternative to other build tools such as Jenkins
- Continuous scaling (no servers to manage or provision - no build queue)
- We pay for the time it takes to complete a build
- Leverages Docker under the hood for reproducible builds
- It offers the possibility to extend capabilities of leveraging our own base Docker images for builds
- It is secure: integrates with KMS for encryption of build artifacts, IAM for build permissions and VPC for network security, CloudTrail for API calls logging
- Supported environments:
    - Java, Android
    - Ruby
    - Python
    - Golang
    - NodeJS
    - PHP
    - Any other with extending a Docker image

## CodeBuild Process Overview

- Source code is taken from GitHub, CodeCommit, CodePipeline, etc.
- Build instructions can be defined in code (`buildspec.yml`). This file should live in the root folder of the repository
- Output logs can be sent to Amazon S3 and AWS CloudWatch Logs
- Build files can be cached in S3 (optional)
- It offers metrics to monitor CodeBuild statistics
- It uses CloudWatch Events to detect failed builds and trigger notifications
- Uses CloudWatch Alarms to notify if we need threshold for failures
- Artifacts are saved in S3

## `buildspec.yml` - Deep Dive

- Must be at the root of our code repository
- Can contain `env` - environment variables (for more details, see environment variables bellow). The type of these can be:
    - `variable`: plain text variables
    - `parameter-store`: variables from SSM Parameter Store
    - `secrets-manager`: variables from AWS Secrets Manager
- Example: [buildspec.yml](buildspec.yml)
- Reference [https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html#build-spec-ref-example](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html#build-spec-ref-example)
- Phases:
    - `install`
        - We can specify the runtime image here, example: `nodejs: 18`, `java: corretto17`
        - We can have multiple runtime images
    - `pre_build`
        - Execute commands before build
    - `build`
    - `post_build`
        - Execute commands after build, for example zip output    
- Commands:
    - Each phase can have one or more commands to execute
    - Each phase can have a `finally` block: the commands from this block is executed wether or not the actual commands from the `commands` block succeeded
- Artifacts: 
    - Files and folder which are kept after the build finished. These are essentially the build result or build output. Example: `.jar` packages
    - Artifacts can be uploaded to S3 to be used later
- Cache:
    - This are intermediary files or dependencies used for build
    - They can be retained in order to speed up the build. Example: maven dependencies can be downloaded once and cached for the next build

## Local Build

- It is possible to run CodeBuild locally to troubleshoot issues
- We have to have Docker installed for this
- We can leverage the CodeBuild Agent after that to reproduce a build locally

## Run Builds Inside VPC

- By default CodeBuild containers are launched outside of our VPCs
- We can specify a VPC configuration (VPC ID, subnet IDs, Security Group IDs) which can be used by CodeBuild to have access to resources inside a VPC

## Docker, ECR and buildspec.yml

- Reference [https://docs.aws.amazon.com/codebuild/latest/userguide/sample-docker.html](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-docker.html)
- buildspec.yml file:

```
version: 0.2

phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com
  build:
    commands:
      - echo Build started on `date`
      - echo Building the Docker image...          
      - docker build -t $IMAGE_REPO_NAME:$IMAGE_TAG .
      - docker tag $IMAGE_REPO_NAME:$IMAGE_TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG      
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker image...
      - docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG
```

- Service roles needs an IAM permission to be able to log into ECR

## CodeBuild Environment Variables

- Reference [https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-env-vars.html](https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-env-vars.html)
- A lot of environment variables are provided by CodeBuild, examples:
    - `AWS_DEFAULT_REGION, AWS_REGION`: the region where the build is running
    - `CODEBUILD_BUILD_ID`: CodeBuild ID of the build
    - `CODEBUILD_BUILD_NUMBER`: current build number
    - `HOME`: always set to `/root`
- Custom Environment Variables:
    - Static - defined at build time (we can override them using `start-build` API call)
    - Dynamic - environment variables from SSM Parameter Store or Secrets Manager
- To print all the env. variables, we can use `printenv`
- We can also provide our own environment variables, these may override the existing ones
- CodeBuild integrates with SSM Parameter Store for being able to store secrets

## CodeBuild Security

- CodeBuilder Service Role: allows CodeBuild to access AWS resources on our behalf
- Use cases for service role:
    - Download code from CodeCommit
    - Fetch parameters from SSM, Secrets Manager
    - Upload artifacts to S3
    - Store logs in CloudWatch
- CodeBuild provides in-transit and at-rest data encryption for cache, logs, etc.
- Build output artifacts are encrypted (requires KMS access)

## Artifacts and S3

- Build artifacts can be uploaded into S3
- We can specify where to store artifact or have no artifacts (in case of testing or if we push to ECR)
- Artifacts are encrypted by default. Also S3 can offer encryption by default

## CloudWatch Integration

- **CloudWatch Logs**: any time a build is run, a new log stream is created for the build ID
- **CloudWatch Metrics**: can give information about how many builds are successful, failed and the total number of build. Can also give information about the average duration of a build
- **CloudWatch Events**:
    - Schedule CodeBuild time to time: we can create a scheduled event to start a build for a CodeBuild project
    - React to events from CodeBuild, example invoke a Lambda function

## Validate CodeCommit and Pull Requests

- Used to build and test pull requests before merging it to main branch
- Used to ensure high level of code quality and avoid code conflicts
- Reference: [https://aws.amazon.com/blogs/devops/validating-aws-codecommit-pull-requests-with-aws-codebuild-and-aws-lambda/](https://aws.amazon.com/blogs/devops/validating-aws-codecommit-pull-requests-with-aws-codebuild-and-aws-lambda/)

## Build Badges

- AWS CodeBuild supports the use of build badges, which provide an embeddable, dynamically generated image (badge) that displays the status of the latest build for a project
- Supported for CodeCommit, Github and Bitbucket
- Badges are available at the branch level
- Badge statuses:
    - PASSING
    - FAILING
    - IN_PROGRESS
    - UNKNOWN (the project has not yet run a build for the given branch/the build badge feature might have been disabled)

## CodeBuild Test Reports

- Visual view of passing/failing tests
- Tests can be: unit tests, configuration tests, functional tests, etc.
- We can create test cases with any framework which is able to create a report that is understood by CodeBuild. Example formats: JUnit XML, NUnit XML, Cucumber JSON, TestNG XML, Visual Studio TRX, etc.
- To create a test report we have to add a `Report Group` in `buildpsec.yml` file with information about tests