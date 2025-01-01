# A Cheat sheet
# Site moved to [https://vettom.pages.dev](https://vettom.pages.dev)
### Eks kube config
Update kubeconfig file with AWS EKS cluster credentials
```bash
aws eks --profile $PROFILE  --region eu-west-2 update-kubeconfig --name $CLUSTERNAME 
```

### Addons
Commands to list and verify EKS addons and its versions for specific K8s versions.
```bash
# Get latest version for specific add on for specific K8s version
aws eks describe-addon-versions --profile $PROFILE \
    --kubernetes-version=1.30 \
    --addon-name=vpc-cni \
    --query 'sort_by(addons  &addonName)[].{owner: owner, addonName: addonName, type: type, Version: addonVersions[0].addonVersion }'

# Get all latest addOn versions
aws eks --profile $PROFILE describe-addon-versions  \
	--kubernetes-version=1.30 \
    --query 'sort_by(addons  &addonName)[].{owner: owner, addonName: addonName, type: type, Version: addonVersions[0].addonVersion }'

# Get available configurations for specific addon
aws eks --profile $PROFILE describe-addon-configuration --addon-name vpc-cni --addon-version v1.15.5-eksbuild.1 --output yaml

# List addons installed on your cluster
aws eks --profile $PROFILE list-addons --cluster-name uk-as-dev-cluster1
```

# ECR public repo unable to retrieve credentials
When trying to access public repo hosted on AWS ECR can result in Authentication failure. 

** ERROR
```bash
helm pull oci://public.ecr.aws/karpenter/karpenter       
                                               
Error: GET "https://public.ecr.aws/v2/karpenter/karpenter/tags/list":
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

### Service account
```bash
# List SVC Account 
eksctl get iamserviceaccount --profile $PROFILE --cluster $CLUSTERNAME 
```