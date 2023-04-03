# Chapter 8: Data Distribution Shifts and Monitoring

Deploying a model isn't the end of the process. You have to continually monitor its performance to detect issues as well as deploy updates to fix those issues

This and the next chapter discuss how to keep an ML model in production

## Causes of ML System Failures

Defining failure: when one or more expectations of the system is violated.

- Traditional software is usually just about the operational expectations (latency, throughput, uptime)
- ML systems care about operational metrics and ML performance metrics (accuracy, etc.)

Operational violations are easier to detect, they usually have an error message. ML performance expectation violations are harder. For this reason ML systems often fail silently

### Software System Failures

#### Dependency failure

A software package or a codebase your system depends on breaks. This is common when dependencies are managed by third parties, especially when those third parties stop supporting that dependency

#### Deployment Failure

Examples: Deploying a binary of an older version of your model, or when systems don't have the right permissions

#### Hardware Failures

When hardware doesn't behave as expected or breaks down (overheat, OOM, etc.)

#### Downtime or crashing

A component of your system running on a platform like AWS fails because AWS is down

Software system failures are a very common cause of failures in ML systems

- Many are related to distributed systems
  - orchestration errors
  - data structure issues

### ML-Specific Failures

Failures specific to ML problems.

Examples: data collection and processing issues, poor hyperparameters, training and inference pipeline inconsistencies, data distributional shifts, edge cases, degenerative feedback loops

#### Production data differing from training data

It's essential in ML for the training data to come from similar distributions to the unseen data. That's how models "learn" and "generalize". They learn a distribution that can be applied to new data

This assumption is rarely the case:

- Curating training data that accurately represents the real world is very hard. There's noise in real world data and many sources of bias in training data
  - this common failure mode and divergence is known as `training-serving skew`
- Real world data isn't `stationary`. Things change and data distributions shift
  - Big events don't need to happen to cause shifts. They happen all the time

Many issues that look like data shifts in the metrics may actually be errors in the data pipeline or model code. Make sure to debug these before retraining a model

#### Edge Cases

`Edge cases` are samples so extreme that they cause the model to make catastrophic mistakes. If there's an increase in the number of data samples in which your model doesn't perform well, it could be an indication that the underlying data distribution has shifted

**Outliers vs Edge Cases**
`Outliers` refer to data that differs significantly from other examples
`Edge Cases` refer to performance: an example where a model performed worse than other examples. An edge case may or may not be a result of an outlier

#### Degenerate Feedback Loops

This is when a model's predictions influence the feedback a model gets, which can influence the next iteration of the model. Even if a model's predictions aren't directly used for future labels, they may influence user behavior and contribute to a negative feedback loop

This is one reason why the most popular songs, books, etc. only get more popular in recommendation systems. This issue is very common in production

Another example is a resume tool that recommends people from certain schools who then get hired, so in the future the model only looks at people who come from those schools

These loops can perpetuate and magnify biases seen in the data

#### Detecting Degenerate Feedback Loops

When a model is offline it's difficult to detect these loops, they need user interaction and feedback

For recommender systems, you can look at the popularity diversity in a system's outputs even when the system is offline. There will likely be a long tail of popularity with a few popular items and many less popular items. Measures like `aggregate diversity` and `average coverage of long-tail items` help you to check if your recommendations are heterogenous or homogenous (indicating bias)

You can also divide items into buckets based on popularity and look at accuracy for the items. If it performs well on popular items but not on less popular items there may be bias

If you notice a model in production has predictions becoming more homogenous over time there's likely degenerate feedback loops present

#### Correcting Degenerate Feedback Loops

**Randomization** - introducing randomness into predictions to reduce homogeneity. This makes sure that not all items always only get recommended because they're already popular

- randomization can improve diversity, but at the cost of user experience
- Intelligent exploration strategies can help if complete randomization is a bad idea. Something like `contextual bandits` can help increase diversity with acceptable prediction accuracy loss

**Positional Features** - show what order predictions are given in if that may be an influence on the predictions' value. Can be numerical or boolean

- This will help separate noise by thinking that a song was clicked because it was recommended or just because it was first so the first songs don't just keep getting recommended

## Data Distribution Shifts

This is when the data a model works with changes over time.

`Source distribution` is the distribution of the data the model was trained on. `Target distribution` is the distribution of the data the model runs inference on

### Types of Data Distribution Shifts

There's three types: covariate shift, label shift, and concept drift

Defining terms: inputs to a model *X* and outputs *Y*. Training data is thought to be pulled from the joint distribution **P(X,Y)** and ML usually calculates **P(Y|X)**. **P(X,Y)** can be decomposed as **P(Y|X)P(X)** or **P(X|Y)P(Y)**

