# GitHub Container Registry with Kubernetes

![GHCR logo](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/ghcr.png){: style="height:100px;width:100px" align=right }

Github provides an OCI standard container registry allowing users and organizations to store container images. A Github  access token to publish, install, and delete packages, also token is required to pull private image in to Kubernetes.

In order for kubernetes to authenticate with Github container registry, you must create a `dockerconfigjson` secret in respective namespace.

## Pro's and Con's
**Pro's**

[External Secrets Operator ](https://external-secrets.io/latest/guides/common-k8s-secret-types/) can be used to create secret from External secret store.

**Con's

Secret must be created in each namespace requiring to pull image. There are no options to create Cluster wide secrets or good mechanism to replicate secrets.

### Step 1 : Generate Github token
In `Developer Settings` generate a `Token(classic)` with `read:packages` permission allowing to download images. Copy the access Token.

### Step 2 : build .dockerconfigjson file
Generate Base64 encoded username:PAT combination
```bash
 echo vettom:ghp_ZpFFCDGbNbTirEd4fsM8C | base64
 dmV0dG9tOmdocF9acEZGQ0RHYk5iVGlyRWQ0ZnNNOEMK
```
Using encrypted username and Pat, create .dockerconfigjson file
```json
{
    "auths":
    {
        "ghcr.io":
            {
                "auth":"dmV0dG9tOmdocF9acEZGQ0RHYk5iVGlyRWQ0ZnNNOEMK"
            }
    }
}
```
Encode .dockerconfigjson in to base64 encoded entry
```bash
cat .dockerconfigjson | base64 
ewogICAgImF1dGhzIjoKICAgIHsKICAgICAgICAiZ2hjci5pbyI6CiAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICJhdXRoIjoiZG1WMGRHOXRPbWRvY0Y5YWNFWkdRMFJIWWs1aVZHbHlSV1EwWm5OTk9FTUsiCiAgICAgICAgICAgIH0KICAgIH0KfQo=
```

### Step 3 : Create secret from .dockerconfigjson
Secret type `dockerconfigjson` must be created in the namespace where you will be deploying application. I am deploying to `default` namespace.

**ghcr-secret.yaml**
```yaml
kind: Secret
type: kubernetes.io/dockerconfigjson
apiVersion: v1
metadata:
  name: ghcr-secret
  namespace: default
  labels:
    app: ghcr-secret
data:
  .dockerconfigjson: ewogICAgImF1dGhzIjoKICAgIHsKICAgICAgICAiZ2hjci5pbyI6CiAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICJhdXRoIjoiZG1WMGRHOXRPbWRvY0Y5YWNFWkdRMFJIWWs1aVZHbHlSV1EwWm5OTk9FTUsiCiAgICAgICAgICAgIH0KICAgIH0KfQo=
```
Create secret
` kubectl apply -f ghcr-secret.yaml`

### Step 4 : Create deployment with imagePullSecrets
In deployment yaml file, specify GHCR-secret in imagePullSecrets section
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echoserver
  namespace: default
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: echoserver
    spec:
      imagePullSecrets:
        - name: ghcr-secret
      containers:
        - image: ghcr.io/vettom/echoserver:2.5
          imagePullPolicy: Always
          name: demo-echo
          ports:
            - containerPort: 8080
```
