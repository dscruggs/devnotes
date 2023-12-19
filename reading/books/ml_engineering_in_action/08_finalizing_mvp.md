# Chapter 8: Experimentation in action: Finalizing an MVP with MLflow and Runtime Optimization

This chapter covers tracking throughout the ML development lifecycle

In the selected time series example, retraining is required at each prediciton. But this is usually not the case with supervised learning, so tracking and monitoring is required to understand when to retrain, and will help keep track of all your past experiments and work

Lots of things to track:

- model scoring metrics
- model versions
- optimization history in automated tuning
- validation tests
- metadata
- artifacts
- run-specific data

MLFlow is a tool to help with all this

## Logging: Code, metrics, results

In model development it's important not just to have the current model solution, but also have history for reference

In the time series example there's thousands of airports (and 1 model per airport). It's impossible to keep track of whats going on without nice history and metrics and logs to look at and reference

History is important for explainability and debugging as well as keeping track of things while you build the solution

### MLflow tracking

In the spark example, in the Sparktrials case they just put the MLflow context as a wrapper around the entire tuning run

For the pandas_udf example there's no parent-child relationship in MLflow so you need to use custom tags to establish that

Just try to design you logs for searchability, because while logs are nice, no one likes to scroll through thousands of lines to find the 1 piece of info they want

### Please stop printing and log your information

Print statements are only useful during the run, and can't really be viewed later. So these should only be used for initial quick debugging, not in production code

**ALL** critical info for a project's code execution should be logged and stored for future reference at all times. If you aren't sure if you'll need it later, log it. You'll thank yourself later.

### Version control, branch strategies, and working with others

Version controlling and development patterns should be basically identical to traditional software development

Recommended pattern from the book:

- each person checks out a branch from a master for their feature -> writes changes to that branch -> pulls from master -> tests code -> then creates a pull request to release branch
- at the end of the sprint, merge all changes into sprint release branch and run tests -> if they pass, merge to master

Whatever the decision is, make one and stick to a pattern than minimizes bad merges and git hell

## Scalability and concurrency

Scalability is important in ML, especially when it comes to cost of operation

- One method of scaling is through parallelization / distributed computation to reduce runtime and inefficencies
  - there's a complexity-performance tradeoff here. The more complex your infrastructure is to support scalability, the more complex your codebase becomes and the more engineering skill is required to maintain the ML solution
  - complexity order:
    - serial processing (single machine, looping)
    - parallel processing (multiple machines)
    - concurrent processing (single machine, multiple threads)
    - concurrent parallel processing (multiple machines, multiple threads on each machine)

### What is concurrency?

- `Concurrency` is the act of executing many tasks at the same time on a single core
- `parallelism` is dividing tasks into subtasks to be executed in parallel on discrete cores of a CPU or GPU

Leveraging these can be useful, but make sure the benefit justifies the added complexity

### What you can (and can't) run asynchronously

- Not everything can run in parallel or on a distributed system
  - many algorithms requre maintaining state to function correctly

Try to break up tasks if there's no dependencies. If there are, then you can't really parallelize

Use cases where models need the entire feature set and samples are usually poor use cases for parallelism

Best bet is to check the library and model you're using and see if there's distributed implementations
