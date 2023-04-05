# Chapter 9: Continual Learning and Test in Production

How do we adapt models to data distribution shifts? By continually updating our ML models. This chapter is about doing that, which is mainly an infrastructure problem, and also how to go about testing new models in production

## Continual Learning

Learning from every new example is rarely done in production for several reasons:

- neural networks are susceptible to something called `catastrophic forgetting` or suddenly forgetting previously learned information when new information comes up
- it can make training more expensive because most hardware is designed for batch processing, so processing 1 sample at a time can be a huge waste of resources
- the updated model shouldn't be deployed until its evaluated

Instead companies either do scheduled retraining or retrain every certain number of samples such as 512 or 1024 examples in a `micro-batch`

Evaluation of new models is important, so changes should be made on copies of deployed models to evaluate changes in performance. Only replace the existing model if the updated model performs better. The existing model is called the `champion model`, and the new model the `challenger model`

A good rule is only update the model with enough frequency to improve performance enough to justify the extra overhead of the continual deployment

### Stateless Retraining Versus Stateful Training

When to retrain is only part of the problem. There's also a question of how to retrain

`stateless retraining` is a full training from scratch every time  
`stateful retraining` is a model continuing training on new data. Also called incremental learning or fine-tuning

Stateful training allows you to retrain model with less data

Grubhub dropped their compute training cost 45 times and increased purchase-through rate by 20% by switching from daily stateless to daily stateful retrains

Stateful training also allows you to not have to store every single training sample indefinitely if you don't want to or if privacy restrictions are a problem since you just need the new data to train on

Companies combine both stateful and stateless retrainings by occassionally doing full updates then updating statefully for a while

#### Changing models with stateful retraining

We need to differentiate between different model updates

`Model iteration` - when a new feature is added or the model architecture is changed  
`Data iteration` - when new data is used to refresh a model

Stateful training is mostly applied for data iteration. Model iterations require full retrains as of today

### Why Continual Learning?

1. It helps combat data distribution shifts, especially when shifts happen suddenly
2. It helps to adapt to rare events. Models don't have enough data to learn all possible scenarios so it needs to be able to adapt to changes in conditions (ex: black friday for amazon)
3. Helps with the `continuous cold start` problem
    - this is when models have to make predictions on a new user without any historical data
    - This doesn't just happen with new users but existing users doing very new things or using different platforms, or when users haven't visited in a long time.
    - Continual learning helps to adapt

The question shouldn't be "why continual learning?". It should be "Why not continual learning?"

There's many challenges to continual learning in current day, but all else equal with cost and complexity you should always do it

### Continual Learning Challenges

Fresh data access, evaluation, and algorithms

#### Fresh Data Access Challenge

You need new data as frequently as you want to update your models

There's many bottlenecks to getting fresh data

- you need new data itself
- you need to be able to retrieve the data from a warehouse or real-time transport pipeline
- you need labels. This is often the biggest bottleneck

The best candidates for continual learning are models that generate natural labels with short feedback loops. (stock price predictions, ad click-through rates, recommender systems, etc)

Often times natural labels are not direct labels you can use, but user behavior that needs to be extracted into usable labels. This process of looking into logs to extract labels is called `label computation`, and it can be costly if logs are large

Batch processing for logs can be slow, and streaming computations for labels is currently complex and expensive. Tooling needs to improve here

#### Evaluation Challenge

How do you make sure model updates are good enough to deploy?

The risk of catastrophic failure is amplified with continual learning:

- each update has the risk of failure
- continual learning means models are more susceptible to coordinated manipulation and adversarial attacks

It's important to test your models before deploying. There's either offline or online evaluation. Online is discussed later in this chapter

Sometimes it's hard to test models if its infrequent events you care about (like fraud detection), and this can bottleneck your ability to deploy updates

#### Algorithm Challenge

Not all models have this problem. This only affects matrix-based and tree-based models that want to be updated very fast (hourly)

You need the entire dataset to update a collaborative filtering model or a tree-based model. You can do this, but it can be very expensive and slow, so neural networks are better suited for continual learning

Hoeffding Tree and its variants is one example of tree-based models that can learn incrementally

Both the features extraction code and the model have to be able to work with partial datasets.

