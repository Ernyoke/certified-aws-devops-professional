# EC2 Compliance

## EC2 Instance Migration between AZs

- We can use an AMI to migrate the instance: create an AMI in the original AZ -> restore the AMI in the new AZ

## Cross-Account AMI Sharing

- We can share an AMI with another AWS account
- Sharing an AMI does not affect the ownership of the AMI
- We can share AMIs that have unencrypted volumes and volumes that are encrypted with a customer managed key
- If we share an AMI with encrypted volumes, we must share also any customer managed keys to be able to decrypt the volume

## Cross-Account AMI Copy

- If we copy an AMI that has been shared, we will be the owner of the new AMI in our account
- THe owner of the source AMI must grant read permissions for the storage that backs the AMI (EBS Snapshot)

## EC2 Instance Compliance

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

