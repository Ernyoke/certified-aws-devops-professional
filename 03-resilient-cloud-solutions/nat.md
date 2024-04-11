# NAT Gateways

- NAT Gateways are AWS managed network address translation (NAT) services
- Payment is per hour for usage and bandwidth
- A NAT GW is created in a specific Availability Zone (AZ), uses an Elastic IP address
- Cannot be used by EC2 instances in the same subnet (only from different subnets)
- To connect to the internet, NAT GW requires an Internet Gateway (IGW)
- Bandwidth for a NAT Gateway is 5 Gbps with automatic scaling to 100 Gbps
- We cannot attach a Security Group to a NAT Gateway

## NAT Gateway - High Availability

- NAT GW is resilient only within a single AZ
- For fault-tolerance we can create multiple NAT Gateways in multiple AZs for the same VPC
