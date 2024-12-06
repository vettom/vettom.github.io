# Build container Mac ARM

This document will explain how to build containers on you Silicon Mac using [Podman](https://vettom.github.io/Container/podman/#build-and-run-pod). Publishing containers to Dockerhub/Github repo, also building containers that are X86 compatible

### Architecture
Building and running containers and running them on cloud comes with some challenges. To start  with most container orchestrations are build on X86 servers, though there are more services starting to offer ARM servers. [AWS Graviton](https://aws.amazon.com/pm/ec2-graviton/?trk=b65f25fa-06a7-4db5-ad82-b3038b0f87ff&sc_channel=ps&ef_id=Cj0KCQiAire5BhCNARIsAM53K1j3y6IA1rNhjAmFD0QjvOYHxGwKJITX8KuX7zydy5WC7Lf2Lr1s8NUaAoxuEALw_wcB:G:s&s_kwcid=AL!4422!3!581117978349!e!!g!!aws%20graviton!13377830137!131827885183&gclid=Cj0KCQiAire5BhCNARIsAM53K1j3y6IA1rNhjAmFD0QjvOYHxGwKJITX8KuX7zydy5WC7Lf2Lr1s8NUaAoxuEALw_wcB) instances are ARM based, and [Karpenter autoscaler](https://karpenter.sh/) on EKS supports provisioning of Graviton instances. Another thing to consider is when you build and push the container to the registry for Deployments. If you are on Silicon Mac, by default container is built for ARM architecture. You must make sure to specify architecture during build process if intending to build X86 compatible containers.

### Tooling Podman
Docker containers are licensed product if you are trying to install on Business desktop. A good alternative is to use [Podman](https://vettom.github.io/Container/podman/#build-and-run-pod) which is compatible with other OCI compliant container formats including Docker. It is lightweight, secure and Opensource.

## Build container (X86 Architecture)

- create a Dockerfile with necessary instructions
```bash
FROM alpine:latest
RUN apk update && apk add \
	less \
	curl \
	&& rm -rf /var/cache/apk/* \

CMD ["tail", "-f", "/dev/null"]

```

- Build container for respective Arch
```bash
podman build --arch amd64 .  -t myapp
# On arm mac, default to ARM arch
podman build  .  -t myapp_arm
#Check new image
podman images
# Verify architecture of respective image
podman inspect localhost/myapp | grep Architecture
```

- Test pod locally
```bash
podman run -ti --rm localhost/myapp_arm /bin/sh
```

- Log on to registry.
```bash
# Check if you are already logged on to a registry
podman login --get-login docker.io 
# Logon to docker hub
podman -u <USER> docker.io
# Push  image to docker registry

podman push <image id> docker://docker.io/$USER/myapp
```

## Build image from modified container
Sometimes you make updates to container and like to build an image from the modified container.
```bash
# List and identify your container
podman ps -a
# Create a commit from updated pod
podman commit myapp
```
This should result in a new image being created with no tags. 
```bash
podman images
REPOSITORY                TAG         IMAGE ID      CREATED        SIZE
<none>                    <none>      2f2e4b0f23e8  4 seconds ago  12.3 MB
docker.io/library/alpine  latest      c157a85ed455  2 months ago   9.11 MB
```
Next step is to tag this image
```bash
podman tag <imageid>  newapp
```