# Amazon Inspector

- Enables to analyze the behavior of AWS resources
- Helps identify potential security issues
- Requires installation of Inspector agent to EC2 instances
- Inspector provides 2 types of setup:
    - Network Assessment: 
        - Does not require the agent to be installed
        - If the agent is installed, the assessment also finds processes reachable on ports
    - Host Assessment
        - Requires the agent to be installed
        - It will run on instances and will analyze them from inside
- Inspector provides a list of predefined packages (rules), users can not create their own
- Packages:
    - Network reachability
    - Security best practices
    - CIS OS security configuration benchmark
    - Common vulnerabilities and exposures
- Duration of inspections can be between 1 and 24 hours

## Automation

- Amazon Inspector can be target of en event in CloudWatch
- Scheduled inspection: can be activated from Inspector. It will create a rule in CloudWatch in order to regularly trigger inspections
- Assessment templates can also automatically send messages to an SNS topic. This can be activated from the Inspector using a template settings
- Notifications from Inspector can be done only using the send message to SNS topic functionality