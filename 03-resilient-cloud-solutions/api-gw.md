# API Gateway

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