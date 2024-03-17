# AWS AppConfig

- Allows us to externalize application configuration
- Provides validation for these dynamic configurations
- With AppConfig we can decouple application configuration from our application
- We can define feature flags for our applications, we externalize application allow/block IP listings, etc.
- We can use AppConfig with apps on EC2 instances or with AWS Lambda, ECS, EKS, etc.
- We can gradually deploy the configuration changes and rollback if issues occur
- Configurations can be validated with:
    - JSON Schema (syntactic check)
    - Lambda Function (semantic check)