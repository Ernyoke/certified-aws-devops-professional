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
    eb config save env --cfg initial-configuration
    ```

- Set an environment variable on the selected EB environment:

    ```
    eb setenv PORT=8080
    ```

