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
- AWS Toolkits: IDE plugins which allows us to build, test, debug and deploy Lambda functions using AWS SAM

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

- SAM has builtin support for CodeDeploy
- SAM can do the following things when deploying:
    - Deploy the new version and automatically create aliases that point to the new version
    - Gradually shift customer traffic to the new version until we are satisfied that the function works as expected
    - We can define pre-traffic and post-traffic test functions to verify than the newly deployed code is configured correctly
    - SAM can roll back the deployment if CloudWatch alarms are triggered