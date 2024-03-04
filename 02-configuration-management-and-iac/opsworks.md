# AWS OpsWorks

- OpsWorks provides 3 different services:
    - OpsWorks Stacks
    - OpsWorks for Chef Automate
    - OpsWorks for Puppet Enterprise

## OpsWorks Stacks

- OpsWorks Stacks is a configuration management service which helps deploy applications using Chef Cookbooks
- With OpsWorks we are managing infrastructure, configurations and the application in the same service
- A **stack** is a set of layers, instances and related AWS resources whose configuration we would want to manage together
- A **layer** is a blueprint for a set of EC2 instances. It specifies the instance's settings, associated resources, installed packages, profiles and security groups. We can add also recipes to lifecycle events such as set up, deploy, configure, discover resources
- **Auto healing**: if a stack is unhealthy, OpsWorks will re-provision that stack automatically
- Instance types:
    - **24/7 instances**: Instances which are running all the time
    - **Time-based instances**: OpsWorks will automatically start and stop time-based instances based on a specified schedule
    - **Load-based instances**: OpsWorks will automatically start and stop instances based on response to CPU, memory and application load changes across all the instances in a layer
- OpsWorks autoscaling is not as flexible as ASG. Instances has to be pre-created and they will be started based on time schedule or load
- **Apps** represent code stored in a repository that we want to install on application server instances
- **Deployments**: 
    - OpsWork will run a deploy or undeploy command on a given number of instances
    - Run Command: we can run a command on all the instances (execute recipes)

## OpsWorks Stacks Lifecycle Events

- Each layer has a set of 5 lifecycle events
- Each event has an associated set of recipes
- When an event occurs on a layer's instance, OpsWorks automatically runs the appropriate set of recipes
- Lifecycle events:
    - **Setup**: this event occurs after a started instance finished booting
    - **Configure**: this event occurs on all of the stack instances when:
        - An instance enters or leaves the online state
        - Associate/disassociate an Elastic IP from an instance
        - We attach/detach an ELB load balancer to a layer
    - **Deploy**: occurs when we run a deploy command, typically to deploy an application to a set of application server instances. Setup event includes the deploy event
    - **Undeploy**: occurs when we delete an app or run undeploy command to remove an app from a set of application server instances
    - **Shutdown**: occurs when we shut down an instance, but before the associated EC2 instance is actually terminated
- AWS OpsWorks stack configuration: [https://docs.aws.amazon.com/opsworks/latest/userguide/workingcookbook-json.html](https://docs.aws.amazon.com/opsworks/latest/userguide/workingcookbook-json.html)

## OpsWorks Integration with CloudWatch

- Auto Healing:
    - For Amazon EBS-backed instance:
        1. OpsWorks stops the EC2 instance and verifies that is sopped
        2. Starts the EC2 instance
- CloudWatch Events: we can create rules for reacting to OpsWork instance changes
