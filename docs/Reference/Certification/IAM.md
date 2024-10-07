# IAM

## Org Hierarchy

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

### Creating and managing Org with IAM
- Super admin : Creates Org admin, and owns lifecycle of Org account
- Org Admin : Defines IAM roles and policies. Define structure and hierarchy.

## Resource manager Roles
- Organization
  - Admin : full control over all resources
  - Viewer 
- Folder
  - Admin
  - Creator : Create folders and view structure
  - Viewer
- Project
  - Creator:
  - Deleter:

# Cloud IAM
- Who : Google account, group, service account, Cloud Identity domain. Also called Principle.
- What : Defined by role.
- Every identity has unique email
- Policy binds members to a hierarchy (folder, proj, resource)

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
- Manage users and groups centrally via google Admin console
- Integrates with Existing AD services
- 2 Step verification
- Can be used for SSO on other services as well

## Service Accounts
- Represents special account that represent application. 
- Can be consumed by Application or individual user
- Applications should use service account than accounts API key
- Can created and download keys for non-gcp but not ideal
- Use Cloud-platform managed keys 
  - No downloading of keys
  - Google manages rotation of keys daily

## Cloud IAP
- Guards apps running on GCP via identity verification

## Cloud Audit logs 
- Service like CloudTrail
- Logs cannot be deleted.
- Logs are kept for 400 days no cost
- Access Transparency logs contain actions by google on your behalf
- Data Access logs are kept for 30 days