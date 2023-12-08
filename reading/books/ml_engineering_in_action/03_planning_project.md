# Chapter 3: Planning and Scoping a Project

The biggest killers of ML projects are planning and scoping projects

Teams focusing less on the *how* and more on the *what*, and working more in collaboration and communication with eachother and the business leads to more successful projects

ML projects that start with a vague problem statement from the business and data scientists immediately begin trying out algorithms are doomed to fail

`A better approach:`

- Get requirements
- Scope out project at kickoff meeting with business
- Hold internal meeting to plan research
- Select approaches to test in experimentation phase
- Hold another business unit meeting to explain experiments and gather additional info
- Finalize approach based on discussions

NOW work

This decreases wasted effort chasing what the customer really wants after building a solution, and builds a sense of trust with the customer

**Planning and scoping properly as an ML practitioner, rather than leaving it to PMs, will save you a lot of time and trouble down the road**

## Planning: You want me to predict what?

A lack of planning and solid understanding of the business problem to solve and customer requirements can lead to failure, even if your model metrics are good.

Ex: A product recommendation system has a low RMSE but all the recommendations are of the same product type, when the business wanted variation

Business stakeholders often know very nuances details of datasets and problems that need to be fleshed out through frequent collaboration

Frequent validation and checks from them, and not just model metrics, are important to a successful model

### Basic Planning for a project

The beginning of an ML project starts with an idea. That idea can have many meanings to many people. The first thing to do is have a meeting and discuss *Why are we building this?*

`Goals of the first meeting:`

- Opens a discussion to motivations for the project from different groups
- Defines the expectations of the output of any ML work done
- Decides an metric criteria for the model's performance to be used to measure success
- Aligns everyone's expectations (business users and dev team)

There's a venn diagram with little overlap at the start of a project between business and DS team understanding of the project. **It's the job of the DS team lead and project manager to widen the overlap before any discussion of technical details**

`False Assumptions to Challenge in Planning:`

- Assumption of Business Knowledge
  - ML teams are often insulated and don't share intricate business knowledge that many execs and leaders take for granted
  - SMEs should be used to explain how they currently do related tasks the model is being designed to do, this allows for discovery of nuances and rules that may be applied to model outputs
- Assumption of Data Quality
  - Data quality is a huge problem, and a main reason companies struggle to get ML solutions to production
  - Anticipation, validation, and understanding of the nature of data from SMEs is the way to solve this. Data will never be perfect
- Assumption of Functionality
  - There's always new feature requests from end users in a demo
  - Don't dismiss them, or blindly accept them. Present the time, money, and human cost to the feature and let the customer decide if its worth it
- Curse of Knowledge
  - Details of solutions will probably sound like babble to anyone outside the development team
  - Try to handle complex topics in layperson context always, if they ask for more detail, ease into it
- Analysis Paralysis
  - Without planning, an ML team will just overexperiment in an unbounded fashion
  - Approach projects with the overall goal in mind and keep the solution as simple as possible

### The First Meeting

The first meeting with the business should focus on the **why** and the **what**

Align on what you're actually going to be building and why (not technical details)

A common way to begin these kinds of convos is figuring out how the desired outcome is currently done. Very rarely are truly greenfield projects proposed and done, there's usually someone with good SME experience.

"Explain how you do it so I can help automate this for you"

### Plan for demos

frequent demos are useful in ML the same way they are in agile: they reduce big rework later, they enable continuous engagement and learning, and delivery of continuous value

Getting into a development team echo chamber risks caring too much about the model statistics and not about the real outputs and value of it

You don't need fancy web apps or anything. Slides and images can be fine, a wirefram diagram, etc. to show what the final output for the user will be

### Experimentation by solution building: wasting tame for pride's sake

without proper planning around experimentation, lots of time can be unnecessarily thrown away

Don't just try out a bunch of models and build a solution with each to see how they do at the end of the project.

Do timeboxed experimentation on multiple models, pick the best one, and build out a solution. You can always iterate after you deliver to production

## Experimental scoping: Setting expectations and boundaries

To plan out research and experiments, you first should have had discovery sessions to understand the critical features that need to get built and list the critical aspects for the MVP

Experimentation should **not** be used to scope the project from an ML perspective. This will allow the experimentation to continue boundlessly and a product will never get shipped.

### What is Experimental scoping?

After initial planning and requirements gathering, a few potential solution paths should be defined, and each should be assigned a time for it to be vetted

**Set expectations to the larger team how long you will spend on experimentation, and how much of that time you will dedicate to each potential path**

Setting a deadline to this unknowable task helps bring focus and organization to the process

Straightforward use cases could be 1-2 weeks of research and testing, complex ones could be 2 weeks of pure research then 1-2 weeks of hacking together scripts for testing and visualizations

There's no standard rubric for how long this phase should be. It's dependent on problem, industry, data, experience of the team, and complexity of each option being considered. The most important aspect is this stage and communication of how long it takes to the business should never be overlooked.

### Experimental Scoping for the ML team: Research

Experimentation varies widely across ML projects. The most important theme is **it should be time boxed**. This is frustrating for ML practitioners because it may not result in the "best" solution. But it will result in a working solution more times than not.

Keys to good research phase:

- You should select a good mix of approaches that will allow for easier selection down the line (you won't have to split hairs over 3 deep learning ideas)

- You should whittle down a large list of options to just a few, not too many to be overwhelming and not too few to box you in

Agree on the paths at the end of the research phase and begin experimentation

### Experimental Scoping for the ML team: Experimentation

Keys to good experimentation phase:

- **Time box the experimentation phase, just like research**
- Be clear upfront about the expectation of outcomes from experimentation. Make an experimentation plan (high level outside of stories / tasks on the steps that will be taken for each approach tested, and how datasets and results will be made)
- The goal is to produce a simulation of the end product that allows for unbiased comparison of solutions being considered
- speed and comparability are the most important aspects of development in this phase
  - no need to tune models or for code to be written in a way that is usable in the final codebase
- two main estimates that should be made here are the model performance and complexity to implement

**Be wary of complexity risk, if performance gains are seen at the expense of additional complexity, make sure it's value is clear to everyone (including the business) before choosing the more complex option**

#### Total Cost of Ownership

This is an important concept and involves the total cost of developing and maintaining a solution. It's ofter overlooked in ML as there's significant work post-deployment to maintain the solution. Includes:

- ETL
- Retraining / inference costs
- ML Platform
- Managed service / internal maintenance
- Storage and serving layers

#### Estimating Work

Experimental phase should give an idea on all the aspects that will be needed for the ML project (but they won't be implemented). A team should know broadly the features, ETL, data, enhancement plans, and tools that will be used for the project.

Identify answers to these questions:

- How long will this take to build?
- How much will this cost to run?

^ answer these in between experimentation and development
