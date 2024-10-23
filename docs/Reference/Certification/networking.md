# Networking

## VPC
VPC is global and subnets are regional. Subnets  can span zones to cover region. Multiple VPC's can be created with in a project.  
- Routing tables are build-in
- Distributed firewall comes with it
- Firewall based on tags

## Network connection
- Cloud VPN 
- Direct peering. 
- career peering (via service provider)
- Dedicated interconnect
- Partner interconnect
- Cross cloud interconnect. 

## Cloud VPN
Connect On-prem to GCP.  
## HA VPN
Higher availability. Must create redundant channel.

## Cloud Interconnects
Dedicated interconnect
Partner interconnect
Cross Cloud interconnect 
VPN tunnel over internet
Direct peering : On-prem to googl service.  No SLA

## CDN Interconnect
- Connect to external CDN provider

## Shared VPC
Allows sharing for VPC network with one or more VPC's. One VPC will act as host VPC and service project vpc.

## Private google access
- Enable necessary API
- Route API calls to private.googleapis or restricted.googleapi.com (dns)
- Create necessary cloud router route to private/restricted.googleapi.com