# Getting started with Docker and Kubernetes

## What are things that need to be done?

- Get a basic project which can be deployed, it may be a simple `node.js` project or anything else.
- Download [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- Know how to use commands in terminal ,shell or whatever.

There are two ways to do so :
- Clone this repository or
- Do it all yourself, step-by-step

## Creating a basic project

I will be using a [`node.js`](https://nodejs.org/en/) server for my project. There are only a few things that need to be done, so these are what need to be done

1. Create a new directory for demonstration purpose, where I am gonna keep my source code
2. Execute command `npm init`, to get a *package.json* file setupped.
3. Create a file `index.js` in the same directory and add the code below

    ```js
    const express = require("express");
    const path = require("path");

    const app = express();
    const port = process.env.PORT || 8080

    app.get('/', function(req,res){
        res.sendFile(path.join(__dirname, '/index.html'));
    });

    app.listen(port);
    console.log("Server started at http://localhost:"+ port);
    ```

    All it does it creates a express server and renders it with a `index.html` in the web-browser.

    I used `express` and `path` packages, but I haven't installed those, to install do

    ```shell
    $ npm i express path
    ``` 
4. Also creating a `index.html` for rendering as well, it can contain anything, let's say
    ```html
    <html>
        <body>
            <h1>Simple Node Application</h1>
        </body>
    </html>
    ```
5. Now, execute `npm index.js` to start the node server, and open the [link](http://localhost:8080), received from this command in a web-browser, if it works it should say - __Simple Node Application__, everything worked well.

6. Now, having for this application !

## Adding docker setup

> What is docker?

Docker is a tool, which packs the application or software into a 
bundle into an image that works everywhere it will be used !

There are two things in docker -
- __Container__ - It contains a working environment for our application.
- __Image__ - It contains a information about the environment, our application.

## How to use it in our application ?

To use this, we need to open our docker container and just expose(open) it's `PORT` to our system, and use our web-browser to check if it runs or not.

To do this, doing following 

1. Create a `Dockerfile` inside our application's source code and add this into it.
    
    ```Dockerfile
    FROM node:latest

    LABEL MAINTAINER="ymhere"

    COPY . /src

    RUN cd /src; npm install

    EXPOSE 8080

    CMD cd /src; node app.js
    ```
    This is info required for creating an __Docker image__. Check the [docs](https://docs.docker.com/engine/reference/builder/).

    - __FROM__ uses the latest version of node server for our application
    - __LABEL MAINTAINER__  sets the Author field of the generated images.
    - __COPY__ copies new files or directories from `<src>` and adds them to the filesystem of the container at the path `<dest>`.
    - __RUN__  executes the operation for docker system image,in our case changes directory and install node modules
    - __EXPOSE__ Exposes the given PORT.
    - __CMD__ runs the given commands for our application inside docker.

2. Build a docker image for given configeration using
    ```shell
    $ docker build -t iumo/dimg:1.0 .
    ```
    Here `dimg` is our image's name and `iumo` is the author of the image, 1.0 is the version number of docker image. This command creates a docker image, which can be seen in docker desktop images.

    But, it doesn't runs our application at [localhost](http://localhost:8080), it will run after next step.

3. Running an image, needs a environment, a *__container__*. This can be done with `docker run iumo/dimg:1.0`

    But if I open the `localhost` like before, it does nothing, because we didn't link docker application's port with our system port, do this with next command, but I need to stop the current running environment, do this by going to  docker desktop and stop the container or running the command `docker stop <container-name/id-from-docker-desktop>`

    Re-creating a container with its port linked to our system.
    `docker run -p 8080:8080 iumo/dimg:1.1`, it is linking port 8080 in our system with docker application.
    
    Now, open [localhost](http://localhost:8080), and you can see it working !! Try doing it few more times to familiarize with it!

4. Wait! There's more to it !! Somehow I accidently closed/stopped the container, our application stops running too !!!

    Who wants that ?! So here comes __Kubernetes__ in action, next section discuss more about Kubernetes!


What if I want somebody else to use my docker system and have a repository for it at __Docker Hub__, like `git push` we do
```shell
$ docker push iumo/dimg:1.1
```

Similarly, I want somebody else's docker system from __Docker Hub__, we do pull like `git pull`,

```shell
$ docker pull iumo/dimg2:1.0
```

So till now, what we did was `containerize` everything ! This is what it is called !

## Now, let's go with Kubernetes !!!!

Till now, we containerized, but it came out in one-box and if it failed, everything fails. So here comes a [`Kubernetes`](https://kubernetes.io/docs/home/) a ___*Orchestration*___ tool for automating deployment, scaling, and management of containerized applications. And it's open-source supported by google!

> Fun Fact 😎: Kubernetes is also called `k8s`

Enough with it, let's get our hand's dirty.

- Firstly, we need *__Kubernetes__* itself! For our sake our download docker desktop already has it, so we just need to enable it and download a command line utility [`kubectl`](https://kubernetes.io/docs/tasks/tools/) for it.

    > Check if `kubectl` works by executing this in terminal, if it works by will see some output
    >
    > `$ kubectl`

- Like *__Dockerfile__*, we need two **YAML** files here, namely *`pod.yaml`* and *`service.yaml`*.

    >Note : In **YAML** files, casing,syntax and indentation matters a lot unline __json__.
        
    <h4><code>pod.yaml</code></h4>

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
    name: pod-1
    labels:
        event: podfile
    spec:
    containers:
    - name: node
        image: iumo/dimg:1.1
        ports:
        - containerPort: 8080
    ```
        
    <h4><code>service.yaml</code></h4>

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
    name: service-local
    spec:
    type: NodePort
    ports:
        - port: 8080
        protocol: TCP
        targetPort: 8080
        nodePort: 30001
    selector:
        event: podfile
    ```

    The __pod.yaml__ is like container general information and __service.yaml__ is like a service that a container from pod needs, but what is a pod?
    
    Pod is a small worker unit in __k8s__ world and a group of pods is called cluster and what manages it are different API integrations namely _services_ !!

- We created __k8s__ files, gets start it then !
    - Open terminal, execute
        
        ```kubectl apply -f pod.yaml```
        
        For starting our pod(container), unlike docker it won't our application right now, we need a manager, __service__.

    - Start a service by executing
    
        ```kubectl apply -f service.yaml```

    - Now, if you open [localhost](http://localhost:30001) at PORT `30001`, it will be running.
    
        This time port is not __8080__ due to node's security reasons, instead we used PORT `30001` in `service.yaml`. PORT can be 30000 <= PORT <=32767.
    - Also to see current __Pods__ and __services__ do

        ```shell
        $ kubectl get pods
        ```
        ```shell
        $ kubectl get svc
        ```
        >` kubectl get pods --watch`  --watch option continuosult monitors pods(services if given).
    
    
    > You can also the container and image in docker desktop running, what if you try and delete that container from docker desktop, and load your localhost again, for some fraction of seconds, localhost stops working, after it again starts working!! That's self-healing in __k8s__. 
    - Delete the pods and services after the work is done with

        ```shell
        $ kubectl delete pod <pod-name>
        ```
        
        ```shell
        $ kubectl delete svc <service-name>
        ```
        > __pod-name__ in `pod.yaml` $\rightarrow$ _pod-1_
        >
        > __service-name__ in `service.yaml` $\rightarrow$ _service-local_



And that's how __*k8s*__ was setupped for our project !