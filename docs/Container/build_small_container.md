# Optimized container build

# Site moved to [https://vettom.pages.dev](https://vettom.pages.dev)
![container builder process ](https://vettom-images.s3.eu-west-1.amazonaws.com/kubernetes/container_build.jpg)


Building small container images has several significant advantages, especially for cloud-based and microservices environments. some of them are

- Faster deployment and startup time
- Lower storage and Bandwidth costs
- Improved security
- Resource efficiency 
- Easier to debug and audit


## Techniques for Building Smaller Containers

- Using distroless/minimal base images (minimal,alpine, slim version)
- Multistage build (Builder process). Only necessary files copied in to image. 
- Minimizing the number of layers
- Understanding caching
- Using Dockerignore
- Keeping application data elsewhere

![Alpine Linux logo ](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/alpine-linux.png){: style="height:150px;width:150px" align=right }
### Distroless / Alpine image
[Distroles images](https://github.com/GoogleContainerTools/distroless) contain only your application and its runtime dependencies. They do not contain package managers, shells or any other programs you would expect to find in a standard Linux distribution. Most common applications have a distroless image available to download
Alternatively, start with Alpine, minimal, or slim version of application images

### Multistage build. Builder pattern
![Reduce containe imagesize ](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/image-reduce.jpg){: style="height:150px;width:250px" align=right }
Most compiled languages requires lots libraries during build which can be huge. Use multi stage build to build with one container and create new application container with compiled application only.
```dockerfile
# Stage 1: Build the Go binary
FROM golang:1.18-alpine AS builder
WORKDIR /app
# Copy the Go Modules manifests
COPY go.mod ./
# Download Go modules
RUN go mod download
# Copy the source code
COPY . .
# Build the Go binary
RUN go build -o myapp
# Stage 2: Create the final image
FROM alpine:latest
WORKDIR /app
# Install certificates to ensure the application can make HTTPS requests
RUN apk --no-cache add ca-certificates
# Copy the binary from the builder stage
COPY --from=builder /app/myapp .
# Command to run the binary
CMD ["./myapp"]
```

### Minimize the Number of Layers
Docker images work in the following way – each RUN, COPY, FROM Dockerfile instructions add a new layer & each layer adds to the build execution time & increases the storage requirements of the image.
Reduce number of layers by combining instructions
```Dockerfile
FROM ubuntu:latest
RUN apt-get update -y && \
    apt-get upgrade -y && 
```

### Understanding Caching
As Docker uses layered filesystem, each instruction creates a layer. Due to which, Docker caches the layer and can reuse it if it hasn’t changed.

Due to this concept, it’s recommended to add the lines which are used for installing dependencies & packages earlier inside the Dockerfile – before the COPY commands.

The reason behind this is that docker would be able to cache the image with the required dependencies, and this cache can then be used in the following builds when the code gets modified.

### Use .dockerignore
Only necessary files must be copied to image, use .dockerignore to skip unwanted files

### Keep Application Data Elsewhere
If application require data, ideally this can be in a shared volume or mounted as volume than include in the image.

## Tools
 - [SlimToolkit](https://devopscube.com/slimtoolkit-to-shrink-docker-images/) is a tool that helps to optimize Docker images by removing unnecessary layers and files.
 - [Dive](https://github.com/wagoodman/dive) A tool for exploring a docker image, layer contents, and discovering ways to shrink the size of your Docker/OCI image.
 - [Docker Squash](https://docs.docker.com/reference/cli/docker/image/build/#squash)  `--squash` Once the image is built, this flag squashes the new layers into a new image with a single new layer.

### Building small container by Google

 <iframe width="560" height="315" src="https://www.youtube.com/embed/wGz_cbtCiEA?si=v_2MCWqta2_cJk7a" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>