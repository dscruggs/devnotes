## Experimentation in action: Planning and researching an ML project

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

#### Forget blogs - read the API docs

Blogs are well-intentioned, but due to the short nature of them often don't include important details for real-world ML solutions
