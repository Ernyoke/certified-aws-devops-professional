## AWS Step Functions

- Step Functions allow us to model our workflows as stat machines
- A workflow is represented as a JSON state machine
- Features:
    - Sequence
    - Parallel execution
    - Conditions
    - Timeouts
    - Error handling
- Can also integrate with EC2, ECS, on premise servers, API Gateway
- Maximum execution time is 1 year
- Possibility to implement human approval feature
- Use cases:
    - Order fulfillment
    - Data processing
    - Web applications
    - Any workflow
- When designing a Step Function we get an aspect of visualization (flow diagram)
- The execution can be visually represented on this diagram

## Task States

- States in the state machine which can do some work, for example:
    - Invoke an AWS service such as Lambda function, start an AWS Batch job, run an ECS task, insert an item into DynamoDB table, etc.
    - Run an Activity:
        - Activities run on an EC2 machine, ECS container, on-premises server
        - Activities poll the Step Functions for work
        - Activities send back the result to the Step Function
- State types:
    - Choice State: test for a condition to send to a branch
    - Fail or Succeed State: stop execution with failure or success
    - Pass State: simply pass its input to the output or inject some fixed data
    - Wait State: wait for a certain amount of time or until a specific date/time
    - Map State: dynamically iterate steps
    - Parallel State: begin parallel branches of execution
- Any state can encounter errors:
    - State machine definition issues (example. no matching rules in choice state)
    - Task failures (example: an exception in a Lambda function)
    - Transient failures (example: network partition events)
- By default: when a state reports an error, the execution of the Step Functions fails entirely
- Failures can be retried:
    - Exponential back-off: *IntervalSeconds*, *MaxAttempts*, *BackoffRate*
    - Move on - Catch: *ErrorEquals*, *Next*
- Best practice: include data in the error message

## Standard vs Express Step Functions

- Standard Function
    - Maximum duration: 1 year
    - Exactly-one workflow execution
    - Execution start rate: 2000 per second
    - Price per state transition: more expensive in general
- Express Function 
    - Maximum duration: 5 minutes
    - At-least-once workflow execution, an execution could run more than once
    - Execution start rate: 100_000 per second

## Step Function Use Cases

- Reference: [https://aws.amazon.com/step-functions/use-cases/](https://aws.amazon.com/step-functions/use-cases/)