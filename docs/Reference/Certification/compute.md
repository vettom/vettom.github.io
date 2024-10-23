# Compute engine
- 1 sec billing with min of minute
- Sustained use discount instance running 25% at month
- Committed use discount 1-3yeat
- Pre-emptible or spot VM
    - preemtible can run max 24 hours
    - less features 

## Loadbalancer
- Cloud Loadbalancer. 
    - cross region loadbalancing
    - multi-region failover
    - no pre-warming required

### Types of LB
- Global https
- Global SSL proxy
- Global TCP proxy
- Regional external
- Regional internal
- Cross region Internal


- Cloud DNS
- Cloud CDN

## Cloud Functions
Like Lambda, limited number of languages. Ideal for reactive/event driven apps.

## App engine
Managed code runtime. Can build complete app stack like DB. Support for more languages. OR can use app engin flexible to containarise unsupported languages.

## OS Login
- OS login lets you control access to VM using IAM permission. Also possible to enable 2FA with OS login. 
- OS login can be enabled on instance by adding Metadata `enable-oslogin=true` to instance
- To enable for all instances, enable it in project metadata.
- compute.osLogin or osLoginAdmin role can be assigned. 
- osLoginExternalUser to allow connection from different Org.

## Image
Image canbe created from
- disk
- Image
- Snapshot
- Cloud  Storage file
- virtual disk
Images can be deleted or deprecated.

## Instance template
- Templates are readonly, cannot be updated
- Not all options available via console like image family, persistent disk etc. 

## VM Manager
Tools to manage group of VM and OS. Can control configuration, patch, inventory etc.