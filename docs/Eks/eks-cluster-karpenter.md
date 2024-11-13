# EKS with Karpenter Autoscaling (v1beta)

> Refer updated V1 version [EKS with Karpenter Autoscaling V1](https://vettom.github.io/Eks/eks-cluster-karpenterV1/)

Karpenter Autoscaling is a great choice when it comes scaling EKS cluster. It is very cost efficient, quick and does not have the limitations of cluster autoscaler.
![karpenter](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/karpenter.png){: style="height:100px;width:100px" align=right }

- Cost effective ( Provisions most cost effective instances including SPOT instance)
- Fast auto scaling (Less than 2min)
- Resource consolidation (Reduce number of nodes by organizing pod spread when spare capacity available)
- Regular node recycle (Reprovision nodes at set interval to provision them on up to date hardware.)

> :information_source: EKS API Authentication mode is used while creating EKS cluster. 

- [Source Code](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-karpenter)
- [Sample app](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-karpenter/Sample-App)

<iframe width="560" height="315" src="https://www.youtube.com/embed/INGAmV8SRu0?si=viH9pDXdD4fefbeM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### Contains following: 
- VPC with 2  private and 2 public zones
- EKS cluster with Managed NodeGroup (1 Node)
- Karpenter Autoscaling. 
- Sample App to test Autoscaling

## Instructions
> :warning: If cluster endpoint not updated, Karpenter nodes will not join cluster
```sh
terraform init -upgrade ; terraform apply
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
