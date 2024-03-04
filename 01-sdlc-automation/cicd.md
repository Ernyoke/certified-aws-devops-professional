# CICD - Continuous Integration/Continuous Delivery

## Continuous Integration

- Developers push the code to a code repository often
- A testing/build server checks the code as soon as it is pushed
- The developers gets feedback about the tests and checks the have passed/failed
- Find bugs early, fix bugs early
- Deliver faster as the code is tested
- Deploy often

## Continuous Delivery

- Ensure that the software can be released reliably whenever needed
- Ensure deployments happen often and are quick
- Deployments are fully automated by using tools such as:
    - CodeDeploy
    - Jenkins
    - Spinnaker
    - etc.

## Continuous Delivery vs Continuous Deployment

- Continuous Delivery:
    - Ability to deploy often using automation
    - May involve a manual stop to approve a deployment
    - The deployment itself is still automated a repeatable
- Continuous Deployment:
    - Full automation, every code change is deployed all the way to production
    - No manual intervention of approval

## Technology Stack for CICD in AWS

- Code repository:
    - CodeCommit
    - GitHub
- Build/Test:
    - CodeBuild
    - Jenkins CI
- Deploy:
    - CodeDeploy
- Provision:
    - Elastic Beanstalk
    - CloudFormation
- Orchestrate the steps above:
    - CodePipeline