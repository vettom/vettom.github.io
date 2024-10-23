# App Engine

Components
- Application 
    - Service (Code execution)
        - Version   (Service can support multiple versions.)
            - Instance (Single or multi or autoscale)

## Scaling
By default app-engine will scale based on load. However can control and define scaling options.

## Traffic splitting
When multiple versions are running, split can be done via IP, HTTP cookie or random selection. Http Cookie is preffered as user will end in same version.