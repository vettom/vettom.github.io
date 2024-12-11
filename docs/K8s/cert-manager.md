# Cert-manager with LetsEncrypt
![cert-manager](https://vettom-images.s3.eu-west-1.amazonaws.com/kubernetes/certmanager.png){: style="height:150px;width:150px" }
![cert-manager](https://vettom-images.s3.eu-west-1.amazonaws.com/kubernetes/letsencrypt.png){: style="height:150px;width:150px" }

[Cert-Manager](https://cert-manager.io/) is a powerful and extensible X.509 certificate controller for Kubernetes platforms. It will obtain certificates from a variety of Issuers, both popular public Issuers as well as private Issuers, and ensure the certificates are valid and up-to-date, and will attempt to renew certificates at a configured time before expiry. It also exposes metrics to enable monitoring certificate issues and expiry.

### Values file
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

`helm install  cert-manager cert-manager -n cert-manager  --create-namespace --repo https://charts.jetstack.io/ --version v1.16.2 -f values.yaml`

## Cluster Issuer
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