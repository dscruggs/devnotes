# Chapter 11: The Human Side of Machine Learning

ML systems aren't just technical. They involve business decision makers, users, and developers

## User Experience

ML is different than traditional software:

- probabilistic
- ML predictions can be "mostly correct"
- They can take an unexpectedly long time to produce predictions

### Ensuring User Experience Consistency

Users expect a certain level of consistency. Inconsistencies of ML may prove to be a hinderance tot his

There's a `consistency-accuracy` tradeoff, where the predictions deemed most accurate by the system may not be the most consistent to provide to the users. Business rules can help here

### Combatting "Mostly Correct" Predictions

In some cases you may want less consistency and more diversity in a model's predictions

If it's too expensive to produce routinely accurate results (like for ChatGPT), you could show multiple results in a way that allows users to easily evaluate which is correct and choose it. This is called `human-in-the-loop AI`

### Smooth Failing

Normally fast models might take a long time with some queries. How should you handle these?

- some companies use a less optimal model that's guarunteed to return results fast. Sometimes even just business rules, if the main model takes too long to return results

## Team Structure

What's the optimal structure when organizing an ML team?

### Cross-Functional Teams Collaboration

Having SMEs and non technical people is helpful to make sure the system is useful to users and contains the right logic

Challenging to communicate to non technical people and need to make sure they don't burden engineers

### End-to-End Data Scientists

ML production is not just a ML problem but also an infrastructure problem

To do MLOps, you don't only need ML expertise but also Ops expertise around deployment, containerization, job orchestration, and workflow management

You can either have a separate ops team to handle production, or have data scientists own the whole process

#### Approach 1: Have a separate team manage production

Data science/ML team develops the models, and Ops/platform/ML engineering team handles the models in prod

Benefits:

- makes hiring easier
- makes life easier for each person to only have to think about one corner of the system

Drawbacks:

- communication and coordination overhead increases
- Debugging becomes harder
- Finger pointing can occur
- Narrow context to issues and solutions
  - proactive and large optimizations are hard to do

#### Approach 2: Data scientists own the entire process

Data science team has more to worry about because they also much productionalize models

Data scientists become grumpy unicorns

The drawback is the more you focus on the infrastructure side the less you know about the model development side and vice-versa

It's really important for this model to develop or use tools to abstract away the low-level components of managing parts of the workflow otherwise its overwhelming

## Responsible AI

Responsible AI is a collection of ideas designed to ensure the proper capabilities and uses of AI, and making sure failure doesn't have catastrophic consequences

Some failure points to consider:

- Failure to set the right objective
- Failure to perform fine-grained evaluation to discover potential biases
- Failure to make the model transparent
- Failure to use anonymized data in some cases

### A Framework for Responsible AI

#### Discover sources for model biases

- training data
- labeling
- feature engineering
- model's objective
- evaluation

#### Understand the limits of a data-driven approach

Data isn't always enough to understand and solve problems

#### Understand the trade-offs between different goals

- Privacy vs accuracy trade-off
  - models with less data for privacy reasons may perform worse
- Compactness vs fairness trade-off
  - compression can amplify negative effects on the long-tail end of sample/feature distributions

#### Act Early

Think about this stuff as early as possible

#### Create Model Cards

Provides info on how models are trained, evaluated, and intended to be used

#### Establish processes for mitigating biases

Recommendations and tools exist out there

#### Stay up-to-date on responsible AI

This is constantly new and evolving
