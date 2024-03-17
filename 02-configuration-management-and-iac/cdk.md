# AWS CDK - Cloud Development Kit

- Allows us to define our cloud infrastructure using a familiar programming language such as JavaScript/TypeScript, Python, Java and .NET
- The CDK framework contains high level components called *constructs*
- The code is transpiled into a CloudFormation template (JSON/YAML)
- With CDK we can deploy infrastructure and application runtime code together
    - This is great fro Lambda functions
    - Or great for Docker containers which can be deploy to ECS/EKS

## CDK vs SAM

- SAM:
    - Serverless focused
    - We write our templates in JSON or YAML
    - Great for quickly getting started with Lambda
    - Leverages CloudFormation
- CDK:
    - Superset of CloudFormation, supports every AWS service
    - We write infra in a programming language we know (see above)
    - Leverages CloudFormation
- CDK + SAM:
    - We can use SAM CLI to locally test CDK apps
    - We must first run `cdk synth` -  this will generate CFN template which we can invoke with SAM cli
