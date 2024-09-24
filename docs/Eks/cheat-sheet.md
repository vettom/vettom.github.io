# A Cheat sheet

### Eks kube config
```bash
aws eks --profile $PROFILE  --region eu-west-2 update-kubeconfig --name $CLUSTERNAME 
```

### Addons
```bash
# Add-on verion 
aws eks --profile $PROFILE describe-addon-versions --addon-name aws-ebs-csi-driver

# Get latest version for specific add on for specific K8s version
aws eks describe-addon-versions --profile $PROFILE \
    --kubernetes-version=1.30 \
    --addon-name=vpc-cni \
    --query 'sort_by(addons  &addonName)[].{owner: owner, addonName: addonName, type: type, Version: addonVersions[0].addonVersion }'

# Get all latest addOn versions
aws eks --profile $PROFILE describe-addon-versions  \
	--kubernetes-version=1.30 \
    --query 'sort_by(addons  &addonName)[].{owner: owner, addonName: addonName, type: type, Version: addonVersions[0].addonVersion }'

# List all addons for k8s version and latest version
aws eks --profile $PROFILE describe-addon-versions  \
	--kubernetes-version=1.30 \
	--query 'sort_by(addons  &addonName)[].{addonName: addonName, Version: addonVersions[0].addonVersion }'

# Get  format for addon
aws eks --profile $PROFILE describe-addon-configuration --addon-name vpc-cni --addon-version v1.15.5-eksbuild.1 --output text 
```

### Service account
```bash
# List SVC Account 
eksctl get iamserviceaccount --profile $PROFILE --cluster $CLUSTERNAME 
```