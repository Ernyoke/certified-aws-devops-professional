# AWS Firewall Manager

- It is a service that can be used to manage all the firewall rules in all accounts from an AWS Organization
- We can set Security Policies, which are a common set of security rules, for example:
    - WAF rules
    - AWS Shield Advanced rules
    - Security Groups for EC2, ALB and resources that use ENIs in a VPC
    - AWS Network Firewall (VPC Level)
    - Amazon Route53 Resolver DNS Firewall
- Rules are applied to new resources as they are created (good for compliance) across all and future accounts in our organization

## WAF vs. Firewall Manager vs. Shield

- WAF, Shield and Firewall Manager are used together for comprehensive protection
- We define Web ACL rules in WAF
- For granular protection of our resources WAF alone is the correct choice
- If we want to use AWS WAF across multiple accounts, accelerate WAF configuration, automate the protection of new resources, we should use Firewall Manager with AWS WAF
- Shield Advanced adds additional features on top of AWS WAF such as dedicated support from the Shield Response Team (SRT) and advanced reporting
- If we are prone to frequent DDoS attacks, we should consider purchasing Shield Advanced

## Security Policies

- **Policy Type: AWS WAF**:
    - Enforce applying WebACLs to all ALBs in all accounts in an AWS Organization
    - We can have two options:
        - **Identify resources that don't comply, but don't auto remediate**: create the WebACL in each account without applying the WebACL to any resources
        - **Auto remediate any noncompliant resource**: automatically apply the WebACLs to existing resources
- **Policy Type: Shield Advanced**:
    - Enforce Shield Advanced protection in all accounts in an AWS Organization
    - We can have the option to view only compliance (to assess impact) or to auto remediate
- **Security Group Policy Type: Common Security Groups**:
    - Enforce applying SGs to all EC2 instances in all accounts in an AWS Organization
- **Security Group Policy Type: Auditing of Security Group Policy**:
    - Check and manage SG Rules in all accounts in an AWS Organization
- **Security Group Policy Type: Usage Audit Security Group Policy**:
    - Monitor unused and redundant SGs and optionally perform cleanup
- **Policy Type: Network Firewall**:
    - Centrally manage Network Firewall firewalls in all accounts in an AWS Organization
    - Modes:
        - Distributed: creates and maintains a firewall endpoint in each VPC
        - Centralized: creates and maintains a firewall endpoint in a centralized VPC
    - Import existing firewalls: import firewalls using Resource Sets
- **Policy Type: Route 53 Resolver Firewall**:
    - Manage associations between Resolver DNS Firewall Rule Groups and VPCs in all accounts in an AWS Organization