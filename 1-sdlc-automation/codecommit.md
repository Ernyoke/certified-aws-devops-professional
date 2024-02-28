# AWS CodeCommit

- AWS CodeCommit is a version control system
- Offers private Git repositories
- There is no size limit on the repositories
- It is fully managed, highly available
- Code is stored in AWS cloud account => increased security and compliance
- Security: repositories are encrypted, we have access control using IAM
- Integrates with Jenkins, AWS CodeBuild and other CI tools

## Security

- Interactions are done using Git (standard)
- Authentication:
    - SSH Keys - AWS Users can configure SSH keys in their IAM Console
    - HTTPS - with AWS CLI Credential helper or Git Credentials for IAM user
- Authorization:
    - IAM policies are used to manage users/roles permissions to repositories
- Encryption:
    - Repositories are automatically encrypted at rest using AWS KMS
    - Encryption in transit: we can use only HTTPS or SSH - both are secure
- Cross-account Access
    - We should NOT share SSH keys or AWS credentials
    - We should use an IAM Role in our AWS account and use AWS STS (AssumeRole API)

## Limit Pushes and Merges

- We can create a policy to deny operations on a specific branch for some users
- AWS documentation: [https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-conditional-branch.html](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-conditional-branch.html) 

## AWS CodeCommit Trigger for AWS Lambda Function

- Create CodeCommit trigger for AWS Lambda function: [https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-notify-lambda.html](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-notify-lambda.html)
