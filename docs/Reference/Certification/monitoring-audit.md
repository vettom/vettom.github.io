# Monitoring

## Metric Scope 
Is single pane of glass where you can see resources from  multiple projects. Any role allowed access to a metrics scope can view all data with in that scope. To control access to specific project, create separate metric scope for the project.

## Logging
- Stored for 30 days
- Data can be exported to Cloud Storage, BigQuery or pubsub.
- Big query can analyse and visualize with Looker studio
- Pub/sub enables streaming of logs to endpoint or apps

## Error reporting
## Trace
Latency from resources like App engine, LB or apps instrumented with 
## Profiler
Analyze CPU, memory intensive  processes

## Types of Audit logs 
- **Admin Activity**  : Always on, record API calls activity that modify metadata or configuration of resource.  
- **Data Access**    : API calls that reads metadata or configuration, also user driven API calls that manage user-provided resource data. Data access logging disabled except for BigQuery by default. Can enable to send data to service other than  BigQuery
- **System Event**    : Records Google cloud actions. 
- **Policy denied**   : Records when policy denies access to resource. Enabled by default and cannot disable. However can add exclusion filters.