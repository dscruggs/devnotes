# Chapter 1: Overview of ML Systems

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
  - **throughput** or the number of queries processed in a certain period of time is the goal in research, in production often **latency** or the time it takes to process one query is more important. Often this can mean only processing one query at a time to speed up latency while slowing down throughput for the benefit of the customer waiting for a prediction.
  - Use percentiles when quantifying latency, not something like an average. Because the distribution matters if some queries are taking a very long time. A common metric is 90th percentile latency has to be below a certain number
- Data
  - Data is often clean in research and so the focus is on the model.
  - In production most of the time will be spent cleaning and making sure the data is good to use. It's messy, unstructured, and constantly changing.
- Fairness
  - Fairness in research is considered after models are made
  - In production this has to be considered before deployment

- Interpretability
  - In research again performance is the most important metric
  - In production explainability is important both to increase trust in ML systems, but also to help debugging model performance and helping reduce biases
- Discussion
  - The vast majority of jobs are in production ML applications, not research. So it's better to know that than fully understand ML research

### ML Versus Traditional Software

ML could greatly benefit from more SWE skills in the field. But many challenges in ML are unique to ML and not SWE.

1. In SWE Code and data are treated as separate, and things are kept as modular and separate as possible. In ML, systems are part code, part data, and part artifacts created from both. ML systems can be improved by better code, but also better data
2. In SWE you only need to test and version code. In ML you need to version data too. Questions: *How do you version large datasets? How do you know if a sample is good or bad for your system? How do you sample to most effectively improve your model?*
3. The size of ML models in much larger than traditional code due to data and model artifacts (LLMs have billions of parameters and petabytes of training data)
4. Monitoring and debugging ML systems is significantly more challenging than traditional software

## Summary

- ML can't solve all problems. If it can't solve a whole problem it may be able to solve a part of one
- ML is different in research and production in terms of stakeholder interests, computational priorities, and data used
- ML systems are complex, and understanding each part of the stack and how they interact with each other is important
