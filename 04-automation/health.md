# Health

- In order to ensure services are healthy in AWS we have the following services:
    - **Service Health Dashboard**: [status.aws.amazon.com](status.aws.amazon.com)
        - It is global view reflecting the health status of each region
        - We can get information about status history for services
        - Offers an RSS feed to subscribe to global events
    - **Personal Health Dashboard**:
        - Offers issues relating to services used our personal accounts
        - It is a dashboard for open, scheduled and other notifications

## Personal Health Dashboard

- We can set up notifications for personal dashboard using CloudWatch events
- We can track specific health events for specific services

## AWS Health AWS_RISK_CREDENTIALS_EXPOSED

- Automation about getting notifications if credentials are exposed in some monitored Git repositories
- GitHub repository: [https://github.com/aws/aws-health-tools/tree/master/automated-actions/AWS_RISK_CREDENTIALS_EXPOSED](https://github.com/aws/aws-health-tools/tree/master/automated-actions/AWS_RISK_CREDENTIALS_EXPOSED) 