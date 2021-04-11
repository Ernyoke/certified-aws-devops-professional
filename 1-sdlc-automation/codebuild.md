# CodeBuild

- Fully managed build service
- Alternative to other build tools such as Jenkins
- Continuous scaling (no servers to manage or provision - no build queue)
- We pay for the time it takes to complete a build
- Leverages Docker under the hood for reproducible builds
- It offers the possibility to extend capabilities of leveraging our own base Docker images for builds
- It is secure: integrates with KMS for encryption of build artifacts, IAM for build permissions and VPC for network security, CloudTrail for API calls logging

## CodeBuild Process Overview

- Source code is taken from GitHub, CodeCommit, CodePipeline, etc.
- Build instructions can be defined in code  (buildspec.yml)
- Output logs can be sent to Amazon S3 and AWS CloudWatch Logs
- It offers metrics to monitor CodeBuild statistics
- It uses CloudWatch Events to detect failed builds and trigger notifications
- Uses CloudWatch Alarms to notify if we need threshold for failures

## buildspec.yml - Deep Dive

- Example: [buildspec.yml](buildspec.yml)
- Reference [https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html#build-spec-ref-example](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html#build-spec-ref-example)
- Phases:
    - `install`
        - We can specify the runtime image here, example: `nodejs: 10`, `java: corretto8`
        - We can have multiple runtime images
    - `pre_build`
    - `build`
    - `post_build`
- Commands:
    - Each phase can have one or more commands to execute
    - Each phase can have a `finally` block: the commands from this block is executed wether or not the actual commands from the `commands` block succeeded
- Artifacts: 
    - Files and folder which are kept after the build finished. These are essentially the build result or build output. Example: `.jar` packages
    - Artifacts can be uploaded to S3 to be used later
- Cache:
    - This are intermediary files or dependencies used for build
    - They can be retained in order to speed up the build. Example: maven dependencies can be downloaded once and cached for the next build
