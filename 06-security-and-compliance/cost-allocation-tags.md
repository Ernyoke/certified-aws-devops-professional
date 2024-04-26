# AWS Cost ALlocation Tags

- With tags we can track resources which relate to each other
- With cost allocation tags we can enable detailed costing reports
- Cost allocation tags are just like default tags, but they show up as columns in reports
- There are 2 types of cost allocation tags:
    - AWS generated tags:
        - Automatically applied to the resources we create
        - They start with the prefix `aws` (example: `aws:createdBy`)
        - They are not applied to resources created before activation
    - User tags:
        - Defined by the user
        - They start with the prefix `user`
- Cost allocation tags appear only in the Billing Console
- It can take 24 hours to show up in the billing reports