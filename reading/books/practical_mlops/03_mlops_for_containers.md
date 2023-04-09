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
