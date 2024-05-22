# EKS cluster with Karpenter Autoscaling [Terraform]

Karpenter Autoscaling is a great choice when it comes scaling EKS cluster. It is very cost efficient, quick and does not have the limitations of cluster autoscaler

:desktop_computer:  [EKS with Karpenter](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-karpenter) contains everything required for you to spin up a new cluster and configure Karpenter autoscaling

[Source Code](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-karpenter)
[Sample app](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-karpenter/Sample-App)

### Contains following: 
- VPC with 2  privat and 2 public zones
- EKS cluster with Managed NodeGroup (1 Node)
- Karpenter Loadbalancer 
- Sample App to test Autoscaling

## Instructions
> :warning: If cluster endpoint not updated, Karpenter nodes will not join cluster
```bash
terrafor init -upgrade ; terraform apply
# Update cluster endpoint in Karpenter-app/karpenter-values.yaml file
"clusterEndpoint: https://11111111111.gr7.eu-west-1.eks.amazonaws.com"
# Configure local kubeconfig
aws eks --profile labs  --region eu-west-1 update-kubeconfig --name eks-demo
# Verify cluster access
kubectl cluster-info
# Install Karpenter controller
helm install karpenter -n karpenter --create-namespace oci://public.ecr.aws/karpenter/karpenter \
 --version 0.36.2 -f Karpenter-app/karpenter-values.yaml
# Once karpenter pods are up and running, create nodepoo and node class
kubectl apply -f Karpenter-app/karpenter-nodepool.yaml
# Verify resources are created
get ec2nc,nodepool
```

## Testing Karpenter
Karpenter will provision new nodes when ever pod deployment is pending due to resource constraints. Here is sample app that you can use for test, adjust number of replicas as required.

```bash
kubectl apply -f Sample-App/karpenter-scale.yaml
# Notice pods are pending and if you describe, it will report insufficient CPU. 
# Check Karpenter logs for errors, also notice it provision new node
kubectl logs -f -n karpenter -l app.kubernetes.io/name=karpenter
# Allow a minute and check pods status as well as nodes. 
kubectl get nodes # Should see new node being provisioned.
# Check and ensure all pods are now deployed
kubectl get po -n karpenter-testing
```
