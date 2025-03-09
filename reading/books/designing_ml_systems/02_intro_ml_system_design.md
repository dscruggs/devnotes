# Chapter 2: Introduction to Machine Learning System Design

This chapter discusses the components that come into play when designing an ML system. ML systems need a business objective to give purpose to why its needed. And requirements need to be made to guide the development of this system. The problem must also be framed in a way that ML can solve.

## Business and ML Objectives

Many data scientists / data projects are too focused on increasing model metrics (accuracy) and not enough on increasing business objectives (which are what really matter).

Business objectives should be made for every ML project that is linked either directly or indirectly to increasing profit of the business.

Examples:

- Increasing ad click-through rates
- Purchase rate
- Quality recommendation interactions

### Investment in ML is not a quick solution, but over time the investment can pay off if there's good use cases

Companies like to use ML because being "AI powered" attracts customers.Companies can also be misled by hype around ML to believe it will magically transform their business overnight. It's important to be realistic about expected returns. ML is a long term investment, and the longer you invest the more efficient projects can be.

- 75% of companies that are using ML for over 5 years can deploy a new model in under 30 days. Only 40% can if they haven't.

## Requirements for ML Systems

Requirements vary among use cases, but typically 4 constantly come up: reliability, scalability, maintainability, and adaptability

### Reliability

The system should continue to perform at its required levels in the face of adversity (software and hardware faults). ML systems not only need the code to run, but also need to worry about making sure predictions continue to be correct.

### Scalability

ML systems can scale in many ways:

- model complexity (code, time, space requirements)
- ML system traffic volume
  - can use autoscaling here
- Model count

There should be a reasonable way to deal with this growth, whatever kind of growth it is.

- Autoscaling can be used for resource scaling
- Artifact management for # and complexity of models is a different challenge that will be discussed in later chapters. But requirements vary depending on the size and number of models.

### Maintainability

Structure workloads and set up infrastructure in a way that different contributors can use and feel comfortable with. Code should be documented and models, data, and artifacts should be versioned in a way that models could be reproduced even if the original contributor isn't around. Problems should be able to be solved on the models relatively easily.

### Adaptability

ML systems should be able to adapt to changes in data and business requirements, and new versions should be able to be deployed without interruptions. ML systems need to be able to evolve quickly.

## Iterative Process

Development of an ML system is not a straightforward process. There will be iterations where data is collected, model is trained, and then problems are identified and the process restarts. Once a model is deployed there may be a change in requirements and a need to start over.

Generally an ML project from the perspective of a data scientist would look like this:

- Project Scoping
  - laying out goals, objectives, and constraints of the project.
  - Stakeholders are identified and involved
- Data engineering
  - taking raw data and turning it into something usable by the model
- ML model development
  - feature extraction and developing initial models, then iterating to develop a model ready for production
- Deployment
  - Making the model accessible to users
- Monitoring and continual learning
  - checking for performance decay and adaptive to changes in environment and requirements
- Business analysis
  - Evaluating the changes in business objectives chosen for the modeling project

## Framing ML Problems

Business problems are often not immediately translatable into ML problems. They need to be framed in a way that ML can solve. Investigate the problem and frame in a way that has inputs, outputs, an objective function, and data.

### Types of ML tasks

- Classification
  - Classifies inputs into different categories
- Regression
  - outputs a continuous value

These two main types are relatively interchangeable its just a difference in how the model outputs, and how those outputs are treated.

Classification breaks down into three categories:

- binary classification
  - Two categories. The easiest form of classification
- multiclass
  - multiple categories. This is harder and typically models will need at least 100 samples from each class. High cardinality problems (many many classes) are very hard in terms of the data required alone.
  - hierarchical classification can be useful to dealing with high cardinality. This is having multiple models predicting into increasingly specific categories.
- Multilabel
  - unlike the previous two, predictions here can be on multiple classes
  - two approaches:
    - use one multiclass model and predict on multiple classes instead of just one
    - use a series of binary classification models, each predicting yes/no on each class

Multilabel is usually the categories that companies have the most problems with. Having multiple labels for each piece of data increases problems with labelers disagreeing on proper labeling. It also makes it harder to extract predictions from raw probabilities given the varying classes (in multiclass you just pick the highest probability, but in multilabel how do you know how many classes to pick?).

Changing how you frame a problem may make the task significantly easier or significantly harder.

Example:

- When predicting what app a user might pick next, instead of taking the user and environment feature and predicting multiclass on every app, you could have a model that takes user featuers, app featuers, and environment features and gives a probability. This could be run on each app
  - benefits: this means a new model won't have to be made every time there's a new app. The new app's features can just be fed into the model

### Objective Functions

Choosing objective functions to optimize for and guide the learning process is usually straightforward. There's many common ones that work well.

#### Decoupling Objectives

When there's conflicting objectives or multiple metrics wanting to be optimized this can pose a challenge for model development. There's a couple options:

- Train one model with a joint loss function
  - This works but every time you want to play around with the weights of the loss function to optimize the different metrics you have to retrain the whole model
- Train multiple models. One for each objective. Then combine the results afterwards
  - This is preferred. Doesn't require retraining the model when weights need to be changed, and if one objective stops being useful or needs to be changed this is an easy swap.

Decoupling objectives makes model development and maintenance easier

## Mind Versus Data

Are data or algorithms more important?

- Most people today think data, but others say data can only do so much and ML algorithm development is the future
- Most important thing right now is the **right** data. Not necessarily just more of it. Wrong labels / bad data can hurt model performance
