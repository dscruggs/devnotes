# Chapter 2: MLOps Foundations

This chapter will build a strong foundation for using MLOps Methodologies

## Bash and the Linux Command Line

Most cloud platforms assume you will interact with it to some degree in the terminal

Think of the terminal as the "advanced settings" of the environment. It makes performing advanced tasks easier than in the GUI

Critical components of the terminal to learn:

- Cloud-based shell dev environments
- bash shell and commands
  - files and navigation
  - input/output
  - configuration
  - writing scripts

### Cloud Shell Development Environments

All major clouds make their cloud development environments available on free tiers. You should use it for practice and so your personal projects can be shared with others

Recommend starting with `AWS Cloud`

#### AWS CloudShell

Bash shell with unique AWS command completion built in

You can edit the ~/.bashrc to customize your experience. You can use the built-in vim editor to do this. Vim is essential for using cloud shells

Vim: <https://vimhelp.org/vim_faq.txt.html>

#### AWS Cloud9

AWS Cloud9 is a more comprehensive way to develop software. It has both shell and a GUI editor for syntax highlighting and other things

Cloud9 is ideal for ML development since it allows you to make web requests from the console to deployed services and has deep integration with AWS lambda

Microsoft Azure or Google cloud have equivalent services

### Bash Shell and Commands

The shell is an interactive environment that contains a prompt and the ability to run commands. Most are either Bash or ZSH

Install ZSH and vim configurations immediately if you plan on using an environment a lot

- awesome vim: <https://github.com/amix/vimrc>
- ohmyzsh: <https://ohmyz.sh/>

#### List files

`ls -l` lists files with additional info

#### Run Commands

There's many helpful built-in commands in the shell

- `which ls` figures out the location of shell executables (ls is an executable)
- `ls -l /bin/ | wc -l` counts the executables in /bin/

#### Files and Navigation

- `pwd` shows the full path to where you are
- `cd` changes into a new directory

#### Input/Output

Example of piping commands together to get the word + character count of a file:

- `echo` acts like a print statement
- `>` sends to a file
- `wc -c` gets character count
- `wc -w` gets word count

```bash
bash-3.2$ cd /tmp
bash-3.2$ echo "foo bar baz" > out.txt
bash-3.2$ cat out.txt | wc -c
12
bash-3.2$ cat out.txt | wc -w
3
```

#### Data Processing

Here's a good command for pulling data into a new file (helpful if a file is too big for other libraries or tools to handle)

- `time` times the operation
- `shuf` shuffles the data while the `-n` flag controls how many rows are returned

```bash
bash-3.2$ time shuf -n 100000 en.openfoodfacts.org.products.tsv >\
10k.sample.en.openfoodfacts.org.products.tsv
1.89s user 0.80s system 97% cpu 2.748 total
```

#### Configuration

Config files for ZSH and Bash store settings that invoke every time a terminal opens

- ZSH: `.zshrc`
- Bash: `.bashrc`

Examples:

```bash
## Flask ML Azure
alias flask-azure-ml="/Users/noahgift/src/flask-ml-azure-serverless &&\
source ~/.flask-ml-azure/bin/activate"
```

```bash
## AWS CLI
export AWS_SECRET_ACCESS_KEY="<key>"
export AWS_ACCESS_KEY_ID="<key>"
export AWS_DEFAULT_REGION="us-east-1"
```

#### Writing a Script

Shell scripts are just a list of terminal commands in an executable script

Shell script hello world example:

```sh
#!/usr/bin/env bash

echo "hello world"
```

Calling it in a shell script:

```bash
bash-3.2$ chmod +x hello.sh
bash-3.2$ ./hello.sh
Hello World
```

They help you automate a lot of common command line tasks

## Cloud Computing Foundations and Building Blocks

Cloud computing is essential for ML. Building models benefits from the compute, and especially deploying and automating workflows is made much more accessible with the compute and accessibility of the cloud

AutoML is a promising new development

### Getting started with cloud computing

A great way to get started with cloud computing is setting up a multi-cloud environment

- Github or something similar is the central repo
  - AWS, Azure, and GCP can communicate with this
  - cloud shells exist in all 3 environments and can be used to set up similar workflows to show you how even though the names are different the concepts and services on all of them are similar

### Python

Python is the defacto language of data science

Containers and Cloud Computing make a lot of the speed concerns of python irrelevant

### Math

Goes over a lot of statistics + basic pandas stuff

### Optimization

`Greedy Approaches` take the local optimum in hopes it will also lead to a global optimum. Nice in cases where you can't find a global optimum easily or a "good enough" solution is fine

