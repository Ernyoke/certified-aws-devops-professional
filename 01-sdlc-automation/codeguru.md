# Amazon CodeGuru

- It is an ML-powered service for automated code reviews and application performance recommendations
- Provides two functionalities:
    - *CodeGuru Reviewer*: automated code reviews for static code analysis(development)
        - Can identify critical issues, security vulnerabilities and hard-to-find bugs
        - Uses ML and automated reasoning
        - Contains hard-learned lessons across millions of code reviews on open-source and Amazon repositories
        - Supports Java (and other JVM languages such as Scala and Kotlin) and Python
        - Integrates with GitHub, BitBucket and CodeCommit
    - *CodeGuru Profiler*: visibility/recommendations about application performance during runtime(production)
        - Helps understand the runtime behavior of our application
        - Example: identify if our application is consuming excessive CPU capacity on a logging routine
        - Features:
            - Identify and remove code inefficiencies
            - Improve application performance
            - Decrease compute costs
            - Provides heap summary
            - Anomaly detection
        - Supports applications running on AWS or on on-premises
        - Adds minimal overhead to an application

## Extras

- CodeReview Reviewer Secrets Detector:
    - Uses ML to identify hardcoded secrets embedded in code (passwords, API keys, credentials, SSH keys, etc.)
    - Besides scanning code, it scans configuration and documentation files
    - Suggests remediation to automatically protect secrets with Secrets Manager
- CodeGuru Profiler:
    - Integrates with Lambda functions using a decorator (example: `@with_lambda_profiler` in Python)
    - Requires the `codeguru_profiler_agent` dependency, it can be added with a Lambda Layer
    - Profiling can be enabled in the function configuration, no need to use the decorator in this case