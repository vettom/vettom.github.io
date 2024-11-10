# EKS Addons management
AWS provides multiple addon packages to aid EKS cluster and some of those are installed by default. However it does not always get upgraded unless you specify respective version. It is not always easy finding the right version of package and here is a collection commands to help with that.

There are additional addons provided by vendors as well

### List all addons and latest version for specific k8s version
Following command will list all addons and their latest version for kubernetes 1.30
```bash
aws eks describe-addon-versions \
    --kubernetes-version=1.30 \
    --query 'sort_by(addons  &addonName)[].{owner: owner, addonName: addonName, type: type, Version: addonVersions[0].addonVersion }'
```
### Get latest version for specific addon
This command will return latest version of addon available for k8s version provided
```bash
aws eks describe-addon-versions \
    --kubernetes-version=1.30 \
    --addon-name=vpc-cni \
    --query 'sort_by(addons  &addonName)[].{owner: owner, addonName: addonName, type: type, Version: addonVersions[0].addonVersion }'
```
### Get all versions of specific addon for k8s version
Sometimes you may not want latest version and find out all available versions for an addon
```bash
aws eks describe-addon-versions  \
    --kubernetes-version=1.30 \
    --addon-name=vpc-cni \
    --query 'sort_by(addons  &addonName)[].{owner: owner, addonName: addonName, type: type, Version: addonVersions[].addonVersion }'
```
### Get list of all available addons
Simple list of all available addons
```bash
aws eks describe-addon-versions \
    --kubernetes-version=1.30 \
    --query 'sort_by(addons  &addonName)[].{addonName: addonName}' | grep addonName | awk -F: '{ print $2}'
```
### List all addons provided by AWS and latest version
```bash
aws eks describe-addon-versions  \
    --kubernetes-version=1.30  --owner=aws \
    --query 'sort_by(addons  &addonName)[].{owner: owner, addonName: addonName, type: type, Version: addonVersions[0].addonVersion }'
```
### Get configuration options for addon
Many addons comes with additional configuration options. Yaml output provides best format.
```bash
aws eks --profile $PROFILE describe-addon-configuration --addon-name vpc-cni --addon-version v1.15.5-eksbuild.1 --output yaml 
```
### List addons installed on your cluster
```bash
aws eks --profile $PROFILE list-addons --cluster-name cluster1
```