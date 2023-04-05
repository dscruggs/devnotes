# Chapter 10: Infrastructure and Tooling for MLOps

Infrastructure benefits systems more as complexity of the system grows. Simple use cases with 1 model don't really need infrastructure, but as systems and use cases grow, infrastructure becomes more important. At the extreme case companies develop custom hardware and software for their infrastructure, this is how AWS and google cloud came to be

Most companies are on an intermediate level

- Working with gigabytes or terabytes of data
- having a few models performing things like recommendations, fraud detection, etc.
- data science teams from 10-100s of engineers
- Could be zillow, but not facebook scale

`infrastructure` in the ML world is the facilities to support the development and maintenance of ML systems

Four layers of "fundamental facilities" for ML:

- Storage and compute
- Resource Management
  - Tools to schedule and orchestrate your workloads to make the most out of your available compute resources (airflow, kubeflow, metaflow)
- ML platform
  - provides tools to aid the development of ML applications like model stores, feature stores, and monitoring tools (MLflow, sagemaker)
- Development environment
  - This is where code is written and experiments are run. Code needs to be versioned and tested. Experiments need to be tracked

Even if requirements of two companies are the same their infrastructure may look completely different

## Storage and Compute

### Storage

The `storage layer` is where data is collected and stored.

- could be SSDs, hard drives, S3, etc.

Data storage is cheap, most use cloud solutions here. Data is covered a lot in chapter 3

### Compute

The `compute layer` refers to all the compute resources a company has access to and the mechanism to determine how these resources can be used

- most common form is something like EC2 or GCP

#### Compute Units

Compute layer like a CPU or GPU can be sliced into smaller compute units, and these compute units can be treated as individual units to execute different jobs. These can be short term like Lambda functions or longer term like virtual machines. Longer term compute units are sometimes called `instances`

Threads or cores don't need to be the compute unit of choice, these can be abstracted away

- Spark and Ray use "jobs" as their compute unit
- Kubernetes uses "pods", a wrapper around containers

Compute units need to be able to have data loaded onto them, and then execute the required operations.

So compute units are usually characterized by two things:

- how much memory it has
  - cost scales with amount of memory and also with how fast that memory is with I/O and load times
- how fast it runs an operation
  - cost scales with amount of compute. CPUs are generally cheaper than GPUs and TPUs or other specialized compute for ML
  - FLOPS (floating point operations per second) is often used to measure this. Could also look at number of cores
  - Amount of compute is useless without knowing about utilization. If a program only uses 30% of compute this is inefficient and costly

### Public Cloud vs Private Data Centers

The compute layer, like data layers, are highly commoditized and available on all major cloud providers

Companies have a choice between cloud and setting up data centers. More and more are moving to the cloud

Cloud is appealing to companies who have variable workloads because you don't need to pay for compute you don't use (autoscaling).

In ML this is appealing where during the initial build of a model much compute will be used for experimentation, but afterwards the usage will be lower and more consistent

Cloud makes sense early on, but as companies scale the benefits of cloud wane

Moving off the cloud is a difficult and expensive task up front

Many companies are adopting a hybrid approach. Using the cloud while slowly scaling up their own data centers

## Development Environment

This is where ML engineers write code, run experiments, and interact with the production environment where champion models are deployed and challenger models evaluated. The dev environment consists of:

- IDE
- Verisioning
- CI/CD

Dev environments are underrated and underinvested at most companies outside of large tech companies

"If you have time to set up only one piece of infrastructure, make it the dev environment for data scientists"

### Dev Environment Setup

Dev environments should make it easier for engineers to do their jobs.

#### Versioning

There's a set of ad hoc tools for code, data, and model versioning like DVC, Weights and Biases, Comet.ml, MLFlow, etc.

Versioning is especially important in ML because of the sheer number of things to track

#### CI/CD

Dev environments should be set up with an automated test suite to test your code before pushing it to staging or production environments.

Github Actions, CircleCI are tools used for this

#### IDE

VSCode and Vim are popular examples, AWS Cloud9 is a browser-based example

