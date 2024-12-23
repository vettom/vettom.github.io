# External DNS for K8s
![External DNS](https://vettom-images.s3.eu-west-1.amazonaws.com/kubernetes/external-dns.png){: style="height:250px;width:250px" }
![EKS logo ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/amazon_eks.png){: style="height:200px;width:200px"  }
![Route53](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/route53.jpg){: style="height:150px;width:150px"  }


[External DNS](https://kubernetes-sigs.github.io/external-dns/latest/) is a great tool for automating DNS entries for service exposed externally via Ingress, Service or Gateway API resource.  It supports most common DNS providers, and in this example my domain is configured in AWS Route53 and will be using Envoy Gateway to expose my application externally. 

## Configure External DNS
There are 2 parts to installing and configuring external DNS. In this example, I will demonstrate configuring EKS cluster to manage domains configured on Route53


1. Create IAM policy and Pod Identity association
2. Install External-DNS by passing necessary values

<iframe width="560" height="315" src="https://www.youtube.com/embed/vqHzLShLXhs?si=CALijjq1RbGxyu0F" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### IAM policy and pod identity

**Step 1.** Create IAM policy with necessary permissions. Note that this policy is open to manage all zones, ideally restrict policy to respective ZoneID.

*IAM policy external_dns_iam_policy allowing DNS updates.*
```json
    {
      "Version" : "2012-10-17",
      "Statement" : [
        {
          "Effect" : "Allow",
          "Action" : [
            "route53:ChangeResourceRecordSets"
          ],
          "Resource" : [
            "arn:aws:route53:::hostedzone/*"
          ]
        },
        {
          "Effect" : "Allow",
          "Action" : [
            "route53:ListHostedZones",
            "route53:ListResourceRecordSets"
          ],
          "Resource" : [
            "*"
          ]
        }
      ]
    }
```
**Step 2.** Create IAM role `external-dns-controller` with Pod Identity trust policy. 

*Trust policy for Pod Identity service*

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "pods.eks.amazonaws.com"
            },
            "Action": [
                "sts:TagSession",
                "sts:AssumeRole"
            ]
        }
    ]
}
```
**Step 3.** Attache IAM policy `external_dns_iam_policy` to IAM role `external-dns-controller`

**Step 4.** Create Pod Identity association for Namespace `external-dns`, ServiceAccount `external-dns-controller`  to the role created.

**Step 5.** Install External DNS using Helm charts with custom `values.yaml`.


```yaml
serviceAccount:
  name: external-dns-controller
sources:
  - ingress   
  - gateway-httproute
domainFilters:
  - vettom.online
txtOwnerId: "eks-demo-cluster"   
```

- serviceAccount : Specify service account mentioned while creating Pod Identity association
- sources:       : Supports Ingress, httproute(gateway API) or service. 
- domainFilters  : List of domains ext-dns will manage entries for. 
- txtOwnerId     : Unique string allows you to distinguish between multiple instances of ExternalDNS managing DNS records.


```bash
helm install  external-dns  external-dns -n external-dns \
--create-namespace --repo https://kubernetes-sigs.github.io/external-dns \
--version 1.15.0 -f ./values.yaml
```

**Step 6.** Deploy application with `Httproute` and validate. Sample application can be found at [aws-eks-terraform/EKS-Cluster-generic/Sample-app](https://github.com/vettom/aws-eks-terraform/blob/main/EKS-Cluster-generic/Sample-app/echoserver-gatewayapi.yaml) repo

## Terraform code for IAM
![terraform](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/terraform.png){: style="height:100px;width:100px"  align="right" }
Below terraform code will create IAM role with POD ID trust, create POD ID association, and attaches policy allowing DNS modification.

*Pod ID trust policy*
```bash
data "aws_iam_policy_document" "podidentity" {
  statement {
    effect = "Allow"
    principals {
      type        = "Service"
      identifiers = ["pods.eks.amazonaws.com"]
    }
    actions = [
      "sts:AssumeRole",
      "sts:TagSession"
    ]
  }
}
# Create IAM role with trust policy
resource "aws_iam_role" "externaldns_iam_role" {
  name               = "external-dns-controller"
  assume_role_policy = data.aws_iam_policy_document.podidentity.json
}
# Associate Identity allowing serviceAccount external-dns-controller in NS external-dns
resource "aws_eks_pod_identity_association" "externaldns" {
  cluster_name    = module.eks.cluster_name
  namespace       = "external-dns"
  service_account = "external-dns-controller"
  role_arn        = aws_iam_role.externaldns_iam_role.arn
}
# IAM policy to allow Route53 zone updates
resource "aws_iam_policy" "external_dns_iam_policy" {
  name = "ExternalDNSControllerIAMPolicy"
  policy = jsonencode(
    {
      "Version" : "2012-10-17",
      "Statement" : [
        {
          "Effect" : "Allow",
          "Action" : [
            "route53:ChangeResourceRecordSets"
          ],
          "Resource" : [
            "arn:aws:route53:::hostedzone/*"
          ]
        },
        {
          "Effect" : "Allow",
          "Action" : [
            "route53:ListHostedZones",
            "route53:ListResourceRecordSets"
          ],
          "Resource" : [
            "*"
          ]
        }
      ]
    }
  )
}
# Attach policy to IAM role.
resource "aws_iam_role_policy_attachment" "externaldns_policy_attachement" {
  role       = aws_iam_role.externaldns_iam_role.name
  policy_arn = aws_iam_policy.external_dns_iam_policy.arn
}
```