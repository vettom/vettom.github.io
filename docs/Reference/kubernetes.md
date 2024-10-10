# Kubernetes

###  Gateway API resource
```bash
# Get API gateway resources
 k api-resources --api-group gateway.networking.k8s.io
# Check CRD version and release channel. Check annotations
 helm show crds oci://docker.io/envoyproxy/gateway-helm --version v1.0.2 | less

```

### Retrieve k8s secret with plugin
Install `kubectl-view_secret` plugin using [krew](https://github.com/kubernetes-sigs/krew)
`kubectl view-secret -n argocd argocd-initial-admin-secreta`