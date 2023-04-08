# Chapter 1: Introduction to MLOps

Data science will fade and machine learning engineering will rise

Tools are processes used in ML engineering:

- Cloud native ML platforms
- Containerized workflows
- Serverless tech
- Specialized hardware for machine learning
- Big data platforms and tools

ML is deeply tied to cloud because of its unique compute and memory requirements along with specialized hardware

## What is MLOps?

Most of the problem-building in ML is around the modeling component: data engineering, processing, problem feasibility, business alignment. A lot of focus on the code instead of solving the business problem

Lack of automation and issue of HiPPO (Highest Paid Person's Opinions) culture

Much of ML has an academic feel rather than industrial

There's a lot of tech that exists in the world to solve real world problem but it isn't being used. Why? Models aren't moving into production.

`Kaizen` is a Japanese phrase that means improvement. It originate in the Japenese auto industry after WWII. There's many techniques of Kanban, root cause analyis, the 5 why's, and Six Sigma that it underlies. It's all about getting an accurate and realistic assessment of the world's state and pursuing daily, incremental improvements in the pursuit of excellence.

A common expression of "if its not automated, it's broken" is used to describe DevOps. MLOps come from the same lineage.

Humans are least valuable doing repetitive tasks and most valuable using technology as teh architects and practitioners. Coordination between developers, models, and operations must coordinated through transparent teamwork and healthy collaboration

`Devops` is a combination of best practices (microservices, continuous integration, continuous delivery, removing barries between Ops and Development, teamwork) to get work done faster and better, usually in an automated fashion

`MLOps` is the process of automating machine learning using DevOps methodologies

MLops needs:

- software engineering automation
- data and modeling automation
- monitoring and instrumentation for things like data drift

## DevOps and MLOps

### DevOps best practices

- Continuous Integration
  - continuous testing and improving quality based on test results
  - Jenkins, CircleCI, CodeBuild, etc.
- Continuous Delivery
  - Delivers code to a new environment without human intervation. Usually uses IaC
- Microservices
  - software services with a distinct function that had little to no dependencies
  - flask or FastAPI are popular frameworks in Python
  - Containerized microservices can use services like lambda, Fargate, Google CLoud RUn, Azure App Services
- Infrastructure as Code (IaC)
  - Checking the infrastructure into source control and "deploying" it to push changes to that repo
  - Ensures infrastructure doesn't require humans to build it out. AWS SAM, AWS Cloud Formation, Pulumi, Terraform are common techs
- Monitoring and Istrumentation
  - Allows an organization to make decisions about a software system's performance and reliability
  - logging, performance monitoring, etc.
- Effective Technical Communication
  - Create effective repeatable, and efficient communication methods
  - Prevents work on intractable problems
- Effective Technical Project Management
  - efficient using humans and technology solutions
  - Ticket systems, spreadsheets, etc.
  - Breaking down problems into small, discreet chunks of work so incremental progress can occur

A well maintained CI/CD system is a form of investment in the future of the team and company. All members of the team should help out with this

MLOps requires all the needs of DevOps with extra considerations for the data and models

## An MLOps Hierarchy of Needs

DevOps -> Data Automation -> Platform automation -> MLOps

### Implementing Devops

You need Continuous integration first. No automated testing, no moving forward with devops

Python project scaffold (assumes linux system):

- MakeFile
  - runs "recipes" via the Make system which comes with Unix-based OS
  - Does not replace a virtual environment in python, it just runs commands
  - Key terms
    - `Make install` installs software
    - `Make lint` checks for syntax errors
    - `Make test` runs tests
  - keeps track of complicated build steps that are hard to remember and type out correctly
- requirements.txt
  - used by pip installation tools, can have 1 or more of these
- Source code and tests
  - add a source file and a test file. make the first test super easy, use pytest
- Virtual environment
  - venv

Scaffolding will allow you to set up continuous integration:

1. Use make install to install the libraries for your project
2. run make lint to lint your project
3. Run make test to test your project

This is straightforward to integrated this process with a remote SaaS build server once working locally

#### CI With GitHub Actions

Pretty straightforward .yml file. Example in <https://github.com/dscruggs/devopsscaffold>

### DataOps and Data Engineering

An org without a reliable automated flow of data cannot reliably do MLOps

DataOps tools include:

- Airflow
- AWS Data pipeline, AWS Glue, AWS Athena, AWS Quicksight
- Data lakes
  - think S3

Data engineering efforts can be built around:

- periodic collection of data and running jobs
- processing streaming data
- serverless and event-driven data
- big data jobs
- data and model versioning for ML engineering

An org without data automation cannot use advanced methods for machine learning, just like a village without running water can't use an automated dishwasher

### Platform Automation

Choose ML platforms based on how orgs automate data. Ex: if most of the data is in S3 use Sagemaker

This is all about spinning up the relevant infrastructure and storage necessary to do ML

An ML platform solves real-world repeatability, scale, and operationalization problems.

### MLOps

Once DevOps, Data Automation, and Platform Automation are complete, MLOps is possible

MLOps is a mindset and a behavior, not usually a full job. Machine Learning Engineers should use MLOps practices to make machine learning systems

#### DevOps and MLOps conbined best practices

Create a feedback loop: Train and retrain models -> deploy and version -> audit trail and artifacts -> monitor -> train and retrain models...

- Create and retrain models with reusable ML pipelines
  - Creating a model once isn't enough. Data can change, customers can change, and people making the models can change
  - Have reusable ML pipelines that are versioned
- Continuous Delivery of ML models
  - Have all steps automated, including the infrastructure using IaC. Now the model is deployable at any time to a new environment, including production
- Audit trail for MLOps pipeline
  - Need an audit trail for ML models. (to detect issues like security, bias, failure, and accuracy)
  - Not only helps detect failures, but helps you to improve your approach to the problem
- Observe model data drift use to improve future models
  - a model that works 2 years ago probably won't work today
  - By monitoring data drift you can prevent accuracy problems before they become production issues

#### Deployment options

- The typical pattern is to deploy to an elastically-scalable HTTP endpoint
- also edge devices, browser, etc.

## Exercises

- [x] Create a new GitHub repository with necessary Python scaffolding using a Makefile, linting, and testing. Then, perform additional steps such as code formatting in your Makefile.
- [x] Using GitHub Actions, test a GitHub project with two or more Python versions.
- [ ] Using a cloud native build server (AWS Code Build, GCP CloudBuild, or Azure DevOps Pipelines), perform continuous integration for your project.
- [ ] Containerize a GitHub project by integrating a Dockerfile and automatically registering new containers to a Container Registry.
- [ ] Create a simple load test for your application using a load test framework such as locust or loader io and automatically run this test when you push changes to a staging branch.

## Critical Thinking Questions

- What problems does a continuous integration (CI) system solve?
  - This ensure new changes don't break old code and that new code works as intended with automated testing
- Why is a CI system an essential part of both a SaaS software product and an ML
system?
  - This ensures consistency in coding between different people and ensures changes don't break the code before deploying
- Why are cloud platforms the ideal target for analytics applications? How do data
engineering and DataOps assist in building cloud-based analytics applications?
  - Cloud platforms allow for elastic compute and storage usage which scales well with ML and data applications that may not need many resources for a while but then suddenly need lots of compute for things like training and inference
- How does deep learning benefit from the cloud? Is deep learning feasible without
cloud computing?
  - Deep learning is feasible without cloud computing but setting up GPUs is difficult locally and getting access to the compute necessary for training large models is difficult locally. On the cloud its relatively cheap and very easy to get. Especially with specialized compute like TPUs
- Explain what MLOps is and how it can enhance a machine learning engineering
project.
  - MLOps helps ensure there's consistent, reliable deployment of ML systems because most of the steps needed to get data, train, deploy, and monitor models is all automated. This also helps onboard new members and ensure team cohesion in how deploymnet is handled
