# Chapter 6: Experimentation in action: Testing and Evaluating a project

This chapter looks at how to evaluate and select potential approaches for an ML project

You should try to identify a "goldilocks zone" between testing too few approaches (you might not find one that works) and too many approaches (takes too much time)

## Testing ideas

Once you're ready to test you should have your standardized pipeline with standard data, visualizations, and metrics you will use to evaluate each approach

### Setting guidelines in code

Two main priorities in dealing with comparing multiple approaches:

- `standardizaton`: everyones on a level playing field and comparisons are easy
  - standard code and pipelines lets everyone focus on the goal: figuring out if the modeling approach is the right one. not wasting time trying to communicate results or format visuals
- `communication`: if you have standard visuals you can easily communicate results with the business
  - you need a clear idea of whats the best and be able to communicate that

**Don't communicate with the business with anything but the easiest-to-understand, most important plots and data**

#### Baseline comparison visualization

Make a really simple model for later visual comparison with other approaches.

Time series example:

- moving average, exponentially smoothed average

#### Standard Metrics

Everyone should be using the same metrics for evaluation and comparison of methods

### Running Quick Forecasting tests

Time is the most important aspect of this phase. Communication between everyone involved is very important to move fast. Possibly a good idea to designate a specific person to facilitate this communication and disperse group learnings

#### Creating a validation dataset

It's important to work with the same data across all your experiments. Make sure you're splitting your data in a repeatable and standard way too.

#### VAR model tests (what to do about bad results)

One of the examples given is a VAR model (which is multi variate unlike the univariate approaches)

They got poor initial results. The job here is to identify as quickly as possible (hours not days) if you can figure out the issues and get decent results with this approach

**embrace failure, don't have an ego about things. Failure in experimentation is common and often leads to great learnings**

The team re-read the API docs and tried out some other approaches, getting better results but still not great.

They learned VAR only works with stationary data, and this data is nonstationary based on their EDA. They also identified a few other issues with this approach (all series in the multivariate analysis must be the same length, doesn't handle divergent series well, etc.)

Since they have 1 more day to test things out, they work on addressing those concerns (make the data stationary and work on some seasonality issues) and get decent results to show

#### Rapid testing for ARIMA

They looked at the API docs and had a good start on what they needed to do from the prework in terms of parameters

They tried out forcing stationarity in the data, but discovered the raw data performed better with the model. They plot both results, and ARIMA looks promising

#### Rapid testing Holt-Winters Exponential Smoothing

The team ran into some versioning issue with their API version vs documentation

They got great results after finding the right documentation

## Whittling down the possibilities

**Criteria to consider for each model / path:**

- predictive power / accuracy (for now probably just model metrics-wise. But think about the real value / output here)
- solution complexity
- difficulty in delivering the solution on-time
- total cost of ownership
- extensibility

### Evaluating prototypes properly

- Don't just use metrics, use visuals
- Make a decision matrix on the criteria and results for each model approach
- ignore all the clearly bad ones as soon as possible, don't cloud the discussion by continuing to show those

### Making a call on the direction to go in

Once a model is chosen, have another planning meeting for hard questions before writing any code and fully committing to a direction for the project

Hard questions are below:

- **How often does this need to run?**
  - This is important to know because it will decide retraining frequency and validate if the model will work
  - Ask the business "At what point will predictions become irrelevant" or "how often will these be viewed" to determine this
  - Decide on a min and max SLA for the prediction freshness
- **Where is the data for this right now?**
  - We need to know where the data is to ensure stable ETL that can meet the prediction SLA
  - If the team that currently owns the data can't meet the SLA, extra time will need to be dedicated for new ETL
- **Where are the forecasts going to be stored?**
  - Where are the outputs going to be used / viewed? By how many people?
  - This will determine the serving architecture / platform and scope that work
- **How are we setting up the codebase?**
  - Where's the code going to live, what's the design approach, and what's the deployment approach?
  - Where are logs and monitoring stored? Model Versioning / artifacts? Etc.
- **Where is this going to run for training?**
  - this should never be local. But this decision will help decide others
- **Where is the inference going to run?**
  - also should never be local. Also will help decide other factors
- **How are we going to get predictions to end users?**
  - is it going to powerBI? Webpage? SQL? etc.
  - If it's more complex than dropping in a DB, then you need extra work and people on that part of the project specifically
- **How much of our existing code can be used for this project?**
  - How much new code needs to be written vs can be reused from other projects / libraries
  - May be worth considering refactoring other projects if it's simple enough to reuse code
- **What is our development cadence, and how are we going to work on features?**
  - prepare for features and epic to be thrown away or refactored, don't get too attached to a certain way of coding during ML development
  - create a development cadence and structure that supports the rapid experimentation and feedback that supports success

### What's next?

Building an MVP!
