# Elastic Beanstalk

***Elastic Beanstalk* is a developer centric view of deploying application on AWS.**
- A managed service
  - Instance configuration
  - OS is handled by Beanstalk
  - Deployment strategy is configurable but performed by Beanstalk
  - Application code configurable
- It will leverage all the AWS components that we have gone over thus far:
  - EC2
  - ASG
  - ELB
  - RDS
  - Etc..
- Elastic Beanstalk is free but we pay for the underlying instances
- Three architecture models:
  - Single instance deployment: good for developers
  - LB + ASG: great for production or staging web applications
  - ASG only: great for non-web apps in production
- Elastic Beanstalk has three different components:
  - Application
  - Application Version (Each deployment gets assigned a version)
  - Environment name (dev, staging, prod): free naming
- We deploy application versions to environments and can promote application versions to the next environment
- Rollback feature to previous application versions
- Full control over the lifecycle of environments
- Support for many platforms:
  - Go
  - Java
  - Python
  - Node.js
  - Ruby
  - Single Container Docker
  - Multi Container Docker
  - Pre-configure Docker
  - Write our own custom platforms (If the any of the above is not supported)

## Elastic Beanstalk Deployment modes

- **Single Instance deployment**: good for development, we get 1 ec2 instance in 1 ASG and 1 Elastic IP. DNS names maps straight ot the Elastic IP
- **High Availability with Load Balancer**: great for production, we have an ASG across multiple AZs
- Deployment updates:
  - **All at once**: we deploy all our applications in on go. Fastest, but instances have a downtime. No additional cost is applied while deploying
  - **Rolling**: update e few instances at a time, move onto next set of instances (bucket) if the first set was healthy. The application will run bellow capacity for a given period (bucket size). At some point the application will run both versions. No additional cost is encountered during deployment
  - **Rolling with additional batches**: similar deployment procedure as the **rolling** approach with the difference of having an additional batch started at the beginning of deployment with the newer version of the application. Afterwards, the older instanced are gradually moved to newer version. While deploying, both application version will be running at the same time. Minor additional cost may be encountered since the additional batch will be present until the deployment is finished.
  - **Immutable**: new version of the application is deployed to an entirely new ASG. If the new version passes the initial validation, the old ASG is terminated. Deployment will cause 0 downtime. The additional cost encountered while deploying is the highest compared to other deployment types.

### Blue / Green Deployment

- This is not a direct feature of Elastic Beanstalk
- Zero downtime and release facility
- Create a new staging environment and deploy our newest version there
- The new environment (green) can be validated independently and roll back if there's issues
- Route 53 can be setup using weighted policies to redirect a little bit of traffic to the staging environment
- Using the elastic beanstalk console, we can "swap URLs" when with the testing environment
- This is a manual feature, it's not directly embedded in EB

## Elastic Beanstalk Lifecycle Policy

- Elastic Beanstalk can store at most 1000 application versions
- When this limit is reached, we wont be able to deploy a new version
- In order to be able to deploy again, we have to remove older versions
- To phase out old versions, we can use a lifecycle policy
- This policy can be based on:
  - Time (remove versions which are older than...)
  - Space (remove older versions if we have more versions than...)
- Currently used versions are not deleted
- There is an option to not delete source bundles from S3, only from beanstalk interface

## Elastic Beanstalk Extensions

- A zip file containing our code must be deployed to Elastic Beanstalk
- All the parameters set in the UI can be configured with code using files
- Requirements:
  - in the .ebextensions/ directory in the root of source code
  - YAML / JSON format
  - .config extensions (example: logging.config)
  - Able to modify some default settings using: option_settings
  - Ability to add resources such as RDS, ElastiCache, DynamoDB, etc...
- Resources managed by .ebextensions get deleted if the environment goes away
- The .ebextensions folder goes to the root of our project

## Elastic Beanstalk Under the Hood

- Elastic Beanstalk uses CloudFormation under the hood
- We can take advantage of this by provisioning other resources from beanstalk
- We can place config files in the .ebextensions to provision basically anything we want

## Elastic Beanstalk Cloning and Migrations

- We can clone an environment with the exact same configurations
- Can be useful for deploying "test" versions for our applications
- All resources and configurations are preserved after cloning
- Settings can be changed for the new EB stack after cloning
- Migrate Load Balancer:
  - The LB type can not be changed after the EB environment was created
  - In order to be able to change the type of an LB, we need to do a migration:
      1. Create a new environment with the same configurations as the original except LB => **this means we can not do cloning !!!**
      2. Deploy our application into the new environment
      3. Shift the traffic from the old environment to the new one (this can be done with a CNAME swap or DNS update)
