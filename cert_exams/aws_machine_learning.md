# AWS Certified Machine Learning - Specialty Notes

Collection of random notes during my study for the AWS Machine Learning Specialty Exam <https://aws.amazon.com/certification/certified-machine-learning-specialty/>

This assumes a large knowledge of ML already so I skipped most notes on that. Mostly just notes on AWS Services

## AWS Services

### Compute / Scaling

#### Elastic Container Service

Manages and scales docker services. Should work w/ default GPU AMI

#### Elastic Kubernetes Service

AWS Kubernetes service

#### EC2

VM solution. Need associated Deep learning AMIs for GPUs

### Storage

#### Elastic Block Storage

Block storage, very fast + scalable. Expensive

#### Elastic File System

Multiple instances, scalabe

#### FSx for Lustre

High performance fully managed file services, seamless integration with S3

#### S3

Data lake, storage, archiving, highly available + reliable

Tiers:

- standard, glacier, glacier deep archive
- intelligent tiering for unknown access patterns

#### AWS Storage Gateway

Services for backing up data on the cloud, connects to on-prem systems

### Databases

#### Relational

- RDS (standard SQL)
- Aurora (higher performance + reliabiity)

Supports MySQL + Postgres

#### NoSQL

- DynamoDB (serverless, easy to set up + manage, good for documents + key-value stores)
- DocumentDB (documents)
- NeptuneDB (graph)

Parquet is good for optimized storage of tabular data

### ETL

#### AWS Glue

- ETL services, can catalog data to be able to be queried
  - this service can't do SQL queries, but can use athena on top of this
- Can invoke based off events + lambda
- Data catalog can be used in place of Apache Hive metastore
- Serverless
- Can be used with Python + scala code, needs semi-standard data

### Logging + Messaging Services

#### Cloudtrail

Allows monitoring of who did what with applications

#### Cloudwatch

See resource usage + scaling info

#### Eventbridge

Event bus service to send events to targets which can be other services like lambda

#### Simple Notification Service

Pushes messages immediately to subscribers, good for multiple subscribers needing the same message, batches, many-to-many

#### Simple Queue Service

Sends, stores, and receive messages between software components. Only one subscriber, decoupling applications and allowing asych ops

### Analytics / ML (besides sagemaker)

#### Quicksight

BI tool for basic charts + analytics. Sagemaker integration for regression + classification, only batch predictions

#### Amazon Macie

Sensitive data searching in S3 buckets. Has integration w/ EventBridge to take actions

#### ML Capabilities

|Service Name|Use Case|
|:------------|:-------:|
| Translate | Automatic translation|
| Transcribe | Speech-to-text |
| Lex | Chatbot (can take speech or text) |
| Personalize | Recommendations for similar items or promotions |
|Kendra| ML search across many systems. Can return both answers + documents|
|Polly|text-to-speech|
|Comprehend| NLP suite. NER, topic models, sentiment analysis, classification, etc. |
|Rekognition| Computer vision suite. Detects scenes, faces, objects, extracts text, etc. Allows search on images + videos|
| Textract | OCR |
|Fraud Detector|Service for anomaly detection specifically for fraud|
|Forecast| forecasting service |
|Augmented AI| Human review workflow for AWS services for low-confidence predicitons. Ex: Rekognition, textract|
|CodeGuru| ML powered code quality recommendations|
|Factorization Machines| High dimensional sparse dataset algs for regression + classification|

### Sagemaker

AWS's ML platform.

Needs S3 path, docker path, name to deploy

#### Ground Truth

Labeling service that can label data based on human labels, also allows for `Mechanical Turk`, 3rd party, or employee labels. Option for automated labeling

#### Neo

Open stardard for optimizing model performance on various edge and cloud hardware. Ideal for multi system models

#### Inference Modes

- Real time
- Serverless (spin up times a factor)
- Asynchronous (for large payload and processing times)
- Batch
- Edge for on prem

#### Endpoints

- Can weight endpoints. A: 2, B: 1 would give 2/3 traffic to A
- 10 models on 1 endpoint max
- Updating an endpoint:
  - Creates new endpoint, switches to it, and deletes previous resources
- Describe endpoint describes status of endpoint
- Production Variants describes model config + resources on an endpoint. Weights specified here. Can have multiple on an endpoint
- HyperparameterTuner is an object to help with tuning
  - supports Bayesian tuning, Random search

#### Autopilot

Sagemaker's AutoML solution

#### Jumpstart

Pretrained models

#### Pipelines

CI/CD

#### Sagemaker Studio

Visual interface for Sagemaker

#### Sagemaker Canvas

No code ML solution

#### Sagemaker Autoscaling

- Model endpoint must be deployed to register auto-scaling. Register w/ scalable target then set scaling policy
- Updating endpoint:
  - need to delete policy, deregister as a scalable target, update endpoint, then reregister + set scaling policy

#### Sagemaker Pipe

For streaming training data instead of downloading

### Security

#### IAM

AWS identity and access management service. Used for all permissions provisioning

#### VPC

- VPC peering for private data transfer

### Data Loading

#### Kinesis Data Firehose

Captures, transforms, loads streaming data to services + data lakes. Can convert data into various formats

#### Kinesis Data Streams

Ingests and collections terabytes of data per day from application + service logs

#### Kinesis Data Analytics

Query and analyze stream data, real time analytics w/ low latency. Includes anomaly detection + SQL/Apache Fink

#### Kinesis Video Streams

For video data

## Misc ML Terms

- Xavier
  - initialization for NN weights for constant variance across layers
- AUC/ROC curves
  - ROC curves gives indication on model performance across multiple decision thresholds. AUC is the measure of how this ROCS curve looks
  - Sensitivity = TPR, Specificity = TNR. FPR = 1- Specificity
- Recall = Minimize FN
- Accuracy = Minimize FP, could be at cost of FN
- Precision = Minimize FP
- F1 = takes in both recall + precision, would increase accuracy while not at cost of FN
