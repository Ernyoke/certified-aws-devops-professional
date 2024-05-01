# Tagging Strategies and AWS Tag Editor

- Whitepaper: [https://d1.awsstatic.com/whitepapers/aws-tagging-best-practices.pdf](https://d1.awsstatic.com/whitepapers/aws-tagging-best-practices.pdf)

## General Best Practices

- When creating a tagging strategy, we should make sure that it accurately represents organizationally relevant dimensions and adheres to the following best practices:
    - Always use standardized, case-sensitive format for tags
    - Consider tag dimensions that support the ability to manage resource access control, cost tracking, automation and organization
    - Implement automated tool sto help manage resource tags
    - Better to use too many tags than to few tags
    - Tags are easy to modify and accommodate changing business requirements

## Tagging Categories

- Technical tags
- Tags for automation
- Business tags
- Security tags

## AWS Tag Editor

- Allows to manage tags of multiple resources at once
- We can add/update/delete tags
- We can search for tagged/untagged resources in all the all AWS regions