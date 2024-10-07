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

## Audit logs
- API Calls    : Data access Audit logs(user access). Disabled by default due to size except for BigQuery.
- System event : By systems, not users. Records all Cloud resource activities. Cannot be disabled.
- Admin activity : Enabled by default and cannot disable.
 