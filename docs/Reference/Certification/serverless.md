# Serverless

> Only single App Engine App per project!

## App Engine
Managed code runtime. Can build complete app stack like DB. Support for more languages. OR can use app engin flexible to containarise unsupported languages.

- Service (Code execution) single service can support multiple revisions.
- Version 
    - Instance (Single or multi or autoscale)

### Traffic splitting
When multiple versions are running, split can be done via IP, HTTP cookie or random selection. Http Cookie is preffered as user will end in same version.

## Scaling
Standard : saves money by waiting until no instances are available to serve
Autoscale : Set threshold, CPU, throughput or Max concurrent requests.
By default app-engine will scale based on load. However can control and define scaling options.
`min_idle_instance ` can be used to maintain minimum number of instances.

## Traffic splitting
When multiple versions are running, split can be done via IP, HTTP cookie or random selection. Http Cookie is preferred as user will end in same version.

## Cloud functions
Like Lambda, limited number of languages. Ideal for reactive/event driven apps.

- Default 1 min timeout, can be up to 9min. Https can be up to 60min!
- Events   : An action in Google cloud, eg pub/sub, s3
- Trigger  : A way to respond to event. 
- Function : Actual code.

Functions used when single function to be executed, App engine ideal for grouping multiple tasks.
Pub/sub has only one event that is when message is published.
