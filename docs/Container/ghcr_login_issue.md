# GitHub container registry
# Site moved to [https://vettom.pages.dev](https://vettom.pages.dev)
## Error pulling image/chart from ghcr.io
When attempting to install package, or download image from Ghcr.io it can throw following error on Mac. Standard GH login, and docker login also can return same error. This is due to docker trying to store credential in keychain.

`error getting credentials - err: exec: "docker-credential-osxkeychain": executable file not found in $PATH, out:`

### Solution
Install `docker-credential-osxkeychain` and log on to docker 
```bash
brew install docker-credential-helper
# Use GH auth or docker auth
gh auth token | helm registry login ghcr.io -u vettom --password-stdin
# Docker login
docker login
# Once authenticated, you can install your app
helm install eg oci://docker.io/envoyproxy/gateway-helm --version v1.0.2
```