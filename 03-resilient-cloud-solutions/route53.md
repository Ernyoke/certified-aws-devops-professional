# Amazon Route 53

- It is a highly available, scalable, fully managed and *authoritative* DNS
    - Authoritative: the customer can update the DNS records
- Route 53 is also a Domain Registrar
- We have the ability to check the health of our resources to which we create DNS records
- It is the only AWS service which provides 100% availability SLA

## DNS Records

- Each record contains:
    - Domain/subdomain name: example com
    - Record type: `A`, `AAAA`, `CNAME`, etc.
    - Value: for example an IP address
    - Routing Policy: how Route 53 responds to queries
    - TTL: amount of time the record is cached at the DNS server
- Route 53 supports a bunch of record types:
    - `A` / `AAAA`/ `CNAME` / `NS`
    - Advanced records: `CAA` / `DS` / `MX` / `NAPTR` / `PTR` / `SOA` / `TXT` / `SPF` / `SRV`
- Important record types (from the exam perspective):
    - `A`: maps a hostname to an IPv4 address
    - `AAAA` : maps a hostname to an IPv6 address
    - `CNAME`: maps a hostname to another hostname:
        - The target is a domain name which must have an `A` or `AAAA` record
        - We can't create a `CNAME` record for the top node of a DNS namespace (Zone Apex)
    - `NS`: Name Servers for the Hosted Zone

## Hosted Zones

- Hosted Zones are containers for records that define how to route traffic to a domain and its subdomains
- Public Hosted Zones: contains records that specify how to route traffic on the internet
- Private Hosted Zones: contains records that can be resolved within one or more VPCs (private domain names)
- We pay $0.5 per month for any hosted zone we create