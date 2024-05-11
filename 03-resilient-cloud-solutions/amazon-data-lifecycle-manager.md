# Amazon Data Lifecycle Manager

- We can use Amazon Data Lifecycle Manager to automate the creation, retention, and deletion of EBS snapshots and EBS-backed AMIs
- When we automate snapshot and AMI management, it helps us to:
    - Protect valuable data by enforcing a regular backup schedule
    - Create standardized AMIs that can be refreshed at regular intervals
    - Retain backups as required by auditors or internal compliance
    - Reduce storage costs by deleting outdated backups.
    - Create disaster recovery backup policies that back up data to isolated Regions or accounts

## Policies

- With Amazon Data Lifecycle Manager, we create policies to define your backup creation and retention requirements
- These policies typically specify the following:
    - Policy type — Defines the type of backup resources that the policy manages (snapshots or EBS-backed AMIs)
    - Target resources — Defines the type of resources that are targeted by the policy (instances or EBS volumes)
    - Creation frequency — Defines how often the policy runs and creates snapshots or AMIs
    - Retention threshold — Defines how long the policy retains snapshots or AMIs after creation
    - Additional actions — Defines additional actions that the policy should perform, such as cross-Region copying, archiving, or resource tagging
- Default policies: back up all volumes and instances in a Region that do not have recent backups
- Custom policies: target specific resources based on their assigned tags and support advanced features, such as fast snapshot restore, snapshot archiving, cross-account copying, and pre and post scripts