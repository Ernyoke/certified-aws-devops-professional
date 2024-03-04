# Serverless - AWS Lambda Functions, Step Functions and API Gateway

## Serverless Introduction

- Serverless is a new paradigm in which the developers do not have to manage servers anymore
- Initially Serverless == FaaS (Function as a Service), but now it can mean a lot more
- Serverless was pioneered by AWS Lambda but now the concept also includes anything that is not managed: databases, messaging, storage, etc.
- Serverless does not meant there are no servers, it means that we are not responsible for managing the underlying infrastructure

## AWS Lambda Introduction

- AWS Lambda Functions are virtual functions, which means there are no servers to manage
- There are limited to time - they require short execution times
- They run on-demand - we are only billed when the function is running
- Scaling is automated

## Benefits of Lambda

- Easy pricing
    - Pay per request and compute time
    - Generous free tier: 1 million AWS requests for free and 400K GBs compute time
- Lambda is integrated with the AWS suite of services
- It supports a lot of programming languages
- Easy monitoring through AWS CloudWatch
- Easy to get more resources per functions (up to 3GB or RAM)
- Increasing the RAm will also increase the CPU and network
- Supported languages:
    - NodeJS (JavaScript)
    - Python
    - Java
    - C# (.NET Core and PowerShell)
    - Golang
    - Ruby
    - Custom Runtime API (community supported, example Rust)
- **Docker does not run on AWS Lambda!**

## AWS Lambda Integrations

- Main ones:
    - API Gateway: allows to create an external API which invokes the Lambda function
    - Application Load Balancer: similar to API gateway, API gateway may give many for API functionalities (example: authentication)
    - CloudWatch Events and EventBridge: we can react to events happening in the cloud. Provides solution for having a scheduled Lambda function
    - CloudWatch Logs: create alerts monitoring the logs
    - CodeCommit, CodeDeploy
    - DynamoDB Streams: react to events happening in DynamoDB tables
    - Kinesis
    - S3 Events: async invocation, react changes happening in S3 buckets
    - CloudFront
    - SNS and SQS
    - AWS Cognito

## AWS Lambda Limits - Per Region

- Execution:
    - Memory allocation: 128 MB - 3008 MB (64 MB increments)
    - Maximum execution time: 900 seconds (15 minutes)
    - Environment variables: 4 KB
    - Disk capacity in the function container (`/tmp`): 512 MB
    - Concurrent executions: 1000 per account (can be increased after a request)
- Deployment:
    - Lambda function deployment size (compressed.zip): 50 MB
    - Uncrompressed deployment size: 250 MB
    - We can use `/tmp` directory to load other files at the startup

## AWS Lambda@Edge

- Used for running global Lambda functions alongside edge locations (for CDN for example)
- Can be used for:
    - To change CLoudFront requests and responses:
        - After CloudFront receives a request from a  viewer (viewer request)
        - Before CloudFront receives the request from the origin (origin request)
        - After CloudFront receives the response from the origin (origin response)
        - Before CloudFront forwards the response to the viewer (viewer response)
    - Customize the CDN content
- Use cases:
    - Website security and privacy
    - Dynamic Web Application at the Edge
    - Search Engine Optimization (SEO)
    - Intelligent Route across origins and data centers
    - Bot mitigation at the Edge
    - Real-time image transformation
    - A/B testing
    - User authentication and authorization
    - User prioritization
    - User tracking and analytics

## Environment Variables

- Lambda provides storage of environment variables
- It has integration with KMS for being able to encrypt secrets
- We can use SSM Parameter Manager as well for storing environment variables and secrets

## AWS Lambda Versions

- When we work on a Lambda function, we work on version named **$LATEST**
- When we are ready to publish this function, we create a version (v1, v2, ...)
- Versions are immutable
- Versions have increasing numbers
- Versions get their own ARN (Amazon Resource Name)
- Version = code + configuration
- Each version of the Lambda function can be accessed

## AWS Lambda Aliases

- Aliases are pointers to Lambda function versions
- We can define multiple aliases adn have them point at different Lambda versions
- Aliases are mutable
- Aliases enable Blue/Green deployments by assigning weights to Lambda functions
- Aliases enable stable configuration of our event triggers/destinations
- Aliases have their own ARNs


## SAM - Serverless Application Model

1. Download a sample application

    ```
    sam init --runtime java11
    ```

2. Build the application

    ```
    sam build
    ```

3. Invoke function locally

    ```
    sam local invoke <function-name> -e event.json
    sam local start-api
    ```

