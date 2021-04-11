# CodeCommit

- CodeCommit is a version control system
- Offer private Git repositories
- There is no size limit on the repositories
- It is fully managed, highly available
- Code is stored in AWS cloud account => increased security and compliance

## Limit Pushes and Merges

- We can create a policy to deny operations on a specific branch for some users
- AWS documentation: [https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-conditional-branch.html](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-conditional-branch.html) 

## AWS CodeCommit Trigger for AWS Lambda Function

- Create CodeCommit trigger for AWS Lambda function: [https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-notify-lambda.html](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-notify-lambda.html)
