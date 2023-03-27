# Designing Machine Learning Systems

By Chip Huyen

[Accompanying Github Repo](https://github.com/chiphuyen/dmls-book)

## [Chapter 1: Overview of ML Systems](01_overview_ml_systems.md)

- Discusses many use cases of ML and what makes a good ML use case
  - There needs to be data with patterns in it to learn from, and there must be business value to gain from an ML project
- Discusses the difference between ML in research and ML in production
  - production is more concerned about latency and maintainability of models than small gains in accuracy
- Discusses how ML systems differ from traditional software systems
  - ML is part code, part data, part model artifacts
- ML engineers need to worry about much more than just the ML model and should understand all components of software applications and systems

## [Chapter 2: Introduction to ML Systems Design](02_intro_ml_system_design.md)

- Discusses that models must be motivated by business metrics which need to be translated into ML objectives to guide the development of the ML systems
- Discusses general requirements of ML systems
  - Reliability, scalability, maintainability, and adaptability
- Discusses the iterative process to develop an ML system that meets requirements
- Discusses the role of data vs model architecture in ML systems and which is more important
  - Right now data is more important but it won't solve all problems

## [Chapter 3: Data Engineering Fundamentals](03_data_eng_fundamentals.md)

- Discusses the importance of data in ML applications
  - choosing the right storage format is important to make it easier to use the data in the future
- Discusses different data formats and the pros and cons of row-major vs column-major formats
- Discusses the three major data models: relational, document, and graph
- Discusses structured vs unstructured data and how the difference is really on who has to assume the structure
  - structured makes the writer of data assume the structure
  - unstructured makes the reader assume the structure
- Discusses data storage engines and processing
  - transactional vs analytical processing, how those categories are mostly useless now with advances in technology and the decoupling of storage and processing
- Discussing passing data between processes and three methods
    1. Passing through databases
    2. Passing through services (synchronous)
    3. Passing through events (asynchronous, usually for real-time applications)
- Discussed batch vs stream processing and the requirements and pros and cons of each
  - Discusses batch vs stream features and how ML systems usually require both in real-world use cases

## Chapter 4: Training Data

## Chapter 5: Feature Engineering

## Chapter 6: Model Development and Offline Evaluation

## Chapter 7: Model Deployment and Prediction Service

## Chapter 8: Data Distribution Shifts and Monitoring

## Chapter 9: Continual Learning and Test in Production

## Chapter 10: Infrastructure and Tooling for MLOps

## Chapter 11: The Human Side of Machine Learning
