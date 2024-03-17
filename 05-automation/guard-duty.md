# GuardDuty

- Intelligent Threat discovery to protect AWS accounts
- Uses machine learning algorithms, anomaly detection, 3rd party data
- One click to enable, has 30 days trial, no need to install any software
- Input data includes:
    - CloudTrail Logs: unusual API calls, unauthorized deployments
    - VPC Flow Logs: unusual internal traffic, unusual IP addresses
    - DNS Logs: compromised EC2 instances sending encoded data within DNS queries
- Notifies uses in case of findings
- Integrates with AWS Lambda