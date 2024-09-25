# GCP fundamentals

## Hierarchy

4 Levels of Hierarchy.
Organization node
|-Folders
  |- Projects
  	|- Resouce

### Projects
Are separate entities under organization node. Projects can have their on Billing, API controls and colaborators. Resources can belong to single project. Projects can have different owners and users. 
3 attributes of Project:
- Project name
-  Project ID    : Globally unique, and immutable
- Project Number : Same as above

Resource manager tool enabled management of projects

### Folders
Folders can contain Folders or projects. Resource policies can be applied at Folder level.  Folders allows grouping of resources

### Organization node
- Org Policy administrator
- Project creator
Google workspace customers projects will automatically belong to Org nodes. Others can create Org ID using Cloud Identity service 


# IAM
- Who : Google account, group, service account, Cloud Identity domain. Also called Principle.
- What : Defined by role.

### Basic IAM Role
- Owner
- Editor
- Viewer
- Billing Administrator

### Pre-defined roles
- Instance admin : manage instance. 
### Custom roles
- Manage by self
- Can be assigned at Project or Org level only, not folder level.


# Cloud Identity
Manage users and groups centrally via google Admin console
Integrates with Existing AD services

# VPC
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