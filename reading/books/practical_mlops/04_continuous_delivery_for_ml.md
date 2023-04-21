# Chapter 4: Continuous Delivery for Machine Learning Models

A key principle of devops is to not be afraid of pain, but use it to identify problems and automate them away

For software you should have an automated system that builds, verifies, and deploys software. For ML you should also do this at least for model artifacts if not data

## Packaging for ML models

`Packaging` in the case of ML means to get a model into a container to take advantage of containerized processes to help sharing, distributing, and easy deployment

Three characteristics of packaging ML models into containers:

- As long as a container runtime is installed, it is effortless to run a container
locally.
- There are plenty of options to deploy a container in the cloud, with the ability to
scale up or down as needed.
- Others can quickly try it out with ease and interact with the container.

Maintainability becomes less complicated and debugging a non-performant model locally can be as simple as a few commands in a terminal

The more complicated the deployment strategy, the more difficult troubleshooting is

### Flask ONNX App

There's an example of an ONNX model to perform sentiment analysis

File structure (examples for files in book):

```none
Dockerfile
requirements.txt
roberta-sequence-classification-9.onnx
webapp/
    app.py
```

Steps he followed:

1. pick the model
2. write the app
3. write the requirements file
4. test locally w/ curl requests
5. make the dockerfile to build the env and run the app
6. build and run the image

## Infrastructure as Code for Continuous Delivery of ML Models

It's important not only to deploy containers to a public place but also track the source code and artifacts used to create it (dockerfile and relevant files)

Azure has a model registry you can use for versioning. Can define a GitHub Actions job to automatically pull a registered model whenever there are changes to the code repo, and then create a container and push it to a container registry

Example GitHub Actions Workflow:

When building a CICD job break it down into steps. In this case:

1. Check out the current branch of the repo
2. Authenticate on Azure Cloud
3. Configure auto-install of Azure CLI extensions
4. Attach the folder to interact with the workspace
5. Download the ONNX model
6. Build the container for the current repo

```yaml
name: Build and package RoBERTa-sequencing to Dockerhub
on:
    # Triggers the workflow on push or pull request events for the main branch
    push:
        branches: [ main ]
    # Allows you to run this workflow manually from the Actions tab
    workflow_dispatch:
jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Authenticate with Azure
            uses: azure/login@v1
            with:
                creds: ${{secrets.AZURE_CREDENTIALS}}
        - name: set auto-install of extensions
            run: az config set extension.use_dynamic_install=yes_without_prompt
        - name: attach workspace
            run: az ml folder attach -w "ml-ws" -g "practical-mlops"
        - name: retrieve the model
            run: az ml model download -t "." --model-id "roberta-sequence:1"
        - name: Authenticate to Docker hub
            uses: docker/login-action@v1
            with:
                username: ${{ secrets.DOCKER_HUB_USERNAME }}
                password: ${{ secrets.DOCKER_HUB_ACCESS_TOKEN }}
        # this is for docker hub but could feasibly use any container registry
        - name: build flask-app container
            uses: docker/build-push-action@v2
            with:
                context: ./
                file: ./Dockerfile
                # whether to push to docker hub or not
                push: true
                tags: alfredodeza/flask-roberta:latest
```

This packages model and code together in one container to be used in the future and for later debugging

### Using Cloud Pipelines

A pipeline isn't very advanced. It's just a series of steps or instructions that can achieve a specific objective (like pushing a model int oa prod environment when run)

A simple ML pipeline:

1. Get dataset from S3 bucket
2. Validate dataset
3. Train model

Tip: separate different parts of the pipeline into different steps so as to easily identify failure points

CICD pipeline elements and parts should be loosely defined. Make what the problem demands.

Generally there's 5 common elements in CICD pipelines:

- Build
- Test
- Release
- Deploy
- Validate

Basic ML pipeline:

- Feature engineering
- Training
- Model evaluation
- Accuracy/drift check
- Register model

Sagemaker has many templates for getting models into production

### Controlled Rollout of Models

Concepts from web service deployments can work in ML too for switching from an old to a new model

`Blue green deployment` is deploying a new version into a staging environment identical to production. You then route traffic separately to each, and as the old model loses traffic you can close this instance. Kubernetes allows something like this. Replicating production environments is hard though

`Canary Deployment` is starting out with a small amount of traffic going to the new model and slowly increasing the better performing model's traffic

Monitoring, logging, and detailed metrics of production models are critical for a robust deployment strategy

### Testing Techniques for Model Deployment

Steps an HTTP request to a container to produce a prediction goes through:

1. Sending the HTTP request with a JSON body in a specific format
2. A specific HTTP port and endpoint (/predict) have to exist and get routed to
3. Flask has to receive the JSON payload and load it into python
4. ONNX runtime needs to consume this and produce a predicition
5. JSON response with HTTP 200 status code and the boolean prediction need to be sent off

You can test all these steps

#### Automated Checks

Manual checks should be kept to a minimum. It's impossible to remember all failure points yourself, and you could leave at any time. Tests:

- valid JSON request
- valid JSON response
- Invalid JSON request
- Health endpoint
- port and endpoint

#### Linting

Covered in the CI section. Lint your code automatically upon commit to catch errors before you run it

## Exercises

- [ ] Create your own Flask application in a container, publish it to a GitHub repository, document it thoroughly, and add GitHub Actions to ensure it builds correctly.
- [ ] Make changes to the ONNX container so that it pushes to Docker Hub instead of GitHub Packages.
- [ ] Modify a SageMaker pipeline, so it prompts you before registering the model after training it.
- [ ] Using the Azure SDK, create a Jupyter notebook that will increase the percentile of traffic going to a container.

## Critical Thinking Discussion Questions

#### Name at least four critical checks you can add to verify a packaged model in a container is built correctly

1. Check invalid inputs returns a good error
2. Check valid inputs returns the expected response
3. Check valid input for invalid credentials if you have credentials
4. Check the image is correctly published in the container hub idk

#### What are the differences between canary and blue-green deployments? Which one do you prefer? Why?

Blue-green uses a whole separate environment and routes traffic to both until you're happy with performance and then there is a cutover. Canary uses the same environment but runs the new features on a subset of nodes and routes some traffic there, and over time you gradually increase the % of traffic here if you're happy with performance. They're similar but the difference is blue-green has a much more isolated secondary environment.

I think I prefer blue-green because it seems conceptually easier to implement with a load-balancer and there's less toggling of specific configs within an environment

#### Why are cloud pipelines useful versus using GitHub Actions? Name at least three differences

- Cloud pipelines have tighter integration with their respective services if you're also using those, like S3 on AWS. So easier to implement together
- Cloud pipelines allow for access to GPUs in your pipelines, I think github actions does not or its more difficult
- You have more advanced capabilities on cloud pipelines like Sagemaker that was specifically built for ML, whereas github actions is a generic pipeline tool

#### What does packaging a container mean? Why is it useful?

Packaging a container means to implement steps to automatically collect a repo into a container and deploy that to a container hub to be downloaded

It's useful because it allows others and yourself to easily access it in the future and removes any manual errors that might happen in manually building and publishing it

#### What are three characteristics of package machine learning models?

1. As long as a container runtime is installed, it is effortless to run a container
locally.
2. There are plenty of options to deploy a container in the cloud, with the ability to
scale up or down as needed.
3. Others can quickly try it out with ease and interact with the container.
