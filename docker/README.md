# DOCKER 

## What is docker ?

It is a tool designed to make it easier to create, deploy, and run applications by using containers. Containers allow a developer to package up an application with all of the parts it needs, such as libraries and other dependencies, and ship it all out as one package.

### What is a container ?

A container is a environment where all your application dependencies are installled. It is an isolated enviroment needed for your application to run.

### What is a image ?

A image is a template for creating a container. It is a read-only file that contains a set of instructions for creating a container that can run for your application.

## Using Docker

> Installation Requirements

- For Windows:
    1. WSL2 (preferred with Ubuntu installed in it)
    2. Docker Desktop (enable WSL2 support for it, with exposed daemon)

- For linux
    1. Directly install from [official](https://docs.docker.com/desktop/install/linux-install/) site.


When done, run your terminal and execute `docker`, if it gives a bunch of output then you are good to go.
Else check the installation guide again for any issues.

### Docker commands

1. `docker ps` - Lists all the containers running on your system.

2. `docker images` - Lists all the images available on your system.

3. `docker container ls` - Lists the containers

4. `docker stats` - Gives a dynamic report of the machine stats used by the docker containers.

5. `docker pull <image-name>` - Pulls a image from the docker hub with given _image name_.

6. `docker login` - Before pushing a image to the docker hub, you will need to login to the docker hub using this command.

6. `docker push <formatted-image-name>` - Pushes the image to the docker hub with given _formatted image name_ -> __iumo/mypython:1.0__



6. Docker image commands

    - `docker image rm -f <image-id/image-name>` : Removes the image with given _image id_ or _image name_ forcefully.

    - `docker image build -t <image-name> .` : Builds a image with given _image name_ from the current directory using the ___Dockerfile___.

6. Docker container commands

    - `docker container start <container-id/container-name>` - Starts a existing container with given _container id_ or _container name_.

    - `docker container stop <container-id/container-name>` - Stops a running container with given _container id_ or _container name_.

    - `docker container run -d <image-name/image-id>` - Runs a container with given _image name_ or _image id_ in the background.

    - `docker container run -it Ubuntu` - `-it` flag opens the interactive terminal for the container.

    - `docker container run -p 8080:80 nginx` - `-p` flag maps the user's machine port 8080 to the container's port 80 for any request.

7. `docker exec -it <container-id/container-name> bash`- Opens a interactive terminal for the running container with given _container id_ or _container name_.


## Dockerfile

Dockerfile is a batch file. A file with series of instructions to build a docker image of given environment

A project can have multiple modules, but each module can have only one __Dockerfile__ with this name __only__.

### Example

Creating a simple UNIX alpine working enviroment to see today's date

```dockerfile
FROM alpine:latest

CMD ["date"]
```

- `FROM` - Tells the docker to use the given image as the base image for the current image.

- `CMD` - Tells the docker to execute the given command when the container is run. This can only be used __once__ in a Dockerfile.

To build a image, do

```bash
docker image build -t iumo/myalpine-image:1.0 .
```

To verify if the image is created, do `docker images`, and you should see the image with given name.

To run the image, do

```bash
docker container run iumo/myalpine-image:1.0
```

But, it stops the container when done, add `-d` flag to it to run in background.

To verify, open another terminal and do `docker ps`, you should see the container running.

When done do this to stop the container, do `docker container stop <container-id>`. (Container-id from ___docker ps___)
