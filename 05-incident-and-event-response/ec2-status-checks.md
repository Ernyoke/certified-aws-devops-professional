# EC2 Status Checks

- They are automated checks to identify hardware and software issues
- There are 2 types of status checks:
    - **System Status Checks**:
        - Used to monitor problems with AWS systems (software/hardware) issues on the physical host
        - We can check Personal Health Dashboards for any scheduled critical maintenance by AWS to our instances' hosts
        - As a resolution we can stop and start the affected instances. The instance will be migrated to another host
    - **Instance Status Check**:
        - Monitor software/network configuration of our instances. For example monitor for invalid network configurations, exhausted memory, etc.
        - As a resolution we can reboot the instance or change the instance configuration

## CloudWatch Metrics and Recovery

- CloudWatch Metrics (1 minute interval):
    - `StatusCheckFailed_System`
    - `StatusCheckFailed_Instance`
    - `StatusCheckFailed` (for both)
- Recovery option 1: use a CloudWatch Alarm (preferred)
    - It will recover the instance with the same private/public IP, EIP, metadata and placement group
    - It can send a notification using SNS
- Recovery option 2: use an Auto Scaling Group
    - Set min/max/desired capacity to 1 to recover an instance
    - This wont keep the same private and EIP assigned to the instances