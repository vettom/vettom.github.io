# Helm

## Repo commands
```bash
# Add eks repo to local
helm repo add eks https://aws.github.io/eks-charts 
# Update repo manifest to get latest versions
helm repo update eks  
# List all local repos configures
helm repo list      
# Search for prometheus in all your local repos
helm search repo prometheus  
# All all available versions for specific chart
helm search repo prometheus/kube-state-metrics  --versions
```

## Install / upgrade
```bash
# Search for all available versions of vpc-cni
helm search repo eks/aws-vpc-cni --versions  
# Install latest vpc-cni chart to kube-system namespace
helm install vpc-cni -n kube-system eks/aws-vpc-cni  
# Install specific version of vpc-cni
helm install vpc-cni eks/aws-vpc-cni  --version v1.16.2 
# Create namespace cni and install vpc-cni
helm install vpc-cni eks/aws-vpc-cni -n cni --create-namespace 

# Upgrade vpc-cni to new version
helm upgrade vpc-cni eks/aws-vpc-cni --version 1.17.1

```

## New chart / helm repo creation
```bash
# Create a new helm chart template called myapp
helm create myapp .  
# Creates a tar.gz package of myapp
helm package myapp   
# Generate index for hosting in github pages or any other http server
helm repo index --url https://vettom.github.io/demohelmrepo/ --merge index.yaml .  

```