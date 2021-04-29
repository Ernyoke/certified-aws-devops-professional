# AWS Data Protection

- TLS for in transit encryption
- ACM to manage SSL/TLS certificates
- Load Balancers to assign certificates:
    - ALB and NLB provide SSL termination
    - Possible to have multiple SSL certificates per ALB
    - Optional SSL/TLS encryption between ALB and EC2 instances
- CloudFront with SSL
- All AWS services expose HTTP endpoints
- It is possible to send data through HTTP to S3 (we probably should not do this!)

## At Rest Encryption

- S3 encryption:
    - SSE-S3
    - SSE-KMS
    - SSE-C
    - Client side encryption
    - Possibility to enable default encryption
    - Possibility to enforce encryption through S3 bucket policies (`x-amz-server-side-encryption`)
    - Data in Glacier is encryption by default
- EBS, EFS, RDS, ElastiCache, DynamoDB encryption:
    - Usually uses either encryption key of our own
- Category of data:
    - PHI: protected health information
    - PII: personally-identifying information

## AWS Network Protection

- Direct Connect: private, direct connection between site and AWS
- Public internet: use a VPN
    - Site-to-Site VPN supports Internet Protocol security (IPSec) VPN connections (for linking on-premise to the cloud)
- Network ACL: stateless firewall at the VPC level
- WAF (Web Application Firewall): web security rules against exploits
- Security Groups: stateful firewall on the instance's underlying hypervisor
- System Firewalls: install own firewall on EC2 instances