Goes over gradient descent a little

## Machine Learning Key Concepts

ML is the ability for computers to perform tasks without explicit programming. Done by "learning" from data

Goes over a bunch of basics

## Build an MLOps Pipeline from Zero

Example to deploy a Flask Machine Learning app on Azure App Services. Implements CICD in Azure Pipelines. Concepts can be similarly implemented in AWS and GCP

Setting up Azure Cloud Shell with github: <https://www.youtube.com/watch?v=3vtBAfPjQus&ab_channel=PragmaticAILabs>

## Exercises

- [ ] Run a hello world Python GitHub project and check it out and run your tests on all three clouds: AWS, Azure, and GCP.
- [ ] Make a new Flask application that serves out a “hello world” type route using AWS Elastic Beanstalk you think other people would find helpful and put the code into a GitHub repo along with a screenshot of it serving out a request in the GitHub README.md. Then, create a continuous delivery process to deploy the Flask application using AWS CodeBuild.
- [ ] Fork this repository that contains a Flask machine learning application and deploy it with continuous delivery on AWS using Elastic Beanstalk and Code Pipeline.
- [ ] Fork this repository that contains a Flask machine learning application and deploy it with continuous delivery on GCP using Google App Engine and Cloud Build or Cloud Run and Cloud Build.
- [ ] Fork this repository that contains a Flask machine learning application and deploy it with continuous delivery on Azure using Azure App Services and Azure DevOps Pipelines.
- [ ] Use the Traveling Salesman code example and port it to work with coordinates you grab from an API, say all of the best restaurants in a city you want to visit.
You will never think about vacation the same again.
- [ ] Using the TensorFlow Playground, experiment with changing the hyperparameters across different datasets as well as problem types. Can you identify optimal configurations of hidden layers, learning rate, and regularization rate for different datasets?

## Critical Thinking Discussion Questions

#### A company specializing in GPU databases has a key technical member advocating they stop using the cloud because it would be much more practical to buy their GPU hardware since they run it 24/7. This step would also allow them to get access to specialized GPUs much more quickly than they are available. On the other hand, another critical technical member who has all of the AWS certifications has promised to get him fired if he dares to try. He claims that they have already invested too much into AWS. Argue for or against this proposal

I think in this case it probably makes sense to get off the cloud. If they have full usage of their GPUs and they have GPU databases which require specialized GPUs, then likely cloud won't give them the same benefits that it would give to ML practitioners or other applications where usage is variable and deployment and scalability is critical.

#### A “Red Hat Certified Engineer” has built one of the most successful data centers in the Southeast for a company with only 100 employees. Even though the company is an e-commerce company and not a cloud company, he claims this gives the company a huge advantage. On the other hand, a “Google Certified Architect” and “Duke Data Science Masters” graduate claims the company is in a risky position by using a data center they own. They point out that the company keeps losing data center engineers for Google and has no disaster recovery plan or fault tolerance. Argue for or against this proposal

I think this company would probably benefit from moving to the cloud considering their specialization is in e-commerce and not data center management. They're risking their whole operation by maintaining a data center on their own especially if they're losing employees. Moving to the cloud will likely free up engineering time and make future deployments easier.

#### What are the key technical differences between AWS Lambda and AWS Elastic Beanstalk including the pros and cons of each solution?

I've never used beanstalk but from what I can tell it's a Platform as a Service as opposed to Lambda which is just for running specific function calls and quick actions. Elastic Beanstalk seems like its the whole package with load balancing and auto scaling for your application, and you can use containers here for the environment. Lambda is better for atomic actions that may be compute intensive that you can call asynchronously. I used it for a backend for my FastAPI app and it worked great. Basically lambda is easier to set up but if you want the whole application on one service you'll want to use Elastic Beanstalk. You don't need to choose one or the other though you could deploy an application on EB and then use lambda functions for some parts of it or have an API on lambda set up that can be called by your EB application

#### Why would a managed file service like EFS on AWS or Google Filestore be helpful in a real-world MLOps workflow in corporate America?

This creates a centralized place for data to ensure consistent file dependencies in deployments and if data is stored here make sure its consistent across workflows. Everyone will be working with the same files instead of everyone using their own local filestores

#### Kaizen starts with a simple question: can we do better? If so, what should we do to get better this week or today? Finally, how can we apply Kaizen to our machine learning projects?

Kaizen should be a mindset to never settle for "good enough" when it comes to wasted effort and manual tasks in software and ML. All steps of ML shouldn't be accepted at face value as needing to be long and drawn out and nonstandardized
