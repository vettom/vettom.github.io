# Helm

## Repo commands
```bash
helm repo add eks https://aws.github.io/eks-charts # Adds eks repo to local
helm repo update eks  # Update repo manifest to get latest versions
helm repo list      # List all local repos configures
helm search repo prometheus  # Search for prometheus in all your local repos
```

## Install / upgrade
```bash
helm search repo eks/aws-vpc-cni --versions  # Search for all available versions of vpc-cni
helm install vpc-cni -n kube-system eks/aws-vpc-cni  # Install latest vpc-cni chart to kube-system namespace
helm install vpc-cni eks/aws-vpc-cni  --version v1.16.2  # Install specific version of vpc-cni
helm install vpc-cni eks/aws-vpc-cni -n cni --create-namespace # Create namespace cni and install vpc-cni

# Upgrade vpc-cni to new version
helm upgrade vpc-cni eks/aws-vpc-cni --version 1.17.1

```

## New chart / helm repo creation
```bash
helm create myapp .  # Create a new helm chart template called myapp
helm package myapp   # Creates a tar.gz package of myapp
helm repo index --url https://vettom.github.io/demohelmrepo/ --merge index.yaml .  # Generate index for hosting in github pages 

```