- Decouple RDS from the EB stack:
  - RDS database can be added to the EB stack, although it is not recommended for production, because the DB lifecycle is tied to the EB lifecycle
  - Steps to decouple RDS:
    1. Create a snapshot form the DB for safety
    2. Protect the RDS DB from deletion
    3. Create a new EB environment without RDS and point the application to the existing RDS instance
    4. Perform a CNAME swap
    5. Terminate the old EB stack (RDS wont be deleted because of the protection)
    6. Delete CloudFormation stack (it will be in DELETE_FAILED state)

## Elastic Beanstalk and Docker

- We can run docker container if we provide:
  - Dockerfile: EB will build an run the Docker container
  - Dockerrun.aws.json (v1): describe where the Docker image should be downloaded from (DockerHub, ECR, etc.)
- EB in single container mode wont use ECS!
- Multi Docker Container:
  - Will run multiple containers per EC2 instance
  - Will create:
    - ECS Cluster
    - EC2 instances in an ECS cluster
    - Load Balancer (high availability mode)
    - Task definition and execution
  - Requires a config of Dockerrun.aws.json (V2) in the root of the source code
    - Dockerrun.aws.json is used to generate the ECS task definition
    - Docker images should be pre-build and stored in ECR, DockerHub, etc.

## Elastic Beanstalk and HTTPS

- SSL certificate can be loaded from the console (EB console, load balancer configuration) or from the config .ebextensions/securelistener-alb.config
- SSL certificates can be provisioned using  ACM (AWS Certificate Manager) or CLI
- Must configure SG with allowing port 443
- Redirect HTTP to HTTPS:
  - Instances can be configured to redirect traffic
  - Or ALB can be configured with a rule as well
  - Health checks should ne be redirected from the ALB

## EB CLI

- To install run:

    ```
    pip install awsebcli --upgrade --user
    ```

- Initialize an Elastic Beanstalk project:

    ```
    eb init
    ```

- Initialize Elastic Beanstalk environments:

    ```
    eb create dev-env
    ```

- Open the application from a web browser:

    ```
    eb open
    ```

- Other useful CLI commands:
    - `eb status`: shows the health of the environment
    - `eb health (--refresh)` : displays the health of the application
    - `eb logs`
    - `eb deploy`:
    - `eb terminate`: terminate stack

## Saved Configurations

- Used to back up configurations as code and create new environments from them
- Back up current configurations from environment:

    ```
    eb config save env --cfg <initial-configuration>
    ```

- Set an environment variable on the selected EB environment:

    ```
    eb setenv PORT=8080
    ```

- Update existing saved configuration

    ```
    eb config put <config-name>
    ```

- Apply configuration to environment:

    ```
    eb config <environment-name> --cfg <config-name>
    ```

## .ebextensions for Configs

- Configurations for EB can be set in ab .ebextensions folder
- Every configuration file in that folder should have the extension of `.config`
- .ebextensions reference: [https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html)
- Configuration options for .ebextensions: [https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/command-options-general.html](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/command-options-general.html)

## Configuration Precedence

1. Settings applied directly to environment
2. Saved configurations
3. Configuration files (.ebextensions)
4. Default values

## .ebextensions for Resources

- We can add any kind of resources in our `.config` templates in the `.ebextensions` folder
- The format of a config file containing resources should be the same as a CloudFormation template
- **If the environment is deleted, every resource will be deleted with it!**

## .ebextensions Container Commands

- We can specify commands in the .ebextensions files
- There are 2 types of commands:
    - `commands`: the commands run before the application and web server are set up and the application version file is extracted
    - `container_commands`: 
        - Can be used to execute commands that affect our application source code. Container commands run after the application ad web server have been set up and the application version archive has ben extracted but before the application version is deployed
        - `leader_only` flag: can be used if we want the command to be executed by a single EB instance

## Application Versions

- Every time the application is deployed, a new application version is created
- We can see all the application versions in EB console under "Application version"
- EB has a maximum limit of how many application versions we can have, the value of this is 1000
- In order to remove older application versions, we can set up a lifecycle policy
- If application version is deployed to an environment, it wont be deleted

## Rebuild an Environment

- EB will delete the old environment and will recreate everything
- This will remove and recreate resources as well

## Worker Environments

- Used for offloading long running workloads, example processing videos, processing cron jobs, etc.
- It uses SQS queues under the hood for being able to queue upcoming tasks
- Worker environments polls the SQS queue for more work, in case of failure, the task is passed onto the DLQ
- `cron.yml`: configuration file for the worker environment for being able to create cron jobs

## Multi-container Docker Environments

- Uses ECS cluster under the hood
- `Dockerrun.aws.json`: EB specific json file describing how to deploy a set of Docker containers as an Elastic Beanstalk application
- Reference: [https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_docker_ecs.html](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_docker_ecs.html)