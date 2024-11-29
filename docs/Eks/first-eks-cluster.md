# EKS cluster build
![EKS design document ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/eks-design-basic.png){: style="height:300px;width:400px" align=right }
Building EKS cluster can be complex as it has many pre-requisites and make use of many technologies and add-ons. In this document, I will walkthrough all the steps necessary to spin up a newEKS cluster using official terraform modules. Complete terraform code is available in my Git repo [aws-eks-terraform](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-ALB).  [Link video](https://www.youtube.com/watch?v=EAz6ap4pm6Y)

## Setting up VPC

There  are some pre-requisites when it comes to creating VPC. You must create subnet tag for the subnets in which you will be creating the Loadbalancer. For internal network add tag `"kubernetes.io/role/internal-elb" = "1"` and for external network add tag `"kubernetes.io/role/elb" = "1"` to helm EKS identify the subnet in which to create Loadbalancers.


```bash
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.1.2"
  name    = "eks-demo-vpc"
  cidr    = "10.0.0.0/16"

  enable_dns_hostnames = true
  enable_dns_support   = true
  azs                  = ["eu-west-1a", "eu-west-1b"]
  private_subnets      = ["10.0.1.0/24", "10.0.2.0/24"]
  public_subnets       = ["10.0.101.0/24", "10.0.102.0/24"]
  enable_nat_gateway   = true # Required as k8s hosts will be in private_subnet
  enable_vpn_gateway   = false
  single_nat_gateway   = true # NAT GW is required as nodes will be provisioned in private subnet

  public_subnet_tags = {
    "kubernetes.io/role/elb" = "1" # Tag for external LoadBalancer
    "subnet_type"            = "public"
  }

  private_subnet_tags = {
    "kubernetes.io/role/internal-elb" = "1" # Tag for internal LoadBalancer
    "subnet_type"                     = "private"
  }
}
```
In above VPC module, I am creating a VPC with 2 subnets across 2 Zones. `Public_subnets` will have Gateway attached, and `Private_subnets` will have a `NAT Gateway` attached to enable private nodes to communicate with external sources like git repo, container registry etc. DNS Hostnames must be enabled and add respective tags for subnets.

![EKS design document ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/eks_logo.jpg){: style="height:100px;width:100px" align=right }
## Provision EKS cluster
Below code will provision EKS cluster in to the VPC you have just created. It will have single `NodeGroup` with single SPOT instance to save cost. Kubernetes 1.31 is latest supported version available at this point in time. Provisioning EKS cluster in to private_subnet is best practice. Cluster-endpoint can be private or public, if public, you have option to [whitelist cluster endpoint](https://docs.aws.amazon.com/eks/latest/userguide/cluster-endpoint.html#modify-endpoint-access)

```bash
module "eks" {
  source  = "terraform-aws-modules/eks/aws"
  version = "20.10.0"

  cluster_name    = "eks-demo"
  cluster_version = "1.31"

  cluster_endpoint_public_access           = true
  enable_irsa                              = true
  vpc_id                                   = module.vpc.vpc_id
  subnet_ids                               = module.vpc.private_subnets
  control_plane_subnet_ids                 = module.vpc.private_subnets
  authentication_mode                      = "API"
  enable_cluster_creator_admin_permissions = true

  eks_managed_node_groups = {
    eks_nodegroup_1 = {
      ami_type       = "BOTTLEROCKET_x86_64"
      min_size       = 1
      max_size       = 2
      desired_size   = 1
      instance_types = ["m7i.large", "m6i.large", "m5.large"]
      capacity_type  = "SPOT"
    }
  }
}
```

- `cluster_endpoint_public_access` : Make cluster cluster endpoint available via internet. 
- `enable_irsa`                    : IAM-RSA policies enable EKS resources to access AWS resources like S3, Route53 etc. [EKS Pod Identity](https://docs.aws.amazon.com/eks/latest/userguide/pod-identities.html) is newer and simpler than IAM-RSA
- `authentication_mode`            : API authentication is latest, and there is option to use configmap as well.
- `creator_admin_permission`       : When using API only Auth, creater does not have access by default. So must update configuration to allow admin access if you wish. 
- `Managed_Nodegroup`              : Specify initial nodeGroup size and image. BottleRocket image is optimized for container workloads.

## EKS-addon
Your basic cluster can run without any add-on, however if you do not install VPC-CNI add-on, you will be limited in number of pods (IP) you can run on single node. Checkout [VPC-CNI documentation](https://docs.aws.amazon.com/eks/latest/userguide/managing-vpc-cni.html) for details
```bash
resource "aws_eks_addon" "vpc-cni" {
  cluster_name  = module.eks.cluster_name
  addon_name    = "vpc-cni"
  addon_version = "v1.18.5-eksbuild.1"
  configuration_values = jsonencode(
    {
      env = {

        ENABLE_PREFIX_DELEGATION          = "true" # Required for ALB to work with target type ip
        WARM_ENI_TARGET                   = "1"    # optional prefix IP pool.
        WARM_PREFIX_TARGET                = "1"
        POD_SECURITY_GROUP_ENFORCING_MODE = "standard"
        AWS_VPC_K8S_CNI_EXTERNALSNAT      = "true" # Optional when using SG for pod outbound traffic routing.
        ENABLE_POD_ENI                    = "true" # If using Security group for pods

      }
      enableNetworkPolicy = "true"
    }
  )
}
```

## AWSLoadbalancer controller
![EKS design document ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/aws-tg.png){: style="height:200px;width:400px" align=right }
AWS Loadbalancer controller is a service you must install by default. Without LB-Controller EKS supports Nodeport only, which slightly slower and not best for security. Also having to maintain the NodePort is additional overhead. LB-controller enables Loadbalancer Targetgroups to send traffic direct to the pod and its port. LB-controller must be installed before installing Ingress or Gateway API controllers so that Loadbalancer can make use ot `targerType: ip` and route traffic direct to pod.

LB-controller can be used directly with `serviceType: loadbalancer`, however it is ideal to use [Gateway API](https://gateway-api.sigs.k8s.io/) or [Ingress Controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) to route traffic in to K8s cluster. 

```bash
resource "helm_release" "aws-load-balancer-controller" {
  name       = "aws-load-balancer-controller"
  namespace  = "kube-system"
  repository = "https://aws.github.io/eks-charts"
  chart      = "aws-load-balancer-controller"
  version    = "1.10.0"
  set {
    name  = "clusterName"
    value = module.eks.cluster_name
  }
  set {
    name  = "defaultTargetType"
    value = "ip"
  }
  set {
    name  = "serviceAccount.create"
    value = "true"
  }
  set {
    name  = "serviceAccount.name"
    value = aws_iam_role.aws_load_balancer_controller.id
  }
  set {
    name  = "serviceAccount.annotations.eks\\.amazonaws\\.com\\/role-arn"
    value = aws_iam_role.aws_load_balancer_controller.arn
  }
}
```

## Terraform config
Configure appropriate authentication for your Terraform module. In my example, I have created a profile called `labs` and using AWS profile with keys. You must also configure Helm provider so that LB-controller application can be installed once cluster is built. 
```bash
provider "helm" {
  kubernetes {
    host                   = module.eks.cluster_endpoint
    cluster_ca_certificate = base64decode(module.eks.cluster_certificate_authority_data)
    exec {
      api_version = "client.authentication.k8s.io/v1beta1"
      args        = ["eks", "get-token", "--profile", "labs", "--cluster-name", module.eks.cluster_name]
      command     = "aws"
    }
  }
```

## Connecting to EKS cluster
AWS access does not mean you have access to EKS cluster. You must ensure your ID is configured for EKS API access.
```bash
aws eks --profile labs --region eu-west-1 update-kubeconfig --name eks-demo
kubectl cluster-info
```