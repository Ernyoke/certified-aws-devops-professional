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

## Notifications

- We can create Notification Roles for our repositories
- Events that can trigger a notification:
    - Comments added to commits or pull requests
    - Approvals
    - Pull requests: source updated, created, status changed, merges
    - Branches and tags: created, deleted, updated
- Targets can be:
    - SNS topic
    - AWS Chatbot (Slack)

## Triggers

- We can create triggers for events happening in our repository
- These events can be:
    - Push to existing branch
    - Create branch or tag
    - Delete branch or tag
- Targets for triggers can be SNS or Lambda
- Example how to create CodeCommit trigger for AWS Lambda function: [https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-notify-lambda.html](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-notify-lambda.html)


## Monitoring With EventBridge

- We can monitor CodeCommit events in EventBride near real-time
- We can monitor events such as: `pullRequestCreated`, `pullRequestStatusChanged`, `referenceCreated`, `commentOnCommitCreated`, etc.

## Migrate a Git Repository to CodeCommit

- We can migrate a project hosted on another place (GitHub, GitLab) to CodeCommit
- We have to create a new repository in CodeCommit
- We then have to set the refs for our existing cloned repository to point to CodeCommit repository
- We push the repository

## Branch Security - Limit Pushes and Merges

- By default, a new user who has push permissions to CodeCommit repository can contribute to any branch
- We can create an IAM policy to deny operations on a specific branch for some users
- AWS documentation: [https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-conditional-branch.html](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-conditional-branch.html)
- **Resource Policies are not yet supported for CodeCommit repositories!**

## Pull Request Approval Rules

- Help ensure the quality for our code by requiring certain user(s) to approve a PR before merge
- We can specify a pool of users we can approve and number of user who must approve a PR before being merged. Example: we can have a pool of 5 users from which at least 2 must approve a PR
- We can use IAM principal ARN (IAM user, federated users, IAM roles, IAM Groups) for approvals
- Approval Rule Template: automatically apply Approval Rules to PRs