`Notebooks` are a popular IDE for data scientists because they can include images, plots, and data in tabular format all inline

- Notebooks are stateful so they can retain states after runs. If a block fails you can make changes and rerun while keeping other data from previous blocks
- Statefulness is a double edged sword because you can execute cells out of order
- Notebook reproducibility can be an issue without instructions if you're not careful
- They're extremely useful for data exploration

Tools for notebooks:

- Papermill: spawns multiple notebooks with different parameter sets and executes them concurrently. Can also help summarize metrics from a collection of notebooks
- Commuter: notebook hub for viewing, finding, and sharing notebooks in an org
- nbdev: library that encourages you to write documentation and tests in notebooks too

### Standardizing Dev Environments

Dev environments should be standardized. If not company wide, at least team wide

Using the same packages and a requirements file is a good start, but this will not account for hardware differences among developers

Cloud environments allow for standardization of dev environments. You can develop in cloud notebooks or IDEs, or do it locally and SSH into an EC2 instance or Github Cospaces. You can use VSCode for this

There's several benefits of moving from local dev to the cloud:

1. Everyone is using the same tools. No version or hardware inconsistencies
2. Cost (it's not that expensive for low tier resources and you can implement auto-shut offs)
3. IT support is easier (no need to support 1000s of devices)
4. Remote work is convenient with this
5. Security is easier to manage
    - some companies will have security concerns about not storing data on the cloud
6. Dev environments on the cloud reduce the gap between prod and dev environments

Cost security, and other concerns along with the initial investment might deter cloud migrations of dev teams. But there should be some form of standardization regardless. It will save time and money and make developers lives easier

### From Dev to Prod: Containers

Prod deployments need to be able to scale with demand. This is handled automatically by most cloud providers.

Prod environments and compute are inherently stateless and you have to install packages and execute commands whenever they're spun up

`Containers` are designed to be step-by-step instructions to re-create environments for you programs to run on:

- installing packages
- downloading pretrained models
- navigating to folders

**Docker** is the most popular container solution. It allows hardware anywhere to run your code

Key concepts of docker:

- `Dockerfile` - list of instructions
- `Image` - Running all instructions on a Dockerfile gives you an image
- `Container` - Running the image gives you back a container

If the dockerfile is a recipe to construct a mold, then the mold is a dockerfile. From this mold you can create multiple running instances, which are containers

Docker images can be made from scratch or from another Docker image. This is called `base images` and layering of docker images

A `container registry` is where you can share a docker image or find an image created by other people to be shared publicly or only with people inside their organizations. **Docker Hub** and **AWS ECR** are two common container registries

Example simple dockerfile:

1. Download the latest pytorch base image
2. Clone NVIDIA's apex repo on github, navigate to the newly created apex folder, and install apex
3. Set fancy-nlp-project to be the working directory
4. Clone Hugging Face's transformers repository on Github, navigate to the newly created transformers folder, and install transformers

You'll likely need multiple containers. Ex: you have featurizing code that is fast to run but requires a lot of memory, but your model training code is slow and requires less memory. You can run the feature code on a CPU and the training on a GPU instance to save money. You'll need containers for both environments

Container management and orchestration is important as the number of docker containers grow. **Docker Compose** is a lightweight tool for container orchestration on a single host

If each container runs on its own host, **Kubernetes (K8s)** is a tool for this:

- It creates a network for containers to communicate and share resources.
- It can help spin up containers on more instances when you need more compute/memory as well as shutting down containers when you no longer need them
- K8s is not the most data-scientist-friendly tool, but its ubiquitous in production applications

## Resource Management

Pre-cloud there used to be a lot more emphasis on efficiency and on resource management with fixed resource.

Now compute and storage are much more elastic, and companies ok spending more money on compute and storage if it saves engineering time

This section discusses how to manage resources for ML workflows

### Cron, Schedulers, and Orchestrators

There' two key characteristics of ML workflows that influence their resource management:

- repetitiveness
- dependencies

#### Cron Jobs

`Cron` schedules repetitive jobs to run at fixed times

- runs a script at a predetermined time and tells you whether the job succeeded or failed
- Doesn't tell you dependencies between jobs

ML workflows can have complex dependencies between jobs. An example ML workflow:

1. Pull last week's data from the data warehouse
2. Extract features from the data
3. Train two models. A and B, on the extracted features
4. Compare A and B on the test set
5. Deploy A if A is better; otherwise deploy B

Each step depends on the previous step. Step 5 is a conditional dependency because it will perform a different action depending on the previous step

Directed Acyclic Graphs (DAGs) are common methods of expressing ML workflows because there should be no cycles in jobs. Most ML workflow tools
have you express things in this way

#### Schedulers

`Schedulers` are cron programs that can handle dependencies

- It takes the DAG of a workflow and schedules each step accordingly
- You can trigger jobs based on times or events
- You can specify things to do if the job before it succeeds or fails, retry attempts, etc.
- These tools usually leverage Queues to keep track of jobs
- Schedulers also need to be aware of compute and resources available, and how much are needed for jobs to be able to schedule
  - this can be a guess or computed on the fly (google Borg)

#### Orchestrators

Schedulers are concerned with when to run jobs and what resources are needed.

`Orchestrators` are concerned with where to get those resources.

- They deal with lower-level abstractions like machines, instances, clusters, service-level grouping, replication, etc.
- Autoscaling is one example of orchestration

Schedulers are often used for periodical jobs whereas orchestrators are often used for services where you have a long-running server that responds to requests

Kubernetes is the most well-known orchestrator today. This can be on prem or in the cloud with AWS EKS or Google GKE

Many people use schedulers and orchestrators interchangeably becuase schedulers run on top of orchestrators usually. But you can have separate solutions

Data-science specific coordinators like Airflow have their own schedulers

### Data Science Workflow Management

Workflow management tools manage workflows:

- generally allow you to specify your workflows as DAGs in either python or config files
- Most tools come with some schedulers that instead of focusing on individual jobs focus on the workflow as a whole
- The underlying scheduler works with an orchestrator to allocate resources to run the workflow

Common tools: Airflow, Argo, Prefect, Kubeflow, Metaflow

#### Airflow

Airflow was one of the first tools to come around and is general purpose, written in python

Drawbacks:

- It's monolithic, it packages the entire workflow into one container
  - if two different steps in your workflow have different requirements, you can in theory create different containers for them using DockerOperator, but it's not easy
- DAGs aren't parameterized
  - You can't pass parameters into your workflows. If you want to create models with different parameters you have to make different workflows
- DAGs are static
  - it can't automatically create new steps at runtime if needed. If you're reading from a database and want to create a step to process each record, but don't know how many records there are in advance airflow can't handle

Argo and Prefect were created to address different drawbacks of Airflow

#### Prefect

Made by contributors of airflow so its very similar but workflows are parameterized and dynamic

Drawbacks:

- containerized steps aren't the first priority. You can run each step in a container, but you still have to deal with dockerfiles and register your docker with your workflows in prefect

#### Argo

Argo addresses the container problem. Each step in argo runs in its own container, but Argo is defined in YAML instead of python so you need to define each step and its requirements in the same file

Drawbacks:

- YAML can be messy
- You can only run on K8s clusters, which are only in production. You can use minikube to simulate but can get messy

#### Kubeflow and Metaflow

Two tools aimed at helping run the workflow in both dev and prod by abstracting away infrastructure boilerplate code usually needed for airflow or argo.

They promise to give data scientists access to the full compute power of the prod environemt from local notebooks, which allows data scientists to use the same code in both dev and prod environments

Both have some sheduling capabilities but are meant to be used with full blown schedulers and orchestrators. Kubeflow Pipelines are built on top of argo to be used with K8s, and Metaflow can be used with AWS batch or K8s

Both are fully dynamic and parameterized

Kubeflow is more popular

Metaflow may have a better user experience

## ML Platform

More and more companies are pivoting to using the same tools and processes for ML workflows company wide. This collection is called an `ML Platform`

Definitions vary from company to company, but often there's 3 components:

- model development
- model store
- feature store

Things to consider when evaluating tools in this space:

- Whether the tool works with your cloud provider or allows you to use it on your own data center
- Whether its open source or a managed service
  - open source means you can host it yourself and there's no data privacy concerns, but it could require more engineering time
  - managed service may cost more and have issues with data sharing/privacy

### Model Deployment

Deployment services help with pushing models and their dependencies to production and exposing your model as endpoints. Deployment is the most mature among all ML platform components

- AWS Sagemaker
- GCP Vertex AI
- Azure ML
- Alibaba ML studio
- MLFlow Models
- Seldon
- etc.

#### Considerations

- how easy it is to do both online prediction and batch prediction with the tool
  - batch prediction is typically harder to do at a smaller scale
  - you may have separate pipelines for deploying batch and online prediction models
- How to ensure the quality of the model before its deployed? See if the tool supports online testing capabilities for deployment
  - different deployment techniques like canary, A/B testing, etc.

### Model Store

Model stores can be as simple as files in S3 buckets. But it can also be much more

There's a few problems that can arise from debugging stored models:

- The model being used in production is not the same model as is locally stored. There may have been a wrong binary uploaded to production
- The model being used in production is correct, but the list of features used is wrong. Maybe code wasn't rebuilt before pushing to prod
- the model is correct, feature list is correct, but the featurization code is outdated
- The model is correct, the feature list is correct, the featurization code is correct, but something's wrong with the data processing pipeline

What about access to the code used to generate the model?

Tracking artifacts is just as important as storing the model binaries themselves.

#### Artifacts to consider storing

- Model definition
  - info needed to create the model (ex: type, # of layers, etc.)
- Model parameters
  - hyperparameters of model
- Featurize and predict functions
  - Given a prediction request, how do you extact features and input these features into the model to get back a prediciton? These provide instructions to do that. Usually wrapped in endpoints
- Dependencies
  - Python version, packages, etc...usually a container
- Data
  - Data used to train the model. Usually a pointer to a location, or a DVC commit
- Model Generation Code
  - Frameworks used
  - how it was trained
  - how train and test splits were created
  - number of experiments run
  - range of hyperparameters
  - actual hyperparameters used
  - this is especially important if code isn't checked into git or included in the model artifacts
- Experiment artifacts
  - Graphs, loss curves, model performance on test set, etc.
- Tags
  - helps with model discovery and filtering. Owner, person, team, task, etc.

Most companies use a subset of these, and can be either scattered in different places or centralized. Either way, have an easy-to-use and clear system for tracking and finding the metrics and artifacts you care about

MLFlow is a popular solution, but it isn't perfect

### Feature Store

Means different things to different people. But in general it helps address 3 problems:

1. Feature management
    - Helps team share and discover features, as well as manage roles and sharing settings for features. Data doesn't necessarily have to be included if privacy is an issue. Just description and contact information
2. Feature Computation
    - If multiple models are using one expensive feature to compute, it may be worth it to compute it once and store it for future use
    - Feature stores can both compute and store these features
3. Feature consistency
    - Helps ensure consistency in development and production of features, and helps with batch and streaming features

This is a newer concept, and there's not many established general solutions out there

Feast and Tecton are both up and coming but they require deep integration to be useful

AWS Sagemaker and Databricks offer their own solutions

1/2 of companies who use feature stores build their own

## Build vs Buy

The right ML infrastructure is difficult to set up, because it will depend heavily on the applications and scale at which you run applications

How much you invest depends on how much you want to build vs buy

There's extreme cases where you want to build or buy exclusively, but most companies will do both. (ex: use AWS EC2 for compute but build monitoring and dashboards in house)

### Considerations

- What stage your company is at
  - in the beginning vendor solutions may be more attractive, but vendor costs can grow quickly
- What you believe to be the focus or the competitive advantages of your company
  - "If its something you want to be really good at, manage it in house. If not, use a vendor"
- The maturity of the available tools
  - Oftentimes new tools with strong capabilities require a lot of pain when you're an early adopter
