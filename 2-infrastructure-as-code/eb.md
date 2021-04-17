# Elastic Beanstalk

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
- THis will remove and recreate resources as well 