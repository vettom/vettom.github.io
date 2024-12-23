# Cert-manager with LetsEncrypt
![cert-manager](https://vettom-images.s3.eu-west-1.amazonaws.com/kubernetes/certmanager.png){: style="height:150px;width:150px" }
![cert-manager](https://vettom-images.s3.eu-west-1.amazonaws.com/kubernetes/letsencrypt.png){: style="height:150px;width:150px" }
![EKS logo ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/amazon_eks.png){: style="height:200px;width:200px"  }
![Route53](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/route53.jpg){: style="height:150px;width:150px"  }

### **Free SSL certificate with LetsEncrypt**

[Cert-Manager](https://cert-manager.io/) is a powerful and extensible X.509 certificate controller for Kubernetes platforms. It will obtain certificates from a variety of Issuers, both popular public Issuers as well as private Issuers, and ensure the certificates are valid and up-to-date, and will attempt to renew certificates at a configured time before expiry. It also exposes metrics to enable monitoring certificate issues and expiry.

## Configure Cert-manager
There 3 parts to configuring Cert-manager on your Kubernetes cluster. In this example, I have EKS cluster and domain configured in Route53. So I will configure DNS validation for Certificate issue and signing.  [Lets Encrypt](https://letsencrypt.org/) will provide free certificate independent of cluster provider. Check cert-manager for supported certificate issuers. 

1. Configure authentication (Pod Identity to modify DNS entry)
2. Install Cert-manager with custom values
3. Configure **clusterIssuer** or **Issuer** to sign your certificates

## Authentication : IAM policy and Pod Identity

**Step 1.** 

Create IAM policy that allows creation of DNS records. This is required for Certificate issuer to perform [Domain Validation](https://letsencrypt.org/how-it-works/#domain-validation). LetsEncrypt provides two ways for Domain Validation, `DNS record under your domain` or `HTTP resource under specific path`. In this example I will configure DNS validation, hence IAM policy with DNS record update permission.

* *IAM policy `certmanager_iam_policy` allowing DNS record creation*
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

Create IAM role `cert-manager` with `Pod Identity` trust policy and attach `certmanager_iam_policy`

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
In EKS create Pod Identity Association for namespace `cert-manager` and service account `cert-manager` to IAM role `cert-manager`

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

- installCRDs:      CRD's are not installed by default, so this argument must be true.
- config:           Current version support `Gateway API` in `v1alpha1` spec.
- serviceAccount:   SA to be used by Cert-manager installation. Must match Pod Identity spec.

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

## Terraform code for IAM role and Pod Identity
![terraform](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/terraform.png){: style="height:100px;width:100px"  align="right" }