#### Covariate Shift

Math definition: when P(X) changes but P(Y|X) remains the same. This refers to the first decomposition of the joint distribution

Intuitive definition: when the distribution of inputs change but the conditional probability of an output given the input remains the same. Ex: In a breast cancer model you have more women over the age of 40 in your training data than in your inference data, but the probability of women over 40 having breast cancer is constant so this is the same

One of the most widely studied forms of data distribution shift. A `covariate` is an independent variable that can influence the outcome of a given statistical trial but is not of direct interest.

In supervised ML, the label is the variable of direct interest and the input features are the covariates

Causes of covariate shifts:

- biases during the sample selection process
- training data is artificially altered to make it easier for the model to learn
  - oversampling and undersampling
- the model's learning process, especially active learning
  - instead of random selection of samples for training if we use samples that are most helpful for the model to learn this alters the input distribution
- In production major changes to the environment or way your application is used can cause this
  - ex: you have a model that measures probability of a free user becoming a paid user but marketing is campaigning to get higher income users in. now your userbase will change but the probability of a given user at a given income to convert remains the same

`Importance Weighting` can be used if you know how the real-world input distributions differ from training. This is estimating the density ratio between the real-world input distribution and the training input distribution and then weighting the training data according to this ratio. Then training the model on the weighted data

We often don't know the difference in distributions so this is hard in practice.

#### Label Shift

Math definition: when P(Y) changes but P(X|Y) remains the same. This refers to the second decomposition of the joint distribution

Intuitive defintion: this is when the output distribution changes, but for a given output the input distribution is the same. Very related to covariate shift as often changes in input data change P(Y) without changing P(X|Y)

Covariate shift doesn't always cause label shift. In the breast cancer example if there's a preventative drug introduced to the market and lowers everyone's chance of getting breast cancer equally, this is a label shift. But the distribution of inputs will be the same so it's not label shift

Methods for detecting and adapting to label shift are very similar to covariate shift

#### Concept Drift

Math definition: when P(Y|X) changes but P(X) remains the same. This refers to the first decomposition of the joint distribution

Intuitive Definition: posterior shift. When the input distribution remains the same but the conditional distribution of the output given an input changes. Basically a change in the cause-effect relationship between inputs and labels.

An example is if before COVID a three bedroom apartment was 200k and at the beginning of covid a lot of people left the city and now its 100k. The inputs for the house will remain the same but the output changed.

Often concept drift is seasonal, and multiple models for different times of year/days can help

### General Data Distribution Shifts

These aren't statistical concepts but general shifts that happen during model development and maintenance

- `Feature Change` - when new features are added or old features are removed or all set of possible values of a feature changes
  - ex: using months where the model used to use age. Or a feature being all 0s. This is usually a bug in the data pipeline
- `Label schema change` - when the possible set of values for Y change. This changes both P(Y) and P(X|Y).
  - in regression tasks this could happen when the possible range of values changes (ex: credit score system changes from 300 to 850 to 250 to 900)
  - In classification this could happen if you add new classes, classes become outdated, or classes become more fine-grained
  - This problem is especially common in high-cardinality problems like document or product categorization

### Detecting Data Distribution Shifts

Data distribution shifts are only a problem if your model's performance degrades, so the first step is to monitor performance metrics. Set up flags for if performance drops or spikes in some way

Monitoring performance requires ground truth labels which aren't always possible to continuously get in production

There's other methods to monitor distributions of interest instead:

- The input distribution
- the label distribution
- the conditional distribution

The label distribution requires knowing P(Y) which would require ground truth labels, so most methods in industry monitor the input distribution

#### Statistical Methods

**Comparing Statistics**
min, max, mean, median, variance, quantiles, skewness, kurtosis, etc are a common method of detecting drift.

These are a good start to flag issues, but even if these are the same there's no guarantee there's still no issues

**Testing Methods**

`Two-sample Hypothesis Testing` is a test to determine whether the difference between two populations is statistically significant. This will give the probability that the difference is a random fluctuation due to the sampling variability. You can snapshot the data from one day and compare it to the data of another day to see if there's a difference

This isn't a foolproof method and should not be the only metric used. But a good heuristic is if you can detect the difference with a relatively small sample size it's a problem. With a very large sample size it may not be worth worrying about

`Kolmogorov-Smirnov test (KS test)` is a simple two-sample test. It's a nonparametric statistical test which means it doesn't require parameters from the underlying distribution to work. It can also work on any distribution. **BUT it can only be used on 1D data**. It's also expensive and produces a lot of false-positives