For statistics requiring global statistics one idea is to keep running total of statistics to use for feature extraction

### Four Stages of Continual Learning

Continual learning isn't something companies start out with. It's a process

#### Stage 1: Manual, stateless retraining

In the beginning ML teams often focus on developing ML models to solve as many business problems as possible. Becauset the focus is on new models, updating models takes a backseat

Models are updated infrequently, and only when models degrade in performance significantly and people have time to retrain

The process of updating is manual and ad hoc, and someone goes through the process of querying data, extracting features, and retraining on both old and new data

Oftentimes this leads to issues in deploying the new model because model and data code has changed between deployments, causing hard to fix bugs

#### Stage 2: Automated Retraining

Most companies with someone mature ML infrastructure are in this stage

You have 5-10 models in production, and the priority is no longer to put out new models but to maintain existing models

A script is made to automatically execute all steps of retraining to address the manual pain points and this is run periodically with something like Spark in a batch process

Retraining is usually done based on gut feeling, maybe some experiments are happening

The automation script has to adapt to many different requirements if you have different types of models, or different parts to models like embeddings and predictions

Requirements of an automated retraining script/process:

1. Pull data
2. Downsample or upsample data if necessary
3. Extract features
4. Process and/or annotate labels to create training data
5. Kick off new training
6. Evaluate newly trained model
7. Delpoy

The feasibility of making a script that can do this depends generally on the scheduler, data, and model store:

- **scheduler** - you need a tool to orchestrate the scripts to perform these steps automatically, like airflow or argo
- **data** - availability and access of data is important. do you need to gather it yourself, will you need to join data from multiple organizations? Do you need to extract features from scratch and/or label your data? The more complex this process, the more complex the script
- **Model store** - automatically versions and stores all the artifacts needed to reproduce a model. Simplest version could be an S3 bucket. Amazon Sagemaker is a more mature model store. MLFlow is another one that's open source

#### Stage 3: Automated, stateful training

In stage 2 retraining is costly since it's from scratch. So instead your automatic updating script loads the latest version of the model and continues training on less data

Requirements:

- Mainly a mindset shift. Its easy conceptually to train from scratch, but its costly
- Reconfiguring the update script to support this
- Tracking lineage is cool but tools don't really do it, so you could do it in house (see what models are trained off what)
- if you want to support pulling data from streaming instead of data warehouse you'll need to refactor your script and likely infrastructure

#### Stage 4: Continual Learning

In stage 3 models are still updated based on fixed schedules set by developers

In this stage you can set schedules or trigger updates based on shifts in the data distribution

The holy grail of this is continual learning with edge deployment. Where a deployed model never has to sync with a central sever

Requirements:

- you need a mechanism to trigger model updates:
  - time based (every 5 minutes)
  - Performance-based (when performance degrades enough)
  - Volume-based (when the total labeled data increases by x%)
  - Drift-based (when data shifts are detected)
- you need solid monitoring solutions for these triggers and to make sure models perform well after auto deployment
- You need a solid evaluation pipeline

### How Often to Update Your Models

The more gain you get with fresh training, the more argument there is for faster learning cycles

#### Value of data freshness

You should figure out how much gain you'll get from faster retraining

You can do this by training your model on data from different time windows in the past and evaluating it on the data from today to see how the performance changes.  
Ex: train 3 models on data from jan-april,may-august, and september-november and then evaluate on december. See the performance difference

#### Model Iteration vs Data iteration

You can update the model as well as the data. The more resources you devote to one will make the other less of a priority

You should compare the costs of switching to a new model (compute, performance, latency, etc.) with the costs of just retraining more frequently. If its cheaper and simpler to switch to a new model, then do it. If not, then retrain more

## Test in Production

You need a mixture of offline and online evaluation to truly test a model

Offline evaluation has shortcomings:

- testing new models on a static dataset, if the data distribution has changed, is not very helpful
- you can test models on data from the last hour (`backtesting`) to help solve this problem
  - there's a chance this data could be corrupted or there be some anomaly that alters performance in this slice of data

You should evaluate both on static test sets for a sanity check, and backtest for more relevant results

No matter what you do offline, the data will always be stale. So there's no way to know how a model will do in production without running it in production. This section covers those methods

### Shadow Deployment

This is probably the safest way to deploy:

