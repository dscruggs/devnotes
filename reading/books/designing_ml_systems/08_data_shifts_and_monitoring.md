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
