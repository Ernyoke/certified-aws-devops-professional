# AWS Network Firewall

## AWS Network Protection

- To protect a network (VPC) in AWS, we can use:
    - Network Access Control Lists (NACLs)
    - Security Groups
    - AWS WAF
    - AWS Shield and Shield Advanced
    - AWS Network Firewall
    - AWS Firewall Manager to manage all the resources from above

## Network Firewall Intro

- Used to protect the entire VPC
- Offers protection from Layer 3 to Layer 7
- Can inspect traffic in any direction:
    - VPC to VPC traffic
    - Outbound to internet
    - Inbound from the internet
    - To/from Direct Connect and Site-to-Site VPN
- Internally the AWS Network Firewall uses the AWS Gateway Load Balancer
- Rules can be centrally managed cross-account by AWS Firewall Manager to apply to many VPCs

## Fine Grained Control

- Supports thousands of rules at the VPC level
- Filtering can be done by:
    - IP and port
    - Protocol
    - Stateful domain list rule groups
    - General pattern matching using regex
- Traffic filtering: Allow, drop or alert for the traffic that matches one of the rules the we set up
- Active flow inspection: protects against network threats with intrusion-prevention capabilities (like Gateway Load Balancer, but all managed by AWS)
- All the rule matches can be sent to Amazon S3, CloudWatch Logs or Kinesis Data Firehose