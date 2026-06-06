# Docker

## Overview

- docker containers are like very lightweight virtual machines. It ships an application and the environment it runs in

## Containers

- containers get 90% of the benefit of fully virtual machines but without the slow startup times and complexity
- they operate on top of the base system's OS instead of virtual machines which simulate hardware
- as a result there's slightly less isolation between different containers, they operate on a lot of the same resources but secure things through `namespaces`
- `images` is a read-only definition of a container. A `container` is an instance of a virtualized read-write environment

Running a docker image:
```docker
# this is just an example, don't run this
docker run -d -p hostport:containerport namespace/name:tag
```
- `-d`: Run in detached mode (doesn't block your terminal)
- `-p`: Publish a container's port to the host (forwarding)
- `hostport`: The port on your local machine
- `containerport`: The port inside the container
- `namespace/name`: The name of the image (usually in the format username/repo)
- `tag`: The version of the image (often latest)

## Storage

Containers won't retain info between processes if you start a new container from the same image. If you start and stop and start again the same container it will.

To maintain persistent state across containers you can use `docker volumes`.

## Execute

- you can run commands inside a container like `docker exec CONTAINER_ID ls`
- netstat can show what programs are bound to which ports. You could run this on a container to see inside it
- to run a shell you can use something like `docker exec -it CONTAINER_ID /bin/sh`

## Networks

- you can turn off networking on a given container if you want
- you can use `docker network` to set up networks that containers can communicate with eachother, but not with the outside world
- A common setup is one where your load balancer is exposed to the public internet, but the application servers are only accessible via the load balancer

### Proxies

- a `proxy` (forward proxy) sits on the client side and makes requests on behalf of clients
    - forward proxy use cases: hide client IPs, enforce browsing rules, cache outgoing requests, inspect/modify traffic
    - forward proxy examples: corporate web proxy, browser configured to use a proxy, mitmproxy/Fiddler, VPN egress, TOR exit node

- a `reverse proxy` sits on the server side and receives requests on behalf of backend servers
    - reverse proxy use cases: route to multiple services, load balance, TLS termination, rate limiting, auth, caching
    - reverse proxy examples: Nginx, Caddy, Traefik, HAProxy, AWS ALB/ELB, Cloudflare

## Dockerfiles

- basically just IaC for images. This is where you define everything

## Debugging

- `docker logs` to see logs from detached containers
- `docker stats` to see resource utilization
- `docker top` to see running processes inside a container
- you can limit memory and cpu in containers with flags on docker run

## Publishing

- you can push/pull images to remote hubs kindof like git / github
- `tags` — labels you can assign to specific versions of an image. `latest` is the default if you don't set one
- a good practice is to use versioning in your tags but also push latest so that the default will be to pull the latest

### Typical deployment pipeline

1. code is merged to main
2. code is built into an executable
3. docker image is built with the dependencies and code
4. docker image version is pushed to a registry
5. production server pulls down latest version

Or with PRs / GitHub Actions:
1. The developer writes some new code
2. The developer commits the code to Git
3. The developer pushes a new branch to GitHub
4. The developer opens a pull request to the main branch
5. A teammate reviews the PR and approves it (if it looks good)
6. The developer merges the pull request
7. Upon merging, an automated script (perhaps a GitHub Action) is started
8. The script builds the code (if it's a compiled language)
9. The script builds a new Docker image with the latest program
10. The script pushes the new image to Docker Hub
11. The server that runs the containers (perhaps a Kubernetes cluster) is told there is a new version
12. The k8s cluster pulls down the latest image
13. The k8s cluster shuts down old containers as it spins up new containers of the latest image