`Least-Squares Density Difference` is based on least squares density-difference estimation method.

`Maximum Mean Discrepancy (MMD)` is another one that is a kernel-based technique for multivariate two-sample testing (variant `Learned Kernel MMD`) This is only popular in research as of now

`Alibi Detect` is a great open source package for many drift detection algorithms

Two-sample tests work best on low dimensional data, so you can do dimensionality reduction to help the two-sample tests perform better

#### Time scale windows for detecting shifts

Not all shifts are created equal. Some happen at different rates and are harder to detect than others.

Shift can happen across two dimensions: spatial or temporal

- Spatial shifts are across access points (new group of users or different type of device in the application)
- temporal shifts are across time. to detect this a common method is to treat input data to ML applications as time-series data

The time scale window of the data we look at affects the types of shifts we can detect. Seasonal variation can make detecting shifts hard if you aren't aware of it

`Sliding statistics` are computed within a single time window
`Cumulative statistics` are continually updated with more data

Cumulative statistics can obscure what happens in a specific time period

Many companies use the distribution of the training data as the base distribution and monitor the production data distribution at a certain granularity level such as hourly and daily.

The shorter the time window, the faster you can detect changes. But also the smaller your sample size, and so the distribution may not actually be a representative sample comparable to your training data

You can merge statistics from shorter time periods into longer time periods. Such as hourly to daily, daily to monthly, etc.

More advanced monitoring platforms attempt root cause analysis feature that automatically analyzes statistics across various time window sizes to detect exactly the time window where a change in data happened

### Addressing Data Distribution Shifts

Many companies periodically retrain regardless of metrics to address shifts. The frequency of retraining varies by use case

To many a model work with a new distribution in production there's 3 main approaches:

- train models using massive datasets
  - popular in research. This hopes that the training data is large enough that whatever new data is encountered will likely come from this distribution
- adapt a trained model to a target distribution without requiring new labels
  - this isn't very popular in industry and is underresearched
- retrain your model using the labeled data from the target distribution
  - this is the most popular in industry
  - you can retrain from scratch on old and new data, or continue training the existing model on new data (fine tuning)

#### Model Retraining

Retraining Questions:

- How to retrain?
  - `stateless retraining` - retrain from scratch
  - `stateful retraining` - continue training the model from last checkpoint
- What data to train on?
  - Need to pick a period of time to use data from to retrain. May require experimentation to decide

You can think of the data distribution shift problem as a less extreme case of adapting a model to a new domain. And retraining can be thought of a transfer learning

Consider `feature stability` when building a model to make it more resistant to drift. Fast changing features are a sign you'll need to retrain more often. You can bucket fast-changing features to slow them down, but that may hurt the predictive power

