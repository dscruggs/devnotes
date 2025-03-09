# Chapter 6: Model Development and Offline Evaluation

Model development is the iterative process of selecting, training, testing, and evaluating models on training and validation data

There are different things to consider in this step such as debugging, experiment tracking and versioning, distributed training, and AutoML

## Model Development and Training

### Evaluating ML Models

There's not a one-size-fits-all solution in ML, and different models and approaches should be tested for each problem

#### Classical ML vs Neural networks

Classical ML isn't going away any time soon even with neural nets on the rise

Many recommender systems rely on collaborative filtering and matrix factorization

Tree based algorithms like gradient-boosted trees still power many classification tasks with strict latency requirements

Often classical ML is used in tandem with neural networks in ensemble models. k-means may be used to extract features to input into a neural network.

Neural nets may be used to generate word embeddings to put into a logistic regression

#### Tips for Model Selection

Different models have different use cases. So a specific use case will already narrow down what models to look at. Know which models to use for what problems.

Don't just consider performance of different models in terms of accuracy. Also think of compute, data, and latency requirements of both training and inference for those models

Six tips for model selection:

1. **Avoid the state-of-the-art trap** - many people want to jump straight to the state of the art. But even though those models perform the best in academic settings doesn't mean it will perform the best for your problem.
    - If there's a model that can solve your problem that's cheaper and simpler than state-of-the-art, use that instead
    - Stay up to date on state of the art in case there are useful breakthroughs
2. **Start with the simplest models**
    - Simple models are easier to deploy, and deploying early allows faster validation of predictions
    - Starting simple and moving to more complex models allows you to understand your pipeline better which makes debugging easier
    - Simple models can serve as a baseline to compare complex models to (to justify their complexity with performance gains)
3. **Avoid human biases in selecting models**
    - Compare architectures under comparable setups. Don't spend lots of time evaluating a model you think will work or like and then spend little time on architectures you don't like.
    - One model is rarely the best in all cases, so don't insert your biases on models you like into all problems

4. **Evaluate good performance now vs. good performance later**
    - the best model now doesn't mean the best model 2 months from now
    - ex: tree based model may work well now because you don't have a lot of data, but in the future when you have more data a neural network may work better
    - look at learning curves to evaluate how certain models will perform with more data
    - don't discount the ability of neural networks to learn over time vs. simpler models
5. **Evaluate trade-offs**
    - Understand what's important in your specific problem to help with model selection, all models will have trade offs
    - One classic example is the false-positive and false-negative trade off
    - compute and accuracy is another trade off. If accuracy is all that matters and you can use a GPU to generate inference, great. but if not, then consider using a smaller model
    - more complex models are not as interpretable
6. **Understand your model's assumptions**
    - Every model comes with its own assumptions, and understanding these and whether the data used fits those assumptions helps
    - Common assumptions:
        - `Prediciton assumption` - every model that aims to predict an output Y from an input X makes the assumptions that its possible to predict Y based on X
        - `IID` - Neural networks assume that examples are independent and identically distributed, which means that all the examples are independently drawn from the same joint distribution
        - `Smoothness` - Every supervised ML method assumes there's a set of functions that can transform inputs into outputs such that similar inputs are transformed into similar outputs. If an input X produces output Y, then an input close to X would produce an output proportionally close to Y
        - `tractability` - Let X be the input and Z be the latent representation of X. Everyone generative model makes the assumption that it's tractable to compute the probability P(Z|X)
        - `boundaries` - linear classifier assumes decision boundaries are linear
        - `conditional independence` - A naive bayes classifier assumes that the attribute values are independent of each other given the class
        - `normally distributed` - many statistical methods assume that data is normally distributed

### Ensembles

Using multiple ML models has been a method that has consistently given performance boosts to models

They're less favored in production than in things like kaggle competitions because they're harder to deploy and maintain

They're more common in places where small performance boosts can give huge financial gain such as ad click through rates

Why do they work?
    - Example: if you have 3 70% accuracy models each with 1 votes, it will give you an accuracy rate of 78.4% because only two of the models need to be correct

This only holds if the models are uncorrelated, the benefit is less if they are correlated. It's common to use very different types of models for an ensemble in most cases

Three ways to create an ensemble: bagging, boosting, and stacking

Bagging and boosting have been shown with resampling to help with unbalanced datasets

#### Bagging

Short for bootstrap aggregating. Designed to improve the training stability and overall accuracy of ML algorithms. Reduces variance and helps avoid overfitting

**Process:**  
Given a dataset, instead of training one classifer on the entire dataset, you sample with replacement to create different datasets called `bootstraps`, and train a model on each of these bootstraps. Sampling with replacement makes sure the models are independent

