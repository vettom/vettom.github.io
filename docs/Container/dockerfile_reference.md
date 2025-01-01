# Dockerfile reference
# Site moved to [https://vettom.pages.dev](https://vettom.pages.dev)
| Commands | Description | 
| ------------- | ------------- |
|COPY  | Copy files in to the image|
| Volume    |Creates a Mountpoint as defined|
|ENTRYPOINT|The executable that runs when container is run. It accepts input parameters, or takes from CMD|
|CMD| Command to execute at start, will ignore input parameters. If ENTRYPOINT is defined CMD arguments are passed as arguments. Only 1 command allowed,  only last one will take effect.|
|EXPOSE|Defines ports to be published.|
|ENV|Variables to be defined in the container.|
|RUN  |A command to be run in a new layer of Image being build|
|FROM|Define base image from which image is built. This is first instruction.|
|WORKDIR |Working Directory of container|

### Difference between CMD and ENTRYPOINT
Both Entry point and CMD execute instructions at start of container, however behaviour differ. 
CMD : When only CMD is specified, it executes command and will ignore any parameters passed. 
ENTRYPOINT: Defines command to execute at start, then inputs are passed as arguments. If CMD also specified,  ENTRYPOINT will treat CMD as arguments than commands.

Use of CMD is preferred unless there is requirement to accept arguments for container run. 