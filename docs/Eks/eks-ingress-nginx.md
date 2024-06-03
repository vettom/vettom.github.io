# EKS cluster and Ingress-NGINX
[Ingress-Nginx](https://github.com/kubernetes/ingress-nginx) is a popular Reverse Proxy and Loadbalancer. In this example, I will demonstrate complete EKS cluster with ingress controller and Sample app to verify installation.

:desktop_computer:  [EKS with ngress-Nginx](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-ingress) contains everything required for you to spin up a new cluster and expose application via Ingress-Nginx controller. [Source Code](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-ingress). [Sample app](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-ingress/Sample-app).

<iframe width="560" height="315" src="https://www.youtube.com/embed/0QhY-QTVSfw?si=WO48SN2AfH_6Zbgu" title="EKS Ingress-Nginx" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Why do we need AWS LB controller?
If Ingress-NGIX is configured without LB controller, it will provision LB with target type of NodePort. VPC CNI and LB controller enables you to make use of target type 'ip' and traffic can flow direct to Pod's IP and Port

|Advantages of Ingress-Ngix over ALB conroller?|
|-------------------------|
|Services can be configured using Cluster IP making it more secure | 
|Traffic roted direct to pod and its port|
| Ingress metrics available in cluster|
| Certificate manager can be used for SSL certs|

## Resources

- VPC with 2  privat and 2 public zones
- VPC CNI add-on with prefix delegation
- AWS Loadbalancer controller
- Ingress-Nginx controller.

