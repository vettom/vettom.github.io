---
draft: false 
date: 2024-06-20
authors: ["vettom"]
categories:
  - podman
  - container
  - docker desktop
  - docker
---
## Build container without Docker (Mac/Linux/Windows)
![alt text](img/podman.png "podman logo"){ width="300" }

[Podman](https://podman.io/) is an opensource tool to manage all your container needs without requiring Docker or Docker desktop. It has same command sets as Docker and it does not require Daemon.

    - Opensource
    - Fast and light
    - Secure : Rootless container allow to contain privileges
    - Compatible : Supports OCI compliant containers including Docker

#### Install Podman 
```bash
brew install podman
# On Mac, each Podman machine is backed by a virtual machine
# After installing, you need to create and start your first Podman machine
podman machine init
podman machine start
podman info
```
#### Simple Flask app
```python
from flask import Flask
app = Flask(__name__)
@app.route('/')
def hello():
    return "My flask container"
if __name__ == '__main__':
    app.run()
```
#### Prepare Docker file
You can use most Docker commands with podman. 
```dockerfile
FROM python:3.10-slim
WORKDIR /app
COPY . /app
RUN pip install --trusted-host pypi.python.org Flask
EXPOSE 5000
CMD ["flask", "run", "--host=0.0.0.0"]
```
#### Build and run pod
```bash
# Build image from FLask App
podman build -t myflask:latest .
# Check your images
podman images
# Run your container
pod podman run localhost/myflask:latest -p 5000:5000
# Verify the site
curl http://localhost:5000/
# Check running containers
podman ps (-a to list all including stopped)
```
## Command references
| Commands | Descreption | 
| ------------- | ------------- |
|podman ps -a|List all containers including stopped|
|podman run -p 5000:5000 -d <image> | Run container in Daemon mode and portforward|
|podman image tag <imageid> newtag:version|Create new Tag for image|
|podman image prune -a| Delete al the images|
|podman inspect <imageID>| Inspect a container image|
|podman container rm -f $(podman container list -aq)|Remove all pods including stopped|
|podman image rm -f $(podman image ls -q)|Remove all images from the system|
