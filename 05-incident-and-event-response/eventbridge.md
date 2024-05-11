# Amazon EventBridge (formerly known as CloudWatch Events)

- With EventBridge we can:
    - Schedule jobs: Cron Jobs (example trigger a Lambda each hour)
    - React to Event Patterns: we create event rules to react to a service doing something
- Source + Rule => Target should change in some ways

## EventBridge Rules

- Sources that can trigger events:
    - EC2 instance state changes
    - CodeBuild events (ex. build failed)
    - S3 Events
    - Trusted Advisor
    - CloudTrail (any API call)
    - Scheduled Events/Cron
- Filters:
    - Filter events based on rules
    - Filters are optional
- Destinations (actions to take in case of an event):
    - Trigger Lambda function
    - Launch an AWS Batch Job
    - Start ECS Task
    - Start Step Function
    - Send a message to SNS, SQS, Kinesis Data Streams
    - Start an SSM Automation
    - EC2 API Call (Reboot, Stop, Terminate)
    - etc.
    - **Note: S3 bucket cannot be a destination!**

## Event Buses

- Default Event Bus: Amazon EventBridge
- Partner Event Bus: partners can send events to a Partner Event Bus
- Custom Event Bus: our own applications can send events to this bus
- Event buses can be access by other AWS accounts using Resource-based Policies
- We can archive events (all/filtered) sent to an event bus for indefinite or a set period
- We can replay archived events

## Schema Registry

- EventBridge can analyze the events in a bus and infer the schema
- The Schema Registry allows us to generate code for our application that will know in advance how the incoming data is structured
- Schemas can be versioned

## Resource-based Policies

- We can manage permissions for a specific event bus. Example: allow/deny events from another AWS account or AWS region
- Use cases: aggregate all events from our AWS Organization in a single AWS account/region

## Content Filtering

- EventBridge supports declarative content filtering using event patterns
- With content filtering, we can write complex event patterns that only match events under very specific conditions
- Filter types:
    - Prefix matching
    - Suffix matching
    - Anything-but matching
    - Numeric matching
    - IP Address matching
    - Exists matching
    - Equals-ignore-case matching
    - Matching using wildcards
- AWS documentation: [https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns-content-based-filtering.html](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns-content-based-filtering.html)

## Input Transformers

- We can customize the text from an event before EventBridge passes the information to the target of a rule
- EventBridge uses JSON Path syntax to do the transformation
- Example of a transformer:

```json
{
  "timestamp" : "$.time",
  "instance" : "$.detail.instance-id", 
  "state" : "$.detail.state",
  "resource" : "$.resources[0]"
}
```

- AWS documentation: [https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-transform-target-input.html](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-transform-target-input.html)

## EvenBridge Integration with CloudTrail API

- All events that are delivered via CloudTrail have `AWS API Call via CloudTrail` as the value for detail-type
- To create a rule that triggers on an action by an AWS service that does not emit events, we can base the rule on API calls made by that service