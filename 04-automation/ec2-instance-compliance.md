# EC2 Instance Compliance

- AWS Config:
    - Ensures instances have proper AWS configurations (example: not open SSH ports, etc.)
    - Offers audit and compliance over time
- Amazon Inspector:
    - Does security vulnerability scan from within the OS using an agent
    - Also does network scans outside of EC2 instances (agent is optional)
- Systems Manager:
    - Runs automation, patches, commands, inventory at scale
- Service Catalog:
    - Restrict how EC2 instances can be launched to minimize configurations
- Configuration Management:
    - Tools: 
        - SSM
        - Opsworks
        - EC2 User Data
    - They ensure EC2 instances have proper configuration files

