# Elastic Load Balancer

- Elastic Load Balancers (ELB) are AWS managed load balancers
- AWS will take care of upgrades, maintenance and high availability of the load balancers
- It is integrated with many AWS offerings such as:
    - EC2, ASGs, Amazon ECS
    - AWS Certificate Manager (ACM)
    - CloudWatch
    - Route 53
    - AWS WAF, AWS Global Accelerator

## Types of Load Balancers on AWS

- AWS offers 4 kinds of load balancers:
    - Classic Load Balancers (v1 - old generation) - CLB: supports HTTP, HTTPS, TCP, SSL (secure TCP)
    - Application Load Balancers (v2 - new generation) - ALB: supports HTTP, HTTPS, WebSockets
    - Network Load Balancer (v2 - new generation) - NLB : supports TCP, TLS, UDP
    - Gateway Load Balancers - GWLB: operates at layer 3 (Network Layer) - IP Protocol

## ALB Listener Rules

- Rules on ALB are processed in order (default rule is going to be processed last)
- Each rule has a supported action: forward, redirect, fixed-response
- Rules can have conditions based on:
    - host-header
    - http-request-method
    - path-pattern
    - source-ip
    - http-header
    - query-string
- Target Group Weighting:
    - We can specify a weight for each Target Group on a single rule
    - Allows us to control the distribution of the traffic to our application

## ELB DualStack Networking

- Allows clients to communicate with the ELB using both IPv4 and IPv6 (dualstack URL)
- Supported by both ALB and NLB
- ALB and NLB can have mixed IPv4 and IPv6 targets, but only separate target groups
- ELB dualstack ensures compatibility between clients and target IP versions:
    - IPv4 clients will communicate with IPv4 targets
    - IPv6 clients will communicate with IPv6 targets
    - In case there are only IPv4 targets, the ELB automatically converts requests from IPv6 to IPv4
