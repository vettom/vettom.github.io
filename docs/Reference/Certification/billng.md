# Billing

Billing account can be selfservice or invoiced. In terms of invoiced, bill is send to org.
Roles
- Creator   : Can create account
- Admin     : Can manage, but not create
- User      : Linke account to projects
- Viewer

## Billingexport
Can be exported to BigQ for detailed analysis. Must create BQ dataset to hold data.  

## Projects
By default all Org users can  create projects. `resourcemanager.project.create` role allows creation of projects.   

## Cloud billing Account types
- Self-service
- Invoiced : Invoice by email, pay by cheque or transfer.

### Payment profile
- Individual
- Business