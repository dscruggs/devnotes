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

## [Chapter 4: Training Data](04_training_data.md)

If your training data is bad, your model won't be able to perform well (no matter how awesome it is)

Topics include:

- Sampling methods for training, both probabilistic and nonprobabilistic
- Labeling techniques and how to look for pre-existing "natural labels"
  - also how to generate labels using different methods of algorithmic supervision
- Class imbalance. The problems with it and how to handle it
  - basically make sure to pay attention to class-specific metrics, not just overall model metrics.
  - regression problems can be bucketed like classification problems to evaluate the full distribution of predictions!
- Data augmentation techniques and data synthesis

## [Chapter 5: Feature Engineering](05_feature_engineering.md)

Features are everything to a model. They need to be chosen well, and time and effort is well worth spending here.

Topics include:

- There's no foolproof answers to how to engineer good features, but several methods are discussed
  - experimentation, intuition, and learning from experts helps
  - kaggle competitions can offer good insight into feature engineering tactics
- SMEs are important to model development but they are often not engineers. So develop workflows in ways that help them contribute
- Discusses common feature engineering operations
- Discusses data leakage and how to avoid it
- Best practices for feature engineering
  - split data by time when training instead of random split when applicable
  - only oversample after splitting
  - scale and normalize only after splitting
  - use statistics from the training set only during training to scale features and fill in missing values
  - Understand how your data is generated, collected, and processed to uncover possible leakage sources
  - keep track of data's lineage
  - understand feature importance
  - use features that generalize well
  - remove no longer useful features from your models

## [Chapter 6: Model Development and Offline Evaluation](06_model_development.md)

Covers ML model and development processes

Topics include:

- how to select ML models for a task
  - things to consider when selecting a model, ensembling, etc.
- Experiment tracking and versioning
- Parallelism in training
  - data, model parellelism
- How to evaluate model performance offline
  - setting baselines, evaluation methods

## [Chapter 7: Model Deployment and Prediction Service](07_model_deployment.md)

Deploying models is an engineering challenge, not an ML challenge. Deployment is not the end of an ML project, only the beginning to a whole new host of work

Topics include:

- ML deployment myths
- Different ways to deploy a model
  - batch vs online
  - Cloud vs on the edge
- Hardware and software optimization for ML models

## [Chapter 8: Data Distribution Shifts and Monitoring]()

## [Chapter 9: Continual Learning and Test in Production]()

## [Chapter 10: Infrastructure and Tooling for MLOps]()

## [Chapter 11: The Human Side of Machine Learning]()
