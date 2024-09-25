# A Cheat sheet

### Eks kube config
```bash
aws eks --profile $PROFILE  --region eu-west-2 update-kubeconfig --name $CLUSTERNAME 
```

### Addons
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

### Service account
```bash
# List SVC Account 
eksctl get iamserviceaccount --profile $PROFILE --cluster $CLUSTERNAME 
```