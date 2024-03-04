# Trusted Advisor

- No need to install anything - high level AWS account assessment
- It does analyze an AWS account and provides recommendations regarding:
    - Const optimization
    - Performance
    - Security
    - Fault tolerance
    - Service limits
- It has 2 tiers:
    - Free tier
    - Payed tier when buying support plan
- Core checks and recommendations are enabled for all customers
    - We can enable weekly email notifications from the console
- Full Trusted Advices: available for Business and Enterprise support plans
    - Provides the ability to set CloudWatch alarms when limits are reached


## Trusted Advisor Automation

- We can use CloudWatch Events to detect and react to changes it the status of Trusted Advisor checks
- CloudWatch can invoke one or more target when a check status changes. Targets may be:
    - AWS Lambda
    - Kinesis
    - SQS
    - CloudWatch Alarm actions
    - SNS topics
- Examples of automation: [https://github.com/aws/Trusted-Advisor-Tools](https://github.com/aws/Trusted-Advisor-Tools)
- Trusted Advisor has integration with CloudWatch Alarms in order to monitor service limits (for business and enterprise support plans)
- Trusted Advisor refresh:
    - Can be done manually every 5 minutes
    - Can be done programmatically:
        - `refresh-trusted-advisor-check`
        - `describe-trusted-advisor-check-result`, `...-statuses`, `...-summaries`