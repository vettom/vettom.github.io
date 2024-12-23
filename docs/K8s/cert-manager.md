# Cert-manager with LetsEncrypt
![cert-manager](https://vettom-images.s3.eu-west-1.amazonaws.com/kubernetes/certmanager.png){: style="height:150px;width:150px" }
![cert-manager](https://vettom-images.s3.eu-west-1.amazonaws.com/kubernetes/letsencrypt.png){: style="height:150px;width:150px" }
![EKS logo ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/amazon_eks.png){: style="height:200px;width:200px"  }
![Route53](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/route53.jpg){: style="height:150px;width:150px"  }

### **Free SSL certificate with LetsEncrypt**

[Cert-Manager](https://cert-manager.io/) is a powerful and extensible X.509 certificate controller for Kubernetes platforms. It will obtain certificates from a variety of Issuers, both popular public Issuers as well as private Issuers, and ensure the certificates are valid and up-to-date, and will attempt to renew certificates at a configured time before expiry. It also exposes metrics to enable monitoring certificate issues and expiry.

## Configure Cert-manager
There 3 parts to configuring Cert-manager on your Kubernetes cluster. In this example, I have EKS cluster and domain configured in Route53. So I will configure DNS validation for Certificate issue and signing.  [Lets Encrypt](https://letsencrypt.org/) will provide free certificate independent of cluster provider. Check cert-manager for supported certificate issuers. 

1. Configure authentication (Pod Identity to modify DNS entry).
2. Install Cert-manager with custom values.
3. Configure **clusterIssuer** or **Issuer** to sign your certificates.
4. Configure `Ingress`/`Gateway` resource with cert-manager annotation and TLS section.

## Authentication : IAM policy and Pod Identity

**Step 1.** 

Create IAM policy that allows creation of DNS records. This is required for Certificate issuer to perform [Domain Validation](https://letsencrypt.org/how-it-works/#domain-validation). LetsEncrypt provides two ways for Domain Validation, `DNS record under your domain` or `HTTP resource under specific path`. In this example I will configure DNS validation, hence IAM policy with DNS record update permission.

* *IAM policy `CertManagerIAMPolicy` allowing DNS record creation*
```json
{
  "Version" : "2012-10-17",
  "Statement" : [
    {
      "Effect" : "Allow",
      "Action" : "route53:GetChange",
      "Resource" : "arn:aws:route53:::change/*"
    },
    {
      "Effect" : "Allow",
      "Action" : [
        "route53:ListHostedZones",
        "route53:ListHostedZonesByName"
      ],
      "Resource" : "*"
    },
    {
      "Effect" : "Allow",
      "Action" : "route53:ListResourceRecordSets",
      "Resource" : "arn:aws:route53:::hostedzone/*"
    },
    {
      "Effect" : "Allow",
      "Action" : "route53:ChangeResourceRecordSets",
      "Resource" : "arn:aws:route53:::hostedzone/*"
    }
  ]
}
```

**Step 2.**

Create IAM role `cert-manager-iam-role` with `Pod Identity` trust policy and attach `CertManagerIAMPolicy`

* Trust policy for Pod Identity service
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

**Step 3.**

In EKS create Pod Identity Association for namespace `cert-manager` and service account `cert-manager` to IAM role `cert-manager-iam-role`

**Step 5.**

Install Cert-Manager using Helm chart with custom `values.yaml` file


* *`values.yaml`*
```yaml
installCRDs: true
namespace: "cert-manager"
config:
  apiVersion: controller.config.cert-manager.io/v1alpha1
  kind: ControllerConfiguration
  enableGatewayAPI: true
serviceAccount:
  create: true
  name: "cert-manager"

```

- installCRDs:      CRD's are not installed by default, so this argument must be true, or install CRD's separate. 
- config:           Ingress is supported by default, add configuration for `Gateway API` support
- serviceAccount:   SA to be used by Cert-manager installation. Must match SA specified in Pod Identity spec.

Install Cert-manager with custom `values.yaml` file.
```bash
helm install  cert-manager cert-manager -n cert-manager  --create-namespace  \
--repo https://charts.jetstack.io/ --version v1.16.2 -f values.yaml
```
**Step 6.**

The first thing you'll need to configure after you've installed cert-manager is an `Issuer` or a `ClusterIssuer`. These are resources that represent certificate authorities (CAs) able to sign certificates in response to certificate signing requests. The `ClusterIssuer` resource is cluster scoped which will be the use case for most users. cert-manager comes with a number of built-in [certificate issuers](https://cert-manager.io/docs/configuration/issuers/), in this example I will be configuring [LetsEncrypt ACME](https://letsencrypt.org/docs/client-options/) provider.

Why not use AWS Cert Authority? : At present AWS only supports `Private Certificate Authority`

* *`ClusterIssuer` with LetsEncrypt and DNS validation.
```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-route53
spec:
  acme:
    email: admin@vettom.online
    server: https://acme-staging-v02.api.letsencrypt.org/directory   # Staging server
    # server: https://acme-v02.api.letsencrypt.org/directory   # Production service
    privateKeySecretRef:
      name: letsencrypt-route53
    solvers:
      - selector:
          dnsZones:
            - vettom.online  
        dns01:
          route53:
            region: eu-west-1
```
- acme.server:                URL of ACME server. Always start with staging URL and verify before switch to production URL. 
- acme.privateKeySecretRef:   Secret file holding Certificate `PrivateKey`
- solvers.selector:           Specify domain for which cert-manager will issue certificate.
- solvers.dns01.route53:      Challenge type to be used. 

**Step 7.**

Create Gateway resource with cert-manager annotation and TLS section. Below example configures gateway to listen for http and https traffic. HTTPS traffic is defined with wildcard domain name `*.vettom.online` and cert-manager annotation added to Gateway itself. Issued certificate will be stored in secret called `mygateway-cert` in namespace `gateway`.

Sample Gateway app can be found in my repo [aws-eks-terraform](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Cluster-generic/ext-gateway). To learn more about configuring Gateway API check [https://vettom.github.io/Eks/eks-cluster-karpenter/](https://vettom.github.io/Eks/eks-cluster-karpenter/)

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: external-gateway
  namespace: gateway
  annotations:
    argocd.argoproj.io/sync-options: SkipDryRunOnMissingResource=true
    cert-manager.io/cluster-issuer: letsencrypt-route53
spec:
  gatewayClassName: external-gatewayclass
  listeners:
    - name: http
      port: 80
      protocol: HTTP
      allowedRoutes:
        namespaces:
          from: All
    - name: https
      hostname: "*.vettom.online"
      port: 443
      protocol: HTTPS
      allowedRoutes:
        namespaces:
          from: All
      tls:
        certificateRefs:
          - kind: Secret
            group: ""
            name: "mygateway-cert"
            namespace: gateway
```
**Step 8.**

Deploy an application with respective HTTP route and validate. A sample app can be found in repo [aws-eks-terraform/EKS-Cluster-generic/Sample-app](https://github.com/vettom/aws-eks-terraform/blob/main/EKS-Cluster-generic/Sample-app/cert-gatewayapi.yaml)

## Terraform code for IAM role and Pod Identity
![terraform](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/terraform.png){: style="height:100px;width:100px"  align="right" }
```json
# IAM policy to enable DNS record creation and lookup
resource "aws_iam_policy" "certmanager_iam_policy" {
  name = "CertManagerIAMPolicy"
  policy = jsonencode(
    {
      "Version" : "2012-10-17",
      "Statement" : [
        {
          "Effect" : "Allow",
          "Action" : "route53:GetChange",
          "Resource" : "arn:aws:route53:::change/*"
        },
        {
          "Effect" : "Allow",
          "Action" : [
            "route53:ListHostedZones",
            "route53:ListHostedZonesByName"
          ],
          "Resource" : "*"
        },
        {
          "Effect" : "Allow",
          "Action" : "route53:ListResourceRecordSets",
          "Resource" : "arn:aws:route53:::hostedzone/*"
        },
        {
          "Effect" : "Allow",
          "Action" : "route53:ChangeResourceRecordSets",
          "Resource" : "arn:aws:route53:::hostedzone/*"
        }
      ]
    }
  )
}
# IAM role for cert manager 
resource "aws_iam_role" "certmanager_iam_role" {
  name               = "cert-manager-iam-role"
  assume_role_policy = data.aws_iam_policy_document.podidentity.json
}
# Attach policy to IAM role
resource "aws_iam_role_policy_attachment" "certmanager_policy_attachement" {
  role       = aws_iam_role.certmanager_iam_role.name
  policy_arn = aws_iam_policy.certmanager_iam_policy.arn
}
# Associate Pod identity with IAM role
resource "aws_eks_pod_identity_association" "certmanager" {
  cluster_name    = module.eks.cluster_name
  namespace       = "cert-manager"
  service_account = "cert-manager"
  role_arn        = aws_iam_role.certmanager_iam_role.arn
}
```
