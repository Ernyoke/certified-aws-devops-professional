# AWS Service Catalog

- Self-service portal allowing to launch a set of authorized products pre-defined by admins
- Allows to create an manage catalogs of IT services that are approved in an AWS account
- The products are represented by CloudFormation templates
- All the products are assigned to Portfolios (teams)
- Teams are presented a self-service portal where they can launch products
- All the deployed products are centrally managed
- Helps with governance, compliance and consistency
- Can give users access to launch products without requiring deep AWS knowledge
- Integrates with "self-service portals" such as ServiceNow

## Overview

- As an admin we create a CloudFormation template including all the allowed products
- Then we create a Portfolio, which is a collection of products
- We then apply IAM permissions to users allowing to access specific portfolios
- As an user we are presented with a set of products on the self-service portal
- We can choose the product, we parameterize it and we launch it