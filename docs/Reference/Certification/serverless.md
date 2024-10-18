# Serverless

> Only single App Engine App per project!

## App Engine
Components
- Application 
    - Service (Code execution)
        - Version   (Service can support multiple versions.)
            - Instance (Single or multi or autoscale)

## Scaling
Standard : saves money by aiting until no instances are available to serve
Autoscale : Set threshold, CPU, throughput or Max concurrent requests.
By default app-engine will scale based on load. However can control and define scaling options.

## Traffic splitting
When multiple versions are running, split can be done via IP, HTTP cookie or random selection. Http Cookie is preffered as user will end in same version.

## Cloud functions
- Default 1 min timeout, can be up to 9min. Https can be up to 60min!
- Events   : An action in Google cloud, eg pub/sub, s3
- Trigger  : A way to respond to event. 
- Function : Actual code.

Functions used when single function to be executed, App engine ideal for grouping multiple tasks.
Pub/sub has only one event that is when message is published.