# Chapter 7: Experimentation in action: Moving from prototype to MVP

After experimentation you'll have an approach selected, but that approach's implementation is likely very unrefined.

Hyperparameters need to be tuned, code refactored and hardened with tests, etc.

This chapter covers that shift

Goal of this phase: build a solution that is good enough to solve the proble min a way that is robust enough so that it's not breaking constantly

Shift your focus to monitoring, automated tuning, scalability, and cost. Away from science and towards engineering

The first step in this is model tuning

## Tuning: Automating the annoying stuff

Manual tuning of hyperparameters is boring and time consuming. It's also hard to track and rarely results in the best parameters. You should use automation in this step

You should target 4 things in your tuning of the model:

- `Accuracy:` The model predictions should be as good as possible (as is reasonable to achieve)
- `Timeliness in training:` You shouldn't spend weeks tuning a model. Most models will take hours or days. Be efficient
- `Maintainability:` automated tuning will allow for retraining to be automated
- `Timeliness in development:` tuning code should be reusable and modular since it will be reused in the future for retraining

### Tuning Options

Listing from least to most complex:

- **Manual tuning (educated guessing)**
  - this is hard enough for a single model, but this forecasting problem where you're trying to optimize over many models (for different locations' data) manual tuning is basically a nonstarter
  - only manual tune during experimentation, or if you're working with a VERY basic "baseline mode" with 1 or 2 parameters
- **Grid search**
  - brute force search across parameter spaces
  - the drawback here is it's very time consuming to test multiple options. there's no optimization here
  - only use if you have a few options to test
- **Random Search**
  - random search through a range of values in the parameter space
  - useful for testing a large space without having to brute force every single combination
  - limited in that you won't be testing all combinations so you may miss values if you don't have enough random samples
  - random search is pretty much always better than grid search if you want to have a wide range of values tested
- **Model-based Optimization: tree-structured parzen estimators (HyperOpt)**
  - Uses bayesian techniques for optimization relying on gaussian process or tree of parzen estimators
  - outperforms all classical parameter search methods, but typically can't be run in parallel with great effectiveness
- **More advanced (and complex) techniques**
  - probably don't need to go more advanced than bayesian optimization unless its a highly specialized use case
  - Genetic algorithms with bayesian prior search optimization to create search candidates with the highest likelihood of giving good results
  - AutoML (useful, but sacrifices a lot of control and visibility into decisions)
- **(my note) other tuning libraries**
  - Ray tune
  - scikit-optimize
  - optuna

### Using hyperopt to tune a complex forecasting problem

- hyperopt is a python library that uses an object function and optimizes it for the different hyperparameters, and does it much more efficiently than random search

## Choosing the right tech for the platform and team

In this forecasting example, the pipeline built to tune the model works well for 1 model. But they need thousands of models for the different airports, so this synchronous method doesn't work great.

Distributed or asynchronous methods should be used in cases like this. Going to go over Apache Spark

### Why Spark?

*speed*

Factors influencing decisions here:

- `time:` monolothic pipelines and long running jobs means that any failures lead to huge time wasted
- `stability:` memory leaks and object reference could cause job failures
- `risk:` keeping machines dedicated to long-running jobs risk platform issues that could cause failures
- `cost:` longer runtime means more cost

There's a few different approaches discussed:

- the first would be to keep all data on the driver and evaluate hyperparamters at the same time via the workers.
- the second is to put the data for each airport into a separate worker and treat each as its own VM

### Handling tuning the driver with Sparktrials

Sparktrials is a module from hyperopt

Approach: Each airport is looped through on the driver, but parallel workers go through the hyperparameter optimization. This has a small tradeoff with bayesian methods because subsequent optimizations will not always have the benefits of all previous experiments (due to asynchronous nature). This tradeoff is increased with the number of parallel workers, so you need more iterations

`multi-core iterations = single-core iterations + (parallelism factor / 0.2)`

Little modification is needed for this approach from the single-core approach, and you can easily log these in MLFlow

### Handling tuning from the workers with a pandas_udf

The previous approach works for a small number of airports, but as those grow the risk to sequentially loop through each airport grows (both in execution time and risk of failure). It's also limited in how much we can increase the parallel workers without hurting search performance

The second approach parallelize the model phases themselves (horizontally scaling), rather than a vertical scaling

- This is simpler from an optimization standpoint: it's basically just many single-core cases and no sync issue
- this requires more overhead for each worker because logging and plotting needs to be done inside the minimization function passed to each worker
- you also need new code that will group the outputs together and handle the many different outputs coming from workers

This method has the obvious problem of needing to scale 1 worker per new airport, but dramatically speeds up execution time. For 7,000 airports with 40 workers you reduce the time from > 5,000 hours sequentially to 23 hours

**Scalability of the execution architecture is just as important as any of the ML components. If solving the problem (runtime) takes hundreds or thousands of hours, chances of failure are very high**

### Using new paradigms for teams: platforms and technologies

The right plaform and technology choices have an equal or greater impact on the success of a project as the ML model choice does

The right platform and technology should aim to:

- minimize execution cost
- maximize stability of the solution
- shorten the development cycle

Do your homework and look at a variety of options when deciding

Make sure to incorporate time to learn new technology choices into your timeline estimates
