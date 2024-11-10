# IAM

## Org Hierarchy
- Organization node
  - Folders
    - Projects
      - Resouce

### Projects
Are separate entities under organization node. Projects can have their on Billing, API controls and colaborators. Resources can belong to single project. Projects can have different owners and users. 
3 attributes of Project:

- Project name
-  Project ID    : Globally unique, and immutable
- Project Number : Same as above

Resource manager tool enables management of projects

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

### [Pre-defined roles](https://cloud.google.com/iam/docs/understanding-roles)

- Instance admin : manage instance. 
- Browser : Project role to list org structure
- Billing
    - costsManager : view analzse and export
    - user : Can associate billing account and view
- BigTable
    - viewer  : No data access
    - Reader  : Read data
    - User    : Read write and own data. For users and service accounts.
- serviceAccount
    - user : Run operations are SA
    - TokenCreator  : Allows token creation as SA
    - SAViewer : Read access to SA meta and keys
    - workloadIdentityUser : Impersonate service account from federated accounts.

### [Support levels for roles](https://cloud.google.com/iam/docs/custom-roles-permissions-support)
When custom roles are created, not all IAM permissions can be added. Each permission have support level attached

- Supported     : Fully supported in custom roles
- Testing       : Google is testing this role, can use but may see errors
- Not_Supported : Cannot be added to custom role

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

When role assigned to SA, it is considered as an `Identity`. However when user given permission to access SA it is treated as `resource`. Service account permission can be assigned to user to temporarily elevate users permission. 

#### SA as resource
- Means can have their own access policies like who can access. 
- serviceAccountUser : Allows that user to attach SAto a resource
- serviceAccountAdmin: Edit,delete,disable serviceAccount.
- serviceAccount Insight can be used to identify SA not used in past 90 days.

## Cloud Identity Aware Proxy
- Guards apps running on GCP via identity verification
- Enabled ssh,RDP 
- Central auth for application accessed via https. 

#### [Cloud Audit logs](https://cloud.google.com/logging/docs/audit)

- Service like CloudTrail
- Logs cannot be deleted.
- Admin activity and sys events logs  are kept for 400 days no cost
- Policy denied and data access default is 30 days and charge apply. 
- Access Transparency logs contain actions by google on your behalf
- Data Access logs are kept for 30 days

#### [Audit log roles](https://cloud.google.com/logging/docs/access-control)

- logViewr  : Can view admin activity, policy denied and system event
- privateLogViewer : Required to view data access logs.