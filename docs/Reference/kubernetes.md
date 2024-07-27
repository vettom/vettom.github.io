# Kubernetes

###  Gateway API resource
```bash
# Get API gateway resources
 k api-resources --api-group gateway.networking.k8s.io
# Check CRD version and release channel. Check annotations
 helm show crds oci://docker.io/envoyproxy/gateway-helm --version v1.0.2 | less

```

aws secretsmanager  --profile cadm-prd get-secret-value --secret-id eks/github_token --region eu-west-2  | jq --raw-output '.SecretString' | jq -r .Argocd-RayoAudioBotToken