In classification problems, the final prediction is a vote of all models. In regression, the final prediction is an average of all models

Unstable methods like neural networks, trees, and subset selection in linear regression. But it can hurt performance mildly in stable methods like k-nearest neighbors

A random forest is an example of bagging. It's a collection of decision trees constructed by bagging and feature randomness, where each tree only uses a random subset of features

#### Boosting

It's a family of iterative ensemble algorithms that convert weak learners to strong ones

**Process:**  
Each learner in the ensemble is trained on the same set of samples, but the samples are weighted differently among iterations. As a result, future weak learners focus more on the examples that previous weak learners misclassified.

1. Train the first weak classifier on the original dataset
2. Samples are reweighted based on how well the first classifier did on them (misclassified ones are given higher weight)
3. Train the second classifier on this reweighted dataset. Now the ensemble is the first and second classifier
4. Samples are weighted based on how well the ensemble classified them
5. Train the third classifier on this new dataset and add to the ensemble
6. Repeat for as many iterations as needed
7. Form the final strong classifier as a weighted combination of the existing classifiers, and classifiers with smaller training errors have higher weights

`Gradient Boosting Machines (GBM)` are one example of a popular boosting algorithm. This is what XGBoost is based on, but LightGBM has gained popularity

#### Stacking

Training base learners from the training data then creating a meta-learner that combines the outputs of the base learners to output final predictions.

The meta-learner can be a simple heuristic (majority vote, average) or another model

### Experiment Tracking and Versioning

There's a lot of experimentation that happens during model development and many of them vary by only a few hyperparameters

It's important to keep track of all the definitions needed to re-create an experiment and its relevant `artifacts` (files created during an experiment: loss curves, eval loss graphs, logs, intermediate training results). This is to compare different experiments and help choose the best one for your needs

The process of tracking the process and results of an experiment is called `experiment tracking`. The process of logging all details of an experiment for the purpose of possibly recreating it later is called `versioning`. These two go hand-in-hand, and many tools such as MLFlow and Weights and Biases started as experiment tracking tools and have grown to versioning. Other tools like DVC started as versioning tools and have started experiment tracking

#### Experiment Tracking

A big part of building models is babysitting the learning process. There's a lot of things you need to pay attention to, and tracking these things during training can help debugging and save you from wasting time on training bad runs

**Short list of things to consider tracking:**

- `loss curves` - corresponding to the train split and each of the eval splits
- `model performance metrics` - on all the nontest splits. Could include accuracy, F1, perplexity, etc.
- `log of corresponding sample, prediction, and ground truth label`  - this is useful for ad hoc analytics and sanity checks
- `speed of your model` - evaluate in number of steps per second, or maybe tokens per second on text
- `System performance metrics` - such as memory usage, CPU/GPU utilization. Useful for avoiding bottlenecks and avoid wasting system resources
- `Changes in hyperparameters/parameters` - could be learning rate schedule, gradient norms (both globally and per layer) especially if you're clipping them, and weight norms especially if you're doing weight decay

Track as much as you can. Most of the time you shouldn't need to look at most of them, but when things go wrong its very helpful for debugging to have visibility into your model. In reality, tracking too many things is impractical and can distract from the important metrics. So choose carefully based on your model and problem

Experiment tracking allows you to compare how changes to your model or data change these metrics.

A simple way to do this is to automatically make copies of all the code files needed for an experiment and log all outputs with their timestamps

Third-party libraries give you nice visuals and allow for easier sharing of experiments

#### Verisoning

Versioning is important to be able to recreate experiments after you've done all your tests and picked the most successful and best model.

Everyone versions their code. But data is also important to version because models aren't just code.

Versioning data is hard. One reason is data is often much larger than code, so we can't use the same strategy people use for code.

**Challenges of data versioning:**

- Data is much bigger than code
  - Code is versioned with diffs and line-by-line comparisons of code, data can be indefinitely long and line-by-line comparisons is often infeasible
  - Keeping copies of old data is impossible for some large datasets
  - moving copies of data to local machines like is done with codebases is impossible for some datasets
- What actually is a diff for data
  - when do you consider a dataset "changed". When individual data is changed, when rows change, when columns change, when whole databases move?
  - If two people use a dataset to train two different models and modify them differently, you can't really combine those two changes into one
- There's data privacy concerns with versioning data

Aggressive experiment tracking and versioning helps with reproducibility but doesn't guarantee it. Hardware and frameworks may effect performance in some unknown ways.

### Debugging ML Models

Debugging ML models is more difficult than most software for a few reasons:

1. ML models fail silently. Code can run fine but predictions are still wrong. Developers and users can both miss wrong predictions and no one bats an eye
2. Validation is very slow for bugs, especially when retraining is required.
3. There's a lot of cross-functional complexity to ML systems. Data is managed by data engineers, labels by SMEs, ML algorithms by data scientists, and infrastructure by ML engineers or ML platform team

