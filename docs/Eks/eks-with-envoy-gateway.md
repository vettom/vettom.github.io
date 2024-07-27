# Gateway API (Envoy) with EKS network loadbalancer
[Gateway API](https://gateway-api.sigs.k8s.io/) is the next generation of Kubernetes Ingress, Load Balancing, and Service Mesh APIs. In this document I will focuss on Ingress traffic [North/south](https://gateway-api.sigs.k8s.io/concepts/glossary/#northsouth-traffic) traffic implementation using [Envoy Gateway Fabric](https://gateway.envoyproxy.io/).

Gateway API contains 3 resources
- GatewayClass : Defines type of Gateway (internal/external), custom setting specific to Cloud provider and implementaiton software
- Gateway : Enables creation of Loadbalncer and listener creation. Each gateway creates a new Loadbalancer.
- HTTPRoute : Defines rules for routing traffic like backend, redirects, canary, traffic mirroring etc.
<img src="https://vettom.github.io/Eks/img/envoy-gateway" width="600" height="300">