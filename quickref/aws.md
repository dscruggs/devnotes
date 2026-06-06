# AWS

This is really more infrastructure/networking learnings than AWS-specific.

---

## Networking / VPCs

- A `VPC` is just a list of IP addresses inside a cloud region
- `CIDR Blocks` (Classless Inter-Domain Routing) says how many addresses can exist inside the VPC
    - CIDR describes the block of IP addresses in terms of a starting address and a size var. The size var is actually smaller = more IP addresses. So `10.0.0.0/8` is bigger than `10.25.0.0/16` by a huge factor (these are binary exponents)
- `Subnetting/subnets` lets you divide a VPC into smaller networks
    - this lets you have public subnets and private subnets
    - each subnet is connected to an `availability zone` which is one or more physical AWS data centers in a region. If you put subnets in different AZs an outage in one shouldn't affect the other
    - so basically you can just specify more CIDRs inside the VPC CIDR to have your IP blocks for your subnets. You just pick names and availability zones for them as well as the CIDR blocks

### Internet gateways

- in the basic setup the subnets can't interact with the internet
- usually "public" subnets means internet egress, and limited internet ingress
- internet gateways give you that
- `route tables` are a list of routes that tell the VPC how to handle traffic
    - A default route exists that says "if you don't have a specific address for something look here"
    - and then you define whatever other routes
    - a subnet becomes public by connecting it to a route table and an internet gateway
    - So basically you just set up a default route for the public subnet to route to the internet gateway if you want that to be the default
- private subnets typically have some level of internet connectivity (but with extra, indirect steps)
    - one alternative is having public subnets but gating access with a firewall (whitelist/blacklist IPs)
    - another option if you do use the private subnets is a `NAT (network address translation)` service which lets you keep a subnet private but allow outbound access when needed
    - you can just create a private route table as well though and define private routes (not to the internet gateway)

## IPs

- there's a concrete list of IPs available for public use. These cost money so are used sparingly
- private IPs can be used for all internal stuff
- `elastic IPs` is basically a static IP that you can move around and not worry about changing when your server starts/stops. They cost the same as a normal IP so these should be preferred

## Security groups

- `firewall` in aws terms are security groups
- lets you set rules for incoming and outbound traffic
    - for example outbound traffic can go to your API server, but not your DB

## Stateful vs stateless applications

- `stateful` means critical data that can't be created easily is stored either in memory or on disk
- `stateless` means no critical data is stored, and it operates on data as it passes through the network. This is usually more resilient but can be more complex

---

## IAM (identity and access management)

Core abstractions:
- `users` — "who are you"
- `groups` — groups of users that share permissions
- `policies` — says what you can do

For applications:
- `roles` — temporary, assumable identities with permissions (no long lived credentials)
- `trust policies` — define how applications or services can assume roles

- there's explicit allow / deny policies you can set
- `SSM Parameters` are basically environment variables for managing env variables in production. This is AWS specific, and can be thought of like kubernetes ConfigMaps or Secrets
    - key:value store with organization by path (like `/DB_ENDPOINT`)
    - can encrypt secrets
    - accessibility via IAM permissions
    - versioned
    - they're all stored as strings. So your app needs to parse it that way

---

## DNS

- DNS is eventually consistent, meaning changes are not applied and seen everywhere at once
- AWS has a DNS service for connecting your domain to your servers
- you can verify DNS via checking IP mappings with cli and ssh tools
- `CNAME` basically lets you define aliases for domains
- `route 53` is AWS solution here

---

## ECS

basically gives you a simpler way to manage containerized services than kubernetes

### Cluster

- ECS cluster is a logical grouping of compute resources where containers can run
- a cluster can hold multiple capacity providers, which provide resources to the cluster as needed. It's like the ECS version of EC2 auto scaling
- Fargate is the serverless compute option provided by AWS for long running and short running tasks

### Permissions

- `task execution role`: what permissions does ECS need to start up the task definition?
- `task role`: after the task starts up, what permissions does the app running inside the container require?

### Task definitions

- this basically defines how ECS runs the container: what ports it should expose, how much memory, what permissions, etc.

### Security groups

- controls who can reach ECS tasks
- an example setup would be one security group that allows traffic from an ALB to the ECS task, and another that allows internet traffic to the ALB

### Application load balancer (ALB)

- sits in front of a service and distributes incoming traffic across tasks. Handles health checks, ssl termination, and routing to healthy containers
- `listener` — rules that tell the load balancer what to do with incoming traffic
- `target group` — tells the listener where to send the traffic
- `load balancer` — the actual ALB that receives traffic

### Target groups

- defines the protocol (http/https, etc) and port your tasks are listening on
- defines how the load balancer determines if a target is healthy
- defines where targets are IP addresses (fargate) or instance IDs (EC2)

### ECS Service

- this defines where do you place your tasks, how do you scale them, and how do you connect them to the load balancer
