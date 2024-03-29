# AWS Lambda Functions

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
    - Docker

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
- Aliases enable Canary deployments by assigning weights to Lambda functions
- Aliases enable stable configuration of our event triggers/destinations
- Aliases have their own ARNs
- Aliases cannot reference other aliases!

## Environment Variables

- Lambda provides storage of environment variables
- It has integration with KMS for being able to encrypt secrets
- We can use SSM Parameter Manager as well for storing environment variables and secrets

## AWS Lambda Concurrency and Throttling

- Concurrency limit per account per region: by default up to 1000 concurrent execution
- In case we a higher amount of concurrent execution for our account, we can open a support ticket
- *Reserved concurrency*: 
    - Used to guarantee that a certain amount of concurrency is available for your function at any time
    - It is the maximum number of concurrent instances that we want to allocate to our function
    - When we dedicate reserved concurrency to a function, no other function can use that concurrency. This can impact the concurrency pool available to other functions
    - Each invocation over the concurrency limit will trigger a throttle
    - Throttle behavior:
        - Synchronous invocation: return ThrottleError - 429
        - Asynchronous invocation: retry automatically and then go to the DLQ
- *Provisioned concurrency*:
    - It is the number of pre-initialized execution environments allocated to a function
    - These execution environments are ready to respond immediately to incoming function requests (avoiding cold starts)
    - Configuring provisioned concurrency incurs additional charges to our AWS account
    - Application Auto Scaling can manage concurrency (based on schedule or target utilization)
- Concurrency and Asynchronous invocations:
    - If the function does not have enough concurrency available to process all events, additional requests are throttled
    - For throttling errors (429) and system errors (5xx) Lambda returns the event to the queue and attempts to run the function again for up to 6 hours 
    - The retry interval increases exponentially from 1 second after the first attempt to a maximum of 5 minutes

## File System Mounting

- Lambda functions can access EFS File systems if they are running in a VPC
- We have to configure Lambda to mount the EFS file system to a local directory during initialization. We must leverage EFS Access Points to accomplish the mounting
- Limitations: each new Lambda execution will have a new connection to the EFS Access Point, we should watch our for connection limits and connection burst limits

## AWS Lambda Limits - Per Region

- Execution:
    - Memory allocation: 128 MB - 10240 MB (1 MB increments)
    - Maximum execution time: 900 seconds (15 minutes)
    - Environment variables: 4 KB
    - Disk capacity in the function container (`/tmp`): Between 512 MB and 10240 MB (1-MB increments)
    - Concurrent executions: 1000 per account per region (can be increased after a request)
- Deployment:
    - Lambda function deployment size (compressed.zip): 50 MB
    - Unzipped deployment size: 250 MB
    - Container image: 10 GB

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