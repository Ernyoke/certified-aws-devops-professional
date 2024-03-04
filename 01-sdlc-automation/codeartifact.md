# AWS CodeArtifact

- Used to store and manage software dependencies and libraries
- Storing and retrieving these dependencies is called artifact management
- CodeArtifact is a secure, scalable and cost-effective artifact management for software development
- Works with common dependency management tools such as Maven, Gradle, npm, yarn, twine, pip and NuGet
- Developers and CodeBuild can retrieve dependencies straight from CodeArtifact
- CodeArtifact can act as a proxy for public repositories such as npm, Maven Central, etc. CodeArtifact will also cached these dependencies

## Event Bridge Integration

- CodeArtifact Events (such as a version of a package is created, modified, deleted) will emit events to EventBridge
- These events can be input for Lambda, SNS, SQS or they can trigger CodePipeline

## Resource Policy

- Can be used to authorize another account to access CodeArtifact
- A given principal can either read all the packages in a repository or non of them

## Upstream Repositories

- A CodeArtifact repository can have other CodeArtifact repositories as Upstream Repository
- Having an upstream, allows a package manager client to access the packages that are contained in more then one repository using a single endpoint
- We can have up to 10 upstream repositories to a repo
- A repository can have an external connection (it can have only one!). Example: public npm repo

## External Connections

- An external connection is a connection between a CodeArtifact Repository and an external/public repository such as Maven Central, npm, PyPI, NuGet, etc.
- Allows us to fetch packages that are not already present in our CodeArtifact Repository
- A repository has a maximum of 1 external connection

## Retention

- If a requested package version is found in an upstream repository, a reference to it is retained and is always available from the downstream repository
- The retained package version is not affected by changes to the upstream repository
- Intermediate repositories do not keep the package. Example: A -> B -> C -> npm; if B and C do not have a version requested by A, the downloaded version will be retained only in A (not in B and C)

## Domains

- CodeArtifact domains make it easier to manage multiple repositories across an organization
- We can use a domain to apply permissions across many repositories owned by different AWS accounts
- An asset is stored only once in a domain, even if it's available from multiple repositories
- Domains are useful for the following:
    - Deduplicate storage: assets only need to be stored once in a domain, even if it is available in many repositories
    - Fast copying: only metadata records are updated when we pull packages from upstream CodeArtifact Repositories into a downstream repository
    - Easy Sharing across repositories and teams: all the assets and metadata is a domain are encrypted with a single KMS key
    - Apply Policy across multiple repositories: a domain administrator can apply a policy across the domain as:
        - Restricting which accounts have access to repositories in the domain
        - Who can configure connections to public repositories