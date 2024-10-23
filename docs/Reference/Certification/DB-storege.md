# DB and Storage

## Cloud Storage
Object storage bucket. Any amount of data and retrieve any numer of time. 
Storing BLOB is ideal.  Data stored is immutable, only new version or overwrite.
Allows versioning. 
Cached at edge location by default
Access can be manged by IAM and ACL
Lifecycle management policies to manage data, like removing old, controlling number of versions etc
Directory sync enables VM directory sync

### Types of Cloud Storage
- Standard : Frequently accessed data
- Nearline : Infrequent like once per month
- Coldline : Access once every 90 days
- Archive : Ideal for DR, accessed less than once year. Higher access cost.

### Autoclass
- Saves cost by moving infrequent data to lower cost storage
- Storage transfer service : Allow larger data transfer.
- Transfer Appliance : Hardware appliance to transfer data.

## Filestore
NFS V3 compatible file share.

## Cloud SQL
Fully managed Mysql, postgress SQL or SQL server 

    - Automatic replication
    - data encrypted 
    - Includes Network firewall
    - Point in time recovery option.
    - Cross region replica for DR

## Cloud Datastore
- NoSQL with index and queries (dynamodb)

## Alloy DB
A faster SQL compatible DB managed by google.

## Cloud Spanner
SQL compatible Relational database. 
- Scales horizontally
- Very consistent
- SQL compatible
- High IP operations 
- High cost

## Big query
Peta-scale data warehouse designed to injest, store, analyze and visualize 
- SQL compatible
- Can read from Cloud BigTable
- Query editor displays estimated amount data used, allowing to understand cost
- Supports external data sources like Bigtable, cloud storage etc. Can query without bringing data in including other ext.
- Federated queries: Allows query to be send to SQl, CloudSpanner etc.

### Big Query Roles
- Admin  :Manage all resources, cancel jobs, manage all data in project
- ConnectionAdmin/User 
- dataEditor : Create edit, delete data tables
- DataOwner  : Metadata as well
- jobUser  : Run jobs
- user  : Make use of data, run jobs etc
INFORMATION_SCHEMA views in BigQuery provide metadata about your BigQuery resources, including datasets, jobs, and tables

## Bigtable
No SQL wide-column DB optimized for heavy read and write.  Handles huge data volumes with low latency
Ideal for Operational and machine learning applications. Data stored in key:value 
Ideal if
- more than 1 TB data
- Data is fast throughput, or rapidly changing.
- Time series or data with semantic ordering
- Realtime processing of large data
- Machine learning algorithm

## Firestore
No SQL database for mobile, web, IoT services.  Data is stored in `documents` and organized in to `collections`. 
Stored as key:value pair. No SQL queries to retrieve data.
Indexed by default
Data is cached and get updated when device is online. 
Billing based on storage, queries, documents read
ACID transaction. Ensure entire transaction consistency


## Memory store (Redis)
- Managed redis
- In memory data service

| Option     | Best for | Capacity |
| ------------- | ------------- |------------- |
| Cloud storage  | Immutable blobs larger than 5mb. in buckets  |Petabyts, 5TB biggest object  |
| Cloud SQL  |Full SQL, for webframe work or existing applications |64TB  |
| Cloud Spanner  | SQL compatible, horizontal scaling.  |Petabytes  |
| Firestore  | Massive scaling with real time query. Offline query support  |Petabytes, max object size 1MB |
| Cloud Bigtable  | Large amount of analytical data. No SQL  |Petabytes  |


## Dataproc
- Managed Hadoop, spark, 
- Opensource data science

## Dataflow 
- Streaming analytical data
- Realtime AI/ML 

## Pub/sub
- Must create a topic and  subscription