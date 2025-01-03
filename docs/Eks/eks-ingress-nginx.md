# EKS cluster and Ingress-NGINX
# Site moved to [https://vettom.pages.dev](https://vettom.pages.dev)
![Ingress Nginx ](https://vettom-images.s3.eu-west-1.amazonaws.com/kubernetes/ingress-nginx.png){: style="height:100px;width:100px" align=right }

[Ingress-Nginx](https://github.com/kubernetes/ingress-nginx) is a popular Reverse Proxy and Loadbalancer. In this example, I will demonstrate complete EKS cluster with ingress controller and Sample app to verify installation.

:desktop_computer:  [EKS with ingress-Nginx](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-ingress) contains everything required for you to spin up a new cluster and expose application via Ingress-Nginx controller. [Source Code](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-ingress). [Sample app](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-ingress/Sample-app).

<iframe width="560" height="315" src="https://www.youtube.com/embed/0QhY-QTVSfw?si=WO48SN2AfH_6Zbgu" title="EKS Ingress-Nginx" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Why do we need AWS LB controller?
If Ingress-NGINX is configured without LB controller, it will provision LB with target type of NodePort. VPC CNI and LB controller enables you to make use of target type 'ip' and traffic can flow direct to Pod's IP and Port

![EKS design document ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/amazon_eks.png){: style="height:100px;width:100px" align=right }

|Advantages of Ingress-Nginx over ALB controller?|
|-------------------------|
|Services can be configured using Cluster IP, not exposing in local network | 
|Traffic routed direct to pod and its port from Ingress controller|
| Ingress metrics (prometheus) available with in cluster|
| No IAM permissions requirement (LB controller requires IAM) |
| Certificate manager can be used for SSL certs|

## Resources

- VPC with 2  privat and 2 public zones
- VPC CNI add-on with prefix delegation
- AWS Loadbalancer controller
- Ingress-Nginx controller.

