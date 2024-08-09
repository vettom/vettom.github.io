---
draft: false 
date: 2024-07-28
authors: ["vettom"]
categories:
  - helm
  - ecr
  - oci chart
---
# ECR public repo unable to retrieve credentials
When trying to access public repo hosted on AWS ECR can result in Authentication failure. 

** ERROR
```
helm pull oci://public.ecr.aws/dynatrace/dynatrace-operator                                                       
Error: GET "
https://public.ecr.aws/v2/dynatrace/dynatrace-operator/tags/list":
unable to retrieve credentials
```
### Solution
Log on to helm registry with AWS ECR public access credentials
```bash
aws ecr-public get-login-password \
     --region us-east-1 | helm registry login \
     --username AWS \
     --password-stdin public.ecr.aws
```