# A Cheat sheet

### Eks kube config
```bash
aws eks --profile $PROFILE  --region eu-west-2 update-kubeconfig --name $CLUSTERNAME 
```

### Addons
```bash
# Add-on verion 
aws eks --profile $PROFILE describe-addon-versions --addon-name aws-ebs-csi-driver 

# Get  format for addon
aws eks --profile $PROFILE describe-addon-configuration --addon-name vpc-cni --addon-version v1.15.5-eksbuild.1 --output text 
```

### Service account
```bash
# List SVC Account 
eksctl get iamserviceaccount --profile $PROFILE --cluster $CLUSTERNAME 
```