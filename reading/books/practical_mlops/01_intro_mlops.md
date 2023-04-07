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

### CI With GitHub Actions
