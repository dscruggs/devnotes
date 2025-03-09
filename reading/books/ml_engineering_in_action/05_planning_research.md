## Chapter 5: Experimentation in action: Planning and researching an ML project

This chapter covers the first phase of ML experimentation, using an airline time series example

- planning
- setting up environments
- evaluating a dataset
- conducting research

## Planning Experiments

Once you have an understanding on how a problem might be solved, it's time to plan out your experiments

### Perform basic research and planning

- Look at what data is available to you
- set up a dev environment using Docker or Anaconda or pyenv

#### Quick visualization of the dataset

- Before choosing an ML approach, look at the data
  - come up with a list of questions that should be answered to inform the research for model options and platforms
  - some questions for the time series example:
    - stationarity
    - seasonality
    - scaling to multiple airports
    - what models are good for this use case?
    - do we have enough time to come up with a good direction for the project?

Jumping directly to modeling and skipping EDA will cause pain later

#### Research Phase

Where do you start?

- blog posts (informative but many details of the real world are left out)
- papers (informative, don't do a good job on giving applications)
- API docs (great at understanding the code, but simplistic)
- books **author prefers this one** (lots of great ones are out there for different approaches, and go in detail on topics)

Compile a list of potential algorithms / approaches

- for time series example:
  - linear regression
  - ARIMA
  - Exponential smoothing
  - VAR
  - SARIMA

Next step is to find out which libraries have these algorithms

Over time you should develop a healthy compilation of go-to libraries for different use cases, and continually budget to expand this in the team's skillsets

### Forget blogs - read the API docs

Blogs are well-intentioned, but due to the short nature of them often don't include important details for real-world ML solutions

time series example blog:

- very simple demo
- uses random sampling (not good for time series)
- default hyperparameters
- script-based, no awareness of loading files or using outputs for other purposes than print statements

Use blogs as a reference to get new ideas, then vet those ideas. Don't take blogs at their word

#### Seriously, read the API docs

Once you have a list of approaches to test, look at what libraries offer those and read the API docs

benefits of API docs:

- list hyperparameters and usually give good descriptions of what they do
  - you know what knobs exist and how to turn them, what their effect will be
- you know what the inputs and outputs are supposed to look like
- you see what options are essential vs placeholders

#### Quick testing and rough estimates

The focus of initial experimentation should be to find the best and simplest approach to the problem, but you shouldn't completely optimize each solution

- focus on understanding the models and doing evaluations that will give an idea of performance later when you do have time to tune
- don't spend a ton of time optimizing hyperparameters now, it'll restrict how many options you can look at
- don't spend a ton of time on the data and feature engineering

### Draw straws for an internal hackathon

You need to figure out which direction the project should be going ASAP, which means boundaries on testing

- block off a set amount of time for testing, and give an end-time deadline
- set rules
  - everyone must use the same dataset
  - everyone must use the same eval metrics
  - each evaluation needs to forecast over the same period
  - visualiziations of forecast and metrics should be provided
  - the experimentation code needs to be re-runnable from scratch
  - make sure the language and platform is available for the team to use in case it's chosen (no moonshots)

For the time series example:

- one week of testing, review at the end of that week
- data modeled will be for JFK domestic passengers
- eval metrics will be:
  - mean absolute error
  - mean absolute percentage error
  - mean squared error
  - root mean square error
  - R squared
- forecast period for evaluation will be the last five years of the dataset
- experimentation will be done in jupyter notebooks running python 3, using anaconda in a docker container

#### Level the playing field

You need to prevent indecision and chaos when comparing approaches. So use the same data splits, and the same evaluation metrics. Rank the metrics in order of importance based on your problem (in this case they decided MAPE was most important since they wanted scale-invariance)

## Performing Experimental prep work

The goal of experimental prep work is to do a deep analysis of the dataset, and create a common set of tools for the team to use during experimentation

- EDA
- data cleaning
- basic feature engineering
- visualization / eval functions
- platform / dependency decisions

Time series example:

- EDA on stationarity, decomposition of trend, identification of outliers
- Use jupyter notebooks in docker containers
- building basic visualization tooling that will aid in the rapid phases of model testing

### Performing Data Analysis

time series example:

- make some basic visualizations of trend and variance
- looking for potential problems with splitting the data if they don't reflect trend components
- note that some models perform better than others on stationary data, so do a test for stationary (dickey-fuller)
- validate lags and trend decompositions, visualize results
- continue to try to get rid of repeating trends in the data through decomposition
- got some nice results with this, remember the learnings for experimentation

#### How clean is our data?

time series example:

- outliers
- missing values

### Moving from script to reusable code

Experimentation is meant to be quick and dirty. But you should look to reuse components whenever possible

reusable components to consider:

- data loading
- manipulating / cleaning raw data
- feature engineering
- visualizations
- evaluation metrics

Reusability is key in enforcing best practices and ensuring everyone is working with the same set of tools as well. You can make it really easy for those experimenting to evaluate and build models in the way they will be compared
