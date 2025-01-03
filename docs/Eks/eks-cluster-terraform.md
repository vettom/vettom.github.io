# Complete EKS cluster [Terraform]
# Site moved to [https://vettom.pages.dev](https://vettom.pages.dev)
![EKS design document ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/eks_logo.jpg){: style="height:100px;width:100px" align=right }

Getting started with creating a functional EKS cluster from scratch can be challenging as requires some specific settings. While EKS module will create a new cluster, it does not address how you will expose an application, tags required for subnets, number of pod IP addresses etc

:desktop_computer:  [EKS cluster using terraform](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-ALB) contains everything required for you to spin up a new cluster and expose application via Application Loadbalancer. All you need to do is apply terraform code


<iframe width="560" height="315" src="https://www.youtube.com/embed/EAz6ap4pm6Y?si=IFt0DzUjjMvpQviY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

![EKS design document ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/eks-design-basic.png){: style="height:300px;width:400px" align=right }

### Resources
- VPC with 2  private and 2 public zones
- EKS cluster with Managed NodeGroup (1 Node)
- VPC CNI add-on with prefix delegation
- AWS Loadbalancer controller