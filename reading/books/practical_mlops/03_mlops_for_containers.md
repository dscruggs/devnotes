# Chapter 3: MLOps for Containers and Edge Devices

Virtual Machines came along and were popular, but moving them around was painful

VMs are still used widely in cloud solutions like EC2 and GCP Compute Engines

Containers and edge devices are other abstractions important to MLOps

## Containers

Has all the power of VMs but with lightweight ability to move and share environments in public and private hubs

Registries allow developers and push changes to a central location where others can pull them and run locally

Containers are all about the application itself and only what the application is (source code, files) vs what it needs to run (databases)

- VMs are typically monolithic (application, database, everything)
- containers are typically used for microservices, or fully independent applications

### Container Runtime

`Container runtimes` are what are used to execute containers

- Docker is the most popular but there are others that are sometimes used
- `Docker` is the most popular and works well on windows, mac, and linux

### Creating a Container

`Dockerfile` is at the heart of creating containers. Anytime you create a container you must have the Dockerfile present in the current directory. It has sections and commands that allow creating a container image

Example:

```Docker
# base image (CentOS distribution version 8. Version 8 is a tag)
FROM centos:8

RUN dnf install -y python38
```

`instructions` are keywords used to perform actions in Dockerfiles

`tags` are used to specify versions. If none is specified `latest` will be the default tag

Containers can be `layered` so that you don't need to run the whole dockerfile download every time. You can have a large `base` image (like an OS) and then just add your instructions on top.

`RUN` runs a system command. `dnf` uses a -y flag which prevents a prompt for confirmation that would halt the build process

#### Building and publishing container

`docker build` is the command line to build a docker image.

`docker images` lets you see all your images

```sh
# builds a dockerfile with repo name and tag specified
docker build -t dockerrepo:tag .
# pushes docker image to docker hub (can specify other container registries somehow I assume)
docker push $(username)/$(destination)
```

### Running a Container

```sh
# docker run runs the container
# -ti is a flag to allocate a TTY (terminal) 
# -d makes the container run in the background to make sure it doesn't take over the current terminal
# --name names the container
# --rm make sure that Docker removes the container after stopping it
# /bin/bash is the command. This will make sure the dockerfile can run for a long time. If you don't specify this it will spin up and shut down immediately
# you can specify ENTRYPOINT ["/bin/bash"] in the dockerfile if you don't want to enter this here
docker run -ti -d --name centos-test --rm centos:8 /bin/bash

# makes sure the container is running. Will list all running containers
docker ps

# accesses a running container
docker exec -it $(CONTAINER_ID) bash
```

You can specify whatever commands you want to run in the docker exec stage. Bash is if you want a command line prompt.

### Container Best Practices

#### Linters

`hadolint` helps identify issues with dockerfiles. It's packaged as a container:

```sh
# will give you warnings and errors about your dockerfile
docker run --rm -i hadolint/hadolint < Dockerfile
```

#### Keeping Dockerfiles small

One of the goals of containerization tools is to keep dependencies as small as possible

- RUN instructions create new layers in executions. The fewer number of layers the smaller the size, so try to combine RUN instructions when possible
  - Example: `RUN apk add --no-cache python3 && python3 -m ensurepip && pip3 install pytest`

#### Vulnerability Scans

It's important to make sure your containers aren't vulnerable

A very fast and easy solution to scan dockerfiles for vulnerabilities is `grype` CLI tool from Anchore

Installation and usage in the command line:
``

You can deploy grype into most CI systems to scan for vulnerabilties

```sh
# installs grype
curl -sSfL https://raw.githubusercontent.com/anchore/grype/main/install.sh | sh -s

# checks for all vulnerabilities in the container
grype python:3.8

# checks only for high risk, because there's 1000s of checks
grype python:3.8 | grep High

# automation to allow failure on high vulnerabilities
grype --fail-on=high centos:8
```

### Serving a Trained Model Over HTTP

Container to serve a trained model over an HTTP API using Flask

```docker
FROM python:3.9

# version using a deprecated label schema convention. Use https://github.com/opencontainers/image-spec
ARG VERSION
LABEL org.label-schema.version=$VERSION

COPY ./requirements.txt /webapp/requirements.txt
WORKDIR /webapp
RUN pip install -r requirements.txt
COPY webapp/* /webapp
# runs the app
ENTRYPOINT [ "python" ]
CMD [ "app.py" ]
```

Project directory structure:

```
Dockerfile
/webapp
    app.py # app
    model.joblib # compressed model
```

Running:

```bash
# building image
docker build --build-arg VERSION=AutoML_287f444c -t flask-predict .

# checking for image
docker images flask-predict

#running the image
docker run -p 5000:5000 -d --name flask-predict flask-predict

# open localhost or curl [IP address]:[port_number] to verify it worked

# any tool that can send info over HTTP can process a response
```

### Edge Devices

Cost of chips and hardware decreasing has increased the feasibility of ML compute on device

The closer the computation to the user, the faster the lower the latency

#### Coral

platform that helps build local on-device inferencing

Lots of examples for using it to build models for edge computing

#### Azure Percept

Another up and coming edge platform

`TFHub` has lots of models to be download, some work on the edge

### Containers for Managed ML Systems

You can list and monetize containerized models on Amazon Sagemaker (probably similar for google and azure)

### Build Once, Run Many MLOps Workflow

You can make base containers for all kinds of workflows, and maybe even productize them

## Exercises

- [x] (Probably never doing this) Recompile a model to work with the Coral Edge TPU from TFHub.
- [x] (Probably never doing this) Use the MobileNet V2 model to perform inference on other objects and get accurate results.
- [ ] Create a new container image, based on the Flask example, that serves a model and that provides examples on a GET request to interact with the model. Create another endpoint that provides useful metadata about the model.
- [ ] Publish the newly created image to a container registry like Docker Hub.

## Critical Thinking Discussion Questions

#### Would it be possible to use a container to perform online predictions with an edge TPU device like Coral? How? or Why not?

You could definitely do this if you have natural labels, but it would be very challenging because you'd need all data and retraining pipelines fully automated since you wouldn't be able to retrain with the cloud. There's also the issue that training requires a lot more compute and memory typically than inference. Probably better to just train offline and push periodic updates

#### What is a container runtime, and how does it relate to Docker?

A container runtime is the platform that runs containers. Red hat has one too but Docker is the most popular and best for cross-platform use

#### Name three good practices when creating a Dockerfile

- Lint your dockerfiles (`hadolint`)
- Run vulnerability scans on base images used (`grype`)
- Keep dockerfiles small (this makes them fast to use, lightweight to change, and readable. Combine RUN commands when possible)

#### What are two critical concepts of DevOps mentioned in this chapter? Why are they useful?

1. Containers. They're useful because they make your code run the same no matter where you use it, since its running in a constant environment than you can version like the rest of your code, and publish to a container hub to be used by multiple projects and services
2. Not really sure the other one, there was a lot on containers so you could probably split some of the things mentioned there into multiple devops concepts. Both continuous integration and continuous delivery were mentioned with regard to containers (linting and docker hub)

#### Create a definition, in your own words, of what the “edge” is. Give some ML examples that can be applied

`Edge` is any device or hardware that users are running models on directly. A great example is FaceID on Iphone. But great use cases are those with very low latency requirements or ones that don't require a lot of compute or those that have high security requirements
