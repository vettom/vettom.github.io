---
draft: false 
date: 2024-04-03
authors: ["vettom"]
categories:
  - eks
  - kubernetes
  - loadbalancer controller
  - ingress
---
# Complete EKS cluster [Terraform]

Getting started with creating a functional EKS cluster from scratch can be challenging as requires some specific settings. While EKS module will create a new cluster, it does not address how you will expose an applicaition, tags required for subnets, number of pod IP addresses etc

:desktop_computer:  [EKS cluster using terraform](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-ALB) contains everything required for you to spin up a new cluster and expose application via Application Loadbalancer. All you need to do is apply terraform code

[Source Code](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-ALB), [Sample app](https://github.com/vettom/aws-eks-terraform/tree/main/Sample-app)

- [x] VPC with 2  private and 2 public zones
- [x] EKS cluster with Managed NodeGroup (1 Node)
- [x] VPC CNI add-on with prefix delegation
- [x] AWS Loadbalancer controller


<iframe width="560" height="315" src="https://www.youtube.com/embed/EAz6ap4pm6Y?si=IFt0DzUjjMvpQviY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
