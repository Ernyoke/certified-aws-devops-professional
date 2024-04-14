# AWS Health Dashboards

- In order to ensure services are healthy in AWS we have the following services:
    - **Service history**: [status.aws.amazon.com](status.aws.amazon.com)
        - Shows health information for all regions and all services
        - Shows historical information for each day
        - Offers an RSS feed to subscribe to global events
        - Previously called AWS Service Health Dashboard
    - **AWS Health Dashboard**:
        - Provides alerts and remediation guidance when AWS is experiencing events that may impact our account
        - Gives personalized view into the performance and availability of the AWS services underlying our AWS resources
        - The dashboard displays relevant and timely information to help us manage events in progress and provides proactive notifications to help us plan for scheduled activities
        - Can aggregate data from an entire AWS Organization
        - Previously called AWS Personal Health Dashboard (PHD)

## Health Event Notifications

- We can use EventBridge to react to changes for AWS Health events in our AWS account
- Example: receive email notifications when EC2 instances in our account are scheduled for updates
- These are possible for account events and public events (regional availability for a service)

## AWS Health AWS_RISK_CREDENTIALS_EXPOSED

- Automation about getting notifications if credentials are exposed in some monitored Git repositories
- GitHub repository: [https://github.com/aws/aws-health-tools/tree/master/automated-actions/AWS_RISK_CREDENTIALS_EXPOSED](https://github.com/aws/aws-health-tools/tree/master/automated-actions/AWS_RISK_CREDENTIALS_EXPOSED) 