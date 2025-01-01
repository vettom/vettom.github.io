# Gateway API (Envoy) with EKS network loadbalancer
# Site moved to [https://vettom.pages.dev](https://vettom.pages.dev)
[Gateway API](https://gateway-api.sigs.k8s.io/) is the next generation of Kubernetes Ingress, Load Balancing, and Service Mesh APIs. In this document I will focus on Ingress traffic [North/south](https://gateway-api.sigs.k8s.io/concepts/glossary/#northsouth-traffic) traffic implementation using [Envoy Gateway Fabric](https://gateway.envoyproxy.io/).

<iframe width="560" height="315" src="https://www.youtube.com/embed/pMNWyL7S-R0?si=1uYgVuE0UPIqGku6" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

**Gateway API resources**

- GatewayClass : Defines type of Gateway (internal/external), custom setting specific to Cloud provider and implementation software
- Gateway : Enables creation of Loadbalncer and listener creation. Each gateway creates a new Loadbalancer.
- HTTPRoute : Defines rules for routing traffic like backend, redirects, canary, traffic mirroring etc.

## Implement Envoy gateway Fabric

![Prometheus Alertmanager](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/envoy-gateway.jpg){: style="height:300px;width:600px" }

### Setup
Create an EKS cluster with VPC-CNI and AWS Loabalancer controller installed. Complete terraform code to implement can be found at [EKS-Envoy-Gateway](https://github.com/vettom/aws-eks-terraform/tree/main/EKS-Envoy-Gateway) folder in [https://github.com/vettom/aws-eks-terraform](https://github.com/vettom/aws-eks-terraform) repo.

### Install Envoy gateway Fabric
`helm install gateway oci://docker.io/envoyproxy/gateway-helm --version v1.0.2 -n gateway --create-namespace`

### Create EnvoyProxy to create NLB
Envoy gateway Fabric spins up Envoy Proxy instances per Gateway. By applying `EnvoyProxy` configuration, you can control behaviour and define annotations required for AWS Loadbalancer controller.  In this configuration, an external facing Network loadBalancer will be created with target type of IP. This will only work if `AWS Loadbalancer Controller` and `VPC-CNI` is installed and configured.

```yaml
apiVersion: gateway.envoyproxy.io/v1alpha1
kind: EnvoyProxy
metadata:
  name: external-proxy-config
  namespace: gateway
spec:
  provider:
    type: Kubernetes
    kubernetes:
      envoyService:
        annotations:
          service.beta.kubernetes.io/aws-load-balancer-nlb-target-type: ip
          service.beta.kubernetes.io/aws-load-balancer-scheme: "internet-facing"
          service.beta.kubernetes.io/aws-load-balancer-target-group-attributes: preserve_client_ip.enabled=true
```
### Create a GatewayClass and configre to use Proxy configuration
Envoy `GatewayClass` can be configured to use `EnvoyProxy` configuration. Any gateway created using this `GatewayClass` will inherit configuration

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: external-gatewayclass
spec:
  controllerName: gateway.envoyproxy.io/gatewayclass-controller
  parametersRef:
    group: gateway.envoyproxy.io
    kind: EnvoyProxy
    name: external-proxy-config
    namespace: gateway
---
```
### Create Gateway and listeners
Creation of `Gateway` will result in provisioning of NLB and `Envoy Proxy` pod to handle requests. Define required Listeners, and if using [Cert-manager](https://cert-manager.io/) for certificate management, add necessary annotation. If defining HTTPS, you must define hostname and provide certificate information.

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: external-gateway
  namespace: gateway
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-DNS
spec:
  gatewayClassName: external-gatewayclass
  listeners:
    - name: http
      port: 80
      protocol: HTTP
      allowedRoutes:
        namespaces:
          from: All
    # - name: https
    #   hostname: "mygateway.vettom.pages.dev"
    #   port: 443
    #   protocol: HTTPS
    #   allowedRoutes:
    #     namespaces:
    #       from: All
    #   tls:
    #     certificateRefs:
    #       - kind: Secret
    #         group: ""
    #         name: "mygateway-cert"
    #         namespace: gateway
```

### Add HTTPRoute for your application
Define `HTTPRoute` to specify how to route traffic to your application. In `parentRefs` section define which `Gateway` to attach to. Refer [HTTPRoute](https://gateway-api.sigs.k8s.io/api-types/httproute/) to find out various options available.

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: dvenvoy-http
  namespace: echoserver
spec:
  parentRefs:
    - name: external-gateway
      sectionName: http
      namespace: gateway
  hostnames:
    - mygateway.vettom.pages.dev
    - mygateway1.vettom.pages.dev
  rules:
    - backendRefs:
        - kind: Service
          name: echoserver
          port: 80
      matches:
        - path:
            type: PathPrefix
            value: /
```