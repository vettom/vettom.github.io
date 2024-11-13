# EKS with Karpenter v1 Autoscaling


Karpenter Autoscaling is a great choice when it comes scaling EKS cluster. It is very cost efficient, quick and does not have the limitations of cluster Autoscaler. 
![karpenter](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/karpenter.png){: style="height:100px;width:100px" align=right }

- Cost effective ( Provisions most cost effective instances including SPOT instance)
- Fast auto scaling up/down
- Resource consolidation (Reduce number of nodes by organizing pod spread when spare capacity available)
- Regular node recycle (Re-provision nodes at set interval to provision them on up to date hardware.)

## BottleRocket
[Amazon BottleRocket](https://aws.amazon.com/bottlerocket/?amazon-bottlerocket-whats-new.sort-by=item.additionalFields.postDateTime&amazon-bottlerocket-whats-new.sort-order=desc) is a 
Linux-based open-source operating system that is purpose-built by Amazon Web Services for running containers. Highly secure, optimized for containers and fast provisioning. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/TztGj7lL9o4?si=Ck5HWyI2kKwB8yBr" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Karpenter configuration
There are some prerequisites to provisioning Karpenter on EKS cluster. 
#### IAM Role
Karpenter require IAM role to join Cluster and provision nodes. In this example I am creating IAM role called `karpenter-controller-eks-demo`. IAM trust policy is attached so that `karpenter-controller-eks-demo` serviceAccount in `karpenter` namespaces is allowed to assume this role. Various Ec2 permissions are assigned to the role so that it has necessary permissions to get pricing of instances and provision new nodes. `eks:DescribeCluster` permission is added so that Karpenter pod can identify cluster endpoint, than having to manually update in Helm values.

#### InstanceProfile
It is advised to have dedicated `instanceProfile` for Karpenter. 
#### Helm Values
Few input values at minimum must be provided during helm installation. Clustername so it can identify which cluster to attach nodes to. ServiceAccount name and Annotation to specify which role to assume. Details here must match IAM role created.

#### ec2NodeClass
Define the AMI version to be used while provisioning nodes. Also specify subnet tag to identify the subnet where node will be provisioned and security group to be attached.

#### Nodepool
It is not a pool as name suggested, Karpenter provisions individual nodes based on constraints. Define the type of Node, instance category etc. Also must specify CPU and memory limit so that runaway workloads does not cause too many servers being spun up.


## Installation Instructions
Terraform code provided in [aws-eks-terraform](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-karpenter-V1) includes creation of a new VPC andEKS cluster with single Spot instance. It also includes necessary IAM profile for Karpenter, and custom `instanceProfile` for Karpenter use. Karpenter-app contains minimum required values for Karpenter, and `ec2NodeClass` and `nodePool` configured with `Bottlerocket` AMI
```bash
terraform init -upgrade ; terraform apply
# Configure local kubeconfig
aws eks --profile labs  --region eu-west-1 update-kubeconfig --name eks-demo
# Verify cluster access
kubectl cluster-info
# Install Karpenter controller
helm install karpenter -n karpenter --create-namespace oci://public.ecr.aws/karpenter/karpenter \
 --version 1.0.6 -f Karpenter-app/karpenter-values.yaml
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
