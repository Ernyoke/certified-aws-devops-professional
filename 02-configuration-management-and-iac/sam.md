# AWS SAM - Serverless Application Model

- It is a framework for developing and deploying serverless applications
- All the configurations for SAM are stored in YAML code
- SAML cli generates complex CloudFormation templates from SAM YAML code
- Supports anything from CloudFormation: Outputs, Mappings, Parameters, Resources, etc.
- SAM can be used with CodeDeploy to deploy Lambda functions
- SAM can help use to run Lambda, API Gateway and DynamoDB locally

## Recipe

- SAM YAML template contains the following headers:
    - `Transform: 'AWS::Serverless-2016-10-31`
    - The meaning of this is that CloudFormation knows how to transform this YAML template into CloudFormation template
- Other resources:
    - `AWS::Serverless::Function`
    - `AWS::Serverless::Api` - API Gateway
    - `AWS::Serverless::SimpleTable` - DynamoDB table
- Package and deploy SAM applications:
    - `aws cloudformation package` / `sam package`
    - `aws cloudformation deploy` / `sam deploy`

## SAM CLI

- To run a SAM application locally we can use the SAM cli
- It helps us locally build and debug serverless applications
- SAM has support for IDEs: VSCOde, JetBrains Intellij/PyCharm, AWS Cloud9, etc.
- AWS Toolkit: IDE plugins which allows us to build, test, debug and deploy Lambda functions using AWS SAM

## SAM CLI Commands

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

## SAM with CodeDeploy

- SAM framework natively uses CodeDeploy to update/deploy Lambda functions
- CodeDeploy will leverage the Traffic Shifting feature using Lambda aliases
- Pre and Post traffic hooks can be defined to validate deployment
- Automated rollbacks are supported with CloudWatch Alarms
- SAM with CodeDeploy YAML configuration:
    - `AutoPublishAlias`:
        - Detects when new code is being deployed
        - Creates and publishes an updated version of the function with the latest code
        - Points the alias to the updated version of the Lambda function
    - `DeploymentPreference`: `Canary`, `Linear`, `AllAtOnce`
    - `Alarms`:
        - Can trigger a rollback
    - `Hooks`:
        - Pre and post traffic shifting Lambda functions used to test the deployment