Deploy a separate model for a fast changing subset of data (ex: housing price calculator, you may want to separate out new york or san francisco so you don't have to retrain models for rural arizona so often)

Remember: not all ML problems are related to the ML part. Make sure to look out for human error

## Monitoring and Observability

`Monitoring` is the act of tracking, measuring, and logging different metrics to help determine when something goes wrong

`Observability` means setting up a system in a way that gives us visibility into our system to help investigate what went wrong

observability also can be called `instrumentation` where examples are setting up timers for functions, counting NaNs in features, tracking how inputs are transformed through your systems, logging unusual events such as unusually long inputs, etc. Observability is part of monitoring because without some level of observability monitoring is impossible

Monitoring is all about metrics.

Software metrics for ML systems can be thought of in three main categories of tracking:

- network
  - requests refused, latency, etc.
- machine
  - memory/compute capacity, etc.
- application
  - resource utilization, traffic, etc.

A classic metric is `uptime` which means the proportion of time a system is available with reasonable performance to users. Amazon guaruntees 99.99% uptime for EC2

However, even if the system is running, for ML systems if the predictions are garbage users won't care. There needs to be ML specific metrics considered

### ML-Specific Metrics

#### Monitoring Accuracy-related Metrics

If your system gets any kind of user feedback for the predictions it makes, you should log and track it (click, hide, purchase, upvote, etc.)

Even if the feedback can't be used to infer natural labels directly, it can still help you detect changes in model performance

You can engineer your system to collect more useful user feedback (upvote/downvote) to at least guide future labeling efforts

#### Monitoring Predictions

This is the most common artifact to monitor. Summary stats are easy to monitor and predictions are easy to visualize since they're usually 1 dimensional

You can monitor predictions for distributional shifts. You can also run two-sample tests easily on them, and this can help you infer changes in the data distributions

You can monitor predictions for anything odd happening, and this can help you catch things more quickly than accuracy-related metrics

#### Monitoring Features

You can track changes in features, both for inputs and intermediate transformations from raw inputs to final features

Feature monitoring is appealing compared to raw data because its more structured

`Feature validation` can be used to check for changes in expected schemas for features, usually made from common sense or training data. also called `table testing` or `table validation`

- check if the min, max, median values are within an acceptable range
- check if the values of a feature satisfy a regular expression format
- if all the values of a feature belong to a predefined set
- if the values of a feature are always greater than the values of another feature

Great Expectations and Deequ are open source libraries that help you do basic feature validation

You can use two-sample tests to see if the distribution of feature values or a set of features have changed

Four major concerns whene feature monitoring:

- A company might have hundreds of models in production, and each model uses hundreds if not thousands of features
  - computing even simple summary stats for these features ever hour can be expensive in both compute and memory. This can also add to user latency by increasing system load
- While tracking features is useful for debugging, its not very useful for detecting model performance degradation
  - an individual feature's minor changes might not harm model performance, so you may get false alarms and alert fatigue
- Feature extraction is often done in multiple steps and using multiple libraries
  - even if you detect an issue with a feature it might be impossible to detect whether this change is caused by a change in the underlying input distribution or whether its caused by an error in the processing steps
- The schema that your features follow can change over time
  - if you don't have a way to version your schemas and map each of your features to its expected schema you may get alerts from a mismatched schema rather than data distribution change or issue

#### Monitoring Raw Inputs

Raw inputs are not easier to monitor than features because they can come from multiple sources in different formats

Many ML workflows make it impossible for ML engineers to access raw data, and raw data is often managed by a separate team

If raw data is monitored its usually done by the data platform team and not ML engineers, so this book doesn't discuss

### Monitoring Toolbox

Measuring, tracking, and interpreting metrics for a complex system is a nontrivial task. and tools are used to do this

#### Logs

Traditional software uses logs to record runtime events. Ex: container startup time, memory requirements, when a function is called, outputs of functions, crashes, stack traces, error codes, etc.

In the modern day with so many services a process may hop to 20-30 places before an error occurs. The hard part today isn't finding when something happened, but where the problem was

We want to make it as easy as possible to find errors later when something goes wrong. In microservice architecture this practice is called `distributed tracing` which means giving each process a unique ID so that when something goes wrong the error message will have that ID to search through logs

ML models can be used to analyze logs because the problem of going through this vast data is hard. They can classify or flag logs to be looked at

You can process logs in batch or real-time and look for specific events to alert errors

#### Dashboards

Dashboards to visualize metrics are critical for monitoring: a picture is worth 1000 words

Dashboards make monitoring accessible to non-engineers, to show metrics and monitoring to stakeholders and the dev team

Too many metrics on a dashboard can be counterproductive, a phenomenon called `dashboard rot`. It's important to choose wisely what to show to make sure your signal-to-noise ratio is high

#### Alerts

Its important to alert the right people when the system detects something suspicious. An alert consists of three components:

- An alert policy
  - the condition for the alert. A metric breaching a threshold for some duration is a common flag
- Notification channels
  - These describe who is to be notified when the condition is met. It can show up in monitoring tools like Cloudwatch, but you also may want to configure email or other means of communication in case you aren't checking alerts
- A description of the alert
  - this helps the alerted person understand whats going on and know where to look
  - provide as much detail as possible and try to make it actionable (provide a runbook or possible mitigating actions)

Be careful with alerts (both with who to send them to and how many to send). alert fatigue is real

### Observability

Monitoring helps you figure out *when* things go wrong. But there's no guarantee it will tell you *what* went wrong. That's where observability becomes important

Software systems are complex enough to no longer allow the breaking apart of systems to figure out what's going on. Engineers need to rely on external outputs and observable system design to figure out problems

Observability is the concept drawn from control theory to bringing "better visibility into understanding the complex behavior of software using outputs collected from the system at runtime". `Telemetry` is the term for system outputs collected at runtime (logs and metrics)

Observability is about instrumenting your system in a way to ensure that sufficient information about a system's runtime is collected and analyzed to be able to figure out what went wrong without going into the codebase and pushing new code

In the ML world this means monitoring would be showing aggregate metrics over time, observability means that you could say for what inputs and subgroups performance degrades on (ex: you should be able to say "show me all wrong predictions in the last hour and group them by zip code" in your logs). To do this you need to log with tags and keywords that are useful

In ML observability encompasses interpretibility. Being able to understand what went wrong with a model helps with understanding how it makes predictions and fix them
