---
draft: false 
date: 2024-07-18
authors: ["vettom"]
categories:
  - argocd
---

# ArgoCD how to reset Admin password.
ArgoCD Admin account credentials are stored in `argocd-secret` and stored as k8s secret in `argocd-initial-admin-secret`. For security, you must remove `argocd-initial-admin-secret` whle disabling `admin` account. Admin password in `argocd-initial-admin-secret` is stored as one way hash (.htpassword) and hence cannot be decrypted.
### Recovery steps
 - Enable Admin account and update argocd. `configs.cm.admin.enabled: true`
 - Remove admin.password from secret `argocd-secret` by editing or by running below command
 - Delete/redeploy argocd-server pod to get new admin password

#### Removing admin password
`kubectl patch -n argocd cm argocd-cm --type='json' -p='[{"op": "remove", "path": "/data/accounts.alice"}]'`
Example argocd-secret
```yaml
apiVersion: v1
kind: Secret
  name: argocd-secret
  namespace: argocd
type: Opaque
data:
  admin.password: JDJhJDEwJHhFWTNDNGJwa0ocFdWL2VCZUxHaW1oLnV5TVlzaGdhb3ZxSVE5ZklL
  admin.passwordMtime: MjAyNC0wNi0xOFQxMDowNzo0OVo=
  server.secretkey: K1ZCZlpEeWYwMFpjUzV5NG5tTUROOFllS0plYz0=
```