1. Deploy the candidate model in parallel with the existing model
2. Route requests to both models, but only serve the existing model's prediction to the user
3. Log predictions from the new model for analysis

Replace the existing model when you're satisfied with performance

This is safe, but expensive. because you're running two models now. It can also cause slowdowns in your model latency

### A/B Testing

A/B testing is a way to compare two variants of an object to see which is more effective:

1. Deploy the candidate model alongside the existing model
2. A percentage of traffic is routed to the new model for predictions, the rest is routed to the existing model for predictions
    - typically both models serve predictions in production simultaneously. In some cases where model predictions affect other models or eachother you may have to test on different days or times
3. Monitor and analyze predictions and user feedback to determine if the model results are different

A/B testing done right requires a random experiment:

- traffic should be routed randomly (across platforms and other variables. No seleciton bias)
- there should be a sufficient number of samples to gain enough confidence about the outcome

You can do hypothesis testing to see if the differences in model performance are statistically significant. Whether or not the difference is significant is not the end-all-be-all. You should look at many factors to determine if one model truly did better, not just the p-value of the test

You can do more than just 2 models at once with this method

### Canary Release

This technique reduces the risk of deployment by introducing the new model to a small subset of users before rolling it out to the entire infrastructure for everybody:

1. Deploy the candidate model alongside the existing model. The candidate is the canary
2. A portion of traffic is routed to the candidate model
3. If performance is satisfactory, traffic to the candidate model can be increased, and if not you can abort and route traffic back to the existing model
4. Stop when either the canary serves all the traffic or the canary is aborted

This can be used to implement A/B testing, however you can also do it without randomizing traffic to each model. A common scenario is rolling out a new model to a less critical market

### Interleaving Experiments

This is the idea of exposing users to predictions from both existing and candidate models and letting them chose which is better

There's no guarantee user preference will lead to better core metrics

It's important to remember the position of a recommendation influences user decisions, so you should ensure that given a position, a recommendation is equally likely to be generated by A or B.

`team-draft interleaving` is a process to combat this issue by picking randomly for each spot model A or B and then that model picks the top recommendation left

### Bandits

Bandits are derived from gambling (a slot machine is a one-armed bandit) and the idea is to experiment over time to maximize payout given some inputs and actions

`Multi-armed bandits` are algorithms that balance between exploitation (choosing the slot machine that has paid the most in the past) and exploration (choosing other slot machines that may pay off even more)

Multi-armed bandits can be used to evaluate candidate models to maximize payoff (prediction accuracy) while exploring how to route traffic to the different models

A/B testing is stateless, bandit is stateful. You need to calculate all model's current performance to make decisions which requires:

- Your model must be able to make online predictions
- Short feedback loops are better. You need to know when a prediction is good or not for effective bandits
- A mechanism to collect feedback and keep track of model performance is needed, and to be able to route traffic to models based on their performance

Bandits are popular in academia, but are difficult to implement. Right now they're mostly at big-tech companies

Bandit algorithms

- `epsilon greedy` is the most popular: % of the time exploit, % of the time explore
- `Thompson Sampling` and `Upper Confidence Bound (UCB)` are the most popular exploration algorithms.
  - thompson sampling selects a model with a probability that this model is optimal given the current knowledge
  - UCB selects the item with the highest upper confidence bound (optimism in the face of uncertainty). This gives an uncertainty bonus to items its uncertain about

#### Contextual Bandits as an exploration strategy

Contextual bandits are used to determine the payout of each action. They're great at improving data efficiency in models

In recommendation systems you may have 1000 items but only get feedback on 10 of them. This is the `partial feedback problem`, also known as `bandit feedback`. Contextual bandits are classification problems with bandit feedback

Contextual bandits help balance between showing users the items they will like and showing the items that you want feedback on. They're well research and can improve model performance significantly, but they're harder to implement than model bandits because the exploration strategy depends on model architecture

### Testing Summary

People who develop models should be the ones responsible for evaluating them. And the evaluation metrics should be standardized across the team to avoid bias and to compare models on a level playing field

Teams should outline a clear pipeline for models to be evaluated:

- tests to run
- the order to run tests
- thresholds they must pass

These pipelines should be automated when possible and happen every time a model is deployed
