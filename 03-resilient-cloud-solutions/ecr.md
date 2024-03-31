# ECR - Elastic Container Registry

- ECR is a private Docker image repository
- Access to ECR is controller through IAM
- Supports image vulnerability scanning, versioning, image tags and image lifecycle
- In order to push a Docker image to ECR, we have to do the following commands
    - AWS CLI v1: `$(aws ecr get-login --no-include-email --region eu-west1)`
    - AWS CLI V2: `aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin 12345.dkr.ecr.eu-west-1.amazonaws.com`
    - Build the image: `docker build -d demo .`
    - Tag the image `docker tag demo:latest ...`
    - Push the image: `docker push 1234:dkr.ecr.eu-west-1.amazonaws.com/demo:latest`
- Pull an image from ECR:
    - Login with the same command above
    - Pull the image: `docker pull 1234:dkr.ecr.eu-west-1.amazonaws.com/demo:latest`

## Lifecycle Policies

- Used for automatically removing old or unused images based on age or count
- Each policy may container one or more rules
- All rules are evaluated at the same time and applied based on the priority
- Images are expired within 24 hours after they meet the criteria
- Lifecycle policies help to reduce costs