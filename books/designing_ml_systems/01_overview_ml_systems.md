# Chapter 1. Overview of ML Systems

- algorithm only small part of ML system in production
- other components:
  - business requirements
  - interface where users interact with your model
  - data stack
  - logic for: developing, monitoring, updating model
  - infrastructure  that enables delivery
  -

Relationship between MLOps and the ML Systems Design

- Ops in MLOps comes from DevOps - deploying, monitoring, maintaining
  - MLOps is set of tools and best practices for bringing ML to production
- ML systems design takes a system approach to MLOps, which means that it considers an ML system holistically to ensure that all the components and their stakeholders can work together to satisfy the specified objectives and requirements

## When to use machine learning

ML is not a magic tol that can solve all problems. Before doing ML make sure it is necessary and cost-effective for your problem.

### What makes a good ML use case

Generally, Machine learning is an approach to **learn complex patterns from existing data, and use these patterns to make predictions on unseen data**. Breaking down each part of this can define requirements for ML systems:

- **Learn:** systems must be able to learn a relationship, and be able to learn a relationship between the data and the outcome
- **complex patterns:** there should be patterns to learn, and those patterns to justify ML should be complex
- **existing data:** There must be data available or the ability to collect that data. Continual learning systems can be launched with minimal data if accuracy isn't paramount off the bat
- **predictions:** the problem should be predictive
- **unseen data:** the unseen data should share patterns with the seen data

Additional characteristics that can make projects especially good:

- **it's repetitive:** repetition of patterns in data makes it easier for machines to learn
- **the cost of wrong predictions is cheap:** ML models will never be 100% correct. If a wrong prediction can have catastrophic consequences ML may not be a fit. The benefit of positive predictions should be able to outweigh the harm for wrong ones.
- **it's at scale:** ML solutions should make a lot of predictions to justify the amount of time and effort it takes to build systems, and it means more data for the system to learn
- **the patterns are constantly changing:** hardcoded rules will fail here, but ML can be retrained and adapt to changing patterns

### When not to use ML

- it's not cost effective
- it's unethical
- simpler solutions work

Even if ML can't solve your whole problem, it's possible to break it into smaller subcomponents to have ML solve part of the problem that it's a good fit for. Also don't dismiss a technology just because it's not immediately cost effective. Refusing innovation to save pennies can have costs down the road.

### ML use cases

- search engine
- recommender systems
- predictive typing
- machine translation
- facial recognition
- smart homes
- personal assistant
- fraud detection / anomaly detection
- price optimization
- churn prediction
- support ticket classification
- brand monitoring / sentiment analysis

Most applications are in the enterprise world currently, consumer applications are booming but the cost savings at an enterprise level makes these projects more attractive. Consumers are less tolerant of latency that comes along with many ML solutions that enterprises can tolerate.

## Understanding ML Systems

### Differences between Research vs Production Settings

- Different stakeholders and requirements
  - In research most people just care about model performance on a benchmark dataset, and models are often made complex to get extra performance so they are too complicated to be useful in production
  - In industry there are many different requirements and stakeholders to think about. Example: A recommender system for restaurants
    - ML engineers want the best performing model so will push for model complexity
    - Sales team wants the model to recommend the most expensive restaurants
    - Product team wants reduced latency on results
    - ML platform team wants to hold off on model updates to improve platform performance
    - Manager wants to maximize team margin so may want to lay off some folks
    - How to manage all these requirements? Develop models for multiple objectives and combine their predictions.
  - Model complexity is often just as important as performance in production. Complex models take longer to serve predictions and are harder to interpret and maintain.
  - **Complex models must perform significantly better to justify complexity**
- Computational priorities
  - While building models training time is the bottleneck. But in production inference latency is the bottleneck. Research prioritizes fast training whereas production usually prioritizes fast inference.
  