Here's some common causes of ML failures:

- **Theoretical constraints** - when a model is trained on data that doesn't meet its assumptions (ex: non-linear decision boundary for linear classifier)
- **Poor implementation of model** - Errors with the code, not the model. Ex: Pytorch errors like forgetting to turn off gradient updates.
- **Poor choice of hyperparameters** - the model is good for the data, but hyperparameters are chosen wrong.
- **Data problems** - noisy labels, preprocessing errors, incorrectly paired labels and data, feature normalizing with wrong statistics, etc.
- **Poor choice of features** - Too many features, too few features, features with no correlation with the labels, etc.

Debugging should be both preventative and curative. Follow best practices to minimize the need and effects of debugging and system failures.

Techniques for debugging:

- **Start simple and gradually add more components** - this lets you know your system better and find the bugs early in the process
- **Overfit a single batch** - overfit a small amount of training data and run evaluation on the same data to make sure that it gets to the smallest possible loss. If it can't overfit on a small amount of data, there may be something wrong with your implementation
- **Set a random seed** - this allows you to reproduce errors easier and gets rid of random seed randomness between experiment runs

### Distributed Training

As the scale of data increases distributed training becomes more important

When data doesn't fit into memory, algorithms for preprocessing, shuffling, and batching data will need to run out of core and in parallel

When a sample of your data is large then you can only work with small batch sizes which leads to instability in many ML model trainings

In some cases a data sample is so large that it can't fit into memory. This requires something called `gradient checkpointing`, this leverages the memory footprint and compute trade-off to make your system do more computation with less memory. This increases computation time but can help you fit larger batch sizes into memory

#### Data Parallelism

It's now the norm to train ML moodels on multiple machines. Data parallelism is the most common parallelization method supported by modern ML frameworks.

This involves splitting the data on multiple machines, training the models, and accumulating gradients. There's a few issues with this:

- accurately and efectively accumulating gradients from different machines is challenging.
  - As each machine runs its own gradients, if your model waits for all of them to finish a run `synchronous stochastic gradient descent (SGD)` stragglers will cause the entire system to slow down. This problem grows with the number of machines. There are a lot of algorithms that address this problem
  - `asynchronous SGD` can be a problem because the gradients from one machine might update the weights before gradients from another machine come in
  - asynchronous SGD is in theory slower to converge than synchronous SGD. in practice when the number of weights is large the gradient space is sparse enough that those weight collision events happen infrequently enough to not significantly effect training time
- Spreading your model on multiple machines can cause your batch size to be very large
  - each machine will take a smaller number of steps than one machine if you keep batch size the same (ex: 1000 batch size or 100 batch size on 10 machines). you can increase the learning rate some to account for this but only to a point. Batch size increases yield diminishing returns past a certain point
- The main worker sometimes uses a lot more resources than other workers depending on the model setup
  - using a smaller batch size on the main worker is a common solve for this problem

#### Model Parallelism

Data parallelism uses whole copies of the same model on each machine, model parallelism uses different machines to train different components of the model. Ex: splitting up layers between machines, or splitting up forward and backward passes

In some cases this term is misleading because if you split up the layers then one machine has to wait for the other machine layers to finish

`Pipeline parallelism` is a technique to make different components of a model on different machines run more in parallel: Break the computation of each machine into multiple parts. When machine 1 finishes the first part of the computation it passes the result to machine 2 then continues with the second part, etc... Each machine runs both the forward pass and the backwards pass of each component of the neural network

Model and data parallelism aren't mutually exclusive, you can do both

### AutoML

Can you solve ML problems without much expertise and more compute? AutoML is designed to tackle this problem

#### Soft AutoML: Hyperparameter tuning

Hyperparameters like the learning rate, batch size, number of hidden layers, number of hidden units, dropout, adam optimizer values, quantization of numbers, etc. can be automatically tuned. Many ML frameworks come with built in capabilities for this. There's known methods like `random search`, `grid search`, and `bayesian optimization` that are useful for this. Keep in mind the hyperparameters may have different sensitivities to change

#### Hard AutoML: Architecture search and learned optimizer

What if we treat other components of the model or the entire model as hyperparameters?

Instead of manually combining components of a neural network or even choosing a neural network at all, just have an algorithm experiment with lots of combinations and pick the best one

`Neural Architecture Search (NAS)` is a method of doing this consisting of three components:

- **A search space**: defines possible values / architectures
- **a performance estimation strategy**: how to evaluate performance without training to convergence
- **A search strategy**: how to explore the search space. Random? RL? Evolution?