4. Package the application

    ```
    sam package --output-template packaged.yaml --s3-bucket <bucket-name> --region us-east-2 --profile aws-devops
    ```

5. Deploy the application

    ```
    sam deploy --template-file packaged.yaml --capabilities CAPABILITY_IAM --stack-name <stack-name> --region us-east-2 --profile aws-devops
    ```

- SAM has builtin support for CodeDeploy
- SAM can do the following things when deploying:
    - Deploy the new version and automatically create aliases that point to the new version
    - Gradually shift customer traffic to the new version until we are satisfied that the function works as expected
    - We can define pre-traffic and post-traffic test functions to verify than the newly deployed code is configured correctly
    - SAM can roll back the deployment if CloudWatch alarms are triggered

## AWS Step Functions

- Step Functions allow to build visual workflows which are used to orchestrate Lambda Functions
- Workflow is represented as a JSON state machine
- Features:
    - Sequence
    - Parallel execution
    - Conditions
    - Timeouts
    - Error handling
- Can also integrate with EC2, ECS, on premise servers, API Gateway
- Maximum execution time is 1 year
- Possibility to implement human approval feature
- Use cases:
    - Order fulfillment
    - Data processing
    - Web applications
    - Any workflow
- When designing a Step Function we get an aspect of visualization (flow diagram)
- The execution can be visually represented on this diagram
- Any state can encounter errors:
    - State machine definition issues (example. no matching rules in choice state)
    - Task failures (example: an exception in a Lambda function)
    - Transient failures (example: network partition events)
- By default: when a state reports an error, the execution of the Step Functions fails entirely
- Failures can be retried:
    - Exponential back-off: *IntervalSeconds*, *MaxAttempts*, *BackoffRate*
    - Move on - Catch: *ErrorEquals*, *Next*
- Best practice: include data in the error message

## Standard vs Express Step Functions

- Standard Function
    - Maximum duration: 1 year
    - Exactly-one workflow execution
    - Execution start rate: 2000 per second
    - Price per state transition: more expensive in general
- Express Function 
    - Maximum duration: 5 minutes
    - At-least-once workflow execution
    - Execution start rate: 100_000 per second

## Step Function Use Cases

- Reference: [https://aws.amazon.com/step-functions/use-cases/](https://aws.amazon.com/step-functions/use-cases/)

## API Gateway

- Integration with AWS Lambda: no infrastructure to manage
- Support for WebSockets protocol
- Handles API versioning
- Handles multiple environments (dev, test, prod)
- Handles security (authentication and authorization)
- Ability to create API keys => request throttling
- Supports Swagger/OpenAPI import to quickly define APIs
- Transform and validate requests
- Ability to cache API responses
- Integration types:
    - Lambda functions
    - HTTP: expose HTTP end-points from the back-end
    - Other AWS services, examples: AWS Step Function workflow
    - Mock
    - VPC Link

## API Gateway Endpoint Types

- Edge-Optimized (default): for global clients
    - Requests will be routed through the CloudFront Edge locations
    - API Gateway will still live in one region where it was created
- Regional: for clients within the same region. Can be combined with CloudFront for control over caching strategies and distribution
- Private: can only be access within the VPC using VPC endpoint (ENI)

## API Gateway - Deployment Stages

- Making changes in the API Gateway does are not applied to the current stage until they are deployed
- In order to deploy we have to make a deployment
- Each stage has its own configuration parameters
- Stages can be rolled back as the history of the deployments is kept

## API Gateway - Stage Variables

- Stage variables are similar to environment variables
- We can use them to store often changing configuration values
- They can be used in:
    - Lambda function ARN
    - HTTP endpoint
    - Parameter mapping templates
- Use cases:
    - Configure HTTP endpoints our stages are talking to
    - Pass configuration parameters to AWS Lambda through mapping templates

## API Gateway - Canary Deployments

- There is a possibility to enable canary deployments for any stage in API Gateway
- We can choose the percentage of the traffic the canary channel receives

## API Gateway - Throttling

- API Gateway limits the steady-stage request rate to 10K per second by default
- This limit applies to all API from an account
- If request is throttled, API Gateway returns HTTP error `429 Too Many Requests`
- API Gateway Usage Plan:
    - Helps meter API usage
    - Helps throttle some or every method from a stage
- Usage plans can be associated with an API key

## Fronting Step Functions

- API Gateway can expose step functions
- We we expose a step function, we must select an Action for it (example: StartExecution)
- We also require a step execution role