# Amazon Inspector

- Allows us to run automated security assessments
- Helps identify potential security issues
- For EC2 instances:
    - Requires installation of AWS System Manager (SSM) agent on EC2 instances
    - Inspector will analyze against unintended network accessibility
    - It will analyze the running OS against known vulnerabilities
- For Container Images pushed to Amazon ECR
    - It will assess the images as they are pushed
- For Lambda Functions:
    - Inspector will identify vulnerabilities in function code and package dependencies
    - The assessment happens as the functions are deployed
- Reporting happens with AWS Security Hub, events are created for findings using Amazon EventBridge
- What does Amazon Inspector evaluate?
    - Package vulnerabilities (EC2, ECR and Lambda) using a database of CVE
    - Network reachability (EC2)
- Inspector provides a list of predefined packages (rules), users can not create their own
- Packages:
    - Network reachability
    - Security best practices
    - CIS OS security configuration benchmark
    - Common vulnerabilities and exposures
- Duration of inspections can be between 1 and 24 hours
- A risk score is associated with all vulnerabilities for prioritization

## Amazon Inspector for EC2 Instances

- EC2 instance setup for Inspector requires:
    - SSM Agent is installed and running
    - The instance should be managed by SSM: has an IAM Role attached to it or Default Host Management config is enabled

## Automation

- Amazon Inspector can be target of en event in EventBridge
- Scheduled inspection: can be activated from Inspector. It will create a rule in EventBridge in order to regularly trigger inspections
- Assessment templates can also automatically send messages to an SNS topic. This can be activated from the Inspector using a template settings
- Notifications from Inspector can be done only using the send message to SNS topic functionality