What about optimizers? Setting these as one of the components in AutoML is hard because without tuned hyperparameters optimizers don't perform optimally.

You can train a neural network to be your optimizer! This has been done before and can be trained on either the same dataset or many others, and you can continually improve it across many tasks.

AutoML requires a ton of upfront training costs that many people can't afford. But even if you can't use it directly you can still use its benefits:

- use AutoML generated models like efficientnet
- models made this way may be able to solve previously unsolvable tasks

### Four Phases of ML Model Development

1. Before machine learning
    - try simple heuristics, ranking ,etc.
2. Simplest machine learning models
    - start with a simple algorithm, something that gives you visibility into its working to allow you to validate the usefulness of your problem framing and the data. Logistic regression + gradient boosted trees, k-nearest neighbors are great
    - this allows you to build out pipelines for data engineering and frameworks to iterate and test without worrying about complex ML
3. Optimizing simple models
    - try different objective functions, hyperparameter tuning, feature engineering, etc
4. Complex models
    - If needed experiment with more complex models, and test these models both for initial accuracy and accuracy decay in production

### Model Offline Evaluation

Having a clear understanding of what the evaluation metrics for a model will be is a good start. Working with business partners can help.

Ideally offline testing and production would use the same metrics, but this isn't always possible. In testing you have ground truth labels. In production you don't always

This section talks about evaluating models before they're deployed

#### Baselines

Establishing baselines is important. 90% accuracy doesn't mean anything in a vacuum

Examples of common baselines:

- **Random Baseline** - see what the performance of the model is predicting at random. If the majority label makes up 90% of the labels, a random model may get 90% accuracy on that label. Try both random with uniform distribution and random with the distribution of the labels
- **Simple Heuristic** - Build a simple model to compare against using simple keywords or basic logic and see how it performs
- **Zero rule baseline** - Special case of the heuristic baseline where your model always predicts the most common class. For example in a recommendation system, just recommend the most frequently used app to the user.
- **human baseline** - See how your model performs compared to human experts
- **Existing Solutions** - See how the model does compared to existing solutions in place. Even if it's worse, if its cheaper or simpler it may still be useful

#### Evaluation Methods

In production evaluation is much more than just performance metrics on test data.

- **Perturbation Tests** - Testing to see how small changes to your test splits affect model performance (inserting random words, static to images, random numbers to prediction models, etc.)
  - Ideally inputs used to train the model will be similar to those in production but that's rarely the case. Production data is usually noisier than training data, and collecting more data can be expensive. To get a sense of how well your model might perform with noisy data
- **Invariance Tests** - Testing to see if certain changes to the inputs lead to changes in the output (like race, gender, etc.)
  - A good strategy is to just exclude sensitive information from your model
- **Directional expectation tests** - Test that changes to key inputs cause expected changes in outputs (ex: prediction housing prices. changes in lot size shouldn't decrease the price)
- **Model Calibration** - Making sure that when a model predicts something with 70% probability, it actually happens that much
  - Ex: a recommendation system should show the same distribution of recommendations as the user likes. For movies if they watch 80% romance and 20% comedy, the recommendations should have a similar distribution
  - Ex: for an ad click model if a model is deciding between two ads and predicts one will have a 10% chance of clicking and one has a 50% chance, but the 10% one is actually 5%, the number of estimated clicks will be off even if the ranking if not
  - to measure this a simple measure is counting the number of times a probability is outputted and the frequency that prediction came true, then plot them against eachother. A well calibrated model will have a straight sloped line.
  - Many calibration measures exist. Check out Richardson and Pospisil for calibrating neural networks
- **Confidence Measurement** - Seeing which predictions to show to users based on the model's confidence, and defining the right threshold for this confidence
  - unsure predictions shown to users can cause annoyance or even system failure. Sample-level metrics should be used to calibrate this
- **Slice-based evaluation** - Looking at your models performance against slices of your data separately. A predictable and good model should perform similarly on most slices of data
  - Focusing on overall performance can cause harm if certain slices performance is significantly worse than others
  - If a certain slice is more important than others then its important to ensure the best performance on that slice, even if it means overall metrics drop
  - aggregations can conceal and contradict actual situations. Sliced-based metrics are important. see `Simpson's Paradox`
  - Slices and ad-hoc analysis also helps you detect non-ML problems (ex: a desktop and mobile user having different UI options that's hurting your model's perfmormance on one)

How to find slices in data:

- **Heuristics-based** - use domain knowledge to slice your data using rules (ex: locations, types of users, etc.)
- **Error Analysis** - Go through misclassified examples and look for patterns
- **Slice finder** - automated solutions. Generally work by generating slice candidates with beam search, clustering, decision trees, then pruning out clearly bad candidates